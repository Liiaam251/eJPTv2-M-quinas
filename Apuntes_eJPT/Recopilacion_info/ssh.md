# Enumeración de SSH con Metasploit (`msfconsole`)

## ¿Qué es SSH?

SSH (Secure Shell) es un protocolo de red que permite la administración remota de sistemas de forma segura. Se utiliza comúnmente para conectarse a servidores Linux/UNIX mediante una terminal cifrada.

## ¿Qué es la enumeración en SSH?

Enumerar SSH implica obtener información útil del servicio, como:
- Versión del servidor SSH
- Fuerza bruta de credenciales
- Detección de configuraciones inseguras

## ¿Para qué sirve?

- Identificar versiones vulnerables de SSH
- Acceder mediante credenciales válidas
- Establecer sesiones de control remoto
- Realizar pivoteo o movimiento lateral en una red

## Enumeración de SSH con Metasploit

Metasploit incluye varios módulos auxiliares relacionados con SSH. Aquí te explico cómo usarlos.

### 1. Iniciar `msfconsole`

```bash
msfconsole
```

### 2. Buscar módulos de SSH

```bash
search ssh
```

Módulos clave:
- `auxiliary/scanner/ssh/ssh_version` – Detecta la versión del servicio SSH
- `auxiliary/scanner/ssh/ssh_login` – Realiza fuerza bruta de credenciales
- `auxiliary/scanner/ssh/ssh_enumusers` – Enumera posibles nombres de usuarios (si el servidor revela errores distintos)

### 3. Enumerar la versión de SSH

```bash
use auxiliary/scanner/ssh/ssh_version
set RHOSTS <IP objetivo>
run
```

Esto muestra la versión del daemon SSH (por ejemplo, OpenSSH 7.4p1).

### 4. Fuerza bruta de usuarios y contraseñas

```bash
use auxiliary/scanner/ssh/ssh_login
set RHOSTS <IP objetivo>
set USER_FILE /ruta/usuarios.txt
set PASS_FILE /ruta/contraseñas.txt
set THREADS 10
run
```

Puedes usar listas de credenciales comunes como las de `rockyou.txt`.

### 5. Enumerar usuarios (si el servidor lo permite)

```bash
use auxiliary/scanner/ssh/ssh_enumusers
set RHOSTS <IP objetivo>
set USER_FILE /ruta/usuarios.txt
run
```

Este módulo intenta detectar si un nombre de usuario existe, basándose en respuestas distintas del servidor SSH.

## Recomendaciones

- Asegúrate de que el puerto 22 esté abierto usando `nmap`.
- Prueba primero con usuarios comunes como `root`, `admin`, `user`.
- Si consigues acceso, puedes usar `ssh` manualmente o importar la sesión en Metasploit para automatización.

## Ejemplo de conexión manual

```bash
ssh usuario@<IP objetivo>
```

## Seguridad y ética

- Solo realiza pruebas en entornos controlados o con permiso explícito.
- Practica con laboratorios como Metasploitable o Hack The Box.

