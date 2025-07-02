# Servicios Comunes que se Suelen Explotar en Windows

## 1. SMB (Server Message Block)

- Protocolo para compartir archivos, impresoras y comunicación entre nodos.
- Usa principalmente **puerto TCP 445**.
- Vulnerabilidades famosas:  
  - **EternalBlue (MS17-010)**: Explota SMBv1 para ejecución remota de código.  
  - Configuraciones erróneas permiten relay attacks o acceso anónimo.
- Deshabilitar SMBv1 es clave para la seguridad.
- Herramientas como `crackmapexec` o `enum4linux` permiten enumerar usuarios y recursos SMB.
  
## 2. RDP (Remote Desktop Protocol)

- Permite acceso remoto con interfaz gráfica (GUI) a sistemas Windows.
- Usa por defecto **puerto TCP 3389**.
- Está **habilitado por defecto en ediciones Server**, pero **deshabilitado en clientes Windows** (10, 11) y debe activarse manualmente.
- Vulnerabilidades conocidas incluyen **BlueKeep**, ataques de fuerza bruta y falta de NLA (autenticación a nivel de red).
- Se recomienda habilitar NLA, usar VPN y contraseñas fuertes.

## 3. IIS (Internet Information Services)

- Servidor web nativo de Windows.
- Escucha en **puerto TCP 80** (HTTP) y **443** (HTTPS si está configurado SSL).
- Soporta extensiones como **WebDAV**, que permite edición colaborativa de archivos a través de HTTP(S).
- Mal configurado puede ser vector para inyección, ejecución remota y filtración de información.

## 4. WinRM (Windows Remote Management)

- Servicio para administración remota basado en WS-Management.
- Escucha en **puerto 5985 (HTTP)** y **5986 (HTTPS/SSL)**.
- Usado para ejecución remota de comandos y gestión automatizada.
- Puede ser explotado para movimiento lateral si está mal configurado o con credenciales débiles.

## 5. RPC (Remote Procedure Call)

- Facilita la comunicación entre procesos y servicios remotos.
- Usado internamente por varios servicios de Windows.
- Históricamente explotado para ejecución remota (p.ej., MS03-026).
- Normalmente opera en **puertos dinámicos** asignados por el sistema, pero también el **135 TCP** para el servicio principal.

## 6. NetBIOS

- Protocolo de soporte para SMB en versiones antiguas.
- Usa puertos **137-139 TCP/UDP**.
- Puede permitir enumeración de usuarios y recursos si está expuesto.

---

# Protocolos y Puertos Clave en Windows

| Servicio/Protocolo | Puerto(s)       | Función                              |
|--------------------|-----------------|------------------------------------|
| SMB                | 445 TCP         | Compartición archivos e impresión  |
| RDP                | 3389 TCP        | Acceso remoto con GUI               |
| IIS (HTTP/HTTPS)   | 80 / 443 TCP     | Servidor web y WebDAV               |
| WinRM              | 5985/5986 TCP   | Administración remota               |
| RPC                | 135 TCP + dinámicos | Comunicación entre procesos       |
| NetBIOS            | 137-139 TCP/UDP | Soporte antiguo para SMB            |

---

# Conceptos Clave Relacionados

- **SMBv1**: Vulnerable y deshabilitado por defecto en sistemas modernos para evitar ataques como EternalBlue.
- **RDP**: Para acceso gráfico remoto; requiere configuración en clientes Windows para activarse.
- **WebDAV**: Extensión de IIS para edición remota colaborativa, utiliza HTTP/HTTPS.
- **WinRM**: Facilita administración remota segura (cuando usa SSL).
- **Protección en RDP y SMB**: Requiere contraseñas fuertes, autenticación a nivel de red (NLA para RDP), y actualizaciones frecuentes para mitigar vulnerabilidades.
