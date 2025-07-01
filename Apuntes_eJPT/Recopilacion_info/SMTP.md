# Enumeración de SMTP con Metasploit (`msfconsole`)

## ¿Qué es SMTP?

SMTP (Simple Mail Transfer Protocol) es un protocolo utilizado para enviar correos electrónicos entre servidores. El servicio suele estar activo en los puertos 25, 465 (SSL) o 587 (TLS).

## ¿Qué es la enumeración en SMTP?

La enumeración SMTP busca descubrir información útil como:
- Nombres de usuarios y buzones existentes
- Versión del servidor SMTP
- Configuraciones de relay abierto
- Servicios o software vulnerable

## ¿Para qué sirve?

- Identificar posibles usuarios válidos en el sistema
- Facilitar ataques de ingeniería social
- Evaluar la seguridad del servidor de correo
- Descubrir versiones vulnerables del software

## Enumeración de SMTP con Metasploit

### 1. Iniciar `msfconsole`

```bash
msfconsole
```

### 2. Buscar módulos SMTP

```bash
search smtp
```

Módulos útiles:
- `auxiliary/scanner/smtp/smtp_version` – Detecta la versión del servicio
- `auxiliary/scanner/smtp/smtp_enum` – Enumera usuarios por comandos SMTP
- `auxiliary/scanner/smtp/smtp_relay` – Verifica si el servidor permite relay no autenticado

### 3. Detectar versión del servidor SMTP

```bash
use auxiliary/scanner/smtp/smtp_version
set RHOSTS <IP objetivo>
run
```

Este módulo intenta conectarse y leer la banner de versión del servicio SMTP.

### 4. Enumerar usuarios válidos

```bash
use auxiliary/scanner/smtp/smtp_enum
set RHOSTS <IP objetivo>
set USER_FILE /ruta/usuarios.txt
set THREADS 10
run
```

Este módulo usa comandos como `VRFY`, `EXPN` o `RCPT TO` para detectar usuarios válidos.

### 5. Verificar si hay relay abierto

```bash
use auxiliary/scanner/smtp/smtp_relay
set RHOSTS <IP objetivo>
run
```

Esto comprueba si el servidor permite enviar correos desde dominios externos sin autenticación, una vulnerabilidad crítica.

## Consejos

- Puedes usar `nmap -p 25 --script smtp* <IP>` para ampliar la enumeración con scripts NSE.
- Algunos servidores modernos bloquean `VRFY`, en ese caso prueba con `RCPT TO`.
- La enumeración SMTP puede ser útil para descubrir usuarios válidos antes de hacer ataques por SSH o RDP.

## Ejemplo de conexión manual

```bash
telnet <IP objetivo> 25
```

```text
HELO example.com
VRFY admin
RCPT TO:<usuario@dominio.com>
```

