# Incidencias Tecnicas y Soluciones - NexusPlay Studios

Registro de problemas encontrados durante la implementacion de la topologia en GNS3 y las soluciones aplicadas.

---

## Incidencia 1 - GNS3 VM no arrancaba (virtualizacion anidada)

**Sintoma**
Al iniciar la GNS3 VM, error "Virtualized Intel VT-x/EPT is not supported" y "Module 'HV' power on failed".

**Causa**
El host no concedia VT-x anidado a la VM, requisito de la GNS3 VM para correr KVM dentro.

**Solucion**
En las propiedades de la GNS3 VM, desmarcar la opcion "Virtualize Intel VT-x/EPT". La VM arranca sin aceleracion anidada (KVM no disponible dentro).

---

## Incidencia 2 - Nodos QEMU no arrancaban (KVM no disponible)

**Sintoma**
Error "KVM acceleration cannot be used (/dev/kvm doesn't exist)" al iniciar los nodos MikroTik y el Router-ISP.

**Causa**
Consecuencia de desactivar VT-x. Sin /dev/kvm, QEMU no puede usar aceleracion por hardware.

**Solucion**
En la GNS3 VM, editar /etc/gns3/gns3_server.conf y anadir en la seccion [Qemu] la linea:

    enable_kvm = false

Reiniciar la VM. Los nodos QEMU arrancan en modo emulacion (TCG), mas lento pero funcional. Adicionalmente, en las opciones avanzadas de cada nodo QEMU se anadio:

    -nographic -no-kvm

---

## Incidencia 3 - OPNsense fallaba durante la instalacion por falta de RAM

**Sintoma**
El instalador de OPNsense fallaba al 0% con errores de copia de archivos.

**Causa**
La GNS3 VM tenia asignados solo 2048 MB de RAM. OPNsense requiere al menos 3000 MB para la instalacion desde imagen live.

**Solucion**
Se descarto OPNsense como solucion de firewall y se opto por MikroTik CHR, que consume unicamente 128 MB de RAM por instancia y ofrece las mismas funcionalidades necesarias para el proyecto. La GNS3 VM se amplio a 4096 MB de RAM en VirtualBox.

---

## Incidencia 4 - Pantalla negra en consola de MikroTik

**Sintoma**
Al abrir la consola de un nodo MikroTik recien arrancado, la pantalla aparecia completamente negra sin responder.

**Causa**
Dos factores combinados: el tipo de consola estaba configurado como VNC en lugar de Telnet, y MikroTik tarda varios minutos en el primer arranque mientras instala el sistema internamente.

**Solucion**
Cambiar el tipo de consola a Telnet en la configuracion del template. Esperar entre 3 y 5 minutos tras el primer arranque antes de abrir la consola.

---

## Incidencia 5 - Bucle de arranque en MikroTik (iPXE en bucle)

**Sintoma**
La consola mostraba repetidamente la secuencia iPXE seguida de "Booting from Hard Disk... Load system" sin llegar a arrancar el sistema.

**Causa**
QEMU intentaba usar aceleracion KVM que no estaba disponible en el entorno.

**Solucion**
Anadir en el campo Options de la pestana Advanced del nodo:

    -nographic -no-kvm

Esto fuerza a QEMU a usar emulacion software (TCG) en lugar de aceleracion hardware.

---

## Incidencia 6 - El enlace hacia Madrid no levantaba

**Sintoma**
Ping al NX-Edge desde Router-Madrid fallaba con 100% de perdida. El ARP de 10.10.30.1 aparecia como "failed".

**Causa**
Al clonar el Router-Madrid a partir de NX-Edge, las interfaces internas del nuevo nodo eran ether9 a ether16 en lugar de ether1 a ether8. La IP del enlace estaba asignada al puerto equivocado: el cable hacia NX-Edge salia por ether9 pero la IP estaba en ether10.

**Solucion**
Intercambiar las IPs entre ether9 (LAN Madrid) y ether10 (enlace P2P hacia NX-Edge) y limpiar la entrada ARP fallida. Verificar siempre el cableado en el canvas antes de asignar IPs en equipos clonados.

---

## Incidencia 7 - NX-Edge sin IP en el extremo del enlace P2P

**Sintoma**
Aun con los puertos correctos en Router-Madrid, no habia respuesta en 10.10.30.1.

**Causa**
El NX-Edge no tenia ninguna IP asignada en ether3 (puerto que conecta con Router-Madrid). El puerto estaba cableado pero sin direccion IP.

**Solucion**
Anadir la IP del enlace en NX-Edge y la ruta de retorno hacia la red de Madrid:

    /ip address add address=10.10.30.1/30 interface=ether3
    /ip route add dst-address=192.168.200.0/24 gateway=10.10.30.2

Sin la ruta de retorno, el trafico llegaba a destino pero las respuestas no encontraban el camino de vuelta.

---

## Incidencia 8 - Sin salida al exterior desde la Sede Madrid

**Sintoma**
Madrid llegaba a la WAN del NX-Edge (203.0.113.2) pero no al Router-ISP (203.0.113.1).

**Causa**
El Router-ISP no estaba configurado: interfaces sin IP y administrativamente caidas (shutdown por defecto en IOS).

**Solucion**
Configuracion completa del Router-ISP: asignacion de IPs en ambas interfaces, activacion con "no shutdown" y rutas estaticas de retorno hacia las redes de Barcelona y Madrid:

    ip route 192.168.200.0 255.255.255.0 203.0.113.2
    ip route 192.168.100.0 255.255.255.0 203.0.113.2

---

## Incidencia 9 - GNS3 bloqueado por consumo de CPU

**Sintoma**
GNS3 dejaba de responder con la CPU al 94%. No era posible guardar el proyecto ni interactuar con los nodos.

**Causa**
El nodo IOSv2 (Router-ISP) consume una cantidad elevada de CPU en un equipo con procesador Intel i5-6200U de doble nucleo. Anadir un segundo IOSv2 para simular el router de Madrid saturaba completamente el procesador.

**Solucion**
Sustituir el router de la Sede Madrid por un nodo MikroTik CHR, que consume significativamente menos CPU. No arrancar todos los nodos simultaneamente durante el desarrollo — arrancar solo los necesarios en cada momento.
