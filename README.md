# Obligatorio: Taller de Servidores Linux (Febrero 2026)

Este proyecto automatiza la creación de una infraestructura de red que permite compartir archivos entre servidores de distintos sistemas operativos (CentOS y Ubuntu) de forma automática y transparente.

## 1. Arquitectura y Topología
Para este taller utilizamos dos nodos principales comunicados en la misma red:

* **Servidor NFS (nfs01)**: CentOS Stream 9 - IP: `192.168.10.11`
* **Cliente NFS (app01)**: Ubuntu 24.04 - IP: `192.168.10.22`

## 2. Requisitos Previos
Para que esto funcione, necesitas:
1. Tener acceso SSH desde tu máquina "Bastion" a los nodos.
2. Tener instalado **Ansible** en la Bastion.
3. Haber configurado el archivo `inventories/hosts.ini` con las IPs correctas.

## 3. Cómo ejecutar el proyecto
Solo necesitas correr dos comandos desde la carpeta raíz del proyecto:

1. **Configurar el servidor (CentOS):**
   `ansible-playbook -i inventories/hosts.ini playbooks/nfs-server.yaml --ask-become-pass`

2. **Configurar el cliente (Ubuntu):**
   `ansible-playbook -i inventories/hosts.ini playbooks/nfs-client.yaml --ask-become-pass`

## 4. ¿Qué hace este proyecto? (Paso a paso)
* **En el Servidor**: Instala los servicios de red, crea una carpeta compartida en `/srv/nfs/shared` y genera un archivo de bienvenida personalizado con datos del servidor.
* **En el Cliente**: Configura un sistema de "automontaje" (Autofs). La carpeta compartida no ocupa espacio ni memoria hasta que alguien intenta entrar en ella. Además, levanta un servidor web en Python que publica esos archivos en el puerto 8080.

## 5. Cómo verificar que todo funciona
Sigue estos pasos en la máquina **Ubuntu**:

1. **Verificar el montaje automático**:
   Ejecuta `ls /mnt/shared`. Verás que el archivo `README-NFS.txt` aparece mágicamente.
   
2. **Verificar el servidor web**:
   Ejecuta `curl http://localhost:8080/README-NFS.txt`. Deberías ver el contenido del archivo generado en el servidor CentOS.
