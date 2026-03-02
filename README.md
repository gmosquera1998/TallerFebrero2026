Este proyecto automatiza la implementación de una infraestructura de red híbrida utilizando Ansible. Se configura un servidor de archivos centralizado (NFS) y clientes que acceden a la información de forma dinámica y segura mediante Autofs y un servidor web Python.

La infraestructura se compone de tres nodos comunicados en la misma red privada:

Hostname,Sistema Operativo,Dirección IP,Función
nfs01,CentOS Stream 9,192.168.10.11,Servidor NFS (Storage central)
ubuntu01,Ubuntu 24.04,192.168.10.21,Cliente + Webserver
ubuntu02,Ubuntu 24.04,192.168.10.22,Cliente + Webserver

[ SERVIDOR CENTOS ] <----(Protocolo NFS)----> [ CLIENTES UBUNTU ]
 (Export: /srv/nfs/shared)               (Mount: /mnt/shared)
                                                  |
                                         (Python Webserver 8080)
                                         (Protección: Firewall UFW)

- El servidor CentOS exporta el recurso, los clientes Ubuntu lo montan "on-demand" y lo sirven vía HTTP en el puerto 8080.

- Consideraciones para Servidores Recién Instalados

Para que la automatización funcione en servidores limpios, asegúrese de cumplir estos requisitos en su nodo de control (Bastion):

- Colecciones de Ansible: Es obligatorio instalar la colección para gestionar el firewall en Ubuntu:
- ansible-galaxy collection install community.general

- Acceso SSH: Debe existir conectividad SSH mediante llaves públicas hacia todos los nodos.

- Inventario: Verifique que el archivo inventories/hosts.ini refleje las IPs de su entorno.

El proyecto utiliza una estructura modular orquestada por el archivo site.yaml. Este "Master Playbook" ejecuta todas las tareas en el orden lógico correcto:

ansible-playbook -i inventories/hosts.ini site.yaml --ask-become-pass

¿Qué automatiza este despliegue?

- Servidor NFS: Crea el directorio, configura permisos y habilita servicios en el firewall de CentOS.

- Clientes Autofs: Configura el mapa de montaje automático para /mnt/shared.

- Seguridad (UFW): Implementa una política restrictiva en Ubuntu, permitiendo solo SSH (22) y el puerto 8080.

- Webserver: Despliega un servicio de systemd que publica el contenido compartido de forma persistente.

Verificación del Funcionamiento

Realice estas pruebas en cualquiera de los nodos Ubuntu para confirmar el éxito de la tarea:

- Despertar el Montaje: Ejecute ls /mnt/shared. El contenido del servidor debe aparecer automáticamente.

- Prueba del Servidor Web: Ejecute curl -I http://localhost:8080/. Debería recibir una respuesta HTTP 200 OK.

- Acceso al Contenido: Ejecute curl http://localhost:8080/README-NFS.txt para leer el archivo generado en el servidor.

Referencias de Información

- Ansible Documentation - Playbook Orchestration
- https://docs.ansible.com/projects/ansible/latest/collections/all_plugins.html
- Aulas



