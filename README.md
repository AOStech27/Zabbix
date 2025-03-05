# Instalación de Zabbix 7.2 en Debian 12

Este documento proporciona una guía paso a paso para instalar **Zabbix 7.2** en **Debian 12**, asegurando una configuración óptima y compatible.
# Si necesitas asesoramiento o ayuda para configurar Zabbix en tu empresa, contáctame. Estoy disponible para ofrecer servicios de implementación y optimización de Zabbix. 📩 aostech27@gmail.com

## 📌 Requisitos previos
Antes de comenzar, revisa la documentación oficial de Zabbix sobre los requisitos de instalación:  
🔗 [Documentación Oficial Zabbix 7.2](https://www.zabbix.com/documentation/7.2/es/manual/installation/requirements)

Para este entorno, usaremos:
- **Debian 12**
- **2 GB de RAM**
- **40 GB de almacenamiento**
- **2 núcleos de CPU**
- **MariaDB 11.5, Apache 2.4.x y PHP 8.2.x**

## 🛠 Instalación de MariaDB 11.5
Si tienes una instalación previa de MariaDB, elimínala:

```bash
sudo systemctl stop mariadb
sudo apt remove --purge mariadb-server mariadb-client -y
sudo apt autoremove -y
```

Instalar los paquetes necesarios:

```bash
sudo apt install curl software-properties-common dirmngr -y
```

Configurar el repositorio e instalar MariaDB:

```bash
curl -LsS -O https://downloads.mariadb.com/MariaDB/mariadb_repo_setup
bash mariadb_repo_setup --mariadb-server-version=11.5
sudo apt update
sudo apt install mariadb-server -y
sudo systemctl enable mariadb
mysql_secure_installation
```

## 🌍 Instalación de Apache2 y PHP
Actualizar paquetes:

```bash
sudo apt update && sudo apt upgrade -y
```

Instalar Apache2:

```bash
sudo apt install apache2 -y
sudo apache2 -v
```

Instalar PHP y extensiones necesarias:

```bash
sudo apt install php libapache2-mod-php php-mysql -y
```

## 📥 Instalación del repositorio de Zabbix
Añadir el repositorio oficial de Zabbix 7.2:

```bash
wget https://repo.zabbix.com/zabbix/7.2/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.2+debian12_all.deb
sudo dpkg -i zabbix-release_latest_7.2+debian12_all.deb
sudo apt update
```

Instalar los paquetes de Zabbix:

```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent -y
```

## 🗄 Configuración de la base de datos
Acceder a MariaDB y crear la base de datos:

```bash
mysql -uroot -p
```

Ejecutar los siguientes comandos dentro de MariaDB:

```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER zabbix@localhost IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbix.* TO zabbix@localhost;
SET GLOBAL log_bin_trust_function_creators = 1;
QUIT;
```

Importar el esquema de base de datos de Zabbix (Te pedirá la contraseña del usuario creado en la base de datos de zabbix):

```bash
sudo zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

Deshabilitar `log_bin_trust_function_creators` tras la importación:

```bash
mysql -uroot -p -e "SET GLOBAL log_bin_trust_function_creators = 0;"
```

Configurar Zabbix para usar la base de datos:

```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

Modificar las siguientes líneas:
```
DBName=zabbix
DBUser=zabbix
DBPassword=password
```

## 🚀 Iniciar y habilitar Zabbix
Iniciar y habilitar los servicios:

```bash
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

## 🌐 Configuración en la Interfaz Web
Accede desde un navegador:

```
http://<IP_DEL_SERVIDOR>/zabbix
```

Si hay problemas con el idioma, ejecutar:

```bash
sudo dpkg-reconfigure locales
sudo systemctl restart zabbix-server zabbix-agent apache2
```

### 📝 Acceso inicial:
- **Usuario:** `Admin`
- **Contraseña:** `zabbix`

---
📢 **Si esta guía te ha sido útil, no olvides darle ⭐ en GitHub!**

#Zabbix #Debian #Linux #SysAdmin #Monitoring #OpenSource
