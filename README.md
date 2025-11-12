# Proyecto Final - Servidor de Almacenamiento Compartido con NFS en Ubuntu Server

## Descripción general

Este proyecto tiene como objetivo **configurar un servidor de almacenamiento compartido utilizando NFS (Network File System)** sobre **Ubuntu Server**, permitiendo que **múltiples máquinas virtuales** puedan acceder a los mismos archivos de forma centralizada.  

Este tipo de configuración es ideal para entornos de virtualización como **OpenStack**, donde varias instancias necesitan compartir datos o archivos de configuración.

---

## Requerimientos

- **Ubuntu Server 22.04 LTS** (para el servidor)
- **Una o más máquinas virtuales Linux** (para los clientes)
- **Acceso con privilegios `sudo`**
- **Conectividad de red** entre servidor y clientes

---

## Paso 1. Actualizar el sistema

Antes de iniciar, asegúrate de tener tu sistema actualizado:

```bash
sudo apt update && sudo apt upgrade -y

---

## Paso 2. Instalar el servidor NFS

Instalamos los paquetes necesarios para habilitar el servicio NFS:

```bash
sudo apt install nfs-kernel-server -y

---

## Paso 3. Crear el directorio compartido

Creamos la carpeta que será compartida entre las máquinas:

```bash
sudo mkdir -p /srv/nfs/compartido
sudo chown nobody:nogroup /srv/nfs/compartido
sudo chmod 777 /srv/nfs/compartido

Se asignan permisos amplios para facilitar el acceso desde los clientes. En entornos productivos, se deben aplicar permisos más restrictivos.

## Paso 4. Configurar las exportaciones

Editamos el archivo de configuración principal de NFS:

```bash
sudo nano /etc/exports

Agregamos la siguiente línea (ajustando la IP de red según corresponda):

```bash
/srv/nfs/compartido 192.168.56.0/24(rw,sync,no_subtree_check)



