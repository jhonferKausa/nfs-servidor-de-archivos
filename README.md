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

```
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

```
sudo nano /etc/exports

Agregamos la siguiente línea (ajustando la IP de red según corresponda):

```
/srv/nfs/compartido 192.168.56.0/24(rw,sync,no_subtree_check)

- rw: Permite lectura y escritura.
- sync: Asegura que los cambios se escriban inmediatamente en el disco.
- no_subtree_check: Mejora el rendimiento al evitar verificaciones adicionales.

Guardamos el archivo y aplicamos los cambios:

sudo exportfs -ra
sudo systemctl restart nfs-kernel-server

## Paso 5. Verificar el servicio NFS

Comprobamos el estado del servicio:

sudo systemctl status nfs-kernel-server

Y verificamos qué carpetas están siendo compartidas:

sudo exportfs -v

## Paso 6. Configurar el cliente NFS (máquina que accede)

En otra máquina Linux (cliente), instalamos el paquete necesario:

sudo apt install nfs-common -y

Creamos un punto de montaje:

sudo mkdir -p /mnt/compartido

Montamos el recurso compartido desde el servidor:

sudo mount <IP_DEL_SERVIDOR>:/srv/nfs/compartido /mnt/compartido

Por ejemplo:

sudo mount 192.168.56.10:/srv/nfs/compartido /mnt/compartido

Verificamos que el montaje fue exitoso:

df -h | grep nfs

## Paso 7. Montaje automático (opcional)

Para montar la carpeta compartida automáticamente al iniciar el sistema, editamos el archivo /etc/fstab del cliente:

sudo nano /etc/fstab

Agregamos la línea:

192.168.56.10:/srv/nfs/compartido /mnt/compartido nfs defaults 0 0

Guardamos los cambios y montamos todo nuevamente:

sudo mount -a

## Paso 8. Prueba de funcionamiento

Desde el cliente:

touch /mnt/compartido/prueba.txt

En el servidor:

ls /srv/nfs/compartido/

Deberías ver el archivo prueba.txt.
Esto confirma que la conexión entre el servidor NFS y el cliente funciona correctamente.

## Paso 9. Integración con OpenStack

Cuando tengas tu entorno de OpenStack configurado, podrás integrar este servicio fácilmente:

- Conecta el servidor NFS a la red interna de OpenStack.
- Asigna una IP fija al servidor NFS (por ejemplo, 10.0.0.10).
- En cada instancia Linux dentro de OpenStack:

sudo apt install nfs-common -y
sudo mkdir -p /mnt/nfs
sudo mount 10.0.0.10:/srv/nfs/compartido /mnt/nfs

- Puedes automatizar el montaje editando el archivo /etc/fstab como se mostró en el paso 7.

## Paso 10. Solución de problemas comunes

