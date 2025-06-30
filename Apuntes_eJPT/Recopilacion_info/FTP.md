# Apuntes: Protocolo FTP y uso de Metasploit (msfconsole)

## ¿Qué es FTP?
FTP (File Transfer Protocol) es un protocolo de red para transferir archivos entre sistemas en una red TCP/IP.

### Puertos usados
- **21**: canal de control (comandos)
- **20**: canal de datos (modo activo)

### Modos de funcionamiento
- **Activo:** el servidor se conecta al cliente para enviar datos.
- **Pasivo:** el cliente abre ambas conexiones, ideal detrás de firewalls.

### Autenticación
- Usuario y contraseña (en texto plano si no hay cifrado)
- Soporte para acceso anónimo (`ftp` como usuario)

### Problemas comunes de seguridad
- Credenciales visibles en texto plano
- Acceso anónimo mal configurado
- Permisos mal establecidos
- Uso de software obsoleto o con vulnerabilidades conocidas

---

## Análisis de FTP con Metasploit Framework (msfconsole)

### 1. Iniciar Metasploit
```
msfconsole
```

### 2. Buscar módulos relacionados con FTP
```
search type:auxiliary ftp
```

### 3. Identificar la versión del servidor FTP
```
use auxiliary/scanner/ftp/ftp_version
set RHOSTS <IP_del_objetivo>
run
```

### 4. Comprobar acceso anónimo
```
use auxiliary/scanner/ftp/anonymous
set RHOSTS <IP_del_objetivo>
run
```

### 5. Probar acceso con credenciales (por ejemplo: usuario ftp)
```
use auxiliary/scanner/ftp/ftp_login
set RHOSTS <IP>
set USERNAME ftp
set PASSWORD ftp
run
```

### 6. Buscar exploits específicos por nombre o versión
```
search ftp <nombre_o_version_del_software>
```

### 7. Usar exploit si se encuentra alguno
```
use exploit/unix/ftp/<nombre_exploit>
set RHOST <IP>
set RPORT 21
run
```
### 8. Fuerza bruta de credenciales con Metasploit
Puedes realizar un ataque de fuerza bruta usando un diccionario de usuarios y contraseñas.

```
use auxiliary/scanner/ftp/ftp_login
set RHOSTS <IP>
set USER_FILE /ruta/al/diccionario/usuarios.txt
set PASS_FILE /ruta/al/diccionario/contraseñas.txt
set STOP_ON_SUCCESS true
run
```

Parámetros útiles:
- `USER_FILE`: lista de posibles usuarios
- `PASS_FILE`: lista de posibles contraseñas
- `STOP_ON_SUCCESS`: detiene el ataque si se encuentra una combinación válida

También puedes probar con combinaciones conocidas directamente:
```
set USERNAME admin
set PASSWORD admin123
run
```
---

