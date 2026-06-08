# Investigación: Seguridad en Redes aplicada a NexusPlay Studios

## 1. Tipos de Firewalls implementados

Para este proyecto hemos analizado y aplicado los siguientes tipos:

### Firewall Stateful (con estado)
Filtra el tráfico basándose en el estado de las conexiones, direcciones IP y puertos. Es el tipo que implementamos con **MikroTik CHR** en ambos firewalls. Mantiene una tabla de conexiones activas y permite únicamente el tráfico que pertenece a una conexión establecida o relacionada.

### Firewall de Aplicación (Proxy)
Realiza una inspección profunda en la capa 7, analizando el contenido del tráfico web. Aunque en este proyecto usamos firewalls stateful, en un entorno real de NexusPlay Studios se recomendaría añadir un WAF (Web Application Firewall) delante del Web-Server para proteger la tienda online de ataques como SQL injection o XSS.

### Diferencia clave
El firewall de red (stateful) se centra en el transporte (capas 3 y 4), mientras que el de aplicación entiende el protocolo y puede bloquear ataques específicos de software. En nuestra arquitectura, FW1 actúa como firewall de red protegiendo la DMZ.

---

## 2. Arquitectura de Defensa en Profundidad

NexusPlay Studios implementa una arquitectura de **Defensa en Profundidad** con múltiples capas de seguridad:

```
Internet → [FW1] → DMZ → [FW2] → Red Interna
```

Cada capa añade una barrera adicional. Si un atacante supera FW1, aún tiene que superar FW2 para llegar a los datos sensibles (código fuente, datos de empleados).

---

## 3. Principios CIA aplicados al proyecto

### Confidencialidad
- Garantizada mediante **VPN L2TP/IPSec** para los trabajadores remotos
- Segmentación de red que impide que los departamentos accedan a información de otros (VLAN por departamento)
- FW2 bloquea cualquier acceso de la DMZ hacia la LAN

### Integridad
- Las conexiones VPN cifran el tráfico de extremo a extremo
- Las reglas de firewall evitan modificaciones no autorizadas al bloquear tráfico no permitido
- IPSec en la VPN garantiza que los datos no sean alterados en tránsito

### Disponibilidad
- SD-WAN para conectividad entre sedes Barcelona ↔ Madrid con redundancia
- Separación de servicios públicos (DMZ) y privados (LAN) evita que un ataque a la DMZ afecte a la producción interna
- NAT en FW1 permite que toda la red interna acceda a internet sin exponer IPs privadas

---

## 4. Amenazas específicas del sector gaming

NexusPlay Studios, como empresa de videojuegos y esports, está expuesta a amenazas específicas:

| Amenaza | Descripción | Mitigación en nuestro proyecto |
|---------|-------------|-------------------------------|
| **DDoS** | Ataques de denegación de servicio contra servidores de juego | DMZ aislada — un ataque al Web-Server no afecta a la LAN |
| **Data breach** | Robo de código fuente o datos de usuarios | FW2 bloquea acceso desde DMZ a LAN donde está el código |
| **Man in the Middle** | Interceptación de comunicaciones de empleados remotos | VPN IPSec cifra todas las comunicaciones remotas |
| **Acceso no autorizado** | Intrusión en la red corporativa | Segmentación por zonas y reglas de denegación por defecto |

---

## 5. VPN L2TP/IPSec

Implementada en **MikroTik-FW1** para los trabajadores remotos (programadores y diseñadores freelance):

- **L2TP**: crea el túnel de comunicación
- **IPSec**: cifra el contenido del túnel
- **Credenciales**: autenticación por usuario y contraseña
- **Caso de uso**: un programador freelance desde casa se conecta a la VPN y accede al repositorio de código como si estuviera en la oficina, con todo el tráfico cifrado

---

## 6. Análisis de tráfico con Wireshark

Se han realizado dos capturas de tráfico para verificar el correcto funcionamiento de las reglas de seguridad:

### Captura 1 — `captura-LAN-DMZ.pcap`
Tráfico ICMP legítimo desde PC-Desarrollo (192.168.100.10) hacia Web-Server (10.10.10.10). Demuestra que la comunicación LAN → DMZ funciona correctamente atravesando FW2.

### Captura 2 — `captura-DMZ-bloqueada.pcap`
Intento de ping desde Web-Server (10.10.10.10) hacia PC-Desarrollo (192.168.100.10). Los paquetes salen pero no reciben respuesta — FW2 bloquea el tráfico DMZ → LAN correctamente.
