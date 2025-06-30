# Apuntes: Protocolo SMB, uso de smbclient y Metasploit

## ¿Qué es SMB?
SMB (Server Message Block) es un protocolo de red que permite compartir archivos, impresoras y otros recursos entre máquinas, comúnmente en redes Windows.

### Puerto utilizado
- **TCP 445**

### Usos comunes
- Compartición de archivos e impresoras
- Autenticación de usuarios
- Comunicación entre servicios Windows

---

## Uso de smbclient (Herramienta de línea de comandos)

`smbclient` permite interactuar con recursos SMB desde la terminal, similar a un cliente FTP.

### Conexión a un recurso compartido
```
smbclient //IP/DIRECTORIO -U usuario
```

### Acceso anónimo
```
smbclient -L //IP -N
```

### Acciones comunes
- Ver recursos compartidos:
  ```
  smbclient -L //IP -U usuario
  ```
- Descargar archivos:
  ```
  get nombre_archivo
  ```
- Subir archivos:
  ```
  put nombre_archivo
  ```
- Navegar:
  ```
  ls
  cd nombre_directorio
  ```

---

## Enumeración SMB con Metasploit (msfconsole)

### 1. Buscar módulos SMB
```
search smb
```

### 2. Enumerar recursos compartidos
```
use auxiliary/scanner/smb/smb_enumshares
set RHOSTS <IP>
run
```

### 3. Enumerar usuarios
```
use auxiliary/scanner/smb/smb_enumusers
set RHOSTS <IP>
run
```

### 4. Enumerar sesiones activas
```
use auxiliary/scanner/smb/smb_enumsessions
set RHOSTS <IP>
run
```

### 5. Ver información del sistema
```
use auxiliary/scanner/smb/smb_version
set RHOSTS <IP>
run
```

---

## Fuerza bruta de credenciales SMB con Metasploit

```
use auxiliary/scanner/smb/smb_login
set RHOSTS <IP>
set USER_FILE /ruta/al/diccionario/usuarios.txt
set PASS_FILE /ruta/al/diccionario/contraseñas.txt
set STOP_ON_SUCCESS true
run
```

Parámetros:
- `USER_FILE`: diccionario de usuarios
- `PASS_FILE`: diccionario de contraseñas
- `STOP_ON_SUCCESS`: se detiene al encontrar credenciales válidas

---
