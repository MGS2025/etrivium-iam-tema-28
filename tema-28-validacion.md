# Tema 28 — Checklist de Validación

> **Título oficial**: Virtualización de sistemas y virtualización de puestos de usuario.
> **Versión**: v1.0 — Pendiente validación
> **Fecha**: 2026-08-20
> **Revisores**: María y Ana (eTrivium) · revisión técnica IAM (Jesús Cuadrado)
> **Instrucciones**: marcar cada ítem. Los cambios no se guardan en la web (imprimir o exportar a PDF si se desea fijarlos).

---

## 1. Cobertura del temario oficial

- [ ] **Concepto, evolución y principios**: definición, vocabulario (anfitrión/huésped/hipervisor/VM), historia desde CP-67, principios de Popek y Goldberg y teorema de virtualizabilidad, ventajas e inconvenientes — §1.1
- [ ] **Arquitectura clásica y nivel de abstracción**: niveles de la pila donde se inserta cada virtualización, anillos de privilegio — §1.2
- [ ] **Virtualización total o completa**: traducción binaria, ejecución directa, reubicación de anillos — §1.2.1
- [ ] **Paravirtualización**: llamadas al hipervisor, `dom0`/`domU`, ventajas y límites — §1.2.2
- [ ] **Virtualización asistida por hardware**: VT-x/VMCS, AMD-V/VMCB, EL2, EPT/NPT, IOMMU (VT-d/AMD-Vi) — §1.2.3
- [ ] **Hipervisores de Tipo 1** y **de Tipo 2**: criterio de clasificación, características, ejemplos y casos discutidos (KVM, Hyper-V) — §1.3.1 y §1.3.2
- [ ] **Arquitectura y componentes** de un entorno de virtualización de servidores: clúster, almacenamiento compartido, red, servidor de gestión, formatos de disco y OVF/OVA — §2.1
- [ ] **Asignación y gestión de recursos**: sobreasignación, reserva/límite/participación — §2.2
- [ ] **Planificación de CPU y memoria virtualizada**: vCPU, tiempo de espera, coplanificación, NUMA, doble traducción, cuatro técnicas de recuperación de memoria — §2.2.1
- [ ] **Entradas y salidas y controladores paravirtualizados**: emulación, virtio, passthrough y SR-IOV — §2.2.2
- [ ] **Contenedores y aislamiento de procesos**: namespaces, cgroups, capacidades, seccomp, OCI, orquestación y riesgos — §2.3
- [ ] **Comparativa hipervisor frente a contenedores** — §2.3.1
- [ ] **Alta disponibilidad, balanceo y migración en caliente**: precopia, requisitos, HA, FT, DRS, afinidad/antiafinidad, modo mantenimiento — §2.4
- [ ] **Modelos de virtualización en el cliente**: VDI, sesiones, aplicaciones y virtualización en el propio cliente — §3.1 a §3.1.3
- [ ] **Componentes de la arquitectura VDI**, **broker de accesos** y **gestor de imágenes y aprovisionamiento** — §3.2 a §3.2.2
- [ ] **Protocolos de representación y transporte**: RDP, ICA/HDX, PCoIP, Blast, SPICE, RFB/VNC y técnicas de optimización — §3.3
- [ ] **Estrategias de persistencia**: dedicado y no dedicado, con la gestión del perfil que el modelo exige — §3.4
- [ ] **Virtualización del almacenamiento e hiperconvergencia** — §4.1
- [ ] **Virtualización de redes y SDN**: conmutador virtual, VXLAN/Geneve, SDN, NFV y microsegmentación — §4.2
- [ ] **Gestión centralizada, monitorización y orquestación** — §4.3
- [ ] **ENS en entornos virtualizados** — §5.1
- [ ] **RGPD y LOPDGDD** — §5.2
- [ ] **Continuidad, copias y recuperación ante desastres** — §5.3
- [ ] **Eficiencia energética, consolidación y sostenibilidad** — §5.4

## 2. Contenido teórico

- [ ] El nivel de profundidad (5 secciones, 31 epígrafes, ~18.700 palabras) es adecuado para C1 (¿hay que ampliar o recortar alguna sección?). **Nota**: es el tema más extenso de la serie técnica hasta la fecha, medido con `wc -w`
- [ ] La distinción entre las **tres técnicas** (total / paravirtualización / asistida por hardware) queda nítida, y en particular el matiz de que la paravirtualización **de dispositivos** (virtio) sigue vigente aunque la del núcleo esté desplazada
- [ ] La distinción entre **migración en caliente, HA y FT** queda inequívoca (es el error más frecuente del bloque)
- [ ] La clasificación de **KVM e Hyper-V como Tipo 1** se presenta con el matiz suficiente: ¿se comparte este criterio o el IAM prefiere presentarlo como caso discutido sin decantarse?
- [ ] Las definiciones de EPT/NPT, IOMMU, SR-IOV, ballooning, VNI, RTO/RPO y PUE son correctas y están bien diferenciadas
- [ ] La decisión de tratar los **productos comerciales solo como ilustración** de conceptos, y no como contenido, es la adecuada dada la volatilidad del sector (cambios de propietario y de licencia de las plataformas de virtualización en los últimos años)
- [ ] ⚠️ **Punto a confirmar por el IAM**: el contenido afirma que el **ENS no dedica un grupo de medidas exclusivo a la virtualización**, sino que se aplican las medidas generales de los tres marcos del Anexo II a hipervisor, máquinas virtuales y red virtual. Conviene que la revisión técnica lo confirme o matice contra el texto del RD 311/2022
- [ ] ⚠️ **Punto a confirmar**: las referencias a la **Directiva (UE) 2023/1791** (información sobre rendimiento energético de centros de datos) se citan de forma general, sin detallar umbrales ni plazos de transposición. ¿Es suficiente para C1 o conviene concretar?
- [ ] La frontera con los Temas 11 (arquitectura de ordenadores), 12 (periféricos y almacenamiento), 14 (sistemas operativos), 25 (puesto de usuario y seguridad), 26 (almacenamiento, su virtualización y copias), 27 (administración del SO), 29 (control remoto de puesto), 30 (administración de redes), 31 (cloud), 32 (seguridad de sistemas), 33/34 (comunicaciones y TCP/IP), 36 (seguridad en redes), 37 (redes locales) y 39 (ENS/ENI) está clara y sin duplicidades innecesarias
- [ ] **Solapamiento crítico a validar**: el **Tema 26** cubre «sistemas de almacenamiento y su virtualización» y las copias de seguridad. §4.1 y §5.3 se han limitado deliberadamente a lo que el entorno virtualizado **necesita** de ellos, remitiendo al T26 para el detalle. ¿Es el reparto correcto?
- [ ] Los ejemplos Ayto Madrid (CPD municipal, oficinas de atención, padrón) son verosímiles y coherentes entre secciones

## 3. Fuentes

- [ ] Todas las afirmaciones técnicas están respaldadas por fuente Tier 1 (Popek y Goldberg, Intel, AMD, Arm, PCI-SIG, OASIS, OCI, IETF, ONF, ETSI, NIST, ISO, BOE)
- [ ] Las referencias inline se corresponden con `tema-28-fuentes.md`
- [ ] Atribuciones históricas correctas (CP-67 y VM/370 en los años sesenta y setenta, Popek y Goldberg 1974, Robin e Irvine 2000 con las 17 instrucciones, Xen 2003, Intel VT-x 2005, AMD-V 2006, Docker 2013, Kubernetes 2014)
- [ ] Citas normativas correctas: RD 311/2022 (ENS), RD 4/2010 (ENI), Reglamento (UE) 2016/679, LO 3/2018, Ley 9/2017, Directiva (UE) 2023/1791, ISO/IEC 30134-2, ISO 22301, NIST SP 800-34/125/190

## 4. Test (60 preguntas)

- [ ] Cada pregunta tiene una sola respuesta correcta e inequívoca
- [ ] Los distractores (A/B/C) son plausibles
- [ ] La distribución de la opción correcta entre A/B/C está equilibrada (**verificada 20/20/20** por el generador)
- [ ] Las explicaciones y referencias de cada respuesta son correctas
- [ ] El reparto por bloques (12 / 16 / 16 / 8 / 8) es proporcionado al peso de cada sección

## 5. Casos prácticos (3)

- [ ] Realistas y propios del Ayuntamiento (consolidación del CPD; puesto de trabajo virtual de las oficinas; seguridad, cumplimiento y continuidad)
- [ ] Soluciones orientativas técnicamente correctas
- [ ] **Los cálculos del Caso 1 son correctos y reproducibles** (128 vCPU por anfitrión; memoria como recurso limitante; 2 anfitriones + 1 por regla N+1 = 3)
- [ ] La puntuación de cada caso suma 10 puntos

## 6. Diagramas (15 SVG)

- [ ] Cada diagrama es correcto y legible (también impreso en blanco y negro)
- [ ] Accesibilidad: todos tienen `role="img"` y `aria-label` con las tildes correctas
- [ ] Sin desbordes de texto ni colisiones de estilo entre SVG (clases con sufijo único, QA de caja contenedora con render en navegador)

## 7. Referencias cruzadas a otros temas

- [ ] Validadas contra BOAM 10.032 (T11, T12, T14, T25, T26, T27, T30, T31, T32, T33, T34, T36, T37, T39)
- [ ] Ninguna referencia cruzada cita un enunciado de tema incorrecto

## 8. Calidad editorial

- [ ] Ortografía verificada (tildes y ñ) — sin diacríticos perdidos, también dentro de los `aria-label` de los SVG
- [ ] Coherencia de versión (v1.0) en title, badges, banner y footer del `index.html`
- [ ] El `index.html` abre, navega entre las 8 pestañas y el motor de test funciona
- [ ] Las listas anidadas del Contenido se muestran con sus niveles (sin aplanar)
- [ ] Las tablas comparativas se muestran correctamente y no desbordan en pantalla estrecha

---

## Observaciones abiertas

_(Espacio para anotaciones de María, Ana y la revisión IAM.)_

- Pendiente confirmar si conviene **ampliar el bloque de contenedores** (§2.3) con más detalle de orquestación, o si el nivel actual es el correcto teniendo en cuenta que el temario oficial solo los menciona dentro de este tema y que su peso en él es menor que el de la virtualización clásica.
- Pendiente confirmar si el **desarrollo del bloque normativo** (§5, con ENS, RGPD, continuidad y eficiencia energética) tiene la extensión adecuada. El esqueleto del tema lo pide expresamente, pero buena parte de la materia se desarrolla en los Temas 32 y 39; aquí se ha tratado exclusivamente desde la perspectiva de **qué cambia al virtualizar**.
- Este tema es, junto al **Tema 31** (cloud), uno de los **más sensibles a la obsolescencia** del bloque técnico en su capa de producto (nombres, propietarios y modelos de licencia de las plataformas). Su capa conceptual, en cambio, es muy estable. Conviene fijar una **revisión de vigencia antes de cada convocatoria**, limitada a los nombres de producto de Tier 2.
