# NMAP

## Usos
# 1. Ver qué hosts están activos
nmap -sn 192.168.1.0/24

# 2. Escaneo rápido TCP
nmap -sS IP

# 3. Escaneo completo de puertos
nmap -sS -p- -T4 IP

# 4. Detección de versiones
nmap -sV -p PUERTOS IP

# 5. Detección de sistema operativo
nmap -O IP

# 6. Escaneo agresivo
nmap -A -T4 IP

# 7. Escaneo UDP
nmap -sU -p 53,161,123 IP

# 8. Buscar vulnerabilidades
nmap --script vuln IP

### Nmap completo

````
nmap -sS -sV -sC -O -p- -T4 --script vuln --reason --open -oN escaneo_completo.txt IP
````
