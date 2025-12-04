# 📁 Servidor de Almacenamiento Compartido con NFS en OpenStack

Este documento describe la configuración de un **servidor de almacenamiento compartido** utilizando **NFS (Network File System)**, así como el proceso para permitir que **múltiples máquinas virtuales creadas en OpenStack (MicroStack)** accedan a dicho recurso desde una red interna.
<img width="192" height="166" alt="image" src="https://github.com/user-attachments/assets/18e73665-6bb8-427f-8d78-2923c12a9e41" />

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

---

## 📂 2. Creación del directorio compartido

```bash
sudo mkdir -p /srv/compartido
sudo chmod 777 /srv/compartido
```

Este será el directorio accesible para las instancias de OpenStack.

---

## 🔧 3. Configuración de `/etc/exports`

Editar el archivo:

```bash
sudo nano /etc/exports
```

Agregar la línea que permite acceso a la red interna de OpenStack:

```
/srv/compartido 10.20.20.0/24(rw,sync,no_subtree_check,no_root_squash)
```

Aplicar los cambios:

```bash
sudo exportfs -ra
sudo systemctl restart nfs-kernel-server
```

---

## 🌐 4. Verificación del servidor NFS

Desde cualquier máquina virtual creada en OpenStack:

```bash
showmount -e 10.20.20.1
```

Salida esperada:

```
/srv/compartido 10.20.20.0/24
```

---

## 🖥️ 5. Configuración de las máquinas virtuales de OpenStack

En cada instancia, instalar el cliente NFS:

```bash
sudo apt update
sudo apt install nfs-common -y
```

Crear el punto de montaje:

```bash
sudo mkdir -p /mnt/nfs
```

Montar el recurso compartido:

```bash
sudo mount 10.20.20.1:/srv/compartido /mnt/nfs
```

Comprobar acceso:

```bash
echo "archivo desde la VM" | sudo tee /mnt/nfs/test.txt
```

---

## 🔄 6. Montaje permanente mediante `/etc/fstab` (opcional)

Para que el recurso se monte automáticamente al iniciar la instancia:

Editar el archivo:

```bash
sudo nano /etc/fstab
```

Agregar:

```
10.20.20.1:/srv/compartido   /mnt/nfs   nfs   defaults   0   0
```

Aplicar:

```bash
sudo mount -a
```



Si quieres ampliarlo con un diagrama, explicación conceptual o sección de problemas comunes, puedo generarlo también.
