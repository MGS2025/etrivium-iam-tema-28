# Tema 28 — Contenido Teórico

> **Título oficial**: Virtualización de sistemas y virtualización de puestos de usuario.
>
> **Bloque**: Parte II — Técnico
> **Nivel**: C1 — Técnico Auxiliar TIC, Ayuntamiento de Madrid
> **Versión**: v1.0 — Pendiente validación
> **Fecha generación**: 2026-08-20
> **Fuentes**: Ver tema-28-fuentes.md · **Diagramas**: Ver tema-28-diagramas.md · **Cambios**: Ver tema-28-changelog.md
>
> *Extensión: ~18.700 palabras · 15 diagramas SVG embebidos · 4 tipos de callout transversales*

---

## Convenciones del documento

Este tema incluye cuatro tipos de **cajas callout** para facilitar el estudio:

> **[DATO CLAVE]** Información de alta densidad memorística.

> **[EJERCICIO RESUELTO]** Problema + solución paso a paso (identificación de una tecnología, dimensionamiento, elección arquitectónica razonada).

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** Aplicación de la teoría al entorno municipal (centro de proceso de datos, sede electrónica, puestos de las oficinas de atención a la ciudadanía).

> **[RELACIÓN CON OTROS TEMAS]** Enlace conceptual a otros temas del temario oficial.

Los **nombres de producto** (ESXi, Hyper-V, KVM, Xen, Citrix, Horizon, Proxmox, Docker, Kubernetes) se usan siempre como **ilustración de un concepto general**, nunca como contenido en sí mismo: la parte estable y examinable del tema son los principios, las técnicas y las arquitecturas, no las marcas. Las fuentes se citan con etiquetas breves tipo `[POPEK74]` o `[ENS]`; el registro completo está en `tema-28-fuentes.md`.

**Caso de referencia usado en todo el tema** (contexto Ayuntamiento de Madrid, supuesto simplificado): el Ayuntamiento mantiene un **centro de proceso de datos municipal** en el que conviven los servidores de la **sede electrónica**, del **padrón municipal de habitantes**, del **registro de entrada y salida** y del **gestor de expedientes**, además de un parque de **puestos de trabajo** repartidos entre las oficinas de atención a la ciudadanía, los servicios centrales y los distritos. Sobre ese supuesto se plantean las dos preguntas que estructuran el tema: **cómo se virtualizan los servidores** (secciones 1, 2 y 4) y **cómo se virtualiza el puesto de trabajo del empleado público** (sección 3), y qué obligaciones normativas impone hacerlo en una Administración Pública (sección 5).

---

## 1. Introducción y fundamentos de la virtualización de computadores

### 1.1. Concepto, evolución y principios de la virtualización

Se denomina **virtualización** a la técnica que permite crear una **representación lógica de un recurso informático**, desligada del recurso físico que la soporta, de modo que ese recurso lógico pueda ser utilizado como si fuera real. El elemento que hace posible esa ilusión es una **capa de indirección** —software, microprograma o circuitería— situada entre quien consume el recurso y el recurso mismo [POPEK74] [NIST-SP800-125].

El resultado más característico de aplicar esa técnica a un ordenador completo es la **máquina virtual**: un contenedor de software que se comporta como un ordenador físico independiente, con su propia CPU virtual, su memoria, su almacenamiento y sus interfaces de red, y sobre el que se instala un **sistema operativo huésped** sin modificar. El programa que crea y gobierna esas máquinas virtuales se llama **monitor de máquina virtual** (*Virtual Machine Monitor*, VMM) o, en la terminología actual, **hipervisor** [GOLDBERG73].

Conviene fijar desde el principio el vocabulario, porque se utiliza con precisión:

| Término | Significado |
|---|---|
| **Anfitrión** (*host*) | La máquina **física** que aporta los recursos reales y ejecuta el hipervisor. |
| **Huésped** (*guest*) | El sistema operativo **virtualizado** que se ejecuta dentro de una máquina virtual. |
| **Hipervisor** o **VMM** | La capa de software que crea las máquinas virtuales, les reparte los recursos del anfitrión y las aísla entre sí. |
| **Máquina virtual (VM)** | El conjunto formado por el hardware virtual definido y el sistema huésped instalado sobre él. En disco es un **conjunto de ficheros** (configuración + discos virtuales). |
| **Instantánea** (*snapshot*) | Estado congelado de una máquina virtual (disco y, opcionalmente, memoria) al que se puede volver. **No es una copia de seguridad**. |
| **Plantilla** (*template*) | Máquina virtual preparada y bloqueada que sirve de molde para desplegar máquinas nuevas idénticas. |

> **[DATO CLAVE]** Que una máquina virtual sea, en el sistema de ficheros del anfitrión, **un conjunto de ficheros** tiene tres consecuencias clave: se puede **copiar**, se puede **mover a otro anfitrión** y se puede **restaurar completa** con una sola operación. Esa es la razón última de que la virtualización simplifique tanto la continuidad del servicio y la recuperación ante desastres (§5.3).

**Evolución histórica.** La virtualización no es una tecnología reciente; es una idea de los años sesenta que el hardware de gran consumo tardó cuarenta años en poder ejecutar bien:

| Etapa | Periodo | Hitos |
|---|---|---|
| **Origen en los grandes sistemas** | 1964-1972 | IBM desarrolla CP-40 y **CP-67/CMS** y, después, **VM/370**: cada usuario de un mainframe recibe una máquina virtual completa. El objetivo era **compartir en tiempo compartido** un equipo carísimo [HIST-IBM]. |
| **Formalización teórica** | 1973-1974 | Goldberg clasifica los monitores en **Tipo 1 y Tipo 2** [GOLDBERG73]; Popek y Goldberg formulan las **tres propiedades** y el **teorema de virtualizabilidad** [POPEK74]. |
| **Travesía del desierto en x86** | 1980-1998 | La arquitectura x86 **no cumple** el teorema: 17 instrucciones sensibles no son privilegiadas [ROBIN00]. La virtualización queda confinada a los grandes sistemas. |
| **Virtualización por software del x86** | 1999-2005 | VMware resuelve el problema con **traducción binaria** (1999); Xen introduce la **paravirtualización** (2003) [XEN-DOC]. Comienza la consolidación de servidores. |
| **Asistencia por hardware** | 2005-2010 | Intel **VT-x** (2005) y AMD **AMD-V** (2006) añaden un modo de ejecución para el hipervisor [INTEL-SDM] [AMD-APM]; llegan después **EPT/NPT** para la memoria y **VT-d/AMD-Vi** para la E/S. La virtualización se vuelve la norma en el centro de datos. |
| **Virtualización de todo el centro de datos** | 2010-2015 | Se virtualizan también el **almacenamiento** y la **red** (SDN, NFV) y aparece el escritorio virtual (VDI) como producto maduro. |
| **Contenedores y nube** | 2013-actualidad | Docker (2013) populariza los **contenedores**; Kubernetes (2014) los orquesta. La virtualización deja de ser un fin y pasa a ser **el sustrato invisible de la nube** [K8S-DOC]. |

> **[DATO CLAVE]** Dos fechas y dos nombres clave: la virtualización **nace en los mainframes de IBM en los años sesenta** (CP-67, VM/370), no con VMware; y en x86 el problema no era de potencia sino **arquitectónico**, porque el juego de instrucciones no cumplía el teorema de Popek y Goldberg [POPEK74] [ROBIN00].

**Los tres principios de Popek y Goldberg.** Un monitor de máquina virtual solo merece ese nombre si cumple tres propiedades [POPEK74]:

1. **Equivalencia o fidelidad.** Un programa ejecutado dentro de la máquina virtual debe comportarse **igual** que si se ejecutara directamente sobre el hardware, salvo por diferencias de temporización y por la disponibilidad de recursos.
2. **Control de recursos o seguridad.** El monitor debe tener el **control completo** de los recursos físicos: ningún programa huésped puede acceder a recursos que el monitor no le haya asignado, ni afectar a otra máquina virtual.
3. **Eficiencia o rendimiento.** Una **proporción estadísticamente dominante** de las instrucciones del huésped debe ejecutarse **directamente en la CPU real**, sin intervención del monitor. Es la propiedad que separa un hipervisor de un **emulador**.

> **[DATO CLAVE]** **Virtualizar no es emular.** Un **emulador** (por ejemplo, ejecutar software de arquitectura Arm sobre un x86) **traduce cada instrucción** y por tanto incumple la propiedad de eficiencia; puede, en cambio, ejecutar código de una arquitectura distinta. Un **hipervisor** ejecuta la mayoría de las instrucciones directamente en la CPU real, pero **exige que huésped y anfitrión compartan arquitectura** [POPEK74].

El **teorema de virtualizabilidad** que acompaña a los principios establece la condición formal: una arquitectura es virtualizable de forma clásica (por *trap-and-emulate*, es decir, «atrapar y emular») si **el conjunto de sus instrucciones sensibles está contenido en el de sus instrucciones privilegiadas**. Las **instrucciones sensibles** son las que consultan o modifican el estado de configuración de la máquina; las **privilegiadas**, las que provocan una excepción si se ejecutan fuera del modo supervisor. Si toda instrucción sensible es privilegiada, el monitor puede ejecutar el huésped en modo no privilegiado y limitarse a atrapar y emular las excepciones que se produzcan [POPEK74].

**Ventajas de la virtualización.** Son el motivo por el que hoy es la norma en cualquier centro de datos, también en el sector público:

- **Consolidación y aprovechamiento.** Un servidor físico dedicado a una sola aplicación suele operar muy por debajo de su capacidad; agrupando varias máquinas virtuales en un mismo anfitrión se eleva la utilización y se reduce drásticamente el número de equipos físicos (§5.4).
- **Aislamiento.** El fallo, el bloqueo o el compromiso de una máquina virtual **no arrastra** a las demás del mismo anfitrión.
- **Encapsulación y portabilidad.** La máquina virtual es un conjunto de ficheros: se copia, se mueve, se versiona y se restaura.
- **Independencia del hardware.** El huésped ve siempre el mismo hardware virtual aunque el anfitrión cambie; esto permite renovar servidores sin reinstalar sistemas ni aplicaciones.
- **Agilidad en el aprovisionamiento.** Desplegar un servidor nuevo desde plantilla pasa de semanas (compra, recepción, montaje, instalación) a minutos.
- **Continuidad del servicio.** Migración en caliente, reinicio automático en otro anfitrión y recuperación en un emplazamiento alternativo (§2.4 y §5.3).
- **Entornos de prueba realistas.** Se pueden reproducir entornos completos de preproducción con instantáneas y descartarlos después.

**Inconvenientes y riesgos**, que son la otra cara de las ventajas:

- **Sobrecarga** (*overhead*): siempre existe un coste de rendimiento, hoy muy reducido pero no nulo, y muy visible en cargas de E/S intensiva.
- **Concentración del riesgo**: si un anfitrión soporta veinte máquinas virtuales, su caída afecta a veinte servicios. Esto exige **agrupación en clúster** y reglas de antiafinidad (§2.4).
- **Nueva superficie de ataque**: el hipervisor es una capa crítica; un compromiso a ese nivel afecta a todo lo que aloja (§5.1).
- **Proliferación descontrolada** (*VM sprawl*): al ser tan fácil crear máquinas virtuales, se multiplican las que nadie da de baja, consumiendo licencias, almacenamiento, copias de seguridad y superficie de parcheo (§4.3).
- **Dependencia y licenciamiento**: los modelos de licencia de hipervisores y de sistemas huéspedes son complejos y han sufrido cambios abruptos; es un riesgo contractual real para una Administración.

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** Antes de virtualizar, cada aplicación municipal (padrón, registro, gestor de expedientes, portal interno) tenía su propio servidor físico, dimensionado para su pico anual y ocioso el resto del tiempo. Al consolidar esos servidores como máquinas virtuales sobre un grupo reducido de anfitriones, el Ayuntamiento reduce equipos, consumo eléctrico y espacio de sala, y gana la posibilidad de mover una aplicación de un anfitrión a otro sin interrumpir el servicio a la ciudadanía.

> **[RELACIÓN CON OTROS TEMAS]** La **arquitectura de ordenadores** y sus componentes internos (CPU, memoria, buses) se estudian en el **Tema 11**; los **sistemas operativos** y sus mecanismos de gestión de procesos y memoria, en el **Tema 14**; la **administración del sistema operativo** en el **Tema 27**. Este tema da por conocidos esos conceptos y se centra en **qué cambia cuando se interpone un hipervisor**.

### 1.2. Arquitectura clásica de la virtualización y nivel de abstracción

La virtualización puede introducirse en **distintos niveles de la pila** de un sistema informático, y cada nivel produce un tipo de virtualización distinto. Situar correctamente cada tecnología en esa pila es la mejor forma de no confundirlas:

| Nivel donde se inserta la capa | Qué se virtualiza | Ejemplos |
|---|---|---|
| **Juego de instrucciones** | La propia arquitectura de la CPU | Emuladores (QEMU en modo emulación completa) |
| **Hardware / plataforma** | El ordenador completo | **Hipervisores** (ESXi, Hyper-V, KVM, Xen) — objeto principal de este tema |
| **Sistema operativo** | El espacio de nombres y los recursos de un sistema en ejecución | **Contenedores** (Docker, LXC, Kubernetes) — §2.3 |
| **Biblioteca / entorno de ejecución** | La interfaz de programación | Máquina virtual de Java, entorno de ejecución de .NET |
| **Aplicación** | Una aplicación concreta, empaquetada aislada | App-V, MSIX app attach, ThinApp — §3.1.3 |
| **Escritorio** | El puesto de trabajo completo | VDI, escritorios por sesiones — §3 |
| **Almacenamiento** | Los volúmenes y sistemas de ficheros | SDS, hiperconvergencia — §4.1 |
| **Red** | Conmutadores, encaminadores, cortafuegos y segmentos | Conmutador virtual, VXLAN, SDN, NFV — §4.2 |

> **[DATO CLAVE]** No confundir el nivel: el **hipervisor** virtualiza el **hardware** y por eso cada máquina virtual lleva **su propio núcleo**; el **contenedor** virtualiza el **sistema operativo** y por eso todos comparten el **núcleo del anfitrión**; la **máquina virtual de Java** virtualiza el **entorno de ejecución** y no es en absoluto lo mismo que una máquina virtual de sistema, aunque compartan nombre.

La arquitectura clásica de la virtualización de plataforma se apoya en los **niveles de privilegio** de la CPU. En x86, la arquitectura define cuatro **anillos** (*rings*) numerados del 0 al 3: el sistema operativo se ejecuta en el **anillo 0** (modo supervisor, acceso pleno al hardware) y las aplicaciones en el **anillo 3** (modo usuario). El problema es evidente: si el hipervisor debe controlar el hardware, tiene que ocupar el anillo 0, y entonces **el sistema huésped no puede estar donde espera estar** [INTEL-SDM] [ROBIN00].

De cómo se resuelve ese conflicto nacen las **tres técnicas** que el enunciado del tema pide distinguir: virtualización total, paravirtualización y virtualización asistida por hardware.

#### 1.2.1. Virtualización total o completa

En la **virtualización total** o completa (*full virtualization*) el hipervisor presenta al huésped un **hardware virtual completo y verosímil**, y el sistema huésped **se instala sin ninguna modificación** y sin saber que está virtualizado. Es la técnica que mejor cumple la propiedad de **equivalencia** de Popek y Goldberg [POPEK74].

Su implementación clásica en x86, anterior a la asistencia por hardware, combina dos mecanismos [ROBIN00]:

- **Ejecución directa** del código de **aplicación** del huésped (anillo 3): se ejecuta tal cual en la CPU real, a plena velocidad. De aquí sale la eficiencia.
- **Traducción binaria dinámica** del código **privilegiado** del núcleo huésped: el hipervisor intercepta los bloques de código del sistema huésped antes de ejecutarlos, **reescribe sobre la marcha** las instrucciones sensibles no privilegiadas por secuencias seguras que devuelven el control al hipervisor, y guarda el resultado en una **caché de traducción** para no repetir el trabajo.

A esta combinación se le añade la **reubicación de anillos** (*ring deprivileging*): el hipervisor ocupa el anillo 0 y el núcleo huésped se desplaza a un anillo menos privilegiado (habitualmente el 1), de modo que sus intentos de acceder al hardware provoquen excepciones que el hipervisor pueda atrapar y emular.

Ventajas de la virtualización total:

- **No requiere modificar el sistema huésped**: admite sistemas operativos cerrados o antiguos, cuyo código fuente no está disponible.
- **Aislamiento y equivalencia máximos**: el huésped no puede distinguir, en condiciones normales, que está virtualizado.
- **Portabilidad**: la misma máquina virtual funciona sobre anfitriones distintos.

Inconvenientes:

- **Coste de la traducción binaria**: reescribir y cachear código tiene un precio en CPU y en memoria, sobre todo en cargas con muchas llamadas al sistema.
- **Emulación de dispositivos**: presentar un hardware verosímil obliga a emular controladores de red o de disco reales (tarjetas y adaptadores concretos), lo que es lento; de ahí que la virtualización total moderna se combine casi siempre con **controladores paravirtualizados** de E/S (§2.2.2).

> **[EJERCICIO RESUELTO]** *Un sistema operativo huésped muy antiguo, sin soporte del fabricante y sin posibilidad de instalar controladores nuevos, debe seguir funcionando para consultar un archivo histórico municipal. ¿Qué técnica de virtualización es la única viable?* **Solución**: la **virtualización total**. La paravirtualización queda descartada porque exige **modificar el núcleo** del huésped —imposible en un sistema cerrado y sin soporte— y los controladores paravirtualizados de E/S tampoco pueden instalarse. El huésped verá un hardware emulado clásico, con un rendimiento modesto pero suficiente para una carga de consulta.

#### 1.2.2. Paravirtualización

En la **paravirtualización** se renuncia a la equivalencia perfecta a cambio de rendimiento: el sistema huésped **se modifica** para que **sepa que está virtualizado** y coopere con el hipervisor. En lugar de ejecutar instrucciones sensibles que habría que atrapar y emular, el núcleo huésped invoca directamente al hipervisor mediante **llamadas al hipervisor** (*hypercalls*), que son a la relación huésped-hipervisor lo que las llamadas al sistema son a la relación aplicación-núcleo [XEN-DOC].

El exponente histórico de esta técnica es **Xen** (2003), cuya arquitectura introduce además una distinción que conviene conocer [XEN-DOC]:

- **`dom0`** (dominio 0): máquina virtual privilegiada, con controladores de dispositivo reales, que se arranca la primera y desde la que se administra el sistema y se da servicio de E/S a las demás.
- **`domU`**: los dominios no privilegiados, es decir, las máquinas virtuales de los usuarios.

Ventajas de la paravirtualización:

- **Rendimiento superior** al de la traducción binaria: se eliminan las capturas y emulaciones costosas.
- **E/S mucho más eficiente**, al sustituir la emulación de dispositivos por una interfaz explícita de colas compartidas.
- **Menor complejidad del hipervisor**, que no necesita reescribir código.

Inconvenientes:

- **Exige modificar el núcleo del huésped**: solo es viable con sistemas operativos de código abierto o cuyo fabricante haya publicado los componentes de integración. Un sistema cerrado y antiguo no puede paravirtualizarse.
- **Rompe la equivalencia**: el huésped ya no es idéntico al que correría sobre hardware físico, lo que complica el soporte del fabricante.

> **[DATO CLAVE]** La **paravirtualización pura del núcleo** ha quedado prácticamente desplazada por la virtualización asistida por hardware, que da rendimiento equivalente **sin tocar el huésped**. Lo que **sí sigue plenamente vigente** —y es un error frecuente creer lo contrario— es la **paravirtualización de dispositivos**: los controladores **virtio** [VIRTIO], los *Integration Services* de Hyper-V o VMware Tools son paravirtualización aplicada solo a la E/S, y se usan en la práctica totalidad de las máquinas virtuales actuales (§2.2.2).

#### 1.2.3. Virtualización asistida por hardware

La **virtualización asistida por hardware** resuelve el problema en el origen: en lugar de sortear las carencias del juego de instrucciones por software, los fabricantes **amplían la arquitectura de la CPU** con un modo de ejecución adicional pensado para el hipervisor. Es el modelo dominante desde finales de la década de 2000 [INTEL-SDM] [AMD-APM].

**En Intel (VT-x)**, la extensión **VMX** introduce [INTEL-SDM]:

- Dos modos de operación nuevos: **VMX raíz** (*root*), donde se ejecuta el hipervisor con control pleno, y **VMX no raíz** (*non-root*), donde se ejecuta el huésped. Cada modo conserva sus propios anillos 0-3, de modo que **el núcleo huésped vuelve a ejecutarse en su anillo 0** —el suyo, virtual— y deja de necesitar reubicación ni traducción binaria. Coloquialmente se dice que el hipervisor pasa a ocupar un **«anillo −1»**.
- La estructura de control **VMCS** (*Virtual Machine Control Structure*), una región de memoria por máquina virtual donde se guarda el estado del huésped y del anfitrión y se configura **qué eventos deben provocar la salida** hacia el hipervisor.
- Las transiciones **VM entry** (el hipervisor cede el control al huésped) y **VM exit** (el control vuelve al hipervisor porque se ha producido un evento configurado). Cada *VM exit* tiene un coste: **minimizar el número de salidas es el objetivo central de la optimización** de un hipervisor moderno.

**En AMD (AMD-V o SVM)** el planteamiento es equivalente, con la estructura **VMCB** (*Virtual Machine Control Block*) y las instrucciones `VMRUN`/`VMEXIT` [AMD-APM]. **En Arm**, la arquitectura define un nivel de excepción específico, **EL2**, para el hipervisor, por encima del núcleo huésped (EL1) y de las aplicaciones (EL0) [ARM-ARM].

La asistencia por hardware no se limitó a la CPU; se extendió a los otros dos cuellos de botella:

- **Memoria — EPT / NPT.** Sin ayuda del hardware, el hipervisor tenía que mantener por software **tablas de páginas sombra** (*shadow page tables*) que combinaran la traducción del huésped con la suya propia, un mecanismo correcto pero muy costoso. Las **tablas de páginas extendidas** (**EPT** en Intel) o **anidadas** (**NPT**, también llamada RVI, en AMD) añaden un **segundo nivel de traducción en la propia MMU**: el huésped traduce de dirección virtual a «física» del huésped, y el hardware traduce esa a dirección física real del anfitrión, sin intervención del hipervisor [INTEL-SDM] [AMD-APM]. En Arm, el mecanismo equivalente es la **traducción en dos etapas** (*Stage-1* y *Stage-2*) [ARM-ARM].
- **Entrada/salida — IOMMU.** La **unidad de gestión de memoria de E/S** (**VT-d** en Intel, **AMD-Vi** en AMD) traduce y controla los accesos **DMA** que los dispositivos hacen a la memoria. Sin ella, asignar un dispositivo físico directamente a una máquina virtual sería un agujero de seguridad, porque ese dispositivo podría leer y escribir toda la memoria del anfitrión. Con ella, el hipervisor confina cada dispositivo a las páginas de su máquina virtual [INTEL-VTD].

> **[DATO CLAVE]** Las tres asistencias por hardware y el problema que resuelve cada una: **VT-x / AMD-V / EL2** → el problema de las **instrucciones sensibles** y los anillos de privilegio; **EPT / NPT / Stage-2** → el problema de la **doble traducción de memoria** (sustituyen a las tablas sombra); **VT-d / AMD-Vi (IOMMU)** → el problema del **acceso directo a memoria por parte de los dispositivos**, y son el requisito para el *passthrough* y para SR-IOV [INTEL-SDM] [AMD-APM] [INTEL-VTD].

Comparativa final de las tres técnicas, que es la tabla que conviene llevar memorizada:

| | **Virtualización total** | **Paravirtualización** | **Asistida por hardware** |
|---|---|---|---|
| ¿Se modifica el huésped? | **No** | **Sí** (núcleo modificado) | **No** |
| ¿El huésped sabe que está virtualizado? | No | **Sí** | No (salvo por los controladores de E/S) |
| Mecanismo principal | Traducción binaria + ejecución directa | Llamadas al hipervisor (*hypercalls*) | Modo de CPU adicional (VMX/SVM/EL2) |
| Requisito | Ninguno especial | Código fuente del huésped disponible | **CPU compatible y opción activada en la BIOS/UEFI** |
| Rendimiento | Medio | Alto | **Alto**, y sin tocar el huésped |
| Sistemas huéspedes admitidos | **Cualquiera** | Solo los adaptados | **Cualquiera** |
| Estado actual | Vigente como concepto; combinada con virtio | Desplazada en el núcleo, **vigente en E/S** | **Modelo dominante** |

> **[EJERCICIO RESUELTO]** *Se instala un hipervisor en un servidor y, al crear la primera máquina virtual de 64 bits, la consola informa de que «la virtualización por hardware no está disponible». El procesador es moderno. ¿Cuál es la causa más probable y cómo se corrige?* **Solución**: la extensión de virtualización de la CPU (**Intel VT-x** o **AMD-V**) está **desactivada en la BIOS/UEFI** del servidor, que es como suele venir de fábrica en algunos equipos. Se corrige activándola en la configuración del firmware y reiniciando. Es un fallo de configuración, no de hardware ni de licencia [INTEL-SDM].

### 1.3. Hipervisores y su clasificación

La clasificación de los monitores de máquina virtual en dos tipos procede de Goldberg (1973) y es la taxonomía central del tema [GOLDBERG73]. El criterio de clasificación es **sobre qué se ejecuta el hipervisor**: directamente sobre el hardware, o sobre un sistema operativo anfitrión.

#### 1.3.1. Hipervisores de Tipo 1 o nativos (bare-metal)

Un **hipervisor de Tipo 1**, también llamado **nativo** o *bare-metal* («sobre metal desnudo»), se instala **directamente sobre el hardware del servidor**, sin ningún sistema operativo de propósito general debajo. El propio hipervisor es, en la práctica, un sistema operativo especializado y minimalista cuya única misión es planificar CPU, repartir memoria, gestionar E/S y aislar máquinas virtuales [NIST-SP800-125].

Características:

- **Acceso directo al hardware**: no hay una capa de sistema operativo intermedia que añada latencia.
- **Superficie de ataque reducida**: al no incluir un sistema operativo completo, hay muchísimo menos código expuesto y menos parches que aplicar. Es un argumento de seguridad de primer orden, recogido expresamente en las recomendaciones del NIST [NIST-SP800-125].
- **Gestión centralizada**: se administran en clúster desde una consola externa (§4.3), no desde el propio anfitrión.
- **Uso**: **centros de datos y producción**. Es el único modelo aceptable para servidores de un servicio público en explotación.

Ejemplos habituales: **VMware ESXi** [VMWARE-DOC], **Microsoft Hyper-V** en su rol de servidor [HYPERV-DOC], **Xen** [XEN-DOC], **KVM** sobre Linux [KVM-DOC], y distribuciones que los integran como **Proxmox VE** [PROXMOX] u **oVirt** [OVIRT].

> **[DATO CLAVE]** **KVM es un caso de clasificación discutido.** KVM es un **módulo del núcleo de Linux** que convierte al propio núcleo en hipervisor: puede parecer de Tipo 2 porque hay un Linux completo debajo, pero **se clasifica como Tipo 1**, porque el hipervisor **es** el núcleo y accede al hardware sin intermediarios [KVM-DOC]. Lo mismo ocurre con **Hyper-V**: aunque se activa como un «rol» de Windows Server, al habilitarlo el hipervisor se coloca **por debajo** del sistema, que pasa a ser una partición privilegiada — es de **Tipo 1** [HYPERV-DOC].

#### 1.3.2. Hipervisores de Tipo 2 o alojados (hosted)

Un **hipervisor de Tipo 2** o **alojado** (*hosted*) se instala **como una aplicación más sobre un sistema operativo anfitrión** ya existente (Windows, Linux o macOS). Todas las peticiones de la máquina virtual al hardware atraviesan, por tanto, **dos capas**: el hipervisor y el sistema operativo anfitrión.

Características:

- **Menor rendimiento** por la capa adicional, aunque hoy los hipervisores de Tipo 2 se apoyan también en VT-x/AMD-V y la diferencia es menor que antaño.
- **Mayor superficie de ataque**: hereda todas las vulnerabilidades del sistema operativo anfitrión, de sus servicios y de sus aplicaciones.
- **Dependencia del anfitrión**: si el sistema anfitrión se reinicia o se bloquea, se caen todas las máquinas virtuales.
- **Instalación y uso triviales**: no requiere dedicar un equipo completo ni conocimientos de administración de centro de datos.
- **Uso**: **puesto de trabajo, laboratorio, formación, desarrollo y pruebas**; también análisis de programas maliciosos en entorno aislado, y ejecución puntual de una aplicación heredada que solo funciona sobre un sistema antiguo.

Ejemplos habituales: **Oracle VirtualBox**, **VMware Workstation** (Windows/Linux) y **VMware Fusion** (macOS), **Parallels Desktop**, y **QEMU** en modo autónomo.

| Criterio | **Tipo 1 (nativo)** | **Tipo 2 (alojado)** |
|---|---|---|
| Se ejecuta sobre | **El hardware** | **Un sistema operativo anfitrión** |
| Capas hasta el hardware | 1 | 2 |
| Rendimiento | Alto | Menor |
| Superficie de ataque | Reducida | La del sistema anfitrión + la propia |
| Arranque del equipo | Arranca el hipervisor | Arranca el sistema operativo, luego se lanza el hipervisor |
| Ámbito natural | **Centro de datos / producción** | **Puesto de trabajo / laboratorio** |
| Ejemplos | ESXi, Hyper-V, Xen, KVM, Proxmox VE | VirtualBox, Workstation, Fusion, Parallels |

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** En el Ayuntamiento conviven los dos tipos y no compiten entre sí: los servidores del padrón, del registro y de la sede electrónica se ejecutan sobre **hipervisores de Tipo 1** en clúster dentro del centro de proceso de datos, mientras que un técnico de la unidad de desarrollo usa un **hipervisor de Tipo 2** en su propio portátil para levantar una máquina virtual de pruebas y verificar una migración antes de proponerla. Usar un hipervisor de Tipo 2 para un servicio en producción sería un error grave de arquitectura y de seguridad [NIST-SP800-125].

---

## 2. Virtualización de sistemas y servidores

### 2.1. Arquitectura y componentes de un entorno de virtualización de servidores

Un entorno profesional de virtualización de servidores **no es un hipervisor suelto**: es un conjunto de piezas que trabajan coordinadas. Distinguirlas es imprescindible para leer cualquier arquitectura real [VMWARE-DOC] [HYPERV-DOC] [OPENSTACK].

**1. Anfitriones o nodos.** Los servidores físicos que ejecutan el hipervisor. Aportan CPU, memoria, interfaces de red y, en algunos modelos, discos locales. Se agrupan en **clúster**.

**2. Clúster.** Agrupación lógica de anfitriones que **comparten recursos y políticas** y se tratan como una unidad. El clúster es la unidad sobre la que se definen la alta disponibilidad, el balanceo automático de carga y las reglas de colocación (§2.4). Un requisito práctico habitual es la **homogeneidad de las CPU** (o el uso de un modo de compatibilidad que enmascare las diferencias) para que las máquinas virtuales puedan migrar entre cualesquiera nodos.

**3. Almacenamiento compartido.** Un almacenamiento **accesible desde todos los anfitriones del clúster** —cabina SAN por Fibre Channel o iSCSI, cabina NAS por NFS o SMB, o almacenamiento distribuido hiperconvergente (§4.1)— sobre el que residen los ficheros de las máquinas virtuales. Es la pieza que hace posible que otro anfitrión pueda arrancar una máquina virtual que no es «suya».

**4. Red física y virtual.** Cada anfitrión ejecuta uno o varios **conmutadores virtuales** (*vSwitch*) que conectan las tarjetas de red virtuales de las máquinas con las tarjetas físicas del anfitrión, aplicando etiquetado VLAN, agregación de enlaces y políticas de seguridad (§4.2). Por buena práctica se **separan los flujos** en redes distintas: gestión, máquinas virtuales, almacenamiento y migración en caliente [NIST-SP800-125].

**5. Servidor de gestión.** La consola centralizada que inventaría los anfitriones, gestiona el ciclo de vida de las máquinas virtuales, aplica las políticas del clúster, guarda el catálogo de plantillas y concentra permisos y registros de actividad (vCenter, SCVMM, OpenStack, oVirt) (§4.3).

**6. Componentes internos de la máquina virtual.** Dentro del huésped se instalan los **controladores paravirtualizados y agentes de integración** (VMware Tools, *Integration Services*, `qemu-guest-agent`), que aportan controladores eficientes de red y disco, sincronización horaria, apagado ordenado desde el hipervisor y **congelación del sistema de ficheros** para copias consistentes (§5.3).

**7. Catálogo de plantillas e imágenes.** Repositorio de máquinas virtuales molde, ya actualizadas y bastionadas, desde las que se despliegan servidores nuevos de forma reproducible.

**Formatos de disco virtual.** El disco de una máquina virtual es un fichero (o un conjunto de ellos) en un formato concreto. Los más frecuentes:

| Formato | Origen | Nota |
|---|---|---|
| **VMDK** | VMware | Formato de la familia vSphere/Workstation. |
| **VHD / VHDX** | Microsoft | VHDX amplía el tamaño máximo y añade protección frente a corrupción por corte eléctrico. |
| **QCOW2** | QEMU/KVM | *QEMU Copy-On-Write v2*: admite aprovisionamiento fino, instantáneas internas y compresión. |
| **RAW** | Genérico | Volcado en bruto, sin metadatos: máximo rendimiento, mínima funcionalidad. |
| **OVF / OVA** | DMTF (estándar abierto) | **No es un formato de disco**, sino de **empaquetado e intercambio** de máquinas virtuales: descriptor XML + discos. OVA es el mismo contenido en un único fichero comprimido. |

> **[DATO CLAVE]** Distinguir **aprovisionamiento fino** (*thin provisioning*) de **grueso** (*thick*): en el fino el disco virtual **ocupa en la cabina solo lo realmente escrito** y crece bajo demanda —ahorra mucho espacio, pero **exige vigilar el sobreaprovisionamiento**, porque si la cabina se llena las máquinas virtuales se detienen—; en el grueso se **reserva todo el espacio desde el principio**, con rendimiento más predecible y sin riesgo de agotamiento sorpresivo [VMWARE-DOC].

> **[DATO CLAVE]** Una **instantánea no es una copia de seguridad**. La instantánea congela un punto en el tiempo y a partir de ahí los cambios se escriben en **ficheros delta** que crecen; si se acumulan o se olvidan, **degradan el rendimiento y pueden llenar el almacenamiento**. Además reside en el mismo sistema que la máquina virtual: si se pierde la cabina, se pierden las instantáneas. Sirven para revertir un cambio a corto plazo (una actualización, un despliegue), no para proteger datos (§5.3).

### 2.2. Asignación y gestión de recursos del sistema

El hipervisor es, ante todo, un **repartidor de recursos escasos**. Su trabajo consiste en presentar a cada máquina virtual una vista coherente de CPU, memoria, disco y red mientras multiplexa unos recursos físicos que casi siempre están **sobreasignados**.

La **sobreasignación** (*overcommitment*) consiste en asignar al conjunto de máquinas virtuales más recursos de los que el anfitrión posee, apostando a que no todas los reclamarán simultáneamente. Es la técnica que hace rentable la consolidación, pero no todos los recursos la toleran igual:

| Recurso | ¿Se sobreasigna? | Comportamiento al agotarse |
|---|---|---|
| **CPU** | **Sí, con normalidad** | Las máquinas virtuales **esperan turno**: el servicio se degrada, pero nada falla. |
| **Memoria** | Sí, **con mucha cautela** | El hipervisor recurre a técnicas de recuperación cada vez más agresivas y, en el extremo, a **intercambio a disco**, que hunde el rendimiento. |
| **Disco (capacidad)** | Sí, vía aprovisionamiento fino | Si el almacén se llena, las máquinas virtuales se **detienen**: es el escenario más grave. |
| **Red** | Sí | Congestión, pérdida de paquetes y retransmisiones. |

Los hipervisores ofrecen además tres controles clásicos, presentes con distintos nombres en todos los productos, que conviene no confundir [VMWARE-DOC]:

- **Reserva** (*reservation*): mínimo **garantizado**. Si no puede concederse, la máquina virtual no arranca.
- **Límite** (*limit*): máximo que la máquina virtual podrá consumir **aunque haya recursos libres**.
- **Peso o participación** (*shares*): prioridad **relativa** para repartir el recurso **solo cuando hay contención**.

> **[EJERCICIO RESUELTO]** *Un anfitrión tiene 2 procesadores de 16 núcleos físicos cada uno (32 núcleos, 64 hilos con multihilo simultáneo). Se quieren alojar máquinas virtuales de 4 vCPU con una ratio de consolidación de 4 vCPU por núcleo físico. ¿Cuántas máquinas caben?* **Solución**: capacidad total = 32 núcleos × 4 = **128 vCPU asignables**; a 4 vCPU por máquina, **32 máquinas virtuales**. Advertencias: la ratio depende críticamente del **perfil de carga** (4:1 es razonable para servidores poco cargados y excesivo para bases de datos); el **multihilo simultáneo no duplica la capacidad real**, solo mejora el aprovechamiento; y debe reservarse margen para que el clúster siga funcionando **con un nodo caído** (regla N+1), lo que en la práctica reduce el número aceptable.

#### 2.2.1. Planificación de CPU y gestión de memoria virtualizada

**Planificación de CPU.** El hipervisor presenta a cada máquina virtual una o varias **CPU virtuales** (**vCPU**), que no son núcleos dedicados sino **entidades planificables** que compiten por tiempo de núcleo físico, exactamente igual que los procesos compiten por la CPU en un sistema operativo convencional [KVM-DOC] [VMWARE-DOC]. Conceptos clave:

- **Tiempo de espera** (*CPU ready* o `%RDY`): porcentaje de tiempo que una vCPU está **lista para ejecutarse pero esperando** un núcleo libre. Es **el indicador de contención de CPU por excelencia**: valores sostenidamente altos significan que el anfitrión está sobrecargado, aunque la utilización de CPU no parezca extrema.
- **Coplanificación** (*co-scheduling*): una máquina virtual con varias vCPU necesita que sus vCPU avancen **de forma razonablemente sincronizada**, porque el sistema huésped supone que sus procesadores progresan a la vez. Los hipervisores modernos aplican una **coplanificación relajada**, que no exige arrancarlas todas simultáneamente pero sí corregir las desviaciones (*skew*) entre ellas.
- **Consecuencia práctica y contraintuitiva**: **asignar más vCPU de las necesarias empeora el rendimiento**. Una máquina de 8 vCPU que solo usa 2 obliga al planificador a encontrar hueco para 8 entidades, aumentando su propio tiempo de espera y el de sus vecinas. La regla de oro es **empezar corto y crecer con datos de monitorización**.
- **NUMA** (*Non-Uniform Memory Access*): en servidores con varios procesadores, cada uno tiene memoria «cercana» (rápida) y memoria «lejana» (más lenta, atravesando la interconexión entre zócalos). Si una máquina virtual no cabe en un nodo NUMA, sus accesos se vuelven remotos y pierde rendimiento. Los hipervisores incorporan un **planificador consciente de NUMA** que intenta alojar cada máquina en un solo nodo y mover memoria y vCPU juntas.
- **Afinidad** (*CPU pinning*): fijar una vCPU a un núcleo físico concreto. Mejora la previsibilidad en cargas de latencia crítica, pero **impide al planificador equilibrar** y complica la migración; se reserva para casos justificados.

**Gestión de memoria.** La memoria es el recurso que **de verdad limita** la densidad de un anfitrión, y su gestión combina hardware y software:

- **Doble traducción.** El huésped mantiene sus tablas de páginas (dirección virtual → dirección física del huésped) y el hipervisor debe añadir la suya (física del huésped → física real). Por software se resolvía con **tablas de páginas sombra**; hoy lo resuelve la MMU con **EPT/NPT** (§1.2.3), a costa de recorridos de tabla más largos que se mitigan con cachés de traducción específicas [INTEL-SDM] [AMD-APM].
- **Técnicas de recuperación de memoria**, aplicadas de forma escalonada cuando el anfitrión empieza a quedarse sin memoria libre [VMWARE-DOC] [KVM-DOC]:
  1. **Compartición de páginas idénticas** (*transparent page sharing*, `KSM` en Linux): si varias máquinas virtuales tienen en memoria páginas de contenido idéntico —lo habitual cuando comparten sistema operativo—, se conserva **una sola copia física** marcada como copia en escritura. Muy eficaz en granjas homogéneas, como una granja VDI (§3).
  2. **Globo de memoria** (*ballooning*): un controlador instalado en el huésped **reclama memoria dentro del propio sistema operativo huésped**, obligándolo a liberar sus páginas menos usadas; esa memoria se devuelve al hipervisor. Es elegante porque **quien decide qué página sacrificar es el huésped**, que es quien mejor lo sabe.
  3. **Compresión**: se comprimen páginas poco usadas y se guardan en una caché en memoria, más lenta que la RAM pero mucho más rápida que el disco.
  4. **Intercambio del hipervisor a disco** (*swapping*): último recurso. El hipervisor escoge páginas **a ciegas**, sin saber cuáles importan al huésped, y el desplome de rendimiento es severo. **La aparición de intercambio del hipervisor es siempre señal de un problema de dimensionamiento.**
- **Página grande** (*large pages*, 2 MB frente a 4 KB): reduce el número de entradas de traducción y mejora el rendimiento, pero **inhibe la compartición de páginas idénticas**, porque es muy improbable que dos páginas de 2 MB sean idénticas.
- **Adición en caliente** (*hot-add*) de memoria y vCPU: algunos huéspedes admiten ampliar recursos sin apagar la máquina virtual, lo que reduce las ventanas de parada.

> **[DATO CLAVE]** El orden de las cuatro técnicas de recuperación de memoria, de menos a más dañina: **compartición de páginas → globo → compresión → intercambio a disco**. Y la asimetría fundamental del reparto de recursos: **sobreasignar CPU degrada; sobreasignar memoria rompe**.

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** En el clúster municipal, un servidor de consultas del padrón muestra un tiempo de espera de CPU del 18 % en horario de atención al público, con la utilización del anfitrión en torno al 55 %. El diagnóstico no es «falta CPU» sino **exceso de vCPU asignadas** en varias máquinas del mismo anfitrión: hay más entidades planificables que huecos, y todas esperan. La corrección es reducir las vCPU sobredimensionadas y redistribuir máquinas entre nodos, no comprar más servidores.

#### 2.2.2. Entradas y salidas y controladores paravirtualizados

La **E/S es el punto donde más se nota la virtualización**, porque cada acceso a disco o a red atraviesa la capa del hipervisor. Existen cuatro modelos, ordenados de menor a mayor rendimiento y de mayor a menor flexibilidad:

**1. Emulación completa de dispositivo.** El hipervisor presenta al huésped un dispositivo **real y conocido** (por ejemplo, una tarjeta de red clásica o un controlador IDE) y emula por software su comportamiento registro a registro. Ventaja: **funciona sin instalar nada** en el huésped, incluso durante la instalación del sistema operativo. Inconveniente: **es el modelo más lento**, porque cada operación provoca múltiples salidas al hipervisor.

**2. Dispositivo paravirtualizado.** El huésped instala un **controlador consciente de la virtualización** que no simula hardware alguno: se comunica con el hipervisor mediante **colas circulares en memoria compartida**, agrupando peticiones y notificando en bloque. El estándar abierto de referencia es **virtio** [VIRTIO], adoptado por KVM, QEMU y otros, con dispositivos como `virtio-net` (red), `virtio-blk` y `virtio-scsi` (bloque) y `virtio-fs` (ficheros). Los equivalentes propietarios son los controladores incluidos en **VMware Tools** (`vmxnet3`, `pvscsi`) y en los *Integration Services* de Hyper-V [VMWARE-DOC] [HYPERV-DOC]. Es el modelo **por defecto en producción**: rendimiento muy alto conservando la portabilidad y la migración en caliente.

**3. Asignación directa de dispositivo** (*passthrough* o DirectPath). El hipervisor **cede un dispositivo físico completo** a una única máquina virtual, que lo maneja con su controlador nativo. Rendimiento prácticamente nativo. Requiere **IOMMU** (VT-d / AMD-Vi) para aislar el DMA [INTEL-VTD] y tiene un coste alto de flexibilidad: el dispositivo queda **monopolizado** por esa máquina y, por regla general, **se pierde la migración en caliente**.

**4. SR-IOV.** Evolución del anterior que evita monopolizar el dispositivo: una tarjeta compatible se presenta como una **función física (PF)**, que administra el hipervisor, y un conjunto de **funciones virtuales (VF)**, cada una asignable directamente a una máquina virtual distinta [PCI-SRIOV]. Combina rendimiento casi nativo con reparto entre varias máquinas, manteniendo las mismas restricciones de movilidad y exigiendo soporte en tarjeta, placa y hipervisor.

| Modelo | Rendimiento | Requisitos | Migración en caliente | Uso típico |
|---|---|---|---|---|
| Emulación | Bajo | Ninguno | Sí | Instalación, huéspedes antiguos |
| **Paravirtualizado (virtio)** | **Alto** | Controlador en el huésped | **Sí** | **Producción, caso general** |
| *Passthrough* | Muy alto | **IOMMU** | Normalmente **no** | GPU, tarjetas especiales, criptografía |
| **SR-IOV** | Muy alto | IOMMU + tarjeta compatible | Normalmente **no** | Red de muy alto rendimiento, NFV |

> **[DATO CLAVE]** **virtio es paravirtualización**, aunque el huésped no esté paravirtualizado. Esta es la razón por la que la afirmación «la paravirtualización ya no se usa» es falsa: no se paravirtualiza el **núcleo**, pero sí los **dispositivos**, y eso está en casi todas las máquinas virtuales en producción [VIRTIO].

> **[RELACIÓN CON OTROS TEMAS]** Los **periféricos, sus interfaces y los elementos de almacenamiento** en su dimensión física se estudian en el **Tema 12**; los **sistemas de almacenamiento y su virtualización**, junto con las políticas de copia de seguridad, corresponden al **Tema 26**. Aquí interesa exclusivamente **cómo el hipervisor entrega esos recursos a la máquina virtual**.

### 2.3. Virtualización basada en contenedores y aislamiento de procesos

La **virtualización basada en contenedores**, también llamada **virtualización a nivel de sistema operativo**, aísla **conjuntos de procesos** dentro de un **único núcleo compartido**, en lugar de crear máquinas completas con núcleo propio. Cada contenedor recibe una vista privada del sistema —su propio árbol de procesos, su propia red, su propio sistema de ficheros— pero **todas las llamadas al sistema las atiende el mismo núcleo del anfitrión** [OCI-SPEC] [NIST-SP800-190].

En Linux, los contenedores no son una tecnología monolítica sino la **combinación de primitivas del núcleo** [LINUX-NS]:

- **Espacios de nombres** (*namespaces*): aíslan **qué ve** el proceso. Los principales son `pid` (árbol de procesos: el proceso principal del contenedor se ve a sí mismo como PID 1), `net` (interfaces, tablas de rutas y de filtrado propias), `mnt` (puntos de montaje), `uts` (nombre de máquina y dominio), `ipc` (comunicación entre procesos), `user` (correspondencia de identificadores de usuario y grupo, que permite ser `root` dentro sin serlo fuera) y `cgroup`.
- **Grupos de control** (*cgroups*): limitan y contabilizan **cuánto consume** el contenedor de CPU, memoria, E/S de bloque y número de procesos.
- **Restricción de privilegios**: **capacidades** (`capabilities`) para trocear los poderes de `root`, **`seccomp`** para restringir el conjunto de llamadas al sistema permitidas y **módulos de seguridad** como SELinux o AppArmor para el control de acceso obligatorio.
- **Sistemas de ficheros por capas** (`overlayfs`): la imagen se compone de **capas de solo lectura** apiladas más una **capa de escritura** propia del contenedor. Esto explica por qué las imágenes se comparten entre contenedores y por qué **un contenedor es efímero por diseño**: lo escrito en su capa superior desaparece al eliminarlo, salvo que se use un **volumen** persistente.

El ecosistema se ha estandarizado en torno a la **Open Container Initiative**, que define el formato de **imagen** y la especificación de **tiempo de ejecución** [OCI-SPEC]; sobre ella se apoyan `runc`, `containerd`, CRI-O, Docker [DOCKER-DOC] y los orquestadores. Cuando el número de contenedores crece, se necesita un **orquestador** que los planifique sobre un conjunto de nodos, los reinicie si fallan, los escale y los publique: **Kubernetes** es el estándar de facto, y su unidad mínima de despliegue no es el contenedor sino el **pod**, un grupo de contenedores que comparten red y almacenamiento [K8S-DOC].

**Riesgos propios de los contenedores** [NIST-SP800-190]:

- **Superficie compartida del núcleo**: una vulnerabilidad de escalada en el núcleo puede permitir **escapar** del contenedor y afectar al anfitrión y a sus vecinos. Es la diferencia de seguridad esencial frente a una máquina virtual.
- **Contenedores privilegiados**: ejecutar con `--privileged` o como `root` sin necesidad anula buena parte del aislamiento.
- **Cadena de suministro de las imágenes**: una imagen descargada de un registro público puede contener componentes vulnerables o maliciosos. Exige **registro propio, análisis de vulnerabilidades y firma de imágenes**.
- **Superficie del orquestador**: la interfaz de programación del plano de control es un objetivo de primer orden y debe autenticarse, autorizarse y auditarse.

Como respuesta a la primera de estas debilidades han aparecido los **contenedores aislados por hipervisor** (*sandboxed containers*, como Kata Containers o gVisor), que ejecutan cada contenedor dentro de una máquina virtual mínima: recuperan el aislamiento fuerte a cambio de algo de densidad y de latencia de arranque.

#### 2.3.1. Comparativa entre virtualización basada en hipervisor y contenedores

Es, junto con la clasificación de hipervisores, la comparación central del tema:

| Criterio | **Máquina virtual (hipervisor)** | **Contenedor** |
|---|---|---|
| Qué se virtualiza | **El hardware** | **El sistema operativo** |
| Núcleo | **Uno propio por máquina virtual** | **Compartido** con el anfitrión |
| Aislamiento | **Fuerte** (frontera de hardware, reforzada por la CPU) | Lógico (frontera de núcleo), más débil |
| Tamaño típico | GB (sistema operativo completo) | MB (solo la aplicación y sus dependencias) |
| Tiempo de arranque | Segundos a minutos | **Milisegundos a segundos** |
| Densidad por anfitrión | Decenas | **Cientos o miles** |
| Sistema operativo del huésped | **Cualquiera** compatible con la arquitectura | **Solo el del núcleo del anfitrión** (no se ejecuta Windows sobre un núcleo Linux) |
| Sobrecarga | Moderada | **Mínima** |
| Persistencia | Estado propio y persistente | **Efímero por diseño**; la persistencia va en volúmenes |
| Portabilidad | Alta (imagen pesada) | **Muy alta** (imagen ligera y estándar) |
| Caso de uso natural | Cargas heredadas, sistemas heterogéneos, servicios monolíticos, aislamiento exigente | Microservicios, despliegue continuo, escalado elástico |

> **[DATO CLAVE]** Dos afirmaciones clave: (1) **los contenedores no sustituyen a las máquinas virtuales, se apoyan en ellas**: en la práctica totalidad de las instalaciones reales, y en toda la nube pública, los nodos que ejecutan contenedores **son máquinas virtuales**; (2) **un contenedor no puede ejecutar un sistema operativo distinto del núcleo del anfitrión** — cuando se ven contenedores Linux en un equipo Windows, lo que hay por debajo es una **máquina virtual Linux ligera** que los aloja [NIST-SP800-190].

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** La sede electrónica municipal se moderniza dividiendo el portal en varios servicios pequeños empaquetados en contenedores, mientras que el gestor de expedientes heredado, con dependencias antiguas y certificado por su fabricante sobre un sistema operativo concreto, permanece como **máquina virtual**. Los nodos que ejecutan los contenedores son, a su vez, máquinas virtuales del mismo clúster: la Administración no elige entre las dos tecnologías, las **estratifica**.

### 2.4. Alta disponibilidad, balanceo y migración en caliente de sistemas

La virtualización de servidores alcanza su máximo valor cuando el clúster deja de ser un conjunto de anfitriones y se comporta como **un único gran ordenador con políticas automáticas**.

**Migración en caliente** (*live migration*, comercialmente *vMotion* en VMware y *Live Migration* en Hyper-V). Consiste en **trasladar una máquina virtual en ejecución de un anfitrión a otro sin apagarla y sin interrumpir sus conexiones** [VMWARE-DOC] [HYPERV-DOC]. El mecanismo habitual es la **precopia iterativa**:

1. **Preparación**: el destino reserva recursos y se establece el canal de transferencia por la red dedicada de migración.
2. **Copia iterativa de memoria**: se copian todas las páginas de memoria mientras la máquina **sigue funcionando** en el origen. Como el huésped sigue escribiendo, algunas páginas se «ensucian» y hay que reenviarlas; se repite el ciclo copiando cada vez solo las páginas modificadas, hasta que el conjunto pendiente es pequeño.
3. **Parada breve** (*stun*): se detiene la máquina unos **milisegundos**, se transfiere el último conjunto de páginas sucias y el estado de CPU y de dispositivos.
4. **Conmutación**: la máquina se reanuda en el destino; se emite un aviso en la red (típicamente un *RARP* o un ARP gratuito) para que los conmutadores aprendan la nueva ubicación de su dirección MAC, y se libera el origen.

Existe también la variante de **poscopia**, que conmuta primero y trae las páginas bajo demanda: reduce la parada total pero deja la máquina expuesta a un fallo de red durante la transferencia. La precopia es la usada por defecto.

**Requisitos de la migración en caliente**:

- **Compatibilidad de CPU** entre origen y destino (mismo fabricante y conjunto de características, o uso de un modo de compatibilidad que enmascare las diferencias).
- **Red compartida** y una **red dedicada** de suficiente ancho de banda para la migración.
- **Acceso al mismo almacenamiento** desde ambos anfitriones (o, en la variante «sin nada compartido», migración simultánea de disco y memoria, más lenta).
- **Ausencia de dispositivos asignados directamente** (*passthrough*/SR-IOV) que aten la máquina al hardware del origen.

Además de la migración de cómputo, existe la **migración de almacenamiento** (*Storage vMotion*), que mueve los ficheros de la máquina virtual **entre almacenes de datos** sin apagarla, y que es la herramienta habitual para vaciar una cabina antes de retirarla.

**Alta disponibilidad (HA).** Si un anfitrión falla, el clúster detecta la pérdida (por latidos de red y de almacenamiento) y **reinicia en otro anfitrión** las máquinas virtuales que alojaba [VMWARE-DOC]. Puntos críticos:

- **Hay corte de servicio**: la máquina se reinicia, no continúa. Se pierde lo que hubiera en memoria y el tiempo de indisponibilidad es el de arranque del sistema y de la aplicación.
- Es necesario **reservar capacidad** para que los supervivientes puedan absorber la carga del caído: la política de **N+1** consiste en dimensionar el clúster de modo que pueda perder un nodo sin degradar el servicio.
- El **aislamiento de red** de un nodo (un nodo vivo pero incomunicado) debe distinguirse de su caída real para evitar el escenario de «cerebro dividido» (*split brain*), en el que dos anfitriones arrancarían la misma máquina virtual sobre el mismo disco. De ahí los latidos redundantes por red **y** por almacenamiento y los mecanismos de exclusión del nodo dudoso.

**Tolerancia a fallos (FT).** Un grado más: se mantiene una **copia secundaria de la máquina virtual en otro anfitrión, sincronizada instrucción a instrucción**, que asume el servicio de forma **instantánea y sin pérdida de estado** si el primario cae. Su coste es alto (duplica el consumo de recursos, exige red de muy baja latencia y suele estar limitada en número de vCPU), por lo que se reserva a servicios críticos concretos.

**Balanceo automático de carga (DRS).** El clúster **monitoriza el desequilibrio** entre anfitriones y **migra máquinas en caliente** para repartir la carga, ya sea de forma automática o mediante recomendaciones al administrador [VMWARE-DOC]. A ello se añaden:

- **Reglas de afinidad**: obligan a que dos máquinas se ejecuten en el mismo anfitrión (por ejemplo, por latencia entre ellas).
- **Reglas de antiafinidad**: obligan a que **nunca coincidan** en el mismo anfitrión. Es la regla imprescindible para los **pares redundantes**: los dos nodos de un balanceador o los dos servidores de una base de datos replicada no deben caer juntos porque el clúster los haya colocado, por casualidad, en el mismo hierro.
- **Gestión de energía** (*DPM*): en horas valle, el clúster concentra las máquinas en menos anfitriones y **apaga los sobrantes**, encendiéndolos de nuevo cuando la carga sube (§5.4).
- **Modo mantenimiento**: antes de parchear un anfitrión, se le marca en mantenimiento y el clúster **vacía automáticamente** todas sus máquinas virtuales hacia otros nodos, en caliente. Esta es la razón por la que en un entorno virtualizado bien diseñado **el mantenimiento del hardware deja de requerir ventanas de parada del servicio**.

> **[DATO CLAVE]** Diferenciar con precisión: **migración en caliente** = movimiento **planificado** sin corte; **HA** = respuesta **no planificada** a un fallo, **con reinicio y por tanto con corte**; **FT** = respuesta no planificada **sin corte**, mediante copia en espejo, a un coste muy superior. Confundir HA con FT es el error más frecuente en este bloque.

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** El clúster municipal aloja en pareja los servidores del registro de entrada. Una **regla de antiafinidad** garantiza que los dos nodos del par nunca se ejecuten en el mismo anfitrión; una política de **HA** los reinicia automáticamente si cae un servidor físico; y el **modo mantenimiento** permite parchear los anfitriones un martes por la mañana, sin ventana nocturna y sin que la ciudadanía perciba nada, porque las máquinas se han migrado en caliente antes de reiniciar el equipo.

---

## 3. Virtualización de puestos de usuario y del entorno de trabajo

### 3.1. Modelos de virtualización en el cliente

La segunda mitad del tema traslada la idea de la virtualización desde el servidor hasta el **puesto de trabajo del empleado**. El objetivo cambia: ya no se trata de consolidar servidores, sino de **desacoplar el entorno de trabajo del dispositivo físico** desde el que se accede, de modo que el escritorio, las aplicaciones y los datos residan y se ejecuten en el centro de datos y el terminal se limite a mostrar la imagen y a recoger las pulsaciones.

Las **motivaciones** de una organización pública para dar ese paso son concretas:

- **Seguridad y confidencialidad**: los datos **no salen del centro de datos**. El robo o la pérdida de un portátil deja de ser una brecha de datos personales, porque el terminal no almacena nada.
- **Gestión centralizada**: parchear, actualizar y desplegar aplicaciones se hace **una vez sobre la imagen maestra**, no equipo por equipo.
- **Movilidad y continuidad**: el mismo escritorio es accesible desde cualquier oficina, desde el domicilio en teletrabajo o desde un centro alternativo tras un incidente.
- **Prolongación de la vida del parque**: el terminal apenas hace trabajo, así que un equipo antiguo o un cliente ligero pueden servir muchos años más (§5.4).
- **Homogeneidad y cumplimiento**: la configuración de seguridad es idéntica y auditable en todos los puestos.

Y sus **contrapartidas**, que hay que saber enunciar igual de bien:

- **Dependencia absoluta de la red**: sin conectividad no hay puesto de trabajo. La red pasa a ser un servicio crítico.
- **Concentración del riesgo**: una caída de la plataforma VDI deja sin trabajar a **todos** los usuarios a la vez, no a uno.
- **Inversión inicial elevada** en servidores, almacenamiento de alto rendimiento y licencias, frente a un gasto distribuido en equipos.
- **Cargas mal adaptadas**: diseño gráfico, cartografía, edición de vídeo o aplicaciones que exigen periféricos especiales requieren GPU virtual y redirección de dispositivos, y no siempre compensan.
- **Complejidad operativa**: exige perfiles técnicos especializados y un dimensionamiento cuidadoso, sobre todo en E/S.

Los modelos disponibles son cuatro, y el enunciado del tema pide desarrollar los tres primeros:

| Modelo | Dónde se ejecuta | Qué se entrega | Aislamiento |
|---|---|---|---|
| **VDI** | Servidor, **una VM por usuario** | Escritorio completo con sistema operativo de **cliente** | **Alto** (VM independiente) |
| **Sesiones / RDSH** | Servidor, **un sistema compartido** | Escritorio o aplicación publicada | Medio (sesión dentro de un sistema compartido) |
| **Virtualización de aplicaciones** | Servidor **o** puesto local | **Solo la aplicación**, empaquetada aislada | Aplicación aislada del sistema |
| **Virtualización en el cliente** | **En el propio equipo del usuario** | Máquina virtual local (hipervisor de Tipo 1 o 2 en el puesto) | Alto, pero sin centralización |

> **[DATO CLAVE]** No confundir **cliente ligero** con **VDI**: el **cliente ligero** (*thin client*) es el **dispositivo terminal** —hardware reducido, sin disco relevante, con un sistema mínimo cuya única función es ejecutar el cliente de conexión—; **VDI** es la **arquitectura del lado del servidor**. Se puede hacer VDI con clientes ligeros, con PC completos reutilizados (*repurposed*), con clientes cero (*zero client*, con el protocolo implementado en circuitería) o con un navegador (*HTML5*).

#### 3.1.1. Infraestructura de escritorios virtuales en servidor (VDI)

**VDI** (*Virtual Desktop Infrastructure*) es el modelo en el que **cada usuario dispone de su propia máquina virtual con un sistema operativo de cliente**, ejecutada en el centro de datos sobre un clúster de virtualización, y a la que accede en remoto mediante un protocolo de representación (§3.3) [HORIZON-DOC] [CITRIX-DOC].

Rasgos definitorios:

- **Una máquina virtual por usuario**, con su núcleo, su memoria y su disco virtual: el aislamiento entre usuarios es el de dos máquinas distintas.
- **Sistema operativo de cliente** (de escritorio), no de servidor: la compatibilidad de aplicaciones es la del puesto habitual, sin las diferencias de comportamiento de un sistema de servidor.
- **Personalización profunda posible**: si el escritorio es persistente, el usuario puede instalar y configurar como en un equipo propio (§3.4).
- **Densidad menor y coste por usuario mayor** que en el modelo de sesiones, porque cada usuario paga el coste de un sistema operativo completo en memoria y en disco.

El **dimensionamiento** de una plataforma VDI se hace por **perfiles de usuario**, y el error clásico es dimensionar por CPU cuando el recurso que de verdad limita es **la memoria y, sobre todo, la E/S de almacenamiento**:

| Perfil | Carga típica | Recursos orientativos por escritorio |
|---|---|---|
| **Ligero** | Navegador, correo, ofimática básica, aplicación de gestión | 2 vCPU / 4 GB |
| **Medio** | Ofimática intensa, varias aplicaciones de gestión, muchas pestañas, vídeo ocasional | 2-4 vCPU / 8 GB |
| **Avanzado** | Cartografía, diseño, análisis de datos, vídeo | 4-8 vCPU / 16 GB **y GPU virtual** |

> **[DATO CLAVE]** El fenómeno que hunde una plataforma VDI mal dimensionada tiene nombre propio: la **tormenta de arranque** (*boot storm*), y su variante la **tormenta de inicio de sesión** (*login storm*). A las 8:00 todos los empleados encienden a la vez, y cientos de escritorios arrancan, cargan perfiles y actualizan el antivirus **simultáneamente**, provocando un pico de E/S de lectura y escritura que el almacenamiento debe absorber. Mitigaciones: **almacenamiento de estado sólido**, encendido escalonado y preencendido programado de escritorios, cachés de lectura de la imagen maestra, análisis antivirus desfasado en el tiempo y con exclusiones adecuadas, y clones instantáneos que comparten la memoria y el disco de una máquina plantilla ya arrancada.

#### 3.1.2. Escritorios basados en sesiones y terminal server

El modelo de **escritorios basados en sesiones**, históricamente conocido como **servicios de terminal** (*Terminal Services*, hoy **RDSH**, *Remote Desktop Session Host*), es **anterior a VDI** y sigue siendo el más eficiente en coste [AVD-DOC].

Su principio es distinto: **un único sistema operativo de servidor atiende simultáneamente a muchos usuarios**, cada uno en su **sesión**. Todos comparten el mismo núcleo, los mismos servicios y, en gran medida, las mismas instancias de bibliotecas en memoria; lo que cambia por usuario es su sesión, su perfil y sus procesos.

Se puede entregar de dos formas:

- **Escritorio publicado**: el usuario recibe un escritorio completo del servidor compartido.
- **Aplicación publicada** (*seamless* o **RemoteApp**): el usuario ve **solo la ventana de una aplicación** concreta, integrada visualmente en su escritorio local, aunque se ejecute en el servidor. Es una forma muy elegante de dar acceso a una aplicación de gestión concreta sin virtualizar el puesto entero.

| Criterio | **VDI** | **Sesiones (RDSH)** |
|---|---|---|
| Sistemas operativos en ejecución | **Uno por usuario** (cliente) | **Uno compartido** (servidor) |
| Densidad por servidor | Baja (decenas) | **Alta** (muchas más sesiones) |
| Coste por puesto | Mayor | **Menor** |
| Aislamiento entre usuarios | **Alto** | Medio: un proceso descontrolado o un cuelgue afecta **a todos** los de ese servidor |
| Personalización | **Alta** (si es persistente) | Limitada: no se instala software por usuario |
| Compatibilidad de aplicaciones | La del sistema de **cliente** | La del sistema de **servidor**: algunas aplicaciones no están certificadas |
| Caso de uso natural | Usuarios con necesidades específicas, cargas pesadas, perfiles con requisitos de aislamiento | **Colectivos numerosos y homogéneos** con un conjunto fijo de aplicaciones |

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** El colectivo de las oficinas de atención a la ciudadanía usa siempre las mismas cuatro aplicaciones (padrón, registro, gestor de expedientes y ofimática) y no instala software: es el caso de libro del modelo de **sesiones**, mucho más barato y denso. En cambio, la unidad de cartografía y planeamiento, que trabaja con sistemas de información geográfica y necesita aceleración gráfica y personalización, encaja en **VDI con GPU virtual** [NVIDIA-VGPU]. Una plataforma municipal realista **combina ambos modelos** y asigna cada colectivo al que le corresponde.

#### 3.1.3. Virtualización de aplicaciones

La **virtualización de aplicaciones** desacopla la aplicación del sistema operativo sobre el que se ejecuta: la aplicación se **empaqueta** con todo lo que necesita (ficheros, bibliotecas, claves de registro, configuración) en una **unidad aislada**, y en tiempo de ejecución un motor intercepta sus accesos al sistema de ficheros y a la configuración y los redirige hacia ese paquete, sin instalar nada de forma permanente en el sistema anfitrión [APPV].

Consecuencias prácticas:

- **Se elimina el conflicto entre aplicaciones**: dos versiones incompatibles de la misma aplicación —el caso típico de una aplicación de gestión heredada que exige una versión antigua de un componente— pueden convivir en el mismo puesto, cada una en su burbuja.
- **Despliegue y retirada limpios**: no hay instalación tradicional, así que no queda residuo al retirarla.
- **Entrega bajo demanda**: la aplicación puede transmitirse por la red al puesto cuando el usuario la invoca (*streaming*), en lugar de precargarla en todas las imágenes.
- **Actualización centralizada**: se actualiza el paquete y todos los usuarios reciben la nueva versión en su siguiente ejecución.

Tecnologías representativas: **Microsoft App-V** y su sucesor **MSIX app attach**, **VMware ThinApp**, y los sistemas de **capas de aplicación** (*app layering*) que montan discos virtuales con aplicaciones sobre un escritorio no persistente, como *App Volumes* [APPV] [HORIZON-DOC].

**Límites**: no toda aplicación es virtualizable —las que instalan controladores en modo núcleo, servicios de sistema profundos o componentes de bajo nivel suelen quedar fuera—, y el **empaquetado inicial tiene un coste** de trabajo técnico y de pruebas que hay que contabilizar.

> **[DATO CLAVE]** Ordenar los tres modelos por **lo que se virtualiza**: en **VDI** se virtualiza **la máquina** (una VM completa por usuario); en **sesiones** se virtualiza **el sistema operativo** (una sesión por usuario dentro de un sistema compartido); en **virtualización de aplicaciones** se virtualiza **la aplicación** (una burbuja aislada dentro del sistema del usuario). No son excluyentes: lo normal es usar virtualización de aplicaciones **dentro** de un escritorio VDI o de sesiones.

### 3.2. Componentes de la arquitectura VDI

Una plataforma de escritorio virtual se compone de piezas con funciones muy delimitadas. Saber **qué hace cada una** es lo que permite resolver los casos prácticos de este bloque.

- **Cliente de acceso**: el software (o el circuito, en un cliente cero) que se ejecuta en el terminal del usuario. Puede ser una aplicación instalada o un cliente **HTML5** en el navegador, muy útil para acceso ocasional o desde equipos no gestionados.
- **Pasarela de acceso seguro** (*gateway*): publica el servicio hacia el exterior sin exponer los escritorios. Termina el cifrado, aplica autenticación multifactor y actúa como intermediario entre la red no confiable e interna. Es el componente que hace viable el **teletrabajo** sin abrir la red interna.
- **Portal o catálogo** (*StoreFront* o equivalente): presenta al usuario el conjunto de **escritorios y aplicaciones a los que tiene derecho**.
- **Agente de conexión o broker**: el cerebro de la plataforma (§3.2.1).
- **Directorio corporativo**: la fuente de identidad, autenticación y pertenencia a grupos sobre la que se calculan las autorizaciones.
- **Agente instalado en el escritorio**: componente dentro de cada máquina virtual que registra el escritorio en el broker, informa de su estado y **atiende el protocolo de representación**.
- **Gestor de imágenes y aprovisionamiento** (§3.2.2).
- **Gestor de perfiles de usuario**: separa la identidad y los datos del usuario del escritorio en el que trabaja (§3.4).
- **Infraestructura de virtualización subyacente**: el clúster de hipervisores, el almacenamiento y la red descritos en las secciones 2 y 4. **VDI no es una alternativa a la virtualización de servidores: se construye encima de ella.**
- **Servicios de licencia, monitorización y registro de actividad**.

#### 3.2.1. Agente de conexión o broker de accesos

El **broker de conexiones** (*connection broker*, *Delivery Controller* en Citrix, *Connection Server* en Horizon, servicio de agente de conexión a Escritorio remoto en Microsoft) es el componente central de VDI. Su cometido, en orden, es [CITRIX-DOC] [HORIZON-DOC] [AVD-DOC]:

1. **Autenticar** al usuario contra el directorio corporativo (con segundo factor si procede).
2. **Determinar sus autorizaciones**: qué escritorios y aplicaciones tiene asignados según su grupo o su puesto.
3. **Seleccionar el recurso**: si el escritorio es **dedicado**, localizar el suyo; si es **no dedicado**, tomar uno libre del conjunto disponible.
4. **Encender o preparar** el escritorio si estaba apagado, o clonarlo del catálogo si el conjunto necesita reponerse.
5. **Redirigir al cliente** hacia el escritorio asignado, entregándole los datos de conexión para que el **protocolo de representación se establezca directamente** entre el cliente y el escritorio (§3.3).
6. **Gestionar la reconexión**: si el usuario pierde la conexión o cambia de terminal, el broker debe devolverle **su sesión existente**, no una nueva. Esta capacidad, la **itinerancia de sesión** (*session roaming*), es la que permite a un empleado empezar en una oficina y continuar en otra sin perder lo que estaba haciendo.
7. **Gestionar el ciclo de vida y el equilibrio de carga** del conjunto: mantener escritorios preencendidos para atender picos, apagar los sobrantes y aplicar tiempos de espera de desconexión y de cierre de sesión.

> **[DATO CLAVE]** Dos matices sobre el broker: (1) **el broker interviene en el establecimiento, no en el tráfico**: una vez asignado el escritorio, el flujo del protocolo va **directamente** entre el cliente y el escritorio (o a través de la pasarela), de modo que el broker no es un cuello de botella de ancho de banda; (2) **es un punto único de fallo**: si el broker no está disponible, **nadie puede iniciar sesiones nuevas** (aunque las existentes sigan vivas), por lo que se despliega siempre **redundado y balanceado**, con su base de datos en alta disponibilidad.

#### 3.2.2. Gestor de imágenes, plantillas y aprovisionamiento

La ventaja operativa decisiva de VDI —parchear una vez en lugar de mil— depende por completo de este componente. Su unidad de trabajo es la **imagen maestra** o *golden image*: una máquina virtual plantilla, actualizada, bastionada, con las aplicaciones comunes y **optimizada para VDI** (servicios innecesarios desactivados, tareas programadas de mantenimiento e indexación reducidas, hibernación y desfragmentación deshabilitadas, antivirus configurado con exclusiones).

A partir de ella se aprovisionan los escritorios con distintas técnicas [HORIZON-DOC] [CITRIX-DOC]:

| Técnica | Cómo funciona | Consumo de disco | Uso |
|---|---|---|---|
| **Clon completo** (*full clone*) | Copia independiente y completa de la plantilla | **Alto** (cada escritorio ocupa todo) | Escritorios dedicados persistentes |
| **Clon enlazado** (*linked clone*) | Comparte con la plantilla un disco base de solo lectura y escribe **solo las diferencias** en un disco delta | **Bajo** | Escritorios no persistentes |
| **Clon instantáneo** (*instant clone*) | Se deriva de una máquina plantilla **ya arrancada en memoria**: el escritorio nuevo está disponible en segundos y comparte también páginas de memoria | **Muy bajo** | Conjuntos no persistentes grandes, mitiga la tormenta de arranque |
| **Aprovisionamiento por arranque en red** (*streaming*) | El escritorio **no tiene disco propio**: arranca por red desde una imagen central compartida y usa un disco de escritura temporal | Muy bajo | Grandes conjuntos homogéneos |

El **ciclo de vida de la imagen** es el siguiente: se **clona** la imagen maestra a una copia de trabajo; se **aplican** parches, actualizaciones y cambios de aplicaciones; se **prueba** con un grupo piloto; se **sella** creando una nueva versión; se **publica** al conjunto de escritorios; y los escritorios no persistentes **adoptan la nueva versión en su siguiente reinicio**. Si la versión resulta defectuosa, se **revierte** a la versión anterior con la misma operación. Este mecanismo, y no otro, es el que convierte la gestión de mil puestos en la gestión de una imagen versionada.

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** Ante la publicación de una actualización de seguridad crítica del sistema operativo del puesto, el equipo de sistemas aplica el parche **una sola vez** sobre la imagen maestra, la valida con veinte usuarios piloto de un distrito durante una jornada y la publica esa noche. A la mañana siguiente, los escritorios no persistentes de todas las oficinas arrancan ya parcheados, sin desplazamientos, sin despliegue por equipos y sin puestos rezagados. La trazabilidad de la operación —quién publicó qué versión y cuándo— queda registrada, lo que da soporte directo a las medidas de gestión de cambios y de mantenimiento del **ENS** (§5.1).

### 3.3. Protocolos de representación y transporte para el puesto de trabajo

El **protocolo de representación remota** (*remote display protocol*) es la pieza que determina la calidad percibida del puesto virtual. Su función es transportar en un sentido la **imagen de la pantalla, el audio y los eventos de periféricos redirigidos**, y en el otro las **pulsaciones de teclado, los movimientos de ratón y los datos de los dispositivos locales**. Lo esencial es entender que **no se transmite la aplicación ni los datos, sino su representación**: por eso la información permanece en el centro de datos [MS-RDPBCGR].

Principales protocolos:

| Protocolo | Origen | Notas |
|---|---|---|
| **RDP** | Microsoft | El más extendido; especificación abierta publicada [MS-RDPBCGR]. Puerto habitual **TCP 3389** (con transporte adicional por UDP para mejorar el vídeo). Múltiples **canales virtuales** para impresoras, portapapeles, unidades y dispositivos USB. |
| **ICA / HDX** | Citrix | Protocolo histórico de Citrix, muy optimizado para redes de alta latencia y bajo ancho de banda; HDX es el conjunto de tecnologías de experiencia de usuario construido sobre ICA [CITRIX-DOC]. |
| **PCoIP** | Teradici / HP | Diseñado sobre **UDP** con compresión progresiva de imagen: la pantalla se refina hasta la calidad plena, lo que lo hace resistente a la pérdida de paquetes [PCOIP]. |
| **Blast Extreme** | VMware / Omnissa | Usa códecs de vídeo (H.264/HEVC) con posible aceleración por GPU; funciona sobre TCP y UDP y atraviesa bien cortafuegos y redes públicas [HORIZON-DOC]. |
| **SPICE** | Red Hat (abierto) | Protocolo abierto del ecosistema KVM/oVirt, con buen soporte multimedia y de redirección USB [SPICE]. |
| **RFB / VNC** | Abierto [RFC6143] | Muy simple y universal, basado en actualizar rectángulos de la memoria de imagen. Poco eficiente para escritorios ricos; útil para consola y administración. |

**Técnicas comunes de optimización**, que son lo verdaderamente examinable porque son independientes del producto:

- **Compresión adaptativa** y **códecs de vídeo** para las regiones que cambian rápido, con codificación de texto sin pérdidas para mantener la nitidez de la letra.
- **Actualización solo de las regiones modificadas** de la pantalla, en lugar del fotograma completo.
- **Descarga al cliente** (*client-side rendering* o *redirection*): en lugar de codificar un vídeo en el servidor y enviarlo como imagen, se **envía el flujo original** para que lo decodifique el terminal; lo mismo se aplica a las llamadas de las herramientas de comunicación unificada, que se redirigen para que el flujo de audio y vídeo vaya **directo entre los interlocutores** y no dé un rodeo por el centro de datos.
- **Elección de transporte**: **UDP** tolera mejor la pérdida y la latencia para la imagen en movimiento; **TCP** garantiza la entrega y atraviesa mejor las redes restrictivas. Los protocolos modernos usan ambos y conmutan según las condiciones.
- **Redirección de periféricos** por canales virtuales: impresoras, unidades locales, lectores de tarjeta inteligente (imprescindibles para la **firma electrónica** con certificado en tarjeta), escáneres y dispositivos USB. Cada redirección habilitada es también **una vía potencial de fuga de información**, y por eso se controla por política (§5.2).
- **Adaptación a la latencia**: la calidad percibida depende más de la **latencia de ida y vuelta** y de la **estabilidad** (fluctuación o *jitter*) que del ancho de banda bruto. Un enlace de mucho caudal pero con latencia alta da una experiencia peor que uno modesto y estable.

> **[RELACIÓN CON OTROS TEMAS]** No debe confundirse el **protocolo de representación de un puesto virtual**, que es lo que aquí se trata, con el **control remoto del puesto de usuario para dar soporte y resolver incidencias**, que es materia del **Tema 29**: aunque ambos transporten la imagen de una pantalla, el primero es el medio ordinario de trabajo del empleado y el segundo es una intervención excepcional de un técnico sobre la sesión de otra persona, con las garantías que ello exige (§5.2).

> **[DATO CLAVE]** El consumo de ancho de banda por sesión depende sobre todo del **contenido en movimiento**: una sesión de ofimática y aplicación de gestión consume poco y de forma discontinua; una sesión con vídeo a pantalla completa o cartografía consume un orden de magnitud más. Por eso el dimensionamiento de una plataforma VDI se hace **por perfil de usuario**, y por eso **el enlace de la oficina remota es tan crítico como el servidor**.

### 3.4. Estrategias de persistencia: escritorios dedicados y no dedicados

La decisión de persistencia es **la más determinante del diseño de una plataforma VDI**, porque condiciona el almacenamiento, la gestión de imágenes, la política de perfiles y el coste operativo.

**Escritorio dedicado o persistente.** Se asigna **de forma permanente a un usuario concreto**, que lo encuentra igual cada mañana. Los cambios que hace —configuración, ficheros, incluso software instalado si se le permite— **sobreviven al reinicio**, exactamente como en un equipo físico.

- **Ventajas**: máxima personalización, compatibilidad con aplicaciones que exigen instalación local o licenciamiento por equipo, transición natural desde el PC físico y menor resistencia al cambio por parte del usuario.
- **Inconvenientes**: **cada escritorio hay que gestionarlo y parchearlo individualmente** (se pierde la gran ventaja de VDI), consume mucho más almacenamiento (clones completos) y **acumula desviación de configuración** y riesgo, porque cada puesto termina siendo distinto de los demás.

**Escritorio no dedicado, no persistente o flotante.** Se toma de un **conjunto compartido** al iniciar sesión y **se descarta y se recrea desde la imagen maestra al cerrarla**.

- **Ventajas**: **una sola imagen que gestionar y parchear**, ahorro de almacenamiento muy grande, **escritorio siempre limpio** —lo que aporta una resistencia notable frente al software malicioso, que no sobrevive al reinicio— y despliegue reproducible.
- **Inconvenientes**: **exige resolver la persistencia del usuario por otra vía**; algunas aplicaciones no toleran bien la recreación; y obliga a una disciplina estricta: **todo lo que no esté en la imagen o en el perfil, se pierde**.

**Gestión del perfil y de los datos del usuario**, que es la condición indispensable del modelo no persistente:

- **Redirección de carpetas**: las carpetas personales (documentos, escritorio, favoritos) apuntan a un recurso de red centralizado, no al disco del escritorio.
- **Contenedor de perfil** en disco virtual (**FSLogix** y equivalentes): el perfil completo del usuario, incluido el caché del cliente de correo y el estado de las aplicaciones, se guarda en un disco virtual que **se monta al iniciar sesión y se desmonta al cerrarla**. Es la técnica que hoy hace viable el escritorio no persistente sin degradar la experiencia [FSLOGIX].
- **Capas de aplicación** (*app layering*): las aplicaciones específicas de un usuario o de un colectivo se entregan como discos virtuales que se montan sobre la imagen común, evitando multiplicar imágenes maestras.
- **Directivas de grupo y de configuración**: la configuración del entorno se aplica **por política** en cada inicio de sesión, no se guarda en el escritorio.

| Criterio | **Dedicado / persistente** | **No dedicado / no persistente** |
|---|---|---|
| Asignación | Fija por usuario | Del conjunto, en cada inicio de sesión |
| Cambios del usuario | **Se conservan** | **Se descartan** |
| Gestión de parches | Escritorio a escritorio | **Una sola imagen maestra** |
| Almacenamiento | Alto (clones completos) | **Bajo** (clones enlazados o instantáneos) |
| Perfil de usuario | En el propio escritorio | **Externo obligatorio** (contenedor de perfil + redirección) |
| Seguridad | Acumula estado y riesgo | **Se limpia en cada cierre de sesión** |
| Coste operativo | Alto | **Bajo** |
| Encaje típico | Perfiles técnicos, usuarios con software propio | **Colectivos numerosos y homogéneos** |

> **[EJERCICIO RESUELTO]** *Una organización despliega 500 escritorios no persistentes. Los usuarios se quejan de que pierden las firmas del correo, los favoritos del navegador y los ficheros que dejan en el escritorio. ¿Dónde está el error de diseño y cómo se corrige?* **Solución**: el error **no está en la elección del modelo**, que es correcta para 500 puestos homogéneos, sino en haberlo desplegado **sin la gestión de perfil que el modelo exige**. Corrección: implantar un **contenedor de perfil de usuario** en disco virtual que se monte al iniciar sesión, **redirigir las carpetas personales** a un recurso de red centralizado y respaldado, y aplicar la configuración del entorno **por directiva** en cada inicio. Con esas tres piezas el escritorio sigue siendo desechable y el usuario conserva su entorno.

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** Para las oficinas de atención a la ciudadanía, el Ayuntamiento elige escritorios **no persistentes** con contenedor de perfil: el puesto se recrea limpio cada noche, se parchea desde una única imagen y ningún dato queda en el terminal, lo que refuerza la confidencialidad de los datos del padrón que se manejan en el mostrador. Para el personal técnico de sistemas y desarrollo, que necesita instalar herramientas propias, se reserva un conjunto reducido de escritorios **dedicados** con normas de uso específicas.

---

## 4. Arquitectura de soporte: almacenamiento, redes y gestión

### 4.1. Virtualización del almacenamiento e infraestructuras hiperconvergentes

La **virtualización del almacenamiento** consiste en interponer una capa lógica entre los servidores y los dispositivos físicos de almacenamiento, de modo que aquellos consuman **volúmenes lógicos** cuya composición, ubicación y tecnología subyacente desconocen [SNIA-SSM]. Igual que el hipervisor abstrae la CPU y la memoria, esta capa abstrae los discos: permite **agrupar** dispositivos heterogéneos en un mismo conjunto, **mover datos en caliente** entre ellos y aplicar servicios comunes con independencia del fabricante.

> **[RELACIÓN CON OTROS TEMAS]** Los **sistemas de almacenamiento y su virtualización** son objeto propio del **Tema 26**, junto con las políticas y procedimientos de **copia de seguridad y recuperación**. Este epígrafe se limita a lo que la virtualización de sistemas y de puestos **necesita** del almacenamiento y a cómo lo consume; el detalle de tecnologías, cabinas y procedimientos de respaldo corresponde a aquel tema. Las **redes de área local** y los dispositivos de interconexión se estudian en los **Temas 30 y 37**.

**Modelos de acceso** que un entorno virtualizado utiliza:

| Modelo | Qué entrega | Protocolos habituales | Uso en virtualización |
|---|---|---|---|
| **Bloque** | Un volumen en bruto (LUN) que el anfitrión formatea | Fibre Channel, **iSCSI** [RFC7143], FCoE, **NVMe-oF** | Almacén de datos de máquinas virtuales de alto rendimiento |
| **Fichero** | Un recurso compartido ya formateado | **NFS**, **SMB** | Almacén de máquinas virtuales, perfiles de usuario, carpetas redirigidas |
| **Objeto** | Objetos con metadatos por interfaz web | S3 y compatibles | Repositorio de copias de seguridad, archivo, contenido estático |

**Servicios que aporta la capa de virtualización del almacenamiento** y que el entorno virtualizado explota de forma intensiva:

- **Aprovisionamiento fino**, ya descrito en §2.1.
- **Instantáneas y clones** a nivel de volumen, base del aprovisionamiento rápido de escritorios y servidores.
- **Deduplicación y compresión**: decisivas en VDI, donde cientos de escritorios derivan de la misma imagen y el contenido repetido es enorme.
- **Jerarquización automática** (*tiering*) y **caché**: los bloques calientes se sitúan en soporte rápido y los fríos migran a soporte lento y barato.
- **Replicación** síncrona o asíncrona hacia un segundo emplazamiento, base de la recuperación ante desastres (§5.3).
- **Calidad de servicio**: límites y garantías de operaciones de entrada/salida por segundo para que una máquina virtual ruidosa no ahogue a las demás.

**Almacenamiento definido por software (SDS) e hiperconvergencia.** El modelo tradicional separa los servidores de cómputo de una **cabina externa** especializada, unida por una red de almacenamiento. La **infraestructura hiperconvergente (HCI)** invierte ese planteamiento: los **discos van dentro de los mismos nodos que ejecutan el hipervisor**, y una capa de software distribuido los agrega y **replica los datos entre nodos** presentando un almacén único al clúster [VMWARE-DOC] [HYPERV-DOC].

| Criterio | **Convergente clásico (cabina + servidores)** | **Hiperconvergente (HCI)** |
|---|---|---|
| Dónde están los discos | En una **cabina externa** dedicada | **En los propios nodos de cómputo** |
| Red necesaria | Red de almacenamiento dedicada (FC o iSCSI) | Red Ethernet de alta velocidad entre nodos |
| Crecimiento | Se amplía cómputo y cabina **por separado** | **Añadiendo nodos** (cómputo y disco crecen juntos) |
| Protección del dato | RAID y funciones de la cabina | **Réplicas o codificación de borrado distribuidas** entre nodos |
| Gestión | Dos ámbitos y a menudo dos equipos | **Unificada** desde la consola de virtualización |
| Puntos fuertes | Rendimiento y funciones muy maduras; escalado independiente | **Simplicidad**, despliegue rápido, escalado horizontal predecible |
| Puntos débiles | Complejidad, coste de la red de almacenamiento, riesgo de concentración en la cabina | Cómputo y almacenamiento **acoplados**; dependencia fuerte de la red entre nodos |

> **[DATO CLAVE]** La **hiperconvergencia** es el modelo que mejor encaja con **VDI**, y la razón es esta: los escritorios virtuales generan un patrón de E/S muy exigente y muy repetitivo, y HCI lo atiende con **almacenamiento local rápido en el propio nodo** —evitando el rodeo por la red de almacenamiento— y con **deduplicación** de un contenido que es casi idéntico entre escritorios. Además, su crecimiento **por nodos** encaja con un despliegue VDI que se amplía por bloques de usuarios.

### 4.2. Virtualización de redes y redes definidas por software (SDN)

Virtualizar servidores sin virtualizar la red deja el problema a medias: de nada sirve poder crear una máquina virtual en minutos si conectarla exige solicitar una VLAN y esperar a que alguien configure a mano un conmutador físico.

**Primer nivel: el conmutador virtual.** Cada anfitrión ejecuta un **conmutador virtual** (*vSwitch*) por software que conecta las tarjetas de red virtuales de sus máquinas entre sí y con las tarjetas físicas del anfitrión. Sus funciones son las de un conmutador de nivel 2: reenvío por dirección MAC, **etiquetado VLAN 802.1Q** mediante grupos de puertos, **agregación y balanceo de enlaces** (*NIC teaming*) para tolerancia a fallos, y políticas de seguridad del puerto (rechazo de modo promiscuo, de cambios de dirección MAC y de transmisiones falsificadas), que el NIST recomienda expresamente activar [NIST-SP800-125]. El **conmutador distribuido** eleva ese concepto al clúster: una única configuración lógica que abarca todos los anfitriones, de modo que una máquina migrada conserva su configuración de red y sus contadores.

**Segundo nivel: la superposición** (*overlay*). La VLAN clásica tiene dos límites serios en un centro de datos virtualizado: solo admite **4.094 identificadores** y obliga a **extender dominios de capa 2** por la red física para que las máquinas puedan migrar entre anfitriones distintos. La solución es **encapsular** el tráfico de nivel 2 dentro de paquetes de nivel 3, creando redes lógicas independientes de la topología física [RFC7364]:

- **VXLAN** [RFC7348]: encapsula la trama en **UDP** y añade un identificador **VNI de 24 bits**, lo que permite unos **16 millones** de segmentos frente a los 4.094 de VLAN. Los puntos de encapsulado y desencapsulado se denominan **VTEP** y residen habitualmente en el conmutador virtual del anfitrión.
- **Geneve** [RFC8926]: encapsulado más moderno y **extensible mediante opciones TLV**, pensado para transportar metadatos entre elementos de red virtualizados.
- **NVGRE**: alternativa basada en GRE, hoy menos extendida.

Consecuencia práctica: la red lógica **viaja con la máquina virtual**. Se puede migrar una máquina a otro anfitrión, a otra sala o a otro centro de proceso de datos conservando su dirección IP y su segmento, porque el segmento es lógico y la red física solo tiene que saber encaminar paquetes IP entre los anfitriones.

**Tercer nivel: SDN.** Las **redes definidas por software** separan el **plano de control** —la inteligencia que decide cómo se reenvía el tráfico— del **plano de datos** —los elementos que efectivamente lo reenvían—, y concentran el primero en un **controlador centralizado y programable** [ONF-SDN]. Su arquitectura tiene tres capas y dos interfaces:

- **Capa de aplicación**: las aplicaciones y las políticas de negocio o de seguridad.
- **Interfaz norte** (*northbound*): la interfaz de programación por la que las aplicaciones expresan **qué** quieren de la red.
- **Capa de control**: el **controlador**, que mantiene una **visión global** de la topología y traduce esas intenciones en reglas concretas.
- **Interfaz sur** (*southbound*): el protocolo con el que el controlador programa los dispositivos (**OpenFlow** es el histórico y el más citado).
- **Capa de infraestructura**: conmutadores físicos y virtuales que solo reenvían según las reglas recibidas.

Estrechamente emparentada está la **virtualización de funciones de red (NFV)**, que sustituye equipamiento dedicado —cortafuegos, balanceadores, encaminadores, optimizadores— por **funciones software ejecutadas en máquinas virtuales o contenedores** sobre hardware genérico, gestionadas por un marco de orquestación normalizado por ETSI (**NFV-MANO**) [ETSI-NFV]. Y de la combinación de superposición y control centralizado nace la capacidad que más ha cambiado la seguridad del centro de datos: la **microsegmentación**.

**Microsegmentación.** Consiste en aplicar un **cortafuegos distribuido en el propio conmutador virtual, con reglas por máquina virtual**, en lugar de confiar la seguridad únicamente a un cortafuegos perimetral. Su valor está en el tráfico **este-oeste** —el que circula entre servidores dentro del propio centro de datos, que nunca pasa por el perímetro y que es precisamente el que un atacante utiliza para **moverse lateralmente** tras comprometer un primer sistema—. Con microsegmentación, el servidor web puede hablar con el de aplicación **solo por el puerto necesario**, y no puede hablar con los demás servidores web ni con el sistema de nóminas, aunque estén en el mismo segmento [NIST-SP800-125].

> **[DATO CLAVE]** Tres números y una idea: **VLAN → 4.094** segmentos utilizables (12 bits de identificador); **VXLAN → ~16 millones** (24 bits de VNI) sobre UDP [RFC7348]; **SDN → separación de plano de control y plano de datos** con controlador centralizado e interfaces **norte** (hacia las aplicaciones) y **sur** (hacia los dispositivos) [ONF-SDN]. Y la idea: la microsegmentación protege el tráfico **este-oeste**, que es el que el cortafuegos perimetral **no ve**.

> **[RELACIÓN CON OTROS TEMAS]** Los fundamentos de **comunicaciones y medios de transmisión** se estudian en el **Tema 33**; el modelo **TCP/IP y OSI** en el **Tema 34**; las **redes locales, su tipología y sus dispositivos de interconexión** en el **Tema 37**; la **administración de redes de área local** en el **Tema 30**; y la **seguridad y protección en redes, la seguridad perimetral y las VPN** en el **Tema 36**. Este epígrafe solo cubre la **capa de red virtualizada** que vive dentro del entorno de virtualización.

### 4.3. Gestión centralizada, monitorización y orquestación de recursos

Un entorno virtualizado sin gestión centralizada es ingobernable: la facilidad de crear recursos se convierte, sin control, en su principal problema.

**Gestión centralizada.** La consola de gestión (vCenter, SCVMM, oVirt, OpenStack, Proxmox) concentra [VMWARE-DOC] [OPENSTACK] [OVIRT]:

- **Inventario** completo de anfitriones, máquinas virtuales, almacenes de datos y redes.
- **Ciclo de vida** de las máquinas: creación desde plantilla, modificación, clonación, migración, apagado y retirada.
- **Políticas de clúster**: alta disponibilidad, balanceo, reglas de afinidad y antiafinidad, gestión de energía.
- **Control de acceso basado en roles (RBAC)**, con permisos delegados por carpeta, por proyecto o por unidad organizativa. Es un requisito directo del ENS: el administrador de una aplicación municipal no debe poder apagar máquinas de otra (§5.1).
- **Registro de actividad** (*log*) de todas las operaciones administrativas, con marca de tiempo y usuario, exportado a un sistema centralizado de registros para garantizar la **trazabilidad**.
- **Catálogo de plantillas** y biblioteca de imágenes bastionadas.

**Monitorización.** No basta con vigilar el uso de CPU. Las métricas que de verdad diagnostican un entorno virtualizado son [PROMETHEUS] [VMWARE-DOC]:

| Ámbito | Métrica reveladora | Qué indica |
|---|---|---|
| CPU | **Tiempo de espera (`%RDY`)** | Contención: hay más vCPU listas que núcleos disponibles |
| Memoria | **Globo activo, compresión e intercambio del hipervisor** | Presión de memoria; el intercambio es señal de sobreasignación excesiva |
| Almacenamiento | **Latencia por operación (ms)**, más que las operaciones por segundo | Saturación de la cabina o de la ruta de acceso |
| Red | Descartes, retransmisiones, saturación de enlace | Cuello de botella de red o mala distribución de flujos |
| Escritorio virtual | **Tiempo de inicio de sesión** y latencia del protocolo | Experiencia real del usuario, que ninguna métrica de servidor refleja |
| Capacidad | Tendencia de consumo frente a capacidad | Cuándo habrá que ampliar: **planificación de capacidad**, no reacción |

**Orquestación y automatización.** El objetivo es que el aprovisionamiento sea **reproducible y auditable**, no artesanal: **plantillas** en lugar de instalaciones manuales, **infraestructura como código** [TERRAFORM] para describir en ficheros versionados qué máquinas, redes y políticas deben existir, **gestión de configuración** para garantizar que el estado real coincide con el declarado, y **portales de autoservicio** con flujos de aprobación para que las unidades usuarias soliciten recursos dentro de cuotas definidas.

**Gobierno del ciclo de vida.** El problema operativo más común y menos atendido de los entornos virtualizados es la **proliferación descontrolada** (*VM sprawl*): máquinas creadas para una prueba que nadie apaga, plantillas obsoletas, instantáneas de hace meses, escritorios de personal que ya causó baja. Cada una consume licencias, almacenamiento, ventana de copia de seguridad y, sobre todo, **superficie de ataque sin parchear**. Las contramedidas son de gestión, no técnicas: **etiquetado obligatorio** con responsable y finalidad, **fecha de caducidad** para los entornos temporales, **revisión periódica** de máquinas apagadas y de instantáneas antiguas, y un **procedimiento formal de baja**.

> **[RELACIÓN CON OTROS TEMAS]** La virtualización es **el sustrato sobre el que se construye la nube**: los modelos de servicio (IaaS, PaaS, SaaS) y de despliegue (nube pública, privada e híbrida), junto con los paradigmas de computación distribuida, corresponden al **Tema 31**. Aquí interesa únicamente la **infraestructura propia** del organismo; cuando esa infraestructura se contrata a un tercero, se añaden las obligaciones de servicios externos y en la nube del ENS (§5.1).

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** Una revisión de inventario del clúster municipal detecta 40 máquinas virtuales apagadas desde hace más de un año, 12 instantáneas con más de seis meses de antigüedad ocupando varios terabytes y tres plantillas con un sistema operativo ya sin soporte. La corrección combina lo técnico y lo organizativo: consolidar y eliminar instantáneas, archivar y dar de baja las máquinas obsoletas previa comprobación con la unidad responsable, retirar las plantillas caducas y **establecer un etiquetado obligatorio** (unidad responsable, aplicación, entorno, fecha de revisión) como requisito para crear cualquier máquina nueva.

---

## 5. Marco normativo, seguridad y aplicación en la Administración Pública

> **Material complementario.** El enunciado oficial de este tema no nombra este apartado. Se mantiene porque sitúa la materia en el Ayuntamiento y en la normativa que le aplica, pero lo exigible es lo que enumera el título del tema.

### 5.1. Cumplimiento del Esquema Nacional de Seguridad (ENS) en entornos virtualizados

El **Esquema Nacional de Seguridad**, regulado por el **Real Decreto 311/2022, de 3 de mayo**, es de aplicación obligatoria a todo el sector público y a los sistemas de información que traten información o presten servicios administrativos electrónicos, incluidos los de los ayuntamientos, así como a los sistemas de los **proveedores** que les prestan servicios en esos ámbitos [ENS]. Un centro de proceso de datos virtualizado municipal está, por tanto, plenamente sujeto a él.

**Estructura del ENS que hay que conocer** [ENS]:

- **Principios básicos** (seguridad integral, gestión de la seguridad basada en los riesgos, prevención-detección-respuesta-conservación, existencia de líneas de defensa, vigilancia continua y reevaluación periódica, diferenciación de responsabilidades).
- **Requisitos mínimos** (organización e implantación del proceso de seguridad, análisis y gestión de riesgos, gestión de personal, profesionalidad, autorización y control de accesos, protección de las instalaciones, adquisición de productos de seguridad, seguridad por defecto, integridad y actualización del sistema, protección de la información almacenada y en tránsito, prevención frente a otros sistemas interconectados, registro de actividad, incidentes de seguridad, continuidad de la actividad y mejora continua).
- **Cinco dimensiones de seguridad**: **disponibilidad (D), integridad (I), confidencialidad (C), autenticidad (A) y trazabilidad (T)**. Cada servicio y cada información se valora en cada dimensión.
- **Tres categorías del sistema**: **BÁSICA, MEDIA y ALTA**, determinadas por la valoración más alta obtenida en cualquiera de las dimensiones.
- **Medidas de seguridad del Anexo II**, organizadas en **tres marcos**: **organizativo (`org`)**, **operacional (`op`)** y **medidas de protección (`mp`)**, con exigencia creciente según la categoría.
- **Auditoría y conformidad**: los sistemas de categoría **MEDIA y ALTA** se someten a una **auditoría ordinaria al menos cada dos años** y publican la correspondiente **certificación de conformidad**; los de categoría **BÁSICA** pueden acreditar su conformidad mediante **autoevaluación** y la correspondiente declaración.

> **[DATO CLAVE]** Las **cinco dimensiones** del ENS se recuerdan con las siglas **D-I-C-A-T** (disponibilidad, integridad, confidencialidad, autenticidad, trazabilidad); las **tres categorías**, básica, media y alta; y los **tres marcos** de medidas, `org`, `op` y `mp`. La **auditoría bienal** es obligatoria en categoría **media y alta**, no en básica [ENS].

**Cómo aterriza el ENS en un entorno virtualizado.** El ENS **no dedica un grupo de medidas exclusivo a la virtualización**: son las medidas generales de los tres marcos las que hay que aplicar a los elementos virtuales, entendiendo que el **hipervisor**, la **consola de gestión** y la **red virtual** son componentes del sistema tan reales como un servidor físico. Las implicaciones prácticas más relevantes:

| Ámbito de medida | Traducción concreta en el entorno virtualizado |
|---|---|
| **Control de acceso** | El acceso a la consola de gestión y al hipervisor es **acceso de administración privilegiada**: exige identificación nominal, **autenticación reforzada**, roles con **mínimo privilegio** y ninguna cuenta compartida. |
| **Segregación de redes** | Separar en redes distintas los flujos de **gestión**, de **máquinas virtuales**, de **almacenamiento** y de **migración**; la red de gestión del hipervisor **nunca** debe ser accesible desde la red de usuarios [NIST-SP800-125]. |
| **Configuración de seguridad y bastionado** | Desactivar servicios y consolas no necesarios en el anfitrión, aplicar las guías de configuración segura (serie **CCN-STIC**) y mantener plantillas ya bastionadas para todo despliegue nuevo [CCN-STIC]. |
| **Mantenimiento y actualizaciones** | El hipervisor es **software crítico**: sus parches de seguridad se aplican con la misma diligencia que los del sistema operativo, aprovechando el **modo mantenimiento** para hacerlo sin corte (§2.4). |
| **Gestión de cambios** | Los cambios en imágenes maestras, plantillas y políticas de clúster se documentan, se prueban y se aprueban; la **versión de la imagen** es un elemento de configuración. |
| **Registro de actividad y trazabilidad** | Todas las operaciones administrativas sobre el entorno (crear, migrar, clonar, exportar o eliminar una máquina virtual) se registran y se **envían a un repositorio centralizado** fuera del propio entorno, para que no puedan alterarse desde él. |
| **Protección de soportes y de la información** | Un disco virtual es un **soporte de información**: exportar una máquina virtual equivale a **llevarse una copia completa de sus datos**. Debe estar controlado, cifrado cuando proceda y trazado. |
| **Continuidad del servicio** | Análisis de impacto, plan de continuidad, **medios alternativos** y **pruebas periódicas** documentadas (§5.3). |
| **Servicios externos y en la nube** | Si parte de la infraestructura se contrata a un tercero, se exige acreditación de su conformidad con el ENS y se reparten responsabilidades por contrato [ENS] [CCN-STIC]. |

**Riesgos de seguridad específicos de la virtualización**, tal como los sistematiza el NIST [NIST-SP800-125] [NIST-SP800-190]:

- **Escape de máquina virtual** (*VM escape*): la amenaza más grave en concepto —código que rompe el aislamiento y alcanza el hipervisor o a otra máquina virtual—. Es infrecuente, pero su impacto es total; se mitiga parcheando el hipervisor con máxima prioridad y reduciendo su superficie.
- **Compromiso del plano de gestión**: quien controla la consola controla **todas** las máquinas virtuales sin necesidad de entrar en ninguna. Es, en la práctica, el vector más rentable para un atacante y el que más protección merece.
- **Convivencia de niveles de seguridad distintos** en un mismo anfitrión: alojar juntos un sistema de categoría alta y uno expuesto a Internet de categoría básica es una decisión de riesgo que debe evaluarse explícitamente.
- **Máquinas virtuales latentes o inactivas**: una máquina apagada durante meses **no recibe parches** y se convierte en un sistema vulnerable el día que alguien la enciende. Deben incluirse en los ciclos de actualización o darse de baja.
- **Fugas por copia**: la facilidad para clonar o exportar una máquina virtual completa es también una facilidad para **exfiltrar** todos sus datos en un solo fichero.
- **Canales laterales del hardware compartido**: vulnerabilidades de ejecución especulativa y similares que afectan a la CPU compartida entre máquinas virtuales; se mitigan con microcódigo, parches del hipervisor y, en casos críticos, aislamiento físico.

> **[RELACIÓN CON OTROS TEMAS]** Los **principios básicos del ENS y del ENI** se desarrollan en el **Tema 39**; los **conceptos generales de seguridad de los sistemas, amenazas, criptografía y firma digital** en el **Tema 32**; la **seguridad perimetral, el acceso remoto seguro y las VPN** en el **Tema 36**; y la **confidencialidad y disponibilidad en el puesto de usuario final y la seguridad en el desarrollo** en el **Tema 25**. Aquí se tratan solo las implicaciones **propias de virtualizar**.

### 5.2. Protección de datos personales y garantías de privacidad (RGPD y LOPDGDD)

Los sistemas municipales virtualizados tratan datos personales de forma masiva: el **padrón**, el registro, los expedientes, los tributos y la propia identidad de los empleados públicos. El **Reglamento (UE) 2016/679 (RGPD)** y la **Ley Orgánica 3/2018 (LOPDGDD)** imponen obligaciones que la arquitectura de virtualización debe soportar [RGPD] [LOPDGDD].

**Artículos del RGPD con impacto directo en el diseño de un entorno virtualizado**:

| Precepto | Exigencia | Traducción en el entorno virtualizado |
|---|---|---|
| **Art. 5.1.f** | Integridad y confidencialidad | Aislamiento efectivo entre máquinas virtuales, cifrado, control de acceso al plano de gestión. |
| **Art. 25** | Protección de datos **desde el diseño y por defecto** | Plantillas e imágenes maestras **ya bastionadas y con la configuración más restrictiva por defecto**; redirecciones de periféricos y portapapeles **deshabilitadas salvo necesidad**. |
| **Art. 28** | Encargado del tratamiento | Si el hipervisor, la plataforma VDI o el respaldo los opera un tercero, hace falta **contrato de encargo** con garantías, instrucciones documentadas y régimen de subencargados. |
| **Art. 32** | Seguridad del tratamiento | Cita expresamente **seudonimización y cifrado**, la **capacidad de restaurar la disponibilidad y el acceso** tras un incidente, y la **verificación periódica** de la eficacia de las medidas: es el fundamento jurídico directo de las copias, la replicación y **las pruebas de recuperación** (§5.3). |
| **Arts. 33-34** | Notificación de brechas | Notificación a la autoridad de control **sin dilación indebida y, de ser posible, en 72 horas**; el registro de actividad del entorno virtualizado es la fuente que permite reconstruir el alcance. |
| **Art. 35** | Evaluación de impacto (EIPD) | Necesaria cuando el tratamiento entrañe alto riesgo; un despliegue VDI que centralice el acceso a datos de toda la ciudadanía es candidato a valorarla. |
| **Arts. 44-49** | Transferencias internacionales | Determinante al elegir dónde se alojan las máquinas virtuales y las copias si se recurre a nube pública: **la ubicación de los datos es una decisión jurídica, no solo técnica**. |

La **LOPDGDD**, en su **disposición adicional primera**, remite en el sector público a las **medidas de seguridad del ENS**: para un ayuntamiento, cumplir el ENS y cumplir el artículo 32 del RGPD son **dos caras del mismo trabajo**, no dos proyectos distintos [LOPDGDD] [ENS].

**Cuestiones de privacidad propias de la virtualización del puesto de trabajo**, que son las más finas y las que mejor distinguen a quien domina el tema:

- **El escritorio virtual concentra datos personales** de muchos interesados en una infraestructura común: el impacto de un incidente es mayor, y por eso la microsegmentación y el control de acceso son también medidas de protección de datos.
- **Los canales de redirección son vías de salida de información**: portapapeles, unidades locales, impresión y almacenamiento USB. Deshabilitarlos por defecto y habilitarlos por excepción justificada es la aplicación literal del artículo 25 [RGPD].
- **La monitorización del puesto virtual tiene límites**: la plataforma permite ver sesiones, tiempos de uso e incluso interactuar con el escritorio del usuario para soporte. Sobre el empleado público rigen los **artículos 87 a 91 de la LOPDGDD** (derechos digitales en el ámbito laboral) y el deber de **información previa**; el acceso de un técnico a la sesión de un usuario debe estar **previsto, informado, consentido cuando proceda y trazado** [LOPDGDD].
- **Los datos residuales viven en más sitios de los que parece**: instantáneas, ficheros de intercambio del hipervisor, copias de seguridad y réplicas contienen datos personales. El **borrado seguro** y la política de retención deben alcanzarlos a todos; borrar la máquina virtual no borra sus copias.
- **Minimización aplicada a los entornos de prueba**: clonar una máquina virtual de producción para pruebas **duplica los datos personales reales**. Debe emplearse **seudonimización o datos de prueba**, y la comodidad de clonar no es justificación jurídica suficiente.

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** El equipo de desarrollo pide un clon de la máquina virtual del padrón para probar una migración. La respuesta correcta no es negarse ni acceder sin más: es aplicar el **principio de minimización** clonando el entorno con datos **seudonimizados o sintéticos**, dejar constancia de la autorización, aplicar al clon las mismas medidas de seguridad que al original mientras exista y **fijar su fecha de destrucción**. Un clon de producción olvidado en un entorno de pruebas menos protegido es uno de los orígenes más frecuentes de brecha de datos.

### 5.3. Continuidad del negocio, copias de seguridad y recuperación ante desastres

> **[RELACIÓN CON OTROS TEMAS]** Las **políticas, sistemas y procedimientos de copia de seguridad y recuperación**, incluido el respaldo de sistemas físicos y virtuales, constituyen materia propia del **Tema 26**. Este epígrafe cubre únicamente **lo que la virtualización cambia** en la continuidad y la recuperación, y el marco de obligación que impone el ENS.

**Conceptos que ordenan la materia** [NIST-SP800-34] [ISO22301]:

- **Análisis de impacto en el negocio (BIA)**: identifica los procesos críticos y **cuánto tiempo puede estar caído cada uno** y **cuántos datos puede permitirse perder**. De él salen los dos objetivos siguientes, que son decisiones **de la organización**, no del técnico.
- **RTO** (*Recovery Time Objective*): **tiempo máximo tolerable** desde la interrupción hasta el restablecimiento del servicio.
- **RPO** (*Recovery Point Objective*): **volumen máximo tolerable de datos perdidos**, expresado como el tiempo hacia atrás hasta el último punto recuperable. Un RPO de 24 horas se satisface con una copia diaria; un RPO cercano a cero exige **replicación síncrona**.
- **Plan de continuidad** y **plan de recuperación ante desastres**: el primero cubre la continuidad de la actividad en su conjunto; el segundo, el restablecimiento técnico de los sistemas.

> **[DATO CLAVE]** **RTO mira hacia delante** (cuánto tardo en volver) y **RPO mira hacia atrás** (cuánto pierdo). Los fija el **análisis de impacto**, y de ellos se derivan la tecnología y el coste, nunca al revés. Es una de las confusiones más frecuentes del bloque.

**Qué cambia con la virtualización**, que es lo específico de este tema:

- **La copia deja de ser por agente y pasa a ser de la máquina completa**: en lugar de instalar un agente en cada sistema, la herramienta de respaldo dialoga con el hipervisor, toma una **instantánea** de la máquina virtual y copia sus discos. El resultado es una **imagen completa y arrancable**, no un conjunto de ficheros.
- **Seguimiento de bloques modificados** (*changed block tracking*): el hipervisor sabe **qué bloques han cambiado** desde la copia anterior, así que las copias incrementales son rapidísimas y muy pequeñas. Es lo que hace viable respaldar cientos de máquinas en una ventana corta.
- **Consistencia de la aplicación**: una copia tomada «en frío» sobre una base de datos en marcha puede ser inconsistente. Por eso el agente de integración del huésped **congela momentáneamente el sistema de ficheros y avisa a las aplicaciones** (mediante los mecanismos de instantánea del sistema huésped) antes de tomar la instantánea.
- **Recuperación de grano fino**: desde una única copia de la máquina completa se puede restaurar **la máquina entera**, un **disco**, un **fichero suelto** o incluso un objeto de aplicación, montando la imagen sin restaurarla del todo.
- **Recuperación instantánea** (*instant recovery*): arrancar la máquina virtual **directamente desde el repositorio de copias** para restablecer el servicio en minutos, y migrarla en caliente al almacenamiento de producción después, con el servicio ya en marcha. Es la mayor mejora práctica de RTO que aporta la virtualización.
- **Replicación de máquinas virtuales** hacia un segundo emplazamiento, con orquestación de la conmutación: **planes de recuperación** que definen el orden de arranque, las dependencias y el cambio de direccionamiento, y que pueden **probarse en una red aislada sin afectar a producción**. Esta capacidad de **ensayar el plan sin riesgo** es, junto con la anterior, lo que más ha cambiado la recuperación ante desastres.

**Estrategias de emplazamiento alternativo** [NIST-SP800-34]:

| Estrategia | Descripción | RTO orientativo |
|---|---|---|
| **Frío** (*cold site*) | Espacio e infraestructura básica, sin equipamiento preparado | Días |
| **Templado** (*warm site*) | Equipamiento instalado y datos replicados periódicamente | Horas |
| **Caliente** (*hot site*) | Réplica operativa y sincronizada, lista para asumir el servicio | Minutos |
| **Activo-activo** | Ambos emplazamientos dan servicio simultáneamente | Prácticamente nulo |

**Buenas prácticas ineludibles**, todas ellas exigidas o implicadas por el ENS y por el artículo 32 del RGPD [ENS] [RGPD]:

- **Regla 3-2-1**: **3** copias de los datos, en **2** soportes distintos, con **1** fuera del emplazamiento. Frente al secuestro de datos (*ransomware*) se amplía con **1 copia inmutable o desconectada** (*air gap*), porque el atacante actual busca y cifra **también las copias de seguridad** antes de actuar.
- **Copias fuera del dominio de administración del entorno virtualizado**: si el repositorio de copias se administra con las mismas credenciales que el hipervisor, un compromiso del plano de gestión se lleva por delante los datos y su respaldo a la vez.
- **Pruebas de restauración periódicas y documentadas**. Una copia que nunca se ha restaurado **no es una copia**, es una suposición. El ENS exige pruebas periódicas de continuidad y el RGPD exige verificar la eficacia de las medidas [ENS] [RGPD].
- **Documentar RTO y RPO por servicio** y contrastarlos con lo que la solución realmente consigue, no con lo que se desearía.

> **[EJERCICIO RESUELTO]** *La sede electrónica municipal tiene un RPO de 15 minutos y un RTO de 1 hora. ¿Qué combinación técnica lo satisface y cuál no?* **Solución**: **no lo satisface** una copia de seguridad nocturna, que da un RPO de hasta 24 horas, ni una restauración completa desde cinta, cuyo RTO se mide en horas. **Sí lo satisface** una **replicación asíncrona a un segundo emplazamiento con intervalo igual o inferior a 15 minutos**, combinada con un **plan de recuperación orquestado** que arranque las máquinas replicadas en el orden correcto dentro de la hora, y **probado periódicamente en red aislada**. La copia de seguridad sigue siendo necesaria —la réplica no protege frente a un borrado lógico o un cifrado malicioso, que se replicarían—, pero cubre otro riesgo distinto.

### 5.4. Eficiencia energética, consolidación de infraestructuras y sostenibilidad en el sector público

La virtualización es, además de una decisión técnica, **la palanca de eficiencia energética más eficaz** de un centro de proceso de datos, y en el sector público esa dimensión tiene respaldo normativo.

**Consolidación.** Un servidor físico dedicado a una sola aplicación consume una fracción muy elevada de su potencia máxima incluso cuando está prácticamente ocioso, porque el consumo de un servidor **no es proporcional a su carga**: existe un consumo de base considerable. Sustituir muchos servidores infrautilizados por unos pocos bien utilizados reduce el consumo total, y con él la potencia eléctrica contratada, la carga de refrigeración, el número de tomas y de puertos de red, el espacio de sala y los residuos electrónicos al final de la vida útil.

**Palancas de ahorro propias del entorno virtualizado**:

- **Elevar la utilización media** de los anfitriones mediante consolidación y balanceo automático.
- **Gestión dinámica de energía**: concentrar en horas valle las máquinas virtuales en menos anfitriones y **apagar los sobrantes**, encendiéndolos automáticamente al subir la carga [VMWARE-DOC].
- **Retirada de máquinas virtuales inactivas** (§4.3): cada máquina viva consume memoria, ciclos, copias de seguridad y almacenamiento.
- **Prolongación de la vida del parque de puestos**: con VDI o escritorios por sesiones, un equipo antiguo o un cliente ligero sirve durante años más. El **cliente ligero consume una fracción de lo que consume un PC** y su ciclo de vida es más largo, lo que reduce a la vez el gasto energético y la generación de residuos.
- **Optimización térmica de la sala**: menos equipos, mejor distribución y confinamiento de pasillos frío y caliente.
- **Traslado del pico a fuera de hora**: programar copias, análisis y actualizaciones en franjas de menor demanda.

**Indicadores.** El indicador normalizado del centro de datos es el **PUE** (*Power Usage Effectiveness*), definido en la norma **ISO/IEC 30134-2** como el cociente entre la **energía total consumida por el centro de datos** y la **energía consumida por el equipamiento de tecnologías de la información** [ISO30134]. Su valor **ideal es 1,0** (toda la energía iría al equipamiento TI y nada a refrigeración, pérdidas o iluminación) y en la práctica siempre es mayor. Junto a él se manejan la **ratio de consolidación** (máquinas virtuales por anfitrión) y la **utilización media** de los recursos.

> **[DATO CLAVE]** El **PUE** se calcula como energía **total** del centro de datos dividida entre energía del **equipamiento TI**; **cuanto más bajo, mejor**, y **1,0 es el óptimo teórico** [ISO30134]. Cuidado con la trampa habitual: **un PUE bajo no significa que el centro de datos sea eficiente en su conjunto** —mide la eficiencia de la instalación, no si los servidores están bien aprovechados—; un centro con PUE excelente lleno de servidores ociosos sigue derrochando energía. Por eso el PUE debe leerse junto con la **utilización real** del equipamiento.

**Marco normativo y de gestión aplicable al sector público**:

- **Directiva (UE) 2023/1791** de eficiencia energética: establece, en su artículo 12, obligaciones de **información sobre el rendimiento energético de los centros de datos** a partir de determinado umbral de potencia instalada, con el objetivo de dar transparencia y comparabilidad al sector [EED2023].
- **Ley 9/2017 de Contratos del Sector Público**: obliga a incorporar de manera transversal criterios **sociales y medioambientales** en la contratación pública, lo que permite valorar la eficiencia energética de los equipos y de las soluciones ofertadas en la adjudicación [LCSP].
- **ENS**: la **disponibilidad** es una de las cinco dimensiones y las medidas de protección de las instalaciones incluyen el suministro eléctrico y el acondicionamiento; eficiencia y disponibilidad son objetivos que deben equilibrarse, no oponerse [ENS].
- **Códigos de conducta y marcos de buenas prácticas** europeos para la eficiencia energética de centros de datos, empleados habitualmente como referencia técnica en los pliegos.

> **[EJEMPLO DE APLICACIÓN EN EL AYTO]** Un proyecto municipal de consolidación sustituye un parque de servidores físicos infrautilizados por un clúster reducido de anfitriones con gestión dinámica de energía, y despliega escritorios por sesiones y VDI reutilizando durante tres años más los equipos existentes como terminales. El resultado combina las tres dimensiones que la Administración debe justificar: **económica** (menos equipos, menos consumo, menos mantenimiento), **de servicio** (alta disponibilidad y mantenimiento sin ventanas de parada) y **ambiental** (menor consumo eléctrico y menos residuos de aparatos eléctricos y electrónicos), esta última acreditable mediante indicadores como el PUE y la ratio de consolidación.

---

## Síntesis final del tema

Cinco ideas que ordenan todo lo anterior y que conviene poder reconstruir de memoria:

1. **La virtualización es una capa de indirección**, y su legitimidad técnica se mide con los tres principios de Popek y Goldberg: **equivalencia, control de recursos y eficiencia**. Esa tercera propiedad es la que separa un hipervisor de un emulador [POPEK74].
2. **Hay tres técnicas y dos tipos de hipervisor.** Técnicas: **total** (traducción binaria, huésped intacto), **paravirtualización** (huésped modificado, hoy vigente sobre todo en **dispositivos** vía virtio) y **asistida por hardware** (VT-x/AMD-V/EL2, más EPT/NPT y IOMMU), que es el modelo dominante. Tipos: **1 o nativo** (centro de datos) y **2 o alojado** (puesto de trabajo).
3. **En el servidor, la virtualización compra elasticidad y continuidad**: sobreasignación controlada de recursos, controladores paravirtualizados de E/S, **migración en caliente** (planificada, sin corte), **HA** (no planificada, con reinicio) y **FT** (no planificada, sin corte). Los contenedores no la sustituyen: **se apoyan en ella**.
4. **En el puesto, la virtualización compra control y seguridad**: **VDI** (una VM por usuario), **sesiones** (un sistema compartido, más denso y barato) y **virtualización de aplicaciones** (la burbuja aislada). El **broker** decide y conecta, la **imagen maestra** convierte mil puestos en uno, y la elección entre **persistente y no persistente** determina todo lo demás — incluida la obligación de gestionar el perfil aparte.
5. **En la Administración Pública, virtualizar no exime de cumplir; obliga a cumplir mejor.** El **ENS** (RD 311/2022) impone control de accesos privilegiados, segregación de redes, bastionado, trazabilidad y continuidad probada; el **RGPD** y la **LOPDGDD** exigen protección desde el diseño, control de los canales de fuga y capacidad **verificada** de restaurar el servicio; y la consolidación aporta una eficiencia energética que la normativa europea y la contratación pública ya valoran expresamente.
