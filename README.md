# Proyecto de Redes III - NexusPlay Studios

> Diseño e implementación de una infraestructura de red segura para una empresa de desarrollo de videojuegos y esports con dos sedes y trabajadores remotos.

##  Contexto de la Empresa

**NexusPlay Studios** es una empresa de desarrollo de videojuegos y competición esports con presencia en dos ciudades:

- **Sede Central — Barcelona:** 40 empleados. Desarrollo de videojuegos, servidores de producción, administración y finanzas.
- **Sucursal — Madrid:** 15 empleados. Equipo de esports profesional y departamento de marketing.
- **Trabajadores remotos:** Programadores y diseñadores freelance con acceso VPN al código fuente y recursos internos.

La empresa gestiona una **tienda online**, **servidores de juego públicos** y organiza **torneos presenciales**, lo que la convierte en un objetivo habitual de ataques DDoS y requiere una infraestructura de red robusta y segmentada.

---

## 1. Organización y Metodología

El equipo se ha organizado en las siguientes áreas de trabajo:

- **Diseño de Topología:** Definición de VLANs por departamento, segmentación de red y esquema de direccionamiento IP para ambas sedes.
- **Seguridad Perimetral:** Configuración de dos Firewalls con reglas de filtrado (HTTP, HTTPS, SSH) y diseño de la DMZ que aloja la tienda online y los servidores de juego públicos.
- **Seguridad Inalámbrica:** Implementación de WPA3 y autenticación 802.1X para empleados, con red separada para asistentes a torneos.
- **Conectividad entre Sedes:** Despliegue de SD-WAN para la conexión Barcelona ↔ Madrid con baja latencia y gestión centralizada.
- **Monitorización y Auditoría:** Detección de intrusiones con IDS/IPS y análisis de tráfico con Wireshark, especialmente orientado a mitigar ataques DDoS.

---

## 2. Segmentación de Red — VLANs

| VLAN | Nombre | Descripción |
|------|--------|-------------|
| 10 | Desarrolladores | Acceso al código fuente, builds y repositorios internos |
| 20 | Esports | PCs gaming del equipo profesional, alta prioridad de red |
| 30 | Administración | RRHH, finanzas y dirección — tráfico restringido |
| 40 | Servidores Internos | Repositorios, backups, servidores de builds |
| 50 | DMZ | Tienda online, web corporativa y servidores de juego públicos |
| 60 | WiFi Invitados | Red aislada para torneos presenciales y visitas |

---

## 3. Propuesta Tecnológica

El proyecto se basa en una arquitectura de **Defensa en Profundidad**, especialmente crítica dado el perfil de NexusPlay Studios como objetivo frecuente de ataques DDoS:

- **Doble Firewall + DMZ:** El Firewall 1 filtra el tráfico entre Internet y la DMZ (tienda online, servidores de juego). El Firewall 2 protege la red interna de cualquier amenaza que pueda penetrar la DMZ, impidiendo el acceso directo a código fuente y datos sensibles.
- **Segmentación estricta por VLANs:** Aísla el tráfico por función — los PCs del equipo esports no tienen acceso a los repositorios de desarrollo, y la red de invitados de torneos está completamente separada de la red corporativa.
- **SD-WAN Barcelona ↔ Madrid:** Optimiza el rendimiento y reduce costes en la conectividad entre sedes. Permite gestionar el ancho de banda de forma eficiente y garantiza baja latencia para la sincronización de assets y comunicaciones del equipo.
- **VPN para acceso remoto:** Túneles cifrados para los programadores y diseñadores freelance, garantizando acceso seguro al código fuente desde cualquier ubicación.
- **IDS/IPS:** Detección y prevención de intrusiones orientada a los ataques DDoS habituales en infraestructuras de gaming, así como intentos de acceso no autorizado a los servidores de producción.

---

## 4. Estructura del Repositorio

```
/
├── docs/           # Informe técnico, justificación de decisiones y diagramas de red
├── configs/        # Archivos de configuración de firewalls, switches y routers
└── captures/       # Capturas de tráfico Wireshark y análisis de incidencias
```

- **/docs:** Informe técnico completo con justificación de cada decisión de diseño, diagramas de topología y análisis de seguridad aplicado al contexto de NexusPlay Studios.
- **/configs:** Versiones actualizadas de las configuraciones de todos los dispositivos: firewalls (reglas de filtrado), switches (configuración de VLANs) y routers (SD-WAN, VPN).
- **/captures:** Capturas de tráfico con Wireshark que demuestran el correcto funcionamiento de las reglas de seguridad y el análisis de posibles ataques DDoS al servidor de juego.
