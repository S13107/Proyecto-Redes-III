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
* **Segmentación estricta:** Aislamiento de tráfico sensible mediante VLANs. Para una segmentar la red en diferentes subredes, lo que permite aislar el trafico según su funcion  (usuarios, servidores, administración, etc.). Esto mejora la seguridad, ya que limita la propagación de posibles ataques dentro de la red.
* **Zona Desmilitarizada (DMZ):** Ubicación segura para servicios web desde el exterior, protegida por firewalls de aplicación y evitando el acceso directo a la red interna.
* **SD-WAN:** Optimización del rendimiento y reducción de costes en la conectividad entre diferente sedes y usuarios remotosPermite gestionar de forma más eficiente el ancho de banda. Ademas de facilitar la escalabilidad de la red.
* **Acceso Remoto Seguro:** Túneles VPN para garantizar la confidencialidad de la comunicacion de los trabajadores externos.

## 3. Estructura del Repositorio
* **/docs**: Informe técnico, justificación de decisiones y diagramas.
* **/configs**: Archivos de configuración de los dispositivos de red.
* **/captures**: Capturas y análisis de tráfico (Wireshark).

## 4. Organización del Equipo y Herramientas
* **Gestión de Configuración:** Utilizamos la carpeta `/configs` para mantener versiones actualizadas de los archivos de configuración de routers y firewalls.
* **Documentación Técnica:** Centralizada en la carpeta `/docs` mediante archivos Markdown para facilitar la revisión y el trabajo colaborativo.
* **Monitorización y Pruebas:** El análisis de vulnerabilidades y el tráfico de red se registrará en la carpeta `/captures` utilizando Wireshark.
