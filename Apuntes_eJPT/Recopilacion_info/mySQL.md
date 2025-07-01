# Enumeración de MySQL con Metasploit (`msfconsole`)

## ¿Qué es MySQL?

MySQL es un sistema de gestión de bases de datos relacional (RDBMS) de código abierto. Utiliza el lenguaje SQL (Structured Query Language) para acceder, gestionar y manipular datos. Es ampliamente utilizado en aplicaciones web y sistemas empresariales.

## ¿Qué es la enumeración?

La enumeración es el proceso de recopilar información detallada sobre un sistema, servicio o red. En el contexto de MySQL, implica descubrir versiones, usuarios, contraseñas, bases de datos existentes y configuraciones relevantes del servicio.

## ¿Para qué sirve la enumeración de MySQL?

Enumerar MySQL puede ayudar a:

- Identificar credenciales débiles o predeterminadas
- Descubrir bases de datos sensibles
- Evaluar posibles vectores de ataque
- Verificar configuraciones inseguras

## Enumeración de MySQL con Metasploit

Metasploit Framework ofrece módulos para interactuar y enumerar MySQL. Aquí se muestra cómo hacerlo:

### 1. Iniciar `msfconsole`

```bash
msfconsole
```

### 2. Buscar módulos relacionados con MySQL

```bash
search mysql
```

Algunos módulos útiles:
- `auxiliary/scanner/mysql/mysql_version` – Detecta la versión de MySQL
- `auxiliary/scanner/mysql/mysql_login` – Fuerza bruta de credenciales
- `auxiliary/admin/mysql/mysql_enum` – Enumeración de información del sistema y bases de datos

### 3. Usar el módulo de enumeración

Ejemplo usando el módulo `mysql_enum`:

```bash
use auxiliary/admin/mysql/mysql_enum
set RHOSTS <IP del objetivo>
set USERNAME <usuario>
set PASSWORD <contraseña>
run
```

Este módulo intentará conectarse al servidor MySQL con las credenciales dadas y, si tiene éxito, mostrará información como:
- Versiones
- Bases de datos disponibles
- Variables del sistema
- Usuarios

### 4. Consejos adicionales

- Verifica que el puerto 3306 esté abierto con `nmap` antes de lanzar ataques.
- Puedes usar `mysql_login` para realizar fuerza bruta antes de enumerar.
- Si obtienes acceso, puedes usar `metasploit` para cargar payloads si el servicio lo permite.

## Recomendaciones

- Nunca realices pruebas en sistemas sin autorización.
- Usa laboratorios controlados como Metasploitable o DVWA para practicar.

