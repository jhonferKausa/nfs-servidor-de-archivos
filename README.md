# 🗂️ Proyecto Final - Servidor de Almacenamiento Compartido con NFS en Ubuntu Server

## 📘 Descripción general

Este proyecto tiene como objetivo **configurar un servidor de almacenamiento compartido utilizando NFS (Network File System)** sobre **Ubuntu Server**, permitiendo que **múltiples máquinas virtuales** puedan acceder a los mismos archivos de forma centralizada.  

Este tipo de configuración es ideal para entornos de virtualización como **OpenStack**, donde varias instancias necesitan compartir datos o archivos de configuración.

---

## 🧰 Requerimientos

- **Ubuntu Server 22.04 LTS** (para el servidor)
- **Una o más máquinas virtuales Linux** (para los clientes)
- **Acceso con privilegios `sudo`**
- **Conectividad de red** entre servidor y clientes

---

## ⚙️ Paso 1. Actualizar el sistema

Antes de iniciar, asegúrate de tener tu sistema actualizado:

```bash
sudo apt update && sudo apt upgrade -y
