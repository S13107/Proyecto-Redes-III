# Definicion de la Topologia - NexusPlay Studios

## 1. Idea General

El proyecto consiste en disenar una infraestructura de red segura para **NexusPlay Studios**, una empresa de desarrollo de videojuegos y esports con sede central en Barcelona y sucursal en Madrid. El enfoque principal es la segmentacion de red y la aplicacion de politicas de seguridad perimetral basadas en el modelo de **Defensa en Profundidad**.

La empresa gestiona una tienda online, servidores de juego publicos y organiza torneos presenciales, lo que la convierte en un objetivo habitual de ataques DDoS y requiere una infraestructura robusta y segmentada.

---

## 2. Elementos de la Topologia

### Zonas de red

| Zona | Color en GNS3 | Descripcion |
|------|---------------|-------------|
| Internet/WAN | Rojo | Cloud + Router-ISP simulando la conexion a internet |
| DMZ | Naranja | Servidores publicos: Web-Server y Mail-Server |
| Red Interna LAN | Verde | PCs de empleados segmentados por departamento |
| Sede Madrid | Azul | Sucursal de Madrid conectada mediante enlace punto a punto |

### Dispositivos implementados

| Dispositivo | Tipo | Funcion |
|-------------|------|---------|
| Router-ISP | Cisco IOSv2 | Simula el router del proveedor de internet |
| NX-Edge | MikroTik CHR | Firewall perimetral — protege la DMZ de internet |
| NX-Core | MikroTik CHR | Firewall interno — protege la LAN de la DMZ |
| SW-DMZ | Ethernet Switch | Switch de la zona desmilitarizada |
| SW-Interno | Ethernet Switch | Switch de la red interna de Barcelona |
| Web-Server | VPCS | Servidor web publico (tienda online, web corporativa) |
| Mail-Server | VPCS | Servidor de correo corporativo |
| PC-Desarrollo | VPCS | Equipo del departamento de desarrollo |
| PC-Esports | VPCS | Equipo del equipo profesional de esports |
| PC-Admin | VPCS | Equipo de administracion de red |
| PC-RRHH | VPCS | Equipo del departamento de RRHH |
| PC-Direccion | VPCS | Equipo de direccion |
| Router-Madrid | MikroTik CHR | Router de borde de la sede Madrid |
| SW-Madrid | Ethernet Switch | Switch de la red local de Madrid |
| PC-Madrid1 | VPCS | Equipo empleado sede Madrid |
| PC-Madrid2 | VPCS | Equipo empleado sede Madrid |

---

## 3. Direccionamiento IP

### Sede Barcelona

| Dispositivo | Interfaz | Direccion IP | Red |
|-------------|----------|--------------|-----|
| Router-ISP | g0/0 | 192.168.1.2/24 | WAN |
| Router-ISP | g0/1 | 203.0.113.1/24 | Internet |
| NX-Edge | ether1 (WAN) | 203.0.113.2/24 | Internet |
| NX-Edge | ether2 (DMZ) | 10.10.10.1/24 | DMZ |
| NX-Core | ether1 (DMZ) | 10.10.10.2/24 | DMZ |
| NX-Core | ether2 (LAN) | 192.168.100.1/24 | LAN |
| Web-Server | e0 | 10.10.10.10/24 | DMZ |
| Mail-Server | e0 | 10.10.10.20/24 | DMZ |
| PC-Desarrollo | e0 | 192.168.10.10/24 | VLAN 10 |
| PC-Esports | e0 | 192.168.20.10/24 | VLAN 20 |
| PC-Admin | e0 | 192.168.30.10/24 | VLAN 30 |
| PC-RRHH | e0 | 192.168.40.10/24 | VLAN 40 |
| PC-Direccion | e0 | 192.168.50.10/24 | VLAN 50 |

### Enlace punto a punto Barcelona - Madrid

| Dispositivo | Interfaz | Direccion IP | Red |
|-------------|----------|--------------|-----|
| NX-Edge | ether3 (P2P Madrid) | 10.10.30.1/30 | P2P |
| Router-Madrid | ether10 (P2P) | 10.10.30.2/30 | P2P |

### Sede Madrid

| Dispositivo | Interfaz | Direccion IP | Red |
|-------------|----------|--------------|-----|
| Router-Madrid | ether9 (LAN) | 192.168.200.1/24 | Sede Madrid |
| PC-Madrid1 | e0 | 192.168.200.10/24 | Sede Madrid |
| PC-Madrid2 | e0 | 192.168.200.20/24 | Sede Madrid |

---

## 4. VLANs - Red Interna Barcelona

| VLAN ID | Nombre | Red | Gateway |
|---------|--------|-----|---------|
| 10 | Desarrollo | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Esports | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Admin | 192.168.30.0/24 | 192.168.30.1 |
| 40 | RRHH | 192.168.40.0/24 | 192.168.40.1 |
| 50 | Direccion | 192.168.50.0/24 | 192.168.50.1 |

Configuradas en NX-Core sobre ether2 (interfaz hacia SW-Interno). El SW-Interno tiene los puertos configurados como access por VLAN y el puerto hacia NX-Core como trunk dot1q.

---

## 5. Flujo de trafico

```
Internet --> Router-ISP --> NX-Edge --> DMZ (Web-Server, Mail-Server)
                                    |
                                    +--> NX-Core --> LAN Barcelona (VLANs 10-50)

Sede Madrid --> Router-Madrid --> NX-Edge --> DMZ          [PERMITIDO]
                                          --> Internet      [PERMITIDO]
                                          --> LAN Barcelona [BLOQUEADO]
```

- El trafico externo solo puede acceder a la DMZ mediante HTTP, HTTPS y DNS
- La LAN de Barcelona puede acceder a la DMZ y a internet
- La DMZ nunca puede acceder directamente a la LAN interna
- La Sede Madrid accede a la DMZ e internet pero no a la LAN interna de Barcelona

---

## 6. Matriz de seguridad

### Reglas NX-Edge (Internet hacia DMZ)

| Trafico | Puerto | Accion |
|---------|--------|--------|
| HTTP | 80/TCP | Permitido |
| HTTPS | 443/TCP | Permitido |
| DNS | 53/UDP | Permitido |
| Conexiones establecidas | - | Permitido |
| Resto | - | Bloqueado |

### Reglas NX-Core (DMZ hacia LAN)

| Origen | Destino | Accion |
|--------|---------|--------|
| LAN | DMZ | Permitido |
| LAN | Internet | Permitido |
| DMZ | LAN | Bloqueado |
| Conexiones establecidas | - | Permitido |
| Resto | - | Bloqueado |

### Politica de acceso Sede Madrid

| Origen | Destino | Accion | Motivo |
|--------|---------|--------|--------|
| Sede Madrid | DMZ (Web, Mail) | Permitido | Web corporativa y correo |
| Sede Madrid | Internet | Permitido | Navegacion normal |
| Sede Madrid | LAN Barcelona | Bloqueado | No hay necesidad de acceso directo entre empleados |
| Sede Madrid | Servidores produccion | Bloqueado | Solo gestionables por IT de Barcelona |
