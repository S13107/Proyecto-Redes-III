# Proyecto de Redes III - Implementación de Red Segura

Este repositorio contiene la planificación, diseño y documentación técnica para la implementación de una topología de red avanzada y segura.

## 1. Organización y Metodología
Para asegurar el éxito del proyecto, el equipo se ha organizado en las siguientes áreas de trabajo:

* **Diseño de Topología:** Definición de VLANs, segmentación de red y direccionamiento.
* **Seguridad Perimetral:** Configuración de Firewalls (reglas de filtrado para HTTP, HTTPS, SSH) y diseño de la DMZ.
* **Seguridad Inalámbrica:** Implementación de protocolos WPA3 y autenticación robusta mediante 802.1X.
* **Conectividad y Escalabilidad:** Despliegue de soluciones SD-WAN para soporte de sedes y trabajadores remotos.
* **Monitorización y Auditoría:** Análisis de tráfico mediante Wireshark e implementación de sistemas IDS/IPS.

## 2. Propuesta Tecnológica
El proyecto se basa en una arquitectura de "Defensa en Profundidad", integrando:
* **Segmentación estricta:** Aislamiento de tráfico sensible mediante VLANs.
* **Zona Desmilitarizada (DMZ):** Ubicación segura para servicios web, protegida por firewalls de aplicación.
* **SD-WAN:** Optimización del rendimiento y reducción de costes en la conectividad entre sedes.
* **Acceso Remoto Seguro:** Túneles VPN para garantizar la confidencialidad de los trabajadores externos.

## 3. Estructura del Repositorio
* **/docs**: Informe técnico, justificación de decisiones y diagramas.
* **/configs**: Archivos de configuración de los dispositivos de red.
* **/captures**: Capturas y análisis de tráfico (Wireshark).
