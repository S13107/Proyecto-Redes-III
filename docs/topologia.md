# Definición de la Topología - NexusPlay Studios

## 1. Idea General

El proyecto consiste en diseñar una infraestructura de red segura para **NexusPlay Studios**, una empresa de desarrollo de videojuegos y esports con sede central en Barcelona y sucursal en Madrid. El enfoque principal es la segmentación de red y la aplicación de políticas de seguridad perimetral basadas en el modelo de **Defensa en Profundidad**.

La empresa gestiona una tienda online, servidores de juego públicos y organiza torneos presenciales, lo que la convierte en un objetivo habitual de ataques DDoS y requiere una infraestructura robusta y segmentada.

---

## 2. Elementos de la Topología

### Zonas de red

| Zona | Color | Descripción |
|------|-------|-------------|
| Internet/WAN |  Rojo | Cloud + Router-ISP simulando la conexión a internet |
| DMZ |  Naranja | Servidores públicos: Web-Server y Mail-Server |
| Red Interna LAN |  Verde | PCs de empleados segmentados por departamento |

### Dispositivos implementados

| Dispositivo | Tipo | Función |
|-------------|------|---------|
| Router-ISP | Cisco IOSv2 | Simula el router del proveedor de internet |
| MikroTik-FW1 | MikroTik CHR | Firewall perimetral — protege la DMZ de internet |
| MikroTik-FW2 | MikroTik CHR | Firewall interno — protege la LAN de la DMZ |
| SW-DMZ | Ethernet Switch | Switch de la zona desmilitarizada |
| SW-Interno | Ethernet Switch | Switch de la red interna |
| Web-Server | VPCS | Servidor web público (tienda online, web corporativa) |
| Mail-Server | VPCS | Servidor de correo corporativo |
| PC-Desarrollo | VPCS | Equipo del departamento de desarrollo |
| PC-Esports | VPCS | Equipo del equipo profesional de esports |
| PC-Admin | VPCS | Equipo de administración de red |
| PC-RRHH | VPCS | Equipo del departamento de RRHH |
| PC-Direccion | VPCS | Equipo de dirección |

---

## 3. Direccionamiento IP

| Dispositivo | Interfaz | Dirección IP | Red |
|-------------|----------|--------------|-----|
| Router-ISP | g0/0 | 192.168.1.2/24 | WAN |
| Router-ISP | g0/1 | 203.0.113.1/24 | Internet |
| MikroTik-FW1 | ether1 (WAN) | 203.0.113.2/24 | Internet |
| MikroTik-FW1 | ether2 (DMZ) | 10.10.10.1/24 | DMZ |
| MikroTik-FW2 | ether1 (DMZ) | 10.10.10.2/24 | DMZ |
| MikroTik-FW2 | ether2 (LAN) | 192.168.100.1/24 | LAN |
| Web-Server | e0 | 10.10.10.10/24 | DMZ |
| Mail-Server | e0 | 10.10.10.20/24 | DMZ |
| PC-Desarrollo | e0 | 192.168.100.10/24 | LAN |
| PC-Esports | e0 | 192.168.100.20/24 | LAN |
| PC-Admin | e0 | 192.168.100.30/24 | LAN |
| PC-RRHH | e0 | 192.168.100.40/24 | LAN |
| PC-Direccion | e0 | 192.168.100.50/24 | LAN |

---

## 4. Flujo de tráfico

```
Internet → Router-ISP → FW1 → DMZ (Web-Server, Mail-Server)
                                ↓
                               FW2 → LAN (PCs empleados)
```

- El tráfico externo solo puede acceder a la DMZ (HTTP/HTTPS/DNS)
- La LAN puede acceder a la DMZ y a internet
- La DMZ **nunca** puede acceder directamente a la LAN

---

## 5. Matriz de seguridad

### Reglas FW1 (Internet → DMZ)

| Tráfico | Puerto | Acción |
|---------|--------|--------|
| HTTP | 80/TCP |  Permitido |
| HTTPS | 443/TCP |  Permitido |
| DNS | 53/UDP |  Permitido |
| Resto | - |  Bloqueado |

### Reglas FW2 (DMZ ↔ LAN)

| Origen | Destino | Acción |
|--------|---------|--------|
| LAN | DMZ |  Permitido |
| LAN | Internet |  Permitido |
| DMZ | LAN |  Bloqueado |
| Resto | - |  Bloqueado |
