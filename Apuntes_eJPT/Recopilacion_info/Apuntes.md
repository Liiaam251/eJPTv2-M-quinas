## Apuntes sobre Recopilación de Información

### Qué es la recopilación de información:

Primera fase del reconocimineto, se trata de buscar información activa y pasivamente sobre persona, web, empresa...
Con esto conseguimos una mejor comprensión de lo que estamos intentando atacar.

Con esta recopilación por ejemplo en empresas podemos conseguir correos electronicos de trabajadores para así poder mandar un phising y conseguir inicial acces, con esto podremos hacer pivoting hasta los servidores.

Hay dos grandes tipos de recopilación de información:

Passive: Se trata de conseguir información sin comprometer direcectamente los equipos, por ejemplo con google dorcks o similar
Active: Usando herramientas para enumeración

La huella: Información más particular 

Cuando tenemos un dominio podemos usar la herramienta:
````
host dominioejemplo.com
````
Para así hacer una consulta DNS y conseguir infomación 

La dirección IP (A o AAAA records)

Servidores de correo (MX records)

Servidores de nombres (NS records)

Información general de resolución DNS

### Herramientas identificar tecnologias:

HERRAMIENTA BUILTWITH:
És muy útil, se puede usar como busqueda de información passiva para identificar información web, nos da mucha info como pluggins usados, si es wordpress....
HERRAMIENTA WAPPALYZER:
Otra herramienta para identificar inftomación web
HERRAMIENTA WHATWEB:
````
whatweb <url>
````
Este se hace a través de consola e identifica tecnologias 

### Enumeración whois
El protocolo WHOIS permite consultar información de registro de dominios, como el propietario, fechas de creación/vencimiento y servidores asociados.
````
whois <url>
````
Nombre del registrante (persona o empresa dueña del dominio)

Correo electrónico y contacto técnico/administrativo

Fecha de creación, actualización y vencimiento del dominio

Entidad registradora (como GoDaddy, Namecheap, etc.)

Servidores de nombres (NS) asociados

Estado del dominio (activo, en espera, etc.)

País o región del registrante

ID del registro (en algunos casos)

### Herramietnat sublister 
Esta herramienta no está considerada como enumeración activa ya que no usa diccionarios ni ataques de fuerza bruta, usa motores de busqueda para encontrar estos subdominios

````
sublist3r -d dominio.com -e google,brave,yahoo
````
## Recopilación Activa

### DNS

DNS es el sistema que traduce nombres de dominio legibles (como google.com) en direcciones IP
### HERRAMIENTAS
DNS Zone o herramientas como DumpDNS.com sirven para obtener información detallada sobre los registros DNS de un dominio, como subdominios, registros A, MX, TXT, y más. Esto es útil en pentesting para descubrir infraestructura oculta, identificar vectores de ataque y mapear la red objetivo

dnsrecon es una herramienta de enumeración DNS incluida en Kali Linux que se utiliza para:

Enumerar registros DNS (A, NS, MX, TXT, etc.)

Hacer brute force de subdominios

Detectar transferencias de zona (zone transfer)

Consultar servidores DNS específicos

Realizar búsquedas inversas (PTR)

### Tipos comunes de registros DNS:

| Tipo      | Significado        | ¿Para qué sirve?                                                                        | Ejemplo                                               |
| --------- | ------------------ | --------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| **A**     | Address            | Asocia un dominio con una IP **IPv4**.                                                  | `ejemplo.com → 192.168.1.100`                         |
| **AAAA**  | IPv6 Address       | Asocia un dominio con una IP **IPv6**.                                                  | `ejemplo.com → 2001:db8::1`                           |
| **MX**    | Mail Exchange      | Define los servidores que manejan el **correo** del dominio.                            | `ejemplo.com → mail.google.com (prioridad 10)`        |
| **NS**    | Name Server        | Indica qué servidores DNS tienen autoridad sobre el dominio.                            | `ejemplo.com → ns1.dnsprovider.com`                   |
| **TXT**   | Texto              | Guarda texto libre. Usado para **verificación** y seguridad de email (SPF, DKIM, etc.). | `ejemplo.com → "v=spf1 include:_spf.google.com ~all"` |
| **CNAME** | Canonical Name     | Hace que un subdominio sea un **alias de otro dominio**.                                | `blog.ejemplo.com → midominio.wordpress.com`          |
| **PTR**   | Pointer            | Traduce una IP en su **nombre de dominio** (DNS inverso).                               | `192.0.2.1 → ejemplo.com`                             |
| **SOA**   | Start of Authority | Contiene info técnica de la zona DNS (admin, tiempo de refresco, etc.).                 | `ejemplo.com → SOA → ns1.dnsprovider.com`             |


