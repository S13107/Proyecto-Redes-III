# Definición de la Topología e Idea del Proyecto

## 1. Idea General
El proyecto consiste en diseñar una infraestructura de red segura para una organización con presencia física y soporte para teletrabajo. El enfoque principal es la segmentación de red y la aplicación de políticas de seguridad perimetral basadas en el modelo de confianza cero (Zero Trust).

## 2. Elementos de la Topología
Nuestra red implementará los siguientes niveles:
* **Segmentación por VLAN:** Separación del tráfico de administración, usuarios internos y servicios públicos.
* **Zona Desmilitarizada (DMZ):** Un área aislada para alojar aplicaciones web, evitando que el tráfico externo llegue directamente a la red local.
* **Infraestructura SD-WAN:** Implementación de conectividad entre sedes para mejorar la escalabilidad y reducir costes operativos.
* **Seguridad Wireless:** Configuración de redes inalámbricas con protocolos WPA3 y autenticación mediante 802.1X.

## 3. Matriz de Seguridad Inicial
Se han definido reglas de filtrado para permitir únicamente el tráfico estrictamente necesario:
* **Protocolos permitidos:** HTTP (80), HTTPS (443) y SSH (22) para gestión.
* **Protección:** Implementación de reglas de denegación por defecto (Implicit Deny) para todo el tráfico no identificado.
