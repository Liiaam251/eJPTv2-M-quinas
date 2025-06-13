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













