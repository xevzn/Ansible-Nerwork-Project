# Ansible-Nerwork-Project
---

# Proyecto Ansible – Inventario de Red Automatizado

## 1. Descripción General

Este proyecto tiene como objetivo **automatizar la recolección de información de red** mediante **Ansible**, facilitando la obtención y documentación del inventario de dispositivos Cisco.
El sistema recopila datos de **interfaces físicas** y **SVI (VLANs)**, almacenándolos en archivos CSV estructurados que permiten su posterior análisis o importación a herramientas de gestión.

**Tecnologías utilizadas:**

* **Ansible 2.16+**
* **Python 3.10+**
* **Colección `cisco.ios`** (para conexión y comandos a dispositivos Cisco)
* **Ansible Vault** (para gestión segura de credenciales)
* **SSH / Network CLI** (método de conexión)

**Autor:** Hugo Israel Solís García
**Fecha:** Noviembre de 2025

---

## 2. Estructura del Proyecto

```
ansible_network_project/
│
├── host_vars/
│   ├── SW1.yml
│   ├── SW2.yml
│   ├── SW_CORE.yml
│   └── SWBR.yml
│   # Archivos encriptados con credenciales de conexión de cada dispositivo.
│   # Contraseña de cifrado: 0507
│
├── output/
│   ├── inventario_red.csv      # Interfaces físicas
│   └── inventario_svi.csv      # Interfaces VLAN (SVI)
│   # Carpeta donde se generan los reportes de inventario.
│
├── ansible.cfg                 # Configuración principal de Ansible
├── hosts.txt                   # Lista de hosts (direcciones IP o nombres)
├── inventory.yml               # Inventario que vincula los hosts con host_vars
└── network_info.yml            # Playbook principal con las tareas de recolección
```

---

## 3. Descripción de Archivos Principales

### **`network_info.yml`**

Playbook principal que ejecuta los siguientes comandos en cada dispositivo:

* `show version`
* `show interfaces status`
* `show ip interface brief`

Genera los archivos CSV con la información de interfaces físicas y VLAN.

---

### **`host_vars/`**

Carpeta con los archivos YAML de credenciales, uno por dispositivo (encriptados con **Ansible Vault**).
Contraseña para desencriptar: **0507**

---

### **`output/`**

Carpeta donde se guardan los archivos CSV generados:

* `inventario_red.csv` → Interfaces físicas
* `inventario_svi.csv` → Interfaces VLAN (SVI)

---

### **Otros archivos**

| Archivo           | Descripción                                                                    |
| ----------------- | ------------------------------------------------------------------------------ |
| **ansible.cfg**   | Configuración principal de Ansible.                                            |
| **hosts.txt**     | Lista de direcciones IP o nombres de los dispositivos.                         |
| **inventory.yml** | Define grupos de hosts (por ejemplo, `switches`) y sus credenciales asociadas. |

---

## 4. Ejecución del Playbook

Antes de ejecutar, asegúrate de estar dentro del directorio raíz del proyecto:

```bash
cd ansible_network_project
```

### Ejecutar el playbook para todos los switches

```bash
ansible-playbook -i hosts network_info.yml --limit switches --ask-vault-pass
```

### Ejecutar el playbook para un solo dispositivo (por ejemplo, SWBR)

```bash
ansible-playbook -i hosts network_info.yml --limit SWBR --ask-vault-pass
```

Cuando se solicite, introduce la contraseña de Vault:
**0507**

---

## 5. Gestión de Credenciales con Ansible Vault

### Editar credenciales de un dispositivo

```bash
ansible-vault edit host_vars/SW1.yml
```

### Ver credenciales sin desencriptar el archivo

```bash
ansible-vault view host_vars/SW1.yml
```

### Encriptar nuevamente un archivo (si se editó sin cifrar)

```bash
ansible-vault encrypt host_vars/SW1.yml
```

---

## 6. Archivos de Salida

Una vez ejecutado el playbook, se generan dos reportes CSV dentro de la carpeta `output/`:

| Archivo                | Descripción                                                |
| ---------------------- | ---------------------------------------------------------- |
| **inventario_red.csv** | Detalle de interfaces físicas (estado, VLAN, descripción). |
| **inventario_svi.csv** | Información de interfaces VLAN (SVI) con IP y estado.      |

### Ejemplo – `inventario_red.csv`

```
Dispositivo,Serie,Modelo,Versión IOS,Interfaz,Estado,VLAN,Descripción
SW1,FDO1234ABC,C9200L,17.9.4,Gig1/0/1,connected,10,Uplink
SW1,FDO1234ABC,C9200L,17.9.4,Gig1/0/2,notconnect,1,-
```

### Ejemplo – `inventario_svi.csv`

```
Dispositivo,Interfaz,IP,Estado_L1,Estado_L2
SW1,Vlan1,192.168.1.2,up,up
SW1,Vlan10,10.0.10.1,up,up
```

---

## 7. Notas Adicionales

* Los CSV se actualizan automáticamente en cada ejecución del playbook.
* Se pueden agregar más dispositivos simplemente añadiéndolos a:

  * `inventory.yml`
  * `hosts.txt`
  * `host_vars/NUEVO_DISPOSITIVO.yml` (encriptado con Vault)
* Compatible con equipos **Cisco IOS** utilizando conexión SSH o Network CLI.
* El proyecto es escalable y puede ampliarse para incluir routers u otros tipos de dispositivos.

---

