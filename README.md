# 📁 Servidor de Almacenamiento Compartido con NFS en OpenStack

Este documento describe la configuración de un **servidor de almacenamiento compartido** utilizando **NFS (Network File System)**, así como el proceso para permitir que **múltiples máquinas virtuales creadas en OpenStack (MicroStack)** accedan a dicho recurso desde una red interna.

---

## 📌 Objetivo

* Implementar un **servidor de archivos NFS** en el nodo donde está instalado MicroStack.
* Compartir un directorio accesible a través de la red interna de OpenStack (`10.20.20.0/24`).
* Montar el recurso compartido en **varias instancias** de OpenStack.
* Habilitar el acceso simultáneo para lectura y escritura.

---

## 🖥️ 1. Instalación del servidor NFS en el host MicroStack

Actualizar paquetes e instalar el servicio:

```bash
sudo apt update
sudo apt install nfs-kernel-server -y
```
<img width="636" height="142" alt="image" src="https://github.com/user-attachments/assets/e16e5f31-f1e1-4600-b56a-8cfe8ba7f4a2" />
<img width="625" height="128" alt="image" src="https://github.com/user-attachments/assets/d9da7c81-cea7-4c2a-863e-c1419e298a06" />

---

## 📂 2. Creación del directorio compartido

```bash
sudo mkdir -p /srv/compartido
sudo chmod 777 /srv/compartido
```
<img width="442" height="39" alt="image" src="https://github.com/user-attachments/assets/dba204a2-d441-4443-97b8-e17227c4d187" />

Este será el directorio accesible para las instancias de OpenStack.

---

## 🔧 3. Configuración de `/etc/exports`

Editar el archivo:

```bash
sudo nano /etc/exports
```
<img width="372" height="20" alt="image" src="https://github.com/user-attachments/assets/608cc147-f44c-44d8-a5e9-84306ed30a49" />

Agregar la línea que permite acceso a la red interna de OpenStack:

```
/srv/compartido 10.20.20.0/24(rw,sync,no_subtree_check,no_root_squash)
```
<img width="799" height="203" alt="image" src="https://github.com/user-attachments/assets/060b6537-5594-4612-8470-0642e60dac36" />

Aplicar los cambios:

```bash
sudo exportfs -ra
sudo systemctl restart nfs-kernel-server
```
<img width="527" height="39" alt="image" src="https://github.com/user-attachments/assets/2f84b323-ed93-4e04-9656-253735520e37" />

---

## 🌐 4. Verificación del servidor NFS

Desde cualquier máquina virtual creada en OpenStack:

```bash
showmount -e 10.20.20.1
```
<img width="429" height="22" alt="image" src="https://github.com/user-attachments/assets/4592df5d-d64c-4d19-b892-f3be0011ca5e" />

Salida esperada:

```
/srv/compartido 10.20.20.0/24
```
<img width="303" height="45" alt="image" src="https://github.com/user-attachments/assets/ee421ecc-2de5-409d-b735-4038d734e1b9" />

---

## 🖥️ 5. Configuración de las máquinas virtuales de OpenStack

En cada instancia, instalar el cliente NFS:

```bash
sudo apt update
sudo apt install nfs-common -y
```
<img width="577" height="72" alt="image" src="https://github.com/user-attachments/assets/3c144f2a-783c-4ae7-9790-c14763c58910" />
<img width="438" height="75" alt="image" src="https://github.com/user-attachments/assets/aec70b61-9b64-474a-bc39-c760fecc8484" />

Crear el punto de montaje:

```bash
sudo mkdir -p /mnt/nfs
```
<img width="421" height="25" alt="image" src="https://github.com/user-attachments/assets/e874d080-5cdb-4213-8d93-e55fff0338ee" />

Montar el recurso compartido:

```bash
sudo mount 10.20.20.1:/srv/compartido /mnt/nfs
```
<img width="660" height="24" alt="image" src="https://github.com/user-attachments/assets/91ee7654-1a44-437e-bd59-3c80720ed877" />

Comprobar acceso:

```bash
echo "hola" | sudo tee /mnt/nfs/test.txt
```
<img width="602" height="41" alt="image" src="https://github.com/user-attachments/assets/7d0bb28d-2ff5-41f7-8453-953303cd5cfc" />

---

## 🔄 6. Montaje permanente mediante `/etc/fstab` (opcional)

Para que el recurso se monte automáticamente al iniciar la instancia:

Editar el archivo:

```bash
sudo nano /etc/fstab
```
<img width="401" height="28" alt="image" src="https://github.com/user-attachments/assets/67e0716b-f1cc-4d25-aaaa-90720666b932" />

Agregar:

```
10.20.20.1:/srv/compartido   /mnt/nfs   nfs   defaults   0   0
```
<img width="802" height="288" alt="image" src="https://github.com/user-attachments/assets/21ebb02c-e043-422f-9e6d-a7548c0eb937" />

Aplicar:

```bash
sudo mount -a
```
<img width="330" height="23" alt="image" src="https://github.com/user-attachments/assets/2492b331-543a-41cc-bdd0-b4397a657d0e" />
