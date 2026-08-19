# Tema 28 — Casos Prácticos

> **Título oficial**: Virtualización de sistemas y virtualización de puestos de usuario.
>
> **Formato**: 3 casos prácticos sobre supuestos del Ayuntamiento de Madrid. Cada caso suma **10 puntos**.
> **Nivel**: C1 — Técnico Auxiliar TIC, Ayuntamiento de Madrid

Los tres casos recorren el supuesto de referencia del tema (ver tema-28-contenido.md, «Convenciones»): el **Caso 1** trabaja la **consolidación y el dimensionamiento del clúster de servidores**; el **Caso 2**, el **diseño de la plataforma de puesto de trabajo virtual**; y el **Caso 3**, la **seguridad, el cumplimiento normativo y la continuidad** del entorno virtualizado.

---

## Caso 1 — Consolidación del centro de proceso de datos municipal

### Enunciado

El Ayuntamiento mantiene **34 servidores físicos** dedicados cada uno a una aplicación (padrón, registro, gestor de expedientes, portal interno, servidores de impresión, entornos de preproducción…). La utilización media medida durante tres meses es del **11 % de CPU** y del **28 % de memoria**. Los equipos tienen entre cinco y nueve años, el mantenimiento de cinco de ellos ya no está cubierto por el fabricante y la sala está al límite de potencia eléctrica contratada.

Se plantea un proyecto de **consolidación** sobre un clúster de virtualización. Los servicios de padrón y registro se consideran **críticos** y deben poder mantenerse durante el mantenimiento del hardware. El proyecto debe justificarse también en términos de eficiencia energética.

Datos para el dimensionamiento: cada anfitrión candidato dispone de **2 procesadores de 16 núcleos** (32 núcleos físicos) y **512 GB de memoria**. La suma de vCPU necesarias por las 34 máquinas virtuales previstas es de **96 vCPU** y la suma de memoria asignada, de **680 GB**.

### Cuestiones

**Cuestión 1 — Justificación de la consolidación (2 puntos).** Enumere **cuatro ventajas** de la virtualización aplicables a este supuesto concreto y **dos riesgos** que el proyecto introduce, indicando para cada riesgo su contramedida.

**Cuestión 2 — Dimensionamiento del clúster (3 puntos).** Calcule cuántos anfitriones son necesarios, tomando una ratio de consolidación de **4 vCPU por núcleo físico**, sin sobreasignar memoria y **cumpliendo la regla N+1**. Razone cuál de los dos recursos es el que realmente limita.

**Cuestión 3 — Continuidad durante el mantenimiento (3 puntos).** Explique qué mecanismos permiten parchear el hardware de un anfitrión **sin ventana de parada** de los servicios de padrón y registro, e indique los **requisitos técnicos** que deben cumplirse para que funcionen. Añada qué política protege al par redundante del registro frente a la caída de un único servidor físico.

**Cuestión 4 — Eficiencia energética (2 puntos).** Indique **tres palancas de ahorro energético** que aporta el proyecto y **el indicador normalizado** con el que se mide la eficiencia de la instalación, señalando su limitación.

### Solución orientativa

- **C1**: (§1.1 y §5.4) Cuatro ventajas aplicables: (a) **consolidación y aprovechamiento** —una utilización del 11 % de CPU es el caso de libro—; (b) **independencia del hardware**, que permite retirar los cinco equipos sin soporte sin reinstalar sistemas ni aplicaciones; (c) **encapsulación**, que convierte cada servidor en un conjunto de ficheros copiable y restaurable; (d) **agilidad de aprovisionamiento** para preproducción, desde plantilla y en minutos. Dos riesgos con su contramedida: **concentración del riesgo** (la caída de un anfitrión afecta a muchos servicios) → **clúster con capacidad N+1, HA y reglas de antiafinidad**; y **nueva superficie de ataque en el hipervisor y en el plano de gestión** → **red de gestión separada, acceso privilegiado nominal con autenticación reforzada, bastionado y parcheo prioritario** (§5.1). También serían válidos la proliferación descontrolada (→ etiquetado y ciclo de vida, §4.3) y el riesgo de licenciamiento.

- **C2**: (§2.2) Por **CPU**: 32 núcleos × 4 = **128 vCPU por anfitrión**; 96 vCPU necesarias → **1 anfitrión bastaría**. Por **memoria**, sin sobreasignar: 680 GB / 512 GB = **1,33 → 2 anfitriones**. El recurso limitante es, por tanto, **la memoria**, que es lo habitual en consolidación. Aplicando **N+1** sobre el resultado (2 + 1) → **3 anfitriones**. Con 3 anfitriones, la pérdida de uno deja 1.024 GB, suficientes para los 680 GB comprometidos, y 256 vCPU frente a las 96 necesarias. Se valorará que el opositor señale expresamente que **sobreasignar CPU es admisible y sobreasignar memoria es peligroso**, y que debe reservarse además memoria para el propio hipervisor.

- **C3**: (§2.4) Mecanismos: **modo mantenimiento** del anfitrión, que **vacía automáticamente** sus máquinas virtuales hacia los demás nodos mediante **migración en caliente**, sin apagarlas; la migración usa **precopia iterativa** de memoria y solo detiene la máquina unos milisegundos en la conmutación final. Requisitos técnicos: **compatibilidad de CPU** entre anfitriones (o modo de compatibilidad), **red compartida y una red dedicada de migración**, **acceso de todos los anfitriones al mismo almacenamiento** y **ausencia de dispositivos asignados directamente** en passthrough o SR-IOV en las máquinas a migrar. Para el par redundante del registro: **regla de antiafinidad**, que impide que sus dos nodos se ejecuten en el mismo anfitrión; complementada con **HA** para el reinicio automático ante un fallo no planificado (recordando que HA **sí implica corte**, a diferencia de la migración).

- **C4**: (§5.4) Tres palancas: (a) **elevar la utilización media** de los anfitriones, ya que el consumo de un servidor no es proporcional a su carga y hay un consumo de base considerable; (b) **gestión dinámica de energía**, concentrando máquinas en horas valle y **apagando anfitriones sobrantes**; (c) **retirada de máquinas virtuales inactivas y de instantáneas antiguas**, que consumen memoria, almacenamiento y ventana de copia. También son válidas la reducción de la carga de refrigeración y la menor generación de residuos electrónicos. Indicador: **PUE** (*Power Usage Effectiveness*, ISO/IEC 30134-2) = energía total del centro de datos / energía del equipamiento TI, **óptimo teórico 1,0**. Limitación: mide la eficiencia **de la instalación**, no si los servidores están bien aprovechados —un centro con PUE excelente lleno de servidores ociosos sigue derrochando—, por lo que debe leerse junto con la **utilización real** y la ratio de consolidación.

### Criterios de evaluación

| Criterio | Puntos |
|---|---|
| Cuatro ventajas aplicadas al supuesto y dos riesgos con su contramedida correspondiente | 2 |
| Cálculo correcto por CPU y por memoria, identificación de la memoria como recurso limitante y aplicación de N+1 | 3 |
| Modo mantenimiento y migración en caliente correctamente explicados, con sus cuatro requisitos, más la regla de antiafinidad y la distinción respecto a HA | 3 |
| Tres palancas de ahorro, definición correcta del PUE y mención de su limitación | 2 |

---

## Caso 2 — Diseño del puesto de trabajo virtual para las oficinas de atención a la ciudadanía

### Enunciado

El Ayuntamiento quiere renovar el puesto de trabajo de **900 empleados** repartidos en oficinas de atención a la ciudadanía y servicios de distrito. El análisis del parque arroja tres colectivos:

- **700 usuarios de mostrador**: utilizan siempre las mismas cuatro aplicaciones (padrón, registro, gestor de expedientes y ofimática), no instalan software y firman documentos con **certificado en tarjeta criptográfica**.
- **150 usuarios técnicos y de gestión**: ofimática intensa, varias aplicaciones de gestión y necesidad ocasional de instalar herramientas propias.
- **50 usuarios de cartografía y planeamiento**: sistemas de información geográfica con carga gráfica elevada.

Los equipos actuales tienen entre seis y ocho años. Se detecta que **el 100 % de los usuarios inicia sesión entre las 8:00 y las 8:20**. Dos distritos tienen enlaces de datos con **latencia elevada y variable**.

### Cuestiones

**Cuestión 1 — Asignación de modelo por colectivo (3 puntos).** Asigne a cada uno de los tres colectivos el modelo de virtualización del puesto más adecuado y justifique cada elección con **dos criterios**.

**Cuestión 2 — Persistencia y perfil (3 puntos).** Para el colectivo de mostrador, decida la estrategia de persistencia y enumere las **piezas indispensables** que el modelo elegido exige para que el usuario no pierda su entorno. Explique además cómo se gestiona a partir de entonces la aplicación de un parche crítico del sistema operativo del puesto.

**Cuestión 3 — Tormenta de arranque (2 puntos).** Identifique el fenómeno que provocará el patrón de inicio de sesión descrito, explique **por qué** ocurre y proponga **tres mitigaciones**.

**Cuestión 4 — Enlaces con latencia y periféricos (2 puntos).** Indique qué factor de red determina la calidad percibida en los dos distritos problemáticos y proponga **dos medidas** de optimización del protocolo. Señale además qué mecanismo permite firmar con la tarjeta criptográfica desde un escritorio virtual y qué riesgo introduce esa familia de mecanismos.

### Solución orientativa

- **C1**: (§3.1)
  - **700 usuarios de mostrador → escritorios basados en sesiones (RDSH)**. Criterios: (a) es un **colectivo numeroso y homogéneo** con un conjunto fijo de aplicaciones y sin instalación de software, que es el caso natural del modelo; (b) su **densidad y su coste por puesto** son claramente mejores que los de VDI, al compartir un único sistema operativo de servidor.
  - **150 usuarios técnicos y de gestión → VDI**. Criterios: (a) necesitan **personalización e instalación de herramientas propias**, imposible en un sistema compartido; (b) el **aislamiento** entre usuarios es mayor y la compatibilidad es la de un sistema operativo de cliente.
  - **50 usuarios de cartografía → VDI con GPU virtual**. Criterios: (a) la **carga gráfica** exige aceleración, que se resuelve repartiendo la GPU entre máquinas virtuales; (b) el **perfil de recursos** (4-8 vCPU y 16 GB) es incompatible con la densidad de un servidor de sesiones y degradaría a los demás usuarios.
  - Se valorará que se señale que **los tres modelos conviven** en una misma plataforma y que la **virtualización de aplicaciones** puede usarse dentro de cualquiera de ellos para resolver conflictos entre versiones.

- **C2**: (§3.4 y §3.2.2) Estrategia: **escritorios no dedicados o no persistentes**, por tratarse de 700 puestos homogéneos: una sola imagen que parchear, ahorro grande de almacenamiento y escritorio limpio en cada inicio de sesión, lo que además refuerza la confidencialidad de los datos del padrón que se manejan en el mostrador. Piezas indispensables: (a) **contenedor de perfil de usuario** en disco virtual, que se monta al iniciar sesión y se desmonta al cerrarla; (b) **redirección de carpetas** personales a un recurso de red centralizado y respaldado; (c) **capas de aplicación** para las aplicaciones específicas de un colectivo, evitando multiplicar imágenes maestras; (d) **directivas de configuración** aplicadas en cada inicio, en lugar de guardadas en el escritorio. Parche crítico: se aplica **una sola vez sobre la imagen maestra** —se clona a una copia de trabajo, se parchea, se valida con un grupo piloto, se sella como nueva versión y se publica—, y los escritorios no persistentes **la adoptan en su siguiente reinicio**; si resulta defectuosa, se **revierte a la versión anterior** con la misma operación.

- **C3**: (§3.1.1) Fenómeno: **tormenta de arranque** (*boot storm*) y su variante la **tormenta de inicio de sesión** (*login storm*). Ocurre porque cientos de escritorios **arrancan, cargan perfiles y actualizan el antivirus simultáneamente**, generando un pico de entrada/salida de lectura y escritura que el almacenamiento no absorbe si se ha dimensionado por consumo medio. Tres mitigaciones válidas entre: **almacenamiento de estado sólido** o hiperconvergente con caché de lectura de la imagen maestra; **preencendido programado y escalonado** de escritorios antes de las 8:00; **clones instantáneos**, que se derivan de una plantilla ya arrancada en memoria; **análisis antivirus desfasado en el tiempo y con exclusiones adecuadas**; y **deduplicación**, que reduce el volumen real leído al ser los escritorios casi idénticos.

- **C4**: (§3.3) Factor determinante: la **latencia de ida y vuelta y su estabilidad** (fluctuación), **más que el ancho de banda bruto**; un enlace de gran caudal pero inestable ofrece peor experiencia que uno modesto y estable. Dos medidas: (a) usar **transporte UDP** en el protocolo de representación, que tolera mejor la pérdida y la latencia para la imagen en movimiento, con compresión adaptativa y actualización solo de las regiones modificadas de la pantalla; (b) **descargar en el cliente** la reproducción de contenido multimedia y redirigir los flujos de las herramientas de comunicación para que vayan directos entre interlocutores en lugar de dar un rodeo por el centro de datos. Firma con tarjeta: se resuelve con la **redirección de lectores de tarjeta inteligente por canal virtual** del protocolo. Riesgo: **cada canal de redirección habilitado —portapapeles, unidades locales, impresión, USB— es una vía potencial de fuga de información**, por lo que deben estar **deshabilitados por defecto y habilitados por excepción justificada**, en aplicación del principio de protección de datos desde el diseño y por defecto (art. 25 del RGPD).

### Criterios de evaluación

| Criterio | Puntos |
|---|---|
| Asignación correcta de los tres modelos con dos criterios justificados en cada caso | 3 |
| Elección razonada del modelo no persistente y enumeración de las piezas de gestión del perfil, más el ciclo de vida de la imagen maestra ante un parche | 3 |
| Identificación y explicación de la tormenta de arranque con tres mitigaciones válidas | 2 |
| Latencia como factor determinante, dos medidas de optimización, redirección de la tarjeta y riesgo de los canales de redirección | 2 |

---

## Caso 3 — Seguridad, cumplimiento y continuidad del entorno virtualizado

### Enunciado

Una revisión del entorno virtualizado municipal, previa a la auditoría del ENS, detecta la siguiente situación:

1. La **consola de gestión** del clúster es accesible desde la red de usuarios y **tres administradores comparten** una misma cuenta con contraseña conocida por el equipo.
2. Existen **doce instantáneas** con más de seis meses de antigüedad y **cuarenta máquinas virtuales apagadas** desde hace más de un año, ninguna de ellas parcheada.
3. Las **copias de seguridad** se almacenan en un repositorio del propio clúster, administrado con las mismas credenciales que el hipervisor, y **nunca se ha realizado una prueba de restauración**.
4. La sede electrónica tiene fijados un **RPO de 15 minutos** y un **RTO de 1 hora**; la protección actual consiste en una **copia completa nocturna** en disco.
5. Un equipo de desarrollo trabaja con un **clon de la máquina virtual del padrón** con datos reales, creado hace ocho meses en un entorno de pruebas sin las medidas de producción.

El sistema está categorizado como de **categoría MEDIA** conforme al Anexo I del ENS.

### Cuestiones

**Cuestión 1 — Hallazgos de seguridad del plano de gestión (3 puntos).** Analice los puntos 1 y 2 del enunciado: explique **por qué** son graves y proponga la corrección de cada uno, relacionándola con el tipo de medida del ENS que la exige.

**Cuestión 2 — Copias de seguridad (2 puntos).** Analice el punto 3: enuncie la regla que se está incumpliendo, explique el riesgo concreto de que el repositorio comparta dominio de administración con el hipervisor y justifique jurídicamente la obligación de realizar pruebas de restauración.

**Cuestión 3 — Diseño de la continuidad (3 puntos).** Analice el punto 4: indique si la protección actual satisface los objetivos declarados, razone por qué y proponga la combinación técnica que sí los satisface. Precise quién debe fijar el RTO y el RPO y por qué la copia de seguridad sigue siendo necesaria aunque exista replicación.

**Cuestión 4 — Protección de datos personales (2 puntos).** Analice el punto 5 e indique la actuación correcta, citando los principios y preceptos aplicables y las cuatro condiciones que debería cumplir el entorno de pruebas.

### Solución orientativa

- **C1**: (§5.1 y §4.3)
  - **Consola accesible desde la red de usuarios**: es el activo más crítico del entorno, porque **quien controla la consola controla todas las máquinas virtuales sin necesidad de entrar en ninguna**. Corrección: **segregar la red de gestión** en un segmento propio, no accesible desde la red de usuarios, y publicarla —si es imprescindible el acceso remoto— solo a través de un mecanismo de acceso remoto seguro con doble factor. Se corresponde con las medidas de **separación de flujos en la red** (marco de medidas de protección) y de **control de acceso** (marco operacional) del Anexo II del ENS.
  - **Cuenta compartida entre tres administradores**: destruye la **trazabilidad**, que es una de las cinco dimensiones de seguridad del ENS: ante cualquier operación no es posible determinar quién la ejecutó. Corrección: **cuentas nominales individuales** con **autenticación reforzada**, **roles con mínimo privilegio** (control de acceso basado en roles delegado por unidad o aplicación) y **registro de actividad exportado a un repositorio centralizado externo** al propio entorno, para que no pueda alterarse desde él.
  - **Instantáneas antiguas y máquinas apagadas sin parchear**: es **proliferación descontrolada** (*VM sprawl*). Las instantáneas antiguas **degradan el rendimiento y pueden llenar el almacén de datos**, deteniendo máquinas virtuales; las máquinas apagadas **no reciben parches** y se convierten en sistemas vulnerables el día que alguien las encienda. Corrección: consolidar y eliminar las instantáneas, incluir las máquinas latentes en los ciclos de actualización o **darlas de baja formalmente** previa comprobación con la unidad responsable, e implantar **etiquetado obligatorio** (responsable, aplicación, entorno, fecha de revisión) y **fecha de caducidad** para los entornos temporales. Enlaza con las medidas de **mantenimiento y actualizaciones de seguridad** y de **gestión de la configuración y de cambios**.

- **C2**: (§5.3) Se incumple la **regla 3-2-1**: **3** copias de los datos, en **2** tipos de soporte distintos, con **1** de ellas **fuera del emplazamiento**; en su versión actual debe ampliarse con **1 copia inmutable o desconectada** (*air gap*) frente al secuestro de datos. Riesgo concreto de compartir dominio de administración: un **compromiso del plano de gestión se lleva por delante, con las mismas credenciales, los datos y su respaldo a la vez**, que es exactamente lo que busca un ataque de *ransomware* moderno, el cual localiza y cifra las copias antes de actuar. Justificación jurídica de las pruebas: el **artículo 32 del RGPD** exige la **capacidad de restaurar la disponibilidad y el acceso** a los datos tras un incidente y la **verificación periódica de la eficacia de las medidas**; el **ENS** exige pruebas periódicas de continuidad. Formulación esperada: **una copia que nunca se ha restaurado no es una copia, es una suposición**.

- **C3**: (§5.3) La protección actual **no satisface** los objetivos: una copia completa **nocturna** implica un **RPO de hasta 24 horas**, muy por encima de los 15 minutos declarados, y la **restauración completa** de la máquina desde el repositorio consume, en general, más de una hora, incumpliendo también el RTO. Combinación que sí los satisface: **replicación asíncrona de las máquinas virtuales a un segundo emplazamiento con intervalo igual o inferior a 15 minutos** (o replicación de almacenamiento equivalente), más un **plan de recuperación orquestado** que defina el **orden de arranque, las dependencias y el cambio de direccionamiento**, capaz de completarse dentro de la hora, y **probado periódicamente en una red aislada** sin afectar a producción. Es admisible añadir la **recuperación instantánea** —arrancar la máquina virtual directamente desde el repositorio de copias y migrarla en caliente después— como mecanismo complementario de mejora del RTO. Quién los fija: el **análisis de impacto en el negocio (BIA)**, es decir, la organización responsable del servicio, **no el técnico**; de ellos se derivan la tecnología y el coste, nunca al revés. Por qué sigue haciendo falta la copia: **la réplica no protege frente a un borrado lógico, una corrupción o un cifrado malicioso**, porque esos cambios **se replicarían** al segundo emplazamiento; réplica y copia cubren riesgos distintos y son complementarias.

- **C4**: (§5.2) Actuación correcta: **no mantener el clon con datos reales**. Debe aplicarse el **principio de minimización de datos** (art. 5.1.c del RGPD) y la **protección de datos desde el diseño y por defecto** (art. 25), sustituyendo el clon por un entorno con **datos seudonimizados o sintéticos**; el artículo 32 exige además medidas de seguridad adecuadas al riesgo, y en el sector público la **disposición adicional primera de la LOPDGDD** remite a las medidas del **ENS**. Un clon de producción olvidado en un entorno menos protegido es uno de los orígenes más frecuentes de brecha de datos, con la obligación de notificación de los **artículos 33 y 34 del RGPD** (a la autoridad de control sin dilación indebida y, de ser posible, en **72 horas**). Cuatro condiciones exigibles al entorno de pruebas: (a) **datos seudonimizados o sintéticos**, no reales; (b) **autorización documentada y registrada** de la unidad responsable del tratamiento; (c) **mismas medidas de seguridad** que el entorno de origen mientras el clon exista; (d) **fecha de destrucción fijada** y borrado seguro que alcance también a instantáneas, réplicas y copias de seguridad del clon.

### Criterios de evaluación

| Criterio | Puntos |
|---|---|
| Análisis correcto de los tres hallazgos del plano de gestión, con corrección y vinculación a las medidas del ENS | 3 |
| Regla 3-2-1 enunciada, riesgo del dominio de administración compartido y fundamento jurídico de las pruebas de restauración | 2 |
| Diagnóstico correcto del incumplimiento de RPO y RTO, propuesta técnica válida, atribución del BIA y complementariedad de réplica y copia | 3 |
| Actuación correcta sobre el clon con datos reales, preceptos citados y cuatro condiciones del entorno de pruebas | 2 |
