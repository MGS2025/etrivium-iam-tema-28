# Tema 28 — Test de Autoevaluación

> **Título**: Virtualización de sistemas y virtualización de puestos de usuario.
> **Formato**: 60 preguntas tipo test A/B/C (formato oficial oposición)
> **Nivel**: C1 — Técnico Auxiliar TIC, Ayuntamiento de Madrid
> **Versión**: v1.0 — Pendiente validación
> **Fecha**: 2026-08-20
> **Fuentes**: ver tema-28-fuentes.md

---

## Instrucciones

- Cada pregunta tiene **3 opciones** (A, B, C). Solo una es correcta.
- Penalización en examen real: respuesta incorrecta descuenta **1/3** del valor de una correcta.
- Tiempo orientativo: 1 minuto por pregunta.
- Distribución: Fundamentos e hipervisores (P1-P12), Virtualización de servidores y contenedores (P13-P28), Puesto de usuario y VDI (P29-P44), Almacenamiento, red y gestión (P45-P52), Normativa, seguridad y continuidad (P53-P60).

---

### Pregunta 1

**¿Cuáles son las tres propiedades que Popek y Goldberg (1974) exigen a un monitor de máquina virtual?**

A) Equivalencia, control de recursos y eficiencia
B) Portabilidad, escalabilidad y disponibilidad
C) Aislamiento, cifrado y trazabilidad

<details><summary>Respuesta</summary>

**Correcta: A) Equivalencia, control de recursos y eficiencia** La equivalencia exige que el programa se comporte igual que sobre hardware real; el control de recursos, que el monitor gobierne todo el hardware; y la eficiencia, que la mayoría de las instrucciones se ejecuten directamente en la CPU.

*Referencia: §1.1 [POPEK74]*
</details>

---

### Pregunta 2

**¿Qué propiedad de Popek y Goldberg incumple un emulador y por eso no se considera un hipervisor?**

A) La equivalencia, porque el programa no se comporta igual
B) La eficiencia, porque traduce cada instrucción en lugar de ejecutarla directamente
C) El control de recursos, porque no aísla las máquinas entre sí

<details><summary>Respuesta</summary>

**Correcta: B) La eficiencia, porque traduce cada instrucción en lugar de ejecutarla directamente** A cambio de incumplirla, el emulador puede ejecutar código de una arquitectura distinta a la del anfitrión, algo que un hipervisor no permite.

*Referencia: §1.1 [POPEK74]*
</details>

---

### Pregunta 3

**Según el teorema de virtualizabilidad, ¿cuándo es una arquitectura virtualizable de forma clásica?**

A) Cuando dispone de al menos cuatro anillos de privilegio
B) Cuando su unidad de gestión de memoria admite paginación
C) Cuando toda instrucción sensible es también privilegiada

<details><summary>Respuesta</summary>

**Correcta: C) Cuando toda instrucción sensible es también privilegiada** Si se cumple, el monitor puede ejecutar el huésped sin privilegios y limitarse a atrapar y emular las excepciones. El x86 original no lo cumplía: tenía 17 instrucciones sensibles no privilegiadas.

*Referencia: §1.1 [POPEK74] [ROBIN00]*
</details>

---

### Pregunta 4

**¿Dónde y cuándo nace la virtualización de sistemas tal como la conocemos?**

A) Con VMware sobre x86, a finales de los años noventa
B) En los grandes sistemas de IBM en los años sesenta, con CP-67 y VM/370
C) Con la aparición de Xen y la paravirtualización en 2003

<details><summary>Respuesta</summary>

**Correcta: B) En los grandes sistemas de IBM en los años sesenta, con CP-67 y VM/370** El objetivo entonces era repartir en tiempo compartido un equipo carísimo. VMware y Xen resolvieron después el problema específico del x86, que era arquitectónico y no de potencia.

*Referencia: §1.1 [HIST-IBM] [ROBIN00]*
</details>

---

### Pregunta 5

**En la terminología de virtualización, ¿qué es el «anfitrión» (host)?**

A) La máquina física que aporta los recursos reales y ejecuta el hipervisor
B) El sistema operativo virtualizado que se ejecuta dentro de una máquina virtual
C) La consola centralizada desde la que se gestiona el clúster

<details><summary>Respuesta</summary>

**Correcta: A) La máquina física que aporta los recursos reales y ejecuta el hipervisor** El sistema operativo virtualizado es el huésped (guest) y la consola centralizada es el servidor de gestión.

*Referencia: §1.1 [NIST-SP800-125]*
</details>

---

### Pregunta 6

**¿Qué caracteriza a la virtualización total o completa?**

A) El núcleo del huésped se modifica para emitir llamadas al hipervisor
B) Exige obligatoriamente extensiones de virtualización en la CPU
C) El huésped se instala sin modificar y no sabe que está virtualizado

<details><summary>Respuesta</summary>

**Correcta: C) El huésped se instala sin modificar y no sabe que está virtualizado** Su implementación clásica en x86 combinaba ejecución directa del código de usuario con traducción binaria dinámica del código privilegiado del núcleo huésped.

*Referencia: §1.2.1 [POPEK74] [ROBIN00]*
</details>

---

### Pregunta 7

**¿Cuál es el inconveniente principal de la paravirtualización del núcleo?**

A) Ofrece un rendimiento inferior al de la traducción binaria
B) Impide utilizar controladores de dispositivo eficientes
C) Exige modificar el núcleo del sistema huésped, lo que descarta los sistemas cerrados

<details><summary>Respuesta</summary>

**Correcta: C) Exige modificar el núcleo del sistema huésped, lo que descarta los sistemas cerrados** Su rendimiento es precisamente superior al de la traducción binaria, pero solo puede aplicarse a sistemas cuyo código pueda adaptarse.

*Referencia: §1.2.2 [XEN-DOC]*
</details>

---

### Pregunta 8

**En la arquitectura de Xen, ¿qué es `dom0`?**

A) La máquina virtual privilegiada, con controladores reales, desde la que se administra el sistema
B) El identificador del hipervisor dentro de la estructura de control de la CPU
C) El dominio de red por defecto al que se conectan las máquinas virtuales

<details><summary>Respuesta</summary>

**Correcta: A) La máquina virtual privilegiada, con controladores reales, desde la que se administra el sistema** Las máquinas virtuales no privilegiadas de los usuarios se denominan `domU`.

*Referencia: §1.2.2 [XEN-DOC]*
</details>

---

### Pregunta 9

**¿Qué aportan las extensiones Intel VT-x y AMD-V a la virtualización?**

A) Duplican el número de núcleos disponibles para las máquinas virtuales
B) Añaden un modo de ejecución adicional para el hipervisor, de modo que el huésped conserva su anillo 0 sin traducción binaria
C) Cifran la memoria de cada máquina virtual con una clave distinta

<details><summary>Respuesta</summary>

**Correcta: B) Añaden un modo de ejecución adicional para el hipervisor, de modo que el huésped conserva su anillo 0 sin traducción binaria** Intel lo articula con los modos VMX raíz y no raíz y la estructura VMCS; AMD, con SVM y la estructura VMCB.

*Referencia: §1.2.3 [INTEL-SDM] [AMD-APM]*
</details>

---

### Pregunta 10

**¿Qué problema resuelven las tablas de páginas extendidas (EPT) o anidadas (NPT)?**

A) La doble traducción de memoria, que antes exigía mantener por software costosas tablas de páginas sombra
B) El acceso directo a memoria de los dispositivos asignados a una máquina virtual
C) La compatibilidad de CPU entre anfitriones para permitir la migración en caliente

<details><summary>Respuesta</summary>

**Correcta: A) La doble traducción de memoria, que antes exigía mantener por software costosas tablas de páginas sombra** El acceso directo a memoria de los dispositivos lo resuelve la IOMMU (VT-d / AMD-Vi), que es un mecanismo distinto.

*Referencia: §1.2.3 [INTEL-SDM] [AMD-APM]*
</details>

---

### Pregunta 11

**¿Cuál es el requisito imprescindible para asignar directamente un dispositivo físico a una máquina virtual (passthrough)?**

A) Que el huésped esté paravirtualizado
B) Que el dispositivo admita SR-IOV
C) Que la plataforma disponga de IOMMU (Intel VT-d o AMD-Vi)

<details><summary>Respuesta</summary>

**Correcta: C) Que la plataforma disponga de IOMMU (Intel VT-d o AMD-Vi)** Sin ella, el dispositivo podría leer y escribir por acceso directo toda la memoria del anfitrión. SR-IOV es una evolución que además permite repartir el dispositivo, pero no es requisito de la asignación directa simple.

*Referencia: §1.2.3 y §2.2.2 [INTEL-VTD] [PCI-SRIOV]*
</details>

---

### Pregunta 12

**¿Cómo se clasifica KVM y por qué?**

A) De Tipo 2, porque se ejecuta sobre un sistema operativo Linux completo
B) De Tipo 1, porque es un módulo que convierte al propio núcleo de Linux en hipervisor con acceso directo al hardware
C) No es un hipervisor, sino un emulador de dispositivos

<details><summary>Respuesta</summary>

**Correcta: B) De Tipo 1, porque es un módulo que convierte al propio núcleo de Linux en hipervisor con acceso directo al hardware** Lo mismo sucede con Hyper-V: al habilitar el rol, el hipervisor se sitúa por debajo del sistema, que pasa a ser una partición privilegiada.

*Referencia: §1.3.1 [KVM-DOC] [HYPERV-DOC]*
</details>

---

### Pregunta 13

**¿Qué es OVF/OVA en un entorno de virtualización?**

A) Un formato de disco virtual con aprovisionamiento fino e instantáneas internas
B) Un formato abierto de empaquetado e intercambio de máquinas virtuales, no un formato de disco
C) El protocolo de comunicación entre el hipervisor y la consola de gestión

<details><summary>Respuesta</summary>

**Correcta: B) Un formato abierto de empaquetado e intercambio de máquinas virtuales, no un formato de disco** Combina un descriptor XML con los discos de la máquina; OVA es ese mismo contenido en un único fichero. Formatos de disco son VMDK, VHD/VHDX, QCOW2 y RAW.

*Referencia: §2.1 [VMWARE-DOC]*
</details>

---

### Pregunta 14

**¿Por qué una instantánea (snapshot) no es una copia de seguridad?**

A) Porque solo guarda la memoria y nunca el disco de la máquina virtual
B) Porque el hipervisor la elimina automáticamente cada 24 horas
C) Porque reside en el mismo almacenamiento que la máquina y sus ficheros delta crecen degradando el rendimiento

<details><summary>Respuesta</summary>

**Correcta: C) Porque reside en el mismo almacenamiento que la máquina y sus ficheros delta crecen degradando el rendimiento** Si se pierde la cabina se pierden también las instantáneas. Sirven para revertir un cambio a corto plazo, no para proteger datos.

*Referencia: §2.1 [VMWARE-DOC]*
</details>

---

### Pregunta 15

**¿Cuál es el riesgo específico del aprovisionamiento fino (thin provisioning)?**

A) Que la suma de lo comprometido supere la capacidad real y, al llenarse el almacén, las máquinas virtuales se detengan
B) Que el rendimiento de lectura sea siempre inferior al del aprovisionamiento grueso
C) Que impida tomar instantáneas de la máquina virtual

<details><summary>Respuesta</summary>

**Correcta: A) Que la suma de lo comprometido supere la capacidad real y, al llenarse el almacén, las máquinas virtuales se detengan** Por eso el sobreaprovisionamiento exige vigilancia activa del espacio libre del almacén de datos.

*Referencia: §2.1 [VMWARE-DOC]*
</details>

---

### Pregunta 16

**En cuanto a la sobreasignación de recursos, ¿qué afirmación es correcta?**

A) Sobreasignar CPU degrada el servicio con esperas, mientras que sobreasignar memoria puede llegar a romperlo
B) La memoria es el recurso que más margen de sobreasignación admite sin consecuencias
C) La sobreasignación está desaconsejada en todos los recursos por igual

<details><summary>Respuesta</summary>

**Correcta: A) Sobreasignar CPU degrada el servicio con esperas, mientras que sobreasignar memoria puede llegar a romperlo** Cuando falta CPU las máquinas esperan turno; cuando falta memoria el hipervisor acaba recurriendo al intercambio a disco, que hunde el rendimiento.

*Referencia: §2.2 [VMWARE-DOC]*
</details>

---

### Pregunta 17

**¿Qué expresa la métrica de tiempo de espera de CPU (CPU ready o `%RDY`)?**

A) El porcentaje de tiempo que la CPU física permanece ociosa
B) El porcentaje de tiempo que una vCPU está lista para ejecutarse pero espera un núcleo libre
C) La latencia media de las operaciones de disco de la máquina virtual

<details><summary>Respuesta</summary>

**Correcta: B) El porcentaje de tiempo que una vCPU está lista para ejecutarse pero espera un núcleo libre** Es el indicador de contención de CPU por excelencia: puede ser alto aunque la utilización global del anfitrión no parezca extrema.

*Referencia: §2.2.1 [VMWARE-DOC]*
</details>

---

### Pregunta 18

**¿Qué efecto tiene asignar a una máquina virtual más vCPU de las que necesita?**

A) Ninguno: el hipervisor ignora las vCPU que no se usan
B) Mejora siempre el rendimiento, porque dispone de más capacidad de reserva
C) Empeora el rendimiento, porque el planificador debe encontrar hueco para más entidades y aumenta el tiempo de espera

<details><summary>Respuesta</summary>

**Correcta: C) Empeora el rendimiento, porque el planificador debe encontrar hueco para más entidades y aumenta el tiempo de espera** Perjudica además a las máquinas vecinas del mismo anfitrión. La regla es empezar corto y crecer con datos de monitorización.

*Referencia: §2.2.1 [VMWARE-DOC]*
</details>

---

### Pregunta 19

**Ordene de menos a más dañina las técnicas de recuperación de memoria del hipervisor.**

A) Intercambio a disco, compresión, globo y compartición de páginas
B) Globo, compartición de páginas, intercambio a disco y compresión
C) Compartición de páginas idénticas, globo, compresión e intercambio a disco

<details><summary>Respuesta</summary>

**Correcta: C) Compartición de páginas idénticas, globo, compresión e intercambio a disco** La aparición de intercambio del hipervisor a disco es siempre señal de un problema de dimensionamiento, porque el hipervisor elige a ciegas qué página sacrificar.

*Referencia: §2.2.1 [VMWARE-DOC] [KVM-DOC]*
</details>

---

### Pregunta 20

**¿En qué consiste la técnica del globo de memoria (ballooning)?**

A) Un controlador instalado en el huésped reclama memoria dentro de él, forzándole a liberar sus páginas menos usadas para devolverlas al hipervisor
B) El hipervisor comprime las páginas menos usadas y las guarda en una caché en memoria
C) El hipervisor duplica la memoria asignada a la máquina virtual durante los picos de carga

<details><summary>Respuesta</summary>

**Correcta: A) Un controlador instalado en el huésped reclama memoria dentro de él, forzándole a liberar sus páginas menos usadas para devolverlas al hipervisor** Su virtud es que la decisión de qué página sacrificar la toma el huésped, que es quien mejor conoce su propio uso de memoria.

*Referencia: §2.2.1 [VMWARE-DOC]*
</details>

---

### Pregunta 21

**¿Qué es virtio?**

A) Un formato de disco virtual propio del ecosistema KVM
B) Un estándar abierto de dispositivos paravirtualizados de entrada/salida, con controladores como virtio-net y virtio-blk
C) El protocolo de representación remota usado por los escritorios virtuales de Red Hat

<details><summary>Respuesta</summary>

**Correcta: B) Un estándar abierto de dispositivos paravirtualizados de entrada/salida, con controladores como virtio-net y virtio-blk** Es paravirtualización aplicada a los dispositivos, no al núcleo, y por eso sigue plenamente vigente aunque la paravirtualización del núcleo esté desplazada. El protocolo de representación de Red Hat es SPICE.

*Referencia: §2.2.2 [VIRTIO]*
</details>

---

### Pregunta 22

**¿Qué distingue a SR-IOV de la asignación directa simple de un dispositivo?**

A) No necesita IOMMU, a diferencia de la asignación directa
B) Conserva íntegramente la posibilidad de migrar la máquina virtual en caliente
C) Presenta el dispositivo como una función física y varias funciones virtuales, repartibles entre distintas máquinas virtuales

<details><summary>Respuesta</summary>

**Correcta: C) Presenta el dispositivo como una función física y varias funciones virtuales, repartibles entre distintas máquinas virtuales** Sigue necesitando IOMMU y mantiene, en general, las mismas restricciones de movilidad que la asignación directa.

*Referencia: §2.2.2 [PCI-SRIOV]*
</details>

---

### Pregunta 23

**¿Qué modelo de entrada/salida es el habitual por defecto en producción?**

A) El dispositivo paravirtualizado, por combinar rendimiento alto con portabilidad y migración en caliente
B) La emulación completa, por no exigir controladores en el huésped
C) La asignación directa, por ofrecer rendimiento nativo

<details><summary>Respuesta</summary>

**Correcta: A) El dispositivo paravirtualizado, por combinar rendimiento alto con portabilidad y migración en caliente** La emulación se reserva a la instalación y a huéspedes antiguos, y la asignación directa a casos concretos que justifiquen perder la movilidad.

*Referencia: §2.2.2 [VIRTIO] [VMWARE-DOC]*
</details>

---

### Pregunta 24

**¿Qué primitivas del núcleo Linux sustentan el aislamiento de un contenedor?**

A) Las tablas de páginas extendidas y la IOMMU
B) Los espacios de nombres (namespaces) y los grupos de control (cgroups)
C) Los anillos de privilegio y la traducción binaria

<details><summary>Respuesta</summary>

**Correcta: B) Los espacios de nombres (namespaces) y los grupos de control (cgroups)** Los primeros aíslan qué ve el proceso (procesos, red, montajes, identificadores) y los segundos limitan cuánto consume. Se complementan con capacidades, seccomp y módulos de seguridad como SELinux o AppArmor.

*Referencia: §2.3 [LINUX-NS] [OCI-SPEC]*
</details>

---

### Pregunta 25

**¿Cuál es la diferencia fundamental entre una máquina virtual y un contenedor?**

A) El contenedor arranca más rápido, pero por lo demás son equivalentes
B) La máquina virtual solo admite sistemas de código abierto y el contenedor cualquiera
C) La máquina virtual virtualiza el hardware y lleva su propio núcleo; el contenedor virtualiza el sistema operativo y comparte el núcleo del anfitrión

<details><summary>Respuesta</summary>

**Correcta: C) La máquina virtual virtualiza el hardware y lleva su propio núcleo; el contenedor virtualiza el sistema operativo y comparte el núcleo del anfitrión** De ahí se derivan todas las demás diferencias: tamaño, tiempo de arranque, densidad, aislamiento y sistemas operativos admitidos.

*Referencia: §2.3.1 [NIST-SP800-190]*
</details>

---

### Pregunta 26

**¿Puede un contenedor ejecutar un sistema operativo distinto del núcleo del anfitrión?**

A) No: al compartir el núcleo, solo puede ejecutar el mismo sistema; cuando parece lo contrario, hay una máquina virtual ligera por debajo
B) Sí, porque el motor de contenedores traduce las llamadas al sistema
C) Sí, siempre que la CPU disponga de extensiones de virtualización

<details><summary>Respuesta</summary>

**Correcta: A) No: al compartir el núcleo, solo puede ejecutar el mismo sistema; cuando parece lo contrario, hay una máquina virtual ligera por debajo** Es el caso de los contenedores Linux ejecutados sobre un equipo con otro sistema operativo.

*Referencia: §2.3.1 [NIST-SP800-190]*
</details>

---

### Pregunta 27

**¿Cuál es el riesgo de seguridad más característico de los contenedores frente a las máquinas virtuales?**

A) La imposibilidad de aplicar control de acceso basado en roles
B) Que una escalada de privilegios en el núcleo compartido permita escapar del contenedor y afectar al anfitrión y a sus vecinos
C) Que no admitan cifrado del almacenamiento persistente

<details><summary>Respuesta</summary>

**Correcta: B) Que una escalada de privilegios en el núcleo compartido permita escapar del contenedor y afectar al anfitrión y a sus vecinos** Es la diferencia de aislamiento esencial, y la razón por la que existen los contenedores aislados por hipervisor.

*Referencia: §2.3 y §2.3.1 [NIST-SP800-190]*
</details>

---

### Pregunta 28

**¿Qué diferencia una política de alta disponibilidad (HA) de la tolerancia a fallos (FT)?**

A) HA se aplica a eventos planificados y FT a eventos imprevistos
B) HA reinicia la máquina virtual en otro anfitrión, con corte y pérdida del estado en memoria; FT mantiene una copia en espejo sincronizada y conmuta sin corte
C) HA exige almacenamiento compartido y FT no lo necesita

<details><summary>Respuesta</summary>

**Correcta: B) HA reinicia la máquina virtual en otro anfitrión, con corte y pérdida del estado en memoria; FT mantiene una copia en espejo sincronizada y conmuta sin corte** Ambas responden a eventos imprevistos; la planificada sin corte es la migración en caliente. FT duplica el consumo de recursos y se reserva a servicios críticos.

*Referencia: §2.4 [VMWARE-DOC]*
</details>

---

### Pregunta 29

**En la migración en caliente por precopia iterativa, ¿cuándo se detiene la máquina virtual?**

A) Solo en una parada final de milisegundos, para transferir las últimas páginas sucias y el estado de CPU y dispositivos
B) Durante toda la transferencia de memoria, que dura varios segundos
C) No se detiene en ningún momento, ni siquiera brevemente

<details><summary>Respuesta</summary>

**Correcta: A) Solo en una parada final de milisegundos, para transferir las últimas páginas sucias y el estado de CPU y dispositivos** El grueso de la memoria se copia mientras la máquina sigue funcionando en el origen, repitiendo pasadas sobre las páginas que se van ensuciando.

*Referencia: §2.4 [VMWARE-DOC] [KVM-DOC]*
</details>

---

### Pregunta 30

**¿Cuál de los siguientes NO es un requisito de la migración en caliente?**

A) Compatibilidad de CPU entre el anfitrión de origen y el de destino
B) Acceso de ambos anfitriones al mismo almacenamiento (o migración simultánea del disco)
C) Que la máquina virtual tenga un dispositivo asignado directamente en passthrough

<details><summary>Respuesta</summary>

**Correcta: C) Que la máquina virtual tenga un dispositivo asignado directamente en passthrough** Es justamente lo contrario: un dispositivo asignado directamente ata la máquina al hardware del origen y, por regla general, impide la migración en caliente.

*Referencia: §2.4 [VMWARE-DOC] [PCI-SRIOV]*
</details>

---

### Pregunta 31

**¿Para qué sirve una regla de antiafinidad en un clúster de virtualización?**

A) Para impedir que dos máquinas virtuales concretas se ejecuten en el mismo anfitrión, evitando que un par redundante caiga junto
B) Para forzar que dos máquinas virtuales se ejecuten siempre en el mismo anfitrión y reducir la latencia entre ellas
C) Para excluir a un anfitrión del balanceo automático de carga

<details><summary>Respuesta</summary>

**Correcta: A) Para impedir que dos máquinas virtuales concretas se ejecuten en el mismo anfitrión, evitando que un par redundante caiga junto** Forzar la coincidencia es la regla de afinidad, que es la contraria.

*Referencia: §2.4 [VMWARE-DOC]*
</details>

---

### Pregunta 32

**¿Qué permite el modo mantenimiento de un anfitrión?**

A) Suspender temporalmente las copias de seguridad de sus máquinas virtuales
B) Aplicar parches al sistema huésped sin reiniciarlo
C) Vaciar automáticamente y en caliente todas sus máquinas virtuales hacia otros nodos antes de intervenir el equipo

<details><summary>Respuesta</summary>

**Correcta: C) Vaciar automáticamente y en caliente todas sus máquinas virtuales hacia otros nodos antes de intervenir el equipo** Es la razón por la que, en un entorno virtualizado bien diseñado, el mantenimiento del hardware deja de requerir ventanas de parada del servicio.

*Referencia: §2.4 [VMWARE-DOC]*
</details>

---

### Pregunta 33

**¿Qué define a la infraestructura de escritorios virtuales (VDI)?**

A) Un único sistema operativo de servidor que atiende muchas sesiones de usuario simultáneas
B) Una máquina virtual con sistema operativo de cliente por usuario, ejecutada en el centro de datos y consumida en remoto
C) La entrega de aplicaciones empaquetadas en burbujas aisladas sobre el equipo del usuario

<details><summary>Respuesta</summary>

**Correcta: B) Una máquina virtual con sistema operativo de cliente por usuario, ejecutada en el centro de datos y consumida en remoto** La opción A describe los escritorios basados en sesiones (RDSH) y la C, la virtualización de aplicaciones.

*Referencia: §3.1.1 [HORIZON-DOC] [CITRIX-DOC]*
</details>

---

### Pregunta 34

**¿Qué diferencia hay entre un cliente ligero y VDI?**

A) El cliente ligero es el dispositivo terminal del usuario; VDI es la arquitectura del lado del servidor
B) Son sinónimos: cliente ligero es el nombre comercial de VDI
C) El cliente ligero exige VDI, mientras que VDI puede prescindir de cualquier terminal

<details><summary>Respuesta</summary>

**Correcta: A) El cliente ligero es el dispositivo terminal del usuario; VDI es la arquitectura del lado del servidor** Se puede hacer VDI con clientes ligeros, con clientes cero, con PC reutilizados o con un simple navegador HTML5.

*Referencia: §3.1 [HORIZON-DOC]*
</details>

---

### Pregunta 35

**¿Qué es una tormenta de arranque (boot storm) en VDI?**

A) Un ataque de denegación de servicio contra el broker de conexiones
B) El pico simultáneo de entrada/salida que se produce cuando cientos de escritorios arrancan y cargan perfiles a la misma hora
C) La saturación del enlace de red provocada por la migración masiva de escritorios entre anfitriones

<details><summary>Respuesta</summary>

**Correcta: B) El pico simultáneo de entrada/salida que se produce cuando cientos de escritorios arrancan y cargan perfiles a la misma hora** Se mitiga con almacenamiento de estado sólido, encendido escalonado y preencendido, cachés de la imagen maestra, clones instantáneos y análisis antivirus desfasado en el tiempo.

*Referencia: §3.1.1 [HORIZON-DOC] [CITRIX-DOC]*
</details>

---

### Pregunta 36

**En un modelo de escritorios basados en sesiones (RDSH), ¿qué ocurre?**

A) Cada usuario dispone de su propia máquina virtual con sistema operativo de cliente
B) Cada usuario recibe una copia de la imagen maestra que se descarta al cerrar sesión
C) Un único sistema operativo de servidor atiende simultáneamente a muchos usuarios, cada uno en su sesión

<details><summary>Respuesta</summary>

**Correcta: C) Un único sistema operativo de servidor atiende simultáneamente a muchos usuarios, cada uno en su sesión** Por eso su densidad y su coste por puesto son mucho mejores que los de VDI, a costa de un aislamiento menor y de una personalización limitada.

*Referencia: §3.1.2 [AVD-DOC]*
</details>

---

### Pregunta 37

**¿Cuál es la principal desventaja del modelo de escritorios por sesiones frente a VDI?**

A) Que exige un sistema operativo de cliente por usuario, lo que dispara el coste
B) Que el aislamiento es menor: un proceso descontrolado o un cuelgue afecta a todos los usuarios de ese servidor
C) Que impide publicar aplicaciones individuales

<details><summary>Respuesta</summary>

**Correcta: B) Que el aislamiento es menor: un proceso descontrolado o un cuelgue afecta a todos los usuarios de ese servidor** Además, la compatibilidad es la del sistema operativo de servidor y no se puede instalar software por usuario. La publicación de aplicaciones individuales es precisamente una de sus capacidades.

*Referencia: §3.1.2 [AVD-DOC]*
</details>

---

### Pregunta 38

**¿Qué problema resuelve de forma característica la virtualización de aplicaciones?**

A) La convivencia en un mismo puesto de versiones incompatibles de la misma aplicación, cada una en su burbuja aislada
B) La necesidad de disponer de un sistema operativo de servidor para publicar escritorios
C) La imposibilidad de migrar en caliente una máquina virtual con dispositivos asignados

<details><summary>Respuesta</summary>

**Correcta: A) La convivencia en un mismo puesto de versiones incompatibles de la misma aplicación, cada una en su burbuja aislada** Aporta además despliegue y retirada limpios, entrega bajo demanda y actualización centralizada del paquete.

*Referencia: §3.1.3 [APPV]*
</details>

---

### Pregunta 39

**¿Cuál es una limitación reconocida de la virtualización de aplicaciones?**

A) Obliga a que el puesto del usuario sea un escritorio virtual y no un equipo físico
B) Impide actualizar la aplicación sin reinstalarla en cada puesto
C) No toda aplicación es virtualizable: las que instalan controladores en modo núcleo o servicios de sistema profundos suelen quedar fuera

<details><summary>Respuesta</summary>

**Correcta: C) No toda aplicación es virtualizable: las que instalan controladores en modo núcleo o servicios de sistema profundos suelen quedar fuera** A ello se añade el coste de trabajo técnico y de pruebas del empaquetado inicial.

*Referencia: §3.1.3 [APPV]*
</details>

---

### Pregunta 40

**¿Cuál de estas funciones NO corresponde al broker de conexiones de una plataforma VDI?**

A) Autenticar al usuario y comprobar sus autorizaciones
B) Transportar el flujo del protocolo de representación durante toda la sesión
C) Seleccionar o encender el escritorio y redirigir hacia él al cliente

<details><summary>Respuesta</summary>

**Correcta: B) Transportar el flujo del protocolo de representación durante toda la sesión** El broker interviene en el establecimiento, no en el tráfico: una vez asignado el escritorio, el protocolo fluye directamente entre el cliente y el escritorio, o a través de la pasarela.

*Referencia: §3.2.1 [CITRIX-DOC] [HORIZON-DOC]*
</details>

---

### Pregunta 41

**¿Qué consecuencia tiene la indisponibilidad del broker de conexiones?**

A) No pueden abrirse sesiones nuevas, aunque las sesiones ya establecidas sigan funcionando
B) Todos los escritorios virtuales se apagan de inmediato
C) Las sesiones existentes pierden la conexión y los datos no guardados

<details><summary>Respuesta</summary>

**Correcta: A) No pueden abrirse sesiones nuevas, aunque las sesiones ya establecidas sigan funcionando** Al ser un punto único de fallo, se despliega siempre redundado y balanceado, con su base de datos en alta disponibilidad.

*Referencia: §3.2.1 [CITRIX-DOC]*
</details>

---

### Pregunta 42

**¿Qué caracteriza a un clon instantáneo (instant clone) frente a un clon enlazado?**

A) Que cada escritorio recibe una copia completa e independiente de la plantilla
B) Que el escritorio arranca desde la red sin disco propio
C) Que se deriva de una máquina plantilla ya arrancada en memoria, por lo que está disponible en segundos y comparte también páginas de memoria

<details><summary>Respuesta</summary>

**Correcta: C) Que se deriva de una máquina plantilla ya arrancada en memoria, por lo que está disponible en segundos y comparte también páginas de memoria** Es una de las mejores mitigaciones de la tormenta de arranque en conjuntos no persistentes grandes.

*Referencia: §3.2.2 [HORIZON-DOC]*
</details>

---

### Pregunta 43

**¿Cuál de estos protocolos de representación remota está definido en un RFC del IETF?**

A) ICA/HDX
B) PCoIP
C) RFB, el protocolo en el que se basa VNC

<details><summary>Respuesta</summary>

**Correcta: C) RFB, el protocolo en el que se basa VNC** Está publicado como RFC 6143. ICA/HDX es de Citrix y PCoIP de Teradici/HP; RDP, aunque es de Microsoft, cuenta con especificación abierta publicada.

*Referencia: §3.3 [RFC6143] [MS-RDPBCGR]*
</details>

---

### Pregunta 44

**En una plataforma VDI, ¿qué factor de red determina más la calidad percibida por el usuario?**

A) Exclusivamente el ancho de banda bruto del enlace
B) La latencia de ida y vuelta y su estabilidad, más que el ancho de banda bruto
C) El número de saltos de encaminamiento hasta el centro de datos

<details><summary>Respuesta</summary>

**Correcta: B) La latencia de ida y vuelta y su estabilidad, más que el ancho de banda bruto** Un enlace de gran caudal pero con latencia alta o fluctuante ofrece una experiencia peor que uno modesto y estable.

*Referencia: §3.3 [CITRIX-DOC] [PCOIP]*
</details>

---

### Pregunta 45

**¿Qué exige inevitablemente el modelo de escritorio no persistente?**

A) Gestionar el perfil y los datos del usuario fuera del escritorio, con contenedor de perfil y redirección de carpetas
B) Asignar a cada usuario un escritorio fijo con clones completos
C) Renunciar a la aplicación de directivas de configuración

<details><summary>Respuesta</summary>

**Correcta: A) Gestionar el perfil y los datos del usuario fuera del escritorio, con contenedor de perfil y redirección de carpetas** Como el escritorio se descarta al cerrar sesión, todo lo que no esté en la imagen o en el perfil externo se pierde.

*Referencia: §3.4 [FSLOGIX]*
</details>

---

### Pregunta 46

**¿Cuál es la principal ventaja operativa del escritorio no dedicado frente al dedicado?**

A) Que permite a cada usuario instalar su propio software
B) Que se gestiona y se parchea una sola imagen maestra en lugar de escritorio por escritorio
C) Que elimina la necesidad de disponer de un broker de conexiones

<details><summary>Respuesta</summary>

**Correcta: B) Que se gestiona y se parchea una sola imagen maestra en lugar de escritorio por escritorio** A ello se suman un ahorro de almacenamiento muy notable y un escritorio siempre limpio en cada inicio de sesión.

*Referencia: §3.4 [HORIZON-DOC] [CITRIX-DOC]*
</details>

---

### Pregunta 47

**¿Qué caracteriza a una infraestructura hiperconvergente (HCI)?**

A) Que los discos residen en los propios nodos de cómputo y un software distribuido los agrega y replica presentando un almacén único
B) Que separa el cómputo de una cabina externa mediante una red de almacenamiento dedicada
C) Que sustituye el hipervisor por un motor de contenedores

<details><summary>Respuesta</summary>

**Correcta: A) Que los discos residen en los propios nodos de cómputo y un software distribuido los agrega y replica presentando un almacén único** El crecimiento se produce añadiendo nodos, con lo que cómputo y almacenamiento escalan juntos.

*Referencia: §4.1 [VMWARE-DOC] [HYPERV-DOC]*
</details>

---

### Pregunta 48

**¿Por qué encaja especialmente bien la hiperconvergencia con VDI?**

A) Porque elimina la necesidad de proteger los datos con réplicas
B) Porque permite prescindir del broker de conexiones
C) Porque atiende con almacenamiento local rápido un patrón de entrada/salida muy exigente y muy repetitivo, deduplicable entre escritorios casi idénticos

<details><summary>Respuesta</summary>

**Correcta: C) Porque atiende con almacenamiento local rápido un patrón de entrada/salida muy exigente y muy repetitivo, deduplicable entre escritorios casi idénticos** Además, su crecimiento por nodos encaja con un despliegue VDI que se amplía por bloques de usuarios.

*Referencia: §4.1 [VMWARE-DOC]*
</details>

---

### Pregunta 49

**¿Cuántos segmentos permite identificar VXLAN y sobre qué protocolo encapsula?**

A) 4.094 segmentos, encapsulando sobre TCP
B) Unos 16 millones de segmentos mediante un identificador VNI de 24 bits, encapsulando sobre UDP
C) 65.535 segmentos, encapsulando sobre GRE

<details><summary>Respuesta</summary>

**Correcta: B) Unos 16 millones de segmentos mediante un identificador VNI de 24 bits, encapsulando sobre UDP** Los 4.094 segmentos son el límite de la VLAN 802.1Q, con identificador de 12 bits, que VXLAN viene precisamente a superar.

*Referencia: §4.2 [RFC7348]*
</details>

---

### Pregunta 50

**¿En qué consiste la arquitectura SDN?**

A) En sustituir los conmutadores físicos por conmutadores virtuales alojados en los anfitriones
B) En encapsular tramas de nivel 2 dentro de paquetes de nivel 3
C) En separar el plano de control del plano de datos, concentrando el primero en un controlador centralizado y programable

<details><summary>Respuesta</summary>

**Correcta: C) En separar el plano de control del plano de datos, concentrando el primero en un controlador centralizado y programable** Se articula con una interfaz norte hacia las aplicaciones y una interfaz sur hacia los dispositivos, siendo OpenFlow el protocolo histórico de referencia para esta última.

*Referencia: §4.2 [ONF-SDN]*
</details>

---

### Pregunta 51

**¿Qué protege la microsegmentación que un cortafuegos perimetral no puede proteger?**

A) El tráfico este-oeste entre servidores dentro del propio centro de datos, que nunca atraviesa el perímetro
B) El tráfico cifrado con TLS procedente de Internet
C) El acceso físico a los armarios del centro de proceso de datos

<details><summary>Respuesta</summary>

**Correcta: A) El tráfico este-oeste entre servidores dentro del propio centro de datos, que nunca atraviesa el perímetro** Es precisamente el que un atacante utiliza para moverse lateralmente tras comprometer un primer sistema.

*Referencia: §4.2 [NIST-SP800-125]*
</details>

---

### Pregunta 52

**¿Qué es la proliferación descontrolada de máquinas virtuales (VM sprawl) y por qué preocupa?**

A) Es la migración automática excesiva de máquinas entre anfitriones, que satura la red de migración
B) Es la acumulación de máquinas, plantillas e instantáneas que nadie da de baja, consumiendo licencias, almacenamiento y superficie de ataque sin parchear
C) Es el crecimiento de los discos con aprovisionamiento fino por encima de lo previsto

<details><summary>Respuesta</summary>

**Correcta: B) Es la acumulación de máquinas, plantillas e instantáneas que nadie da de baja, consumiendo licencias, almacenamiento y superficie de ataque sin parchear** Sus contramedidas son de gestión: etiquetado obligatorio con responsable y finalidad, fecha de caducidad de los entornos temporales, revisión periódica y procedimiento formal de baja.

*Referencia: §4.3 [NIST-SP800-125]*
</details>

---

### Pregunta 53

**¿Cuáles son las cinco dimensiones de seguridad del Esquema Nacional de Seguridad?**

A) Confidencialidad, integridad, disponibilidad, eficiencia y sostenibilidad
B) Prevención, detección, respuesta, recuperación y mejora continua
C) Disponibilidad, integridad, confidencialidad, autenticidad y trazabilidad

<details><summary>Respuesta</summary>

**Correcta: C) Disponibilidad, integridad, confidencialidad, autenticidad y trazabilidad** Se recuerdan con las siglas D-I-C-A-T. Prevención, detección, respuesta y conservación son principios básicos, no dimensiones.

*Referencia: §5.1 [ENS]*
</details>

---

### Pregunta 54

**Según el Real Decreto 311/2022, ¿con qué periodicidad deben auditarse los sistemas de categoría media y alta?**

A) Al menos cada dos años, mediante auditoría ordinaria
B) Anualmente, mediante autoevaluación
C) Solo cuando se produzca un cambio sustancial en el sistema

<details><summary>Respuesta</summary>

**Correcta: A) Al menos cada dos años, mediante auditoría ordinaria** Los sistemas de categoría básica pueden acreditar su conformidad mediante autoevaluación y la correspondiente declaración.

*Referencia: §5.1 [ENS]*
</details>

---

### Pregunta 55

**¿Cuál es la buena práctica de seguridad respecto a la red de gestión de un entorno virtualizado?**

A) Compartirla con la red de máquinas virtuales para simplificar el direccionamiento
B) Publicarla a través de la pasarela de acceso seguro para permitir la administración desde Internet
C) Mantenerla separada de los demás flujos y nunca accesible desde la red de usuarios

<details><summary>Respuesta</summary>

**Correcta: C) Mantenerla separada de los demás flujos y nunca accesible desde la red de usuarios** Es una recomendación expresa del NIST, y en el entorno de un ayuntamiento resulta exigible por la vía de las medidas de separación de flujos y de control de accesos del ENS.

*Referencia: §5.1 [NIST-SP800-125] [ENS]*
</details>

---

### Pregunta 56

**¿Por qué el compromiso del plano de gestión es tan grave en un entorno virtualizado?**

A) Porque impide aplicar parches al hipervisor hasta que se restablezca
B) Porque quien controla la consola controla todas las máquinas virtuales sin necesidad de entrar en ninguna de ellas
C) Porque provoca automáticamente el escape de las máquinas virtuales hacia el anfitrión

<details><summary>Respuesta</summary>

**Correcta: B) Porque quien controla la consola controla todas las máquinas virtuales sin necesidad de entrar en ninguna de ellas** Es, en la práctica, el vector más rentable para un atacante, y por eso exige acceso nominal, autenticación reforzada, mínimo privilegio y registro de actividad externo.

*Referencia: §5.1 [NIST-SP800-125]*
</details>

---

### Pregunta 57

**¿Qué exige el artículo 32 del RGPD que afecta directamente a la política de copias de un entorno virtualizado?**

A) La capacidad de restaurar la disponibilidad y el acceso a los datos tras un incidente, y la verificación periódica de la eficacia de las medidas
B) La conservación de las copias durante un mínimo de cinco años
C) El almacenamiento de las copias exclusivamente en soporte cifrado extraíble

<details><summary>Respuesta</summary>

**Correcta: A) La capacidad de restaurar la disponibilidad y el acceso a los datos tras un incidente, y la verificación periódica de la eficacia de las medidas** Es el fundamento jurídico directo de las pruebas de restauración documentadas; el artículo cita además la seudonimización y el cifrado.

*Referencia: §5.2 y §5.3 [RGPD]*
</details>

---

### Pregunta 58

**Se solicita un clon de la máquina virtual del padrón para un entorno de pruebas. ¿Cuál es la actuación correcta?**

A) Clonarla tal cual, ya que se trata del mismo responsable del tratamiento
B) Aplicar el principio de minimización, clonando con datos seudonimizados o sintéticos, con autorización registrada, mismas medidas de seguridad y fecha de destrucción
C) Denegar cualquier copia, porque los datos del padrón no pueden salir de producción bajo ningún concepto

<details><summary>Respuesta</summary>

**Correcta: B) Aplicar el principio de minimización, clonando con datos seudonimizados o sintéticos, con autorización registrada, mismas medidas de seguridad y fecha de destrucción** Un clon de producción olvidado en un entorno menos protegido es uno de los orígenes más frecuentes de brecha de datos.

*Referencia: §5.2 [RGPD] [LOPDGDD]*
</details>

---

### Pregunta 59

**¿Qué diferencia el RTO del RPO?**

A) El RTO mide los datos perdidos y el RPO el tiempo de restablecimiento
B) Ambos miden lo mismo, pero el RTO se expresa en horas y el RPO en minutos
C) El RTO es el tiempo máximo tolerable hasta restablecer el servicio y el RPO la cantidad máxima tolerable de datos perdidos

<details><summary>Respuesta</summary>

**Correcta: C) El RTO es el tiempo máximo tolerable hasta restablecer el servicio y el RPO la cantidad máxima tolerable de datos perdidos** El RTO mira hacia delante y el RPO hacia atrás; ambos los fija el análisis de impacto en el negocio, no la decisión técnica.

*Referencia: §5.3 [NIST-SP800-34]*
</details>

---

### Pregunta 60

**¿Cómo se calcula el indicador PUE de un centro de datos y qué valor es el óptimo?**

A) Energía total del centro de datos dividida entre la energía del equipamiento TI; el óptimo teórico es 1,0
B) Energía del equipamiento TI dividida entre la energía total; el óptimo es el valor más alto posible
C) Número de máquinas virtuales por anfitrión; el óptimo depende del perfil de carga

<details><summary>Respuesta</summary>

**Correcta: A) Energía total del centro de datos dividida entre la energía del equipamiento TI; el óptimo teórico es 1,0** Está definido en la norma ISO/IEC 30134-2. Cuidado: un PUE bajo mide la eficiencia de la instalación, no si los servidores están bien aprovechados, así que debe leerse junto con la utilización real. La opción C describe la ratio de consolidación.

*Referencia: §5.4 [ISO30134]*
</details>
