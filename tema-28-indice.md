# Tema 28 — Índice

> **Título oficial**: Virtualización de sistemas y virtualización de puestos de usuario.
>
> **Bloque**: Parte II — Técnico (Temas 11-40)
> **Nivel**: C1 — Técnico Auxiliar TIC, Ayuntamiento de Madrid

---

## Estructura del tema

1. **Introducción y fundamentos de la virtualización de computadores**
   1.1. Concepto, evolución y principios de la virtualización
   1.2. Arquitectura clásica de la virtualización y nivel de abstracción
   1.2.1. Virtualización total o completa
   1.2.2. Paravirtualización
   1.2.3. Virtualización asistida por hardware
   1.3. Hipervisores y su clasificación
   1.3.1. Hipervisores de Tipo 1 o nativos (bare-metal)
   1.3.2. Hipervisores de Tipo 2 o alojados (hosted)

2. **Virtualización de sistemas y servidores**
   2.1. Arquitectura y componentes de un entorno de virtualización de servidores
   2.2. Asignación y gestión de recursos del sistema
   2.2.1. Planificación de CPU y gestión de memoria virtualizada
   2.2.2. Entradas y salidas y controladores paravirtualizados
   2.3. Virtualización basada en contenedores y aislamiento de procesos
   2.3.1. Comparativa entre virtualización basada en hipervisor y contenedores
   2.4. Alta disponibilidad, balanceo y migración en caliente de sistemas

3. **Virtualización de puestos de usuario y del entorno de trabajo**
   3.1. Modelos de virtualización en el cliente
   3.1.1. Infraestructura de escritorios virtuales en servidor (VDI)
   3.1.2. Escritorios basados en sesiones y terminal server
   3.1.3. Virtualización de aplicaciones
   3.2. Componentes de la arquitectura VDI
   3.2.1. Agente de conexión o broker de accesos
   3.2.2. Gestor de imágenes, plantillas y aprovisionamiento
   3.3. Protocolos de representación y transporte para el puesto de trabajo
   3.4. Estrategias de persistencia: escritorios dedicados y no dedicados

4. **Arquitectura de soporte: almacenamiento, redes y gestión**
   4.1. Virtualización del almacenamiento e infraestructuras hiperconvergentes
   4.2. Virtualización de redes y redes definidas por software (SDN)
   4.3. Gestión centralizada, monitorización y orquestación de recursos

5. **Marco normativo, seguridad y aplicación en la Administración Pública**
   5.1. Cumplimiento del Esquema Nacional de Seguridad (ENS) en entornos virtualizados
   5.2. Protección de datos personales y garantías de privacidad (RGPD y LOPDGDD)
   5.3. Continuidad del negocio, copias de seguridad y recuperación ante desastres
   5.4. Eficiencia energética, consolidación de infraestructuras y sostenibilidad en el sector público

---

## Conceptos clave para memorizar

| Concepto | Dato clave |
|---|---|
| Principios de Popek y Goldberg (1974) | Un monitor de máquina virtual debe cumplir tres propiedades: **equivalencia** (el programa se comporta igual que en la máquina real), **control de recursos** (el monitor controla todo el hardware) y **eficiencia** (la mayoría de instrucciones se ejecutan directamente en la CPU, sin intervención del monitor) |
| Teorema de virtualizabilidad | Una arquitectura es virtualizable de forma clásica si **toda instrucción sensible es también privilegiada**. El x86 original **no lo cumplía**: tenía **17 instrucciones sensibles no privilegiadas** |
| Virtualización total | El sistema huésped **no sabe** que está virtualizado; el monitor emula el hardware. Implementación clásica: **traducción binaria** del código privilegiado más ejecución directa del código de usuario |
| Paravirtualización | El sistema huésped **está modificado** y sabe que está virtualizado: sustituye las instrucciones problemáticas por llamadas explícitas al hipervisor (*hypercalls*). Mejor rendimiento, pero exige tocar el núcleo del huésped |
| Virtualización asistida por hardware | La CPU añade un **modo de ejecución nuevo** para el hipervisor (Intel **VT-x**: modo raíz/no raíz y estructura **VMCS**; AMD **SVM/AMD-V**: **VMCB**; Arm: **EL2**). El huésped se ejecuta sin modificar y sin traducción binaria |
| EPT / NPT | Tablas de páginas **anidadas** en hardware (*Extended Page Tables* en Intel, *Nested Page Tables* o RVI en AMD): resuelven en la MMU la doble traducción de memoria y eliminan las costosas *shadow page tables* por software |
| IOMMU (VT-d / AMD-Vi) | Unidad de gestión de memoria de **E/S**: traduce y aísla los accesos DMA de un dispositivo. **Requisito imprescindible** para asignar un dispositivo físico directamente a una máquina virtual (*passthrough*) |
| Hipervisor de Tipo 1 | Se ejecuta **directamente sobre el hardware**, sin sistema operativo anfitrión debajo (ESXi, Hyper-V, Xen, KVM sobre Linux). Es el modelo de **centro de datos**: mejor rendimiento, menor superficie de ataque |
| Hipervisor de Tipo 2 | Se ejecuta **como aplicación sobre un sistema operativo anfitrión** (VirtualBox, VMware Workstation/Fusion). Modelo de **escritorio, laboratorio y desarrollo** |
| virtio | Estándar **abierto** (OASIS) de dispositivos **paravirtualizados** de E/S: el huésped instala un controlador que habla directamente con el hipervisor por colas compartidas, en lugar de emular un dispositivo real. Es paravirtualización **de dispositivos**, no del núcleo entero |
| SR-IOV | Un dispositivo PCIe se presenta como una **función física (PF)** y varias **funciones virtuales (VF)** asignables directamente a máquinas virtuales: rendimiento casi nativo, a costa de perder la migración en caliente sencilla |
| Sobreasignación (*overcommit*) | Asignar a las máquinas virtuales **más recursos de los que existen físicamente**, apostando a que no los usan todos a la vez. Aceptable en CPU, **peligrosa en memoria** |
| Técnicas de recuperación de memoria | **Compartición de páginas idénticas**, **globo** (*ballooning*), **compresión** y, como último recurso y el peor de todos, **intercambio a disco** del hipervisor |
| Contenedor | Aísla **procesos** sobre un **núcleo compartido** usando *namespaces* y *cgroups*: arranca en milisegundos y ocupa MB, pero **no puede ejecutar otro sistema operativo** ni ofrece el aislamiento de una máquina virtual |
| VM frente a contenedor | La máquina virtual virtualiza **el hardware** (cada VM tiene su núcleo); el contenedor virtualiza **el sistema operativo** (todos comparten el núcleo del anfitrión). **No son alternativas excluyentes**: lo habitual es ejecutar contenedores dentro de máquinas virtuales |
| Migración en caliente | Mover una máquina virtual **en ejecución** entre anfitriones sin apagarla, copiando la memoria de forma iterativa (*precopia*) mientras sigue funcionando y conmutando en una parada final de milisegundos. Requiere **CPU compatible, red compartida y almacenamiento accesible** por ambos anfitriones |
| HA frente a FT | **Alta disponibilidad (HA)**: si cae un anfitrión, sus VM **se reinician** en otro → hay corte y se pierde el estado en memoria. **Tolerancia a fallos (FT)**: se mantiene una **copia en espejo sincronizada** → conmutación sin corte ni pérdida de estado, a un coste mucho mayor |
| VDI | *Virtual Desktop Infrastructure*: **una máquina virtual con sistema operativo de cliente por usuario**, ejecutada en el centro de datos y consumida en remoto |
| Escritorio por sesiones (RDSH) | **Un solo sistema operativo de servidor** compartido por muchas sesiones simultáneas: mucho más denso y barato que VDI, pero con menor aislamiento y sin personalización profunda del sistema |
| Virtualización de aplicaciones | Se virtualiza **solo la aplicación**, empaquetada en una burbuja aislada que se entrega bajo demanda al puesto (App-V, MSIX app attach, ThinApp), sin virtualizar el escritorio entero |
| Broker de conexiones | Pieza central de VDI: **autentica** al usuario, comprueba sus **autorizaciones**, **selecciona o enciende** el escritorio adecuado y **redirige** al cliente hacia él. Es punto único de fallo: se despliega siempre **redundado** |
| Imagen maestra (*golden image*) | Plantilla única de la que se derivan todos los escritorios: se parchea **una sola vez** y los escritorios no persistentes la heredan al siguiente reinicio |
| Escritorio no persistente | Se **descarta al cerrar sesión** y vuelve a la imagen maestra: barato de operar y muy seguro, pero **exige gestionar el perfil aparte** (contenedor de perfil, redirección de carpetas) |
| Protocolos de puesto remoto | **RDP** (Microsoft), **ICA/HDX** (Citrix), **PCoIP** (Teradici/HP), **Blast Extreme** (VMware/Omnissa), **SPICE** (Red Hat) y **RFB/VNC** (abierto, RFC 6143). Transmiten **imagen, audio y periféricos**, no la aplicación |
| Hiperconvergencia (HCI) | Cómputo, almacenamiento y red **en los mismos nodos** con software distribuido: desaparece la cabina externa, se crece **añadiendo nodos** (escalado horizontal) |
| VXLAN | Superposición de red que encapsula tramas de nivel 2 sobre UDP con un identificador **VNI de 24 bits** (~16 millones de segmentos frente a los 4.094 de VLAN) [RFC 7348] |
| SDN | Separación del **plano de control** (decide) y el **plano de datos** (reenvía), con un **controlador centralizado** programable y interfaces norte (aplicaciones) y sur (dispositivos) |
| Microsegmentación | Cortafuegos distribuido **por máquina virtual**, aplicado en el conmutador virtual: controla el tráfico **este-oeste** dentro del propio centro de datos, no solo el perimetral |
| ENS — categorías y dimensiones | RD 311/2022: categorías **básica, media y alta**, y cinco dimensiones de seguridad: **disponibilidad, integridad, confidencialidad, autenticidad y trazabilidad**. Auditoría ordinaria **cada dos años** en categorías media y alta |
| RTO y RPO | **RTO** = tiempo máximo tolerable hasta restablecer el servicio. **RPO** = cantidad máxima tolerable de datos perdidos, medida en tiempo hacia atrás. Los fija el **análisis de impacto en el negocio (BIA)**, no el técnico |
| Regla 3-2-1 | **3** copias de los datos, en **2** tipos de soporte distintos, con **1** de ellas fuera del emplazamiento. Ampliada hoy con **1 copia inmutable o aislada** frente al secuestro de datos |
| PUE | *Power Usage Effectiveness* [ISO/IEC 30134-2]: energía total del centro de datos dividida entre la energía consumida por el equipamiento TI. **El valor ideal es 1,0**; cuanto más bajo, mejor |

---

*Tiempo estimado de estudio: 14-16 horas*
*Extensión del contenido: ~18.700 palabras · 15 diagramas SVG embebidos*
