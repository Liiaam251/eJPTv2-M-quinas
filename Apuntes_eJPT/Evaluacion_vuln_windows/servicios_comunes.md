# Servicios Comunes que se Suelen Explotar en Windows

## 1. SMB (Server Message Block)

- Protocolo para compartir archivos, impresoras y comunicación entre nodos.
- Vulnerabilidades famosas:  
  - **EternalBlue (MS17-010)**: permite ejecución remota de código explotando SMBv1.  
  - Configuraciones incorrectas pueden permitir relay attacks o acceso anónimo.
- Recomendaciones: Deshabilitar SMBv1 y mantener actualizado.

## 2. RDP (Remote Desktop Protocol)

- Permite acceso remoto a escritorios Windows.
- Vulnerabilidades frecuentes:  
  - Ataques de fuerza bruta.  
  - Vulnerabilidades como **BlueKeep** permiten ejecución remota.  
  - Configuraciones débiles o sin autenticación de red (NLA).
- Recomendaciones: Usar VPN, NLA habilitado y contraseñas fuertes.

## 3. RPC (Remote Procedure Call)

- Facilita comunicación entre procesos en red.
- Vulnerabilidades:  
  - Explotación para ejecución remota o escalada de privilegios.  
  - Ataques como **MS03-026**.
- Recomendaciones: Aplicar parches y limitar acceso.

## 4. WMI (Windows Management Instrumentation)

- Servicio para administración remota y automatización.
- Usado por atacantes para movimiento lateral y ejecución de comandos sin ser detectados.
- Recomendaciones: Monitorizar su uso y restringir acceso.

## 5. IIS (Internet Information Services)

- Servidor web de Windows.
- Vulnerabilidades comunes:  
  - Fallos en configuraciones predeterminadas.  
  - Inyecciones, vulnerabilidades en aplicaciones web alojadas.
- Recomendaciones: Actualizar, endurecer configuración y monitorear logs.

## 6. NetBIOS

- Servicios de red para compartir recursos.
- Vulnerabilidades:  
  - Enumeración de usuarios y recursos.  
  - Ataques de spoofing y sniffing.
- Recomendaciones: Deshabilitar si no es necesario y filtrar en firewall.

## 7. SQL Server

- Base de datos Microsoft SQL.
- Vulnerabilidades:  
  - Contraseñas débiles.  
  - Inyección SQL en aplicaciones.
- Recomendaciones: Actualizar, usar contraseñas fuertes y validar entradas.

## Referencias

- [Microsoft Security Updates](https://msrc.microsoft.com/)
- [OWASP Windows Security](https://owasp.org/www-project-windows-security/)
