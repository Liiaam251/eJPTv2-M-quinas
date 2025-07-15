# Vulnerabilidades Comunes en Sistemas Windows

## 1. Vulnerabilidades de Ejecución Remota de Código (RCE)

- **MS17-010 (EternalBlue)**  
  - Explota vulnerabilidades en SMBv1 para ejecutar código remoto.  
  - Usado en ataques como WannaCry y NotPetya.  
  - Mitigación: Deshabilitar SMBv1 y aplicar parches de seguridad.

- **PrintNightmare (CVE-2021-34527)**  
  - Vulnerabilidad en el servicio de cola de impresión que permite ejecución remota con privilegios SYSTEM.  
  - Mitigación: Actualizar sistemas y aplicar parches.

## 2. Elevación de Privilegios

- **Token Impersonation**  
  - Ataques donde un usuario eleva privilegios mediante tokens de acceso robados o mal configurados.

- **UAC Bypass**  
  - Técnicas para evadir el Control de Cuentas de Usuario y obtener privilegios elevados.

## 3. Vulnerabilidades de Contraseñas

- **Contraseñas débiles o por defecto**  
  - Facilitan ataques de fuerza bruta o acceso no autorizado.

- **Pass-the-Hash (PtH)**  
  - Uso de hashes de contraseñas para autenticarse sin conocer la contraseña en texto claro.

## 4. Servicios Vulnerables

- **SMB (Server Message Block)**  
  - SMBv1 inseguro y con vulnerabilidades conocidas.  
  - SMBv2 y v3 más seguros pero pueden tener configuraciones erróneas.

- **RDP (Remote Desktop Protocol)**  
  - Vulnerable a ataques de fuerza bruta, man-in-the-middle y ejecución remota si no está correctamente configurado.

- **Windows DNS Server**  
  - Vulnerabilidades que pueden permitir ataques DoS o ejecución remota.

## 5. Configuraciones Inseguras

- **Comparticiones de red mal configuradas**  
  - Carpetas compartidas con permisos demasiado permisivos.

- **Servicios innecesarios activos**  
  - Exponen vectores de ataque adicionales.

- **Actualizaciones pendientes**  
  - Sistemas sin parches vulnerables a exploits conocidos.

## 6. Malware y Técnicas de Persistencia

- **Uso de herramientas legítimas (Living off the Land)**  
  - PowerShell, WMI y otras para evadir detección.

- **Rootkits y bootkits**  
  - Persistencia profunda en el sistema.


