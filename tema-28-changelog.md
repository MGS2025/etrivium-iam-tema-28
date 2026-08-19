# Tema 28 — Changelog

> **Título oficial**: Virtualización de sistemas y virtualización de puestos de usuario.

---

## v1.0 — 2026-08-20 — Primera versión

**Estado**: pendiente de validación por María y Ana, y de revisión técnica del IAM (Jesús Cuadrado).

**Motivo**: desarrollo del Tema 28, dentro de la serie de temas técnicos generados desde cero, replicando la estructura y el formato de los Temas 1, 11 y 17-24 ya consolidados. Se genera **fuera de secuencia** (T25, T26 y T27 siguen pendientes) a petición de Joan, igual que se hizo con el T23 antes que el T22.

### Alcance de la v1.0

| Entregable | Cantidad |
|---|---|
| Contenido teórico | ~18.700 palabras · 5 secciones (fieles al esqueleto oficial) con 31 epígrafes numerados |
| Diagramas SVG inline | 15 (accesibles con `role`/`aria-label`, clases con sufijo único anti-colisión) |
| Banco de preguntas tipo test | 60 preguntas A/B/C con explicación y referencia, balanceadas **20/20/20** (verificado por el generador) |
| Casos prácticos | 3 (consolidación del CPD municipal con dimensionamiento numérico; diseño del puesto de trabajo virtual para 900 empleados; seguridad, cumplimiento ENS/RGPD y continuidad) · 10 puntos cada uno |
| Fuentes Tier 1 | 33 referencias canónicas (Popek y Goldberg, Goldberg, Robin e Irvine, Intel, AMD, Arm, PCI-SIG, OASIS, OCI, IETF, ONF, ETSI, SNIA, NIST, ISO y normativa española y europea) |

### Decisiones de generación

1. **Sin material de cliente**: solo el esqueleto `Test_Prompting/temas agosto/28.md`. Desarrollado desde fuentes canónicas (literatura fundacional de la virtualización, manuales de arquitectura de Intel, AMD y Arm, especificaciones de PCI-SIG, OASIS, OCI, IETF y ONF, guías del NIST, normas ISO y normativa publicada en el BOE y el DOUE), todas referenciadas.
2. **Estructura fiel al esqueleto oficial**, con numeración jerárquica de hasta tres niveles donde el esqueleto lo exige (H2 > H3 > H4), respetando sus 5 secciones y sus 31 epígrafes sin añadir secciones nuevas de primer nivel.
3. **Ampliaciones dentro de los epígrafes existentes** (decisión de generación, no del esqueleto): vocabulario canónico anfitrión/huésped/hipervisor/instantánea/plantilla, formatos de disco virtual y distinción OVF/OVA, reserva/límite/participación, NUMA y coplanificación, contenedores aislados por hipervisor, gobierno del ciclo de vida frente a la proliferación descontrolada, y una síntesis final de cinco ideas. Todas encajan en epígrafes ya previstos y cubren huecos que en examen se preguntan con frecuencia.
4. **Los productos comerciales se tratan como ilustración, nunca como contenido** (decisión expresa de este tema, a diferencia de T21 con Java/Jakarta o T24 con Kotlin/Swift/Dart, donde el código sí era el objeto). La razón: la capa de producto de la virtualización es **extraordinariamente volátil** —cambios de propietario, de nombre y de modelo de licencia de las principales plataformas en los últimos años—, mientras que la capa conceptual (Popek y Goldberg, las tres técnicas, los dos tipos de hipervisor, VDI frente a sesiones, SDN, RTO/RPO) es estable y es lo que se pregunta. Cada mención de producto se ancla explícitamente al concepto general que ilustra.
5. **Sin fragmentos de código**: es el primer tema técnico de la serie que no los incluye. No hay lenguaje de programación en el enunciado del tema y el contenido examinable es arquitectónico, no sintáctico. Las tablas comparativas asumen el papel que en otros temas tenían los *snippets*.
6. **Caso de referencia único para todo el tema**: el **centro de proceso de datos municipal** (padrón, registro, sede electrónica, gestor de expedientes) y el **parque de puestos** de oficinas de atención y distritos, planteado como **supuesto simplificado** y no como descripción de una infraestructura real concreta. Permite recorrer con un solo hilo la virtualización de servidores, la del puesto y las obligaciones normativas.
7. **Núcleos conceptuales reforzados**: se han tratado como ejes del tema, con diagrama propio y varias preguntas de test cada uno, las tres confusiones que más rinden en examen: (a) **las tres técnicas de virtualización** y el matiz de que virtio es paravirtualización de dispositivos (D2, D6); (b) **migración en caliente frente a HA frente a FT** (D8, D9); (c) **VDI frente a sesiones frente a virtualización de aplicaciones** (D10). Se añade la distinción **máquina virtual frente a contenedor** (D7), con el mensaje explícito de que no son alternativas sino capas estratificadas.
8. **Frontera con temas vecinos** cuidada, con dos remisiones explícitas de delimitación: los **sistemas de almacenamiento y su virtualización** y las **copias de seguridad** al **Tema 26** (§4.1 y §5.3 se limitan a lo que el entorno virtualizado necesita de ellos); los **sistemas operativos** al Tema 14 y su administración al Tema 27; la arquitectura de ordenadores y los periféricos a los Temas 11 y 12; el puesto de usuario y la seguridad en el desarrollo al Tema 25; el control remoto del puesto al Tema 29; la administración de redes al Tema 30; el cloud al Tema 31; la seguridad de sistemas al Tema 32; comunicaciones y TCP/IP a los Temas 33 y 34; la seguridad en redes y las VPN al Tema 36; las redes locales al Tema 37; y el ENS/ENI al Tema 39.
9. **Referencias cruzadas validadas contra BOAM 10.032**: T11, T12, T14, T25, T26, T27, T30, T31, T32, T33, T34, T36, T37 y T39. Todas comprobadas contra el enunciado oficial de cada tema.
10. **Anti-colisión de SVG**: las clases CSS de cada diagrama llevan **sufijo único** (`.t3`, `.s3`, `.h3`… hasta `.t15`), evitando el fallo sistémico de estilos que se filtran de un SVG a otro al estar todos embebidos en la misma página (lección de T5).
11. **Distribución A/B/C planificada antes de redactar** y verificada con el generador (lección de T23 y T24): la secuencia de 60 letras correctas se fijó de antemano con 20 apariciones de cada opción, y `build_t28.py` confirma **20/20/20**.
12. **Cómputo de extensión medido, no estimado**: la cifra de ~18.700 palabras procede de `wc -w` sobre el `.md`. Con el mismo criterio, T24 (el más extenso hasta ahora) arrojaba ≈12.300, de modo que **este es, con diferencia, el tema más extenso de la serie técnica**. La razón es que el esqueleto de agosto es sensiblemente más detallado que los anteriores y añade una quinta sección normativa completa.
13. **Punto marcado para revisión técnica del IAM**: la afirmación de que el ENS no dedica un grupo de medidas exclusivo a la virtualización se ha dejado señalada con una advertencia en `tema-28-validacion.md`, para que la revisión la confirme o matice contra el texto del RD 311/2022 en lugar de darse por buena.

### Pendientes para QA / próxima iteración

- Validación de profundidad por María/Ana/IAM (¿alguna sección a ampliar o recortar?). Con ~18.700 palabras, es razonable preguntar expresamente si el tema debe **recortarse**.
- Confirmar el reparto de materia con el **Tema 26** en almacenamiento y copias, para evitar duplicidad o hueco.
- Confirmar con el IAM el punto sobre las medidas del ENS aplicables a la virtualización.
- **Revisión de vigencia antes de cada convocatoria**, limitada a la capa de producto (Tier 2): nombres, propietarios y modelos de licencia de las plataformas de virtualización y de VDI.
- Verificación ortográfica con corrector es_ES (cuidado con falsos positivos por términos técnicos en inglés: *host*, *guest*, *bare-metal*, *hosted*, *overlay*, *overcommit*, *ballooning*, *sprawl*, *broker*, *thin client*, *snapshot*, *air gap*…).

### Origen

Generado el 2026-08-20 en el flujo de trabajo de eTrivium, replicando el patrón de los Temas 1 (v2.1), 11 (v3.2) y 17-24 (v1.0). `build_t28.py` y `_build_css.txt` persistidos en el repo.
