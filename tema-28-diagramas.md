# Tema 28 — Catálogo de Diagramas

> **Título oficial**: Virtualización de sistemas y virtualización de puestos de usuario.
>
> **Versión**: v1.0
> **Fecha**: 2026-08-20
> **Autor**: ETRIVIUM
> **Formato**: SVG inline (zero-dependencias, escalable, imprimible, accesible con role/aria-label)
> **Paleta**: Ayuntamiento de Madrid #0055a0 (primario) + #d13c3c (alertas) + #2d8659 (ventajas) + #e89822 (callouts)
> **Nota técnica**: las clases CSS de cada SVG llevan sufijo numérico único (`.t1`, `.h1`…) para evitar colisiones de estilos entre los 15 diagramas embebidos en la misma página.

---

## Índice de diagramas

| ID | Título | Sección | Tipo | Formato |
|---|---|---|---|---|
| D1 | Niveles de abstracción: dónde se inserta cada virtualización | §1.2 | Capas | 680×366 |
| D2 | Las tres técnicas de virtualización y los anillos de privilegio | §1.2 | Comparativa | 680×366 |
| D3 | Hipervisor de Tipo 1 frente a hipervisor de Tipo 2 | §1.3 | Bloques comparados | 680×332 |
| D4 | Componentes de un entorno de virtualización de servidores | §2.1 | Arquitectura | 680×388 |
| D5 | Memoria virtualizada: doble traducción y recuperación de memoria | §2.2.1 | Flujo + escala | 680×340 |
| D6 | Los cuatro modelos de entrada/salida en virtualización | §2.2.2 | Comparativa | 680×346 |
| D7 | Máquinas virtuales frente a contenedores | §2.3.1 | Pilas comparadas | 680×340 |
| D8 | Migración en caliente: fases de la precopia iterativa | §2.4 | Flujo temporal | 680×318 |
| D9 | Migración, HA y FT: tres respuestas distintas | §2.4 | Comparativa | 680×326 |
| D10 | Los modelos de virtualización del puesto de usuario | §3.1 | Comparativa | 680×360 |
| D11 | Arquitectura VDI y flujo de conexión con el broker | §3.2 | Flujo numerado | 680×380 |
| D12 | Escritorio dedicado frente a no dedicado y gestión del perfil | §3.4 | Comparativa | 680×354 |
| D13 | Almacenamiento: cabina externa frente a hiperconvergencia | §4.1 | Bloques comparados | 680×326 |
| D14 | Red virtualizada: conmutador virtual, VXLAN y SDN | §4.2 | Capas + flujo | 680×368 |
| D15 | Continuidad: RTO, RPO y regla 3-2-1 | §5.3 | Línea temporal | 680×328 |

---

## D1 · Niveles de abstracción: dónde se inserta cada virtualización

**Sección**: §1.2 — Arquitectura clásica de la virtualización y nivel de abstracción
**Propósito**: Situar cada tecnología del tema en el nivel de la pila donde inserta su capa de indirección, para evitar la confusión más común (hipervisor, contenedor y máquina virtual de Java no virtualizan lo mismo).

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 366" role="img" aria-label="Pila de niveles de abstracción de un sistema informático, indicando en qué nivel inserta su capa cada tecnología de virtualización: emulador en el juego de instrucciones, hipervisor en el hardware, contenedor en el sistema operativo, máquina virtual de Java en el entorno de ejecución y App-V en la aplicación">
  <style>.t2a{font:700 11.5px system-ui,sans-serif;fill:#fff}.s2a{font:9.5px system-ui,sans-serif;fill:#fff}.h2a{font:700 13px system-ui,sans-serif;fill:#0055a0}.k2a{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n2a{font:9.5px system-ui,sans-serif;fill:#444}</style>
  <text x="340" y="20" text-anchor="middle" class="h2a">Niveles de abstracción y tecnología que virtualiza cada uno</text>
  <text x="150" y="42" text-anchor="middle" class="k2a">PILA DEL SISTEMA</text>
  <text x="490" y="42" text-anchor="middle" class="k2a">QUÉ VIRTUALIZA AHÍ</text>
  <rect x="20" y="52" width="260" height="38" rx="4" fill="#0055a0"/><text x="150" y="76" text-anchor="middle" class="t2a">Aplicación</text>
  <rect x="300" y="52" width="360" height="38" rx="4" fill="#e89822"/><text x="480" y="70" text-anchor="middle" class="t2a">Virtualización de aplicaciones</text><text x="480" y="84" text-anchor="middle" class="s2a">App-V · MSIX app attach · ThinApp</text>
  <rect x="20" y="96" width="260" height="38" rx="4" fill="#0055a0"/><text x="150" y="120" text-anchor="middle" class="t2a">Biblioteca / entorno de ejecución</text>
  <rect x="300" y="96" width="360" height="38" rx="4" fill="#888"/><text x="480" y="114" text-anchor="middle" class="t2a">Máquina virtual de Java · entorno .NET</text><text x="480" y="128" text-anchor="middle" class="s2a">NO es una máquina virtual de sistema</text>
  <rect x="20" y="140" width="260" height="38" rx="4" fill="#0055a0"/><text x="150" y="164" text-anchor="middle" class="t2a">Sistema operativo</text>
  <rect x="300" y="140" width="360" height="38" rx="4" fill="#2d8659"/><text x="480" y="158" text-anchor="middle" class="t2a">Contenedores</text><text x="480" y="172" text-anchor="middle" class="s2a">núcleo compartido · namespaces y cgroups</text>
  <rect x="20" y="184" width="260" height="38" rx="4" fill="#0055a0"/><text x="150" y="208" text-anchor="middle" class="t2a">Hardware / plataforma</text>
  <rect x="300" y="184" width="360" height="38" rx="4" fill="#d13c3c"/><text x="480" y="202" text-anchor="middle" class="t2a">HIPERVISOR — objeto de este tema</text><text x="480" y="216" text-anchor="middle" class="s2a">un núcleo propio por máquina virtual</text>
  <rect x="20" y="228" width="260" height="38" rx="4" fill="#0055a0"/><text x="150" y="252" text-anchor="middle" class="t2a">Juego de instrucciones</text>
  <rect x="300" y="228" width="360" height="38" rx="4" fill="#888"/><text x="480" y="246" text-anchor="middle" class="t2a">Emulador</text><text x="480" y="260" text-anchor="middle" class="s2a">traduce cada instrucción: no es eficiente</text>
  <rect x="20" y="278" width="640" height="30" rx="4" fill="none" stroke="#0055a0" stroke-width="2"/>
  <text x="340" y="298" text-anchor="middle" class="k2a">Regla: cuanto más abajo se inserta la capa, más aísla y más cuesta; cuanto más arriba, más ligera y menos aísla</text>
  <rect x="20" y="316" width="640" height="28" rx="4" fill="#f2f6fa"/>
  <text x="340" y="335" text-anchor="middle" class="n2a">También se virtualizan el almacenamiento (§4.1), la red (§4.2) y el escritorio completo (§3), cada uno en su propio nivel</text>
  <text x="670" y="356" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: POPEK74; NIST-SP800-125]</text>
</svg>
```

---

## D2 · Las tres técnicas de virtualización y los anillos de privilegio

**Sección**: §1.2 — Virtualización total, paravirtualización y asistida por hardware
**Propósito**: Mostrar en paralelo cómo resuelve cada técnica el conflicto de privilegios del x86, que es el eje conceptual de la sección 1.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 366" role="img" aria-label="Comparación de las tres técnicas de virtualización: virtualización total con traducción binaria y el núcleo huésped desplazado del anillo cero, paravirtualización con núcleo modificado que hace llamadas al hipervisor, y virtualización asistida por hardware donde la CPU añade un modo raíz para el hipervisor y el huésped conserva su anillo cero">
  <style>.t2b{font:700 11px system-ui,sans-serif;fill:#fff}.s2b{font:9px system-ui,sans-serif;fill:#fff}.h2b{font:700 13px system-ui,sans-serif;fill:#0055a0}.k2b{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n2b{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h2b">Cómo resuelve cada técnica el conflicto de privilegios</text>
  <text x="123" y="42" text-anchor="middle" class="k2b">1. VIRTUALIZACIÓN TOTAL</text>
  <text x="340" y="42" text-anchor="middle" class="k2b">2. PARAVIRTUALIZACIÓN</text>
  <text x="557" y="42" text-anchor="middle" class="k2b">3. ASISTIDA POR HARDWARE</text>
  <rect x="16" y="52" width="214" height="34" rx="4" fill="#888"/><text x="123" y="67" text-anchor="middle" class="t2b">Aplicaciones huésped</text><text x="123" y="80" text-anchor="middle" class="s2b">anillo 3 · ejecución directa</text>
  <rect x="233" y="52" width="214" height="34" rx="4" fill="#888"/><text x="340" y="67" text-anchor="middle" class="t2b">Aplicaciones huésped</text><text x="340" y="80" text-anchor="middle" class="s2b">anillo 3 · ejecución directa</text>
  <rect x="450" y="52" width="214" height="34" rx="4" fill="#888"/><text x="557" y="67" text-anchor="middle" class="t2b">Aplicaciones huésped</text><text x="557" y="80" text-anchor="middle" class="s2b">anillo 3 · ejecución directa</text>
  <rect x="16" y="92" width="214" height="46" rx="4" fill="#0055a0"/><text x="123" y="109" text-anchor="middle" class="t2b">Núcleo huésped SIN tocar</text><text x="123" y="123" text-anchor="middle" class="s2b">desplazado al anillo 1</text><text x="123" y="134" text-anchor="middle" class="s2b">no sabe que está virtualizado</text>
  <rect x="233" y="92" width="214" height="46" rx="4" fill="#e89822"/><text x="340" y="109" text-anchor="middle" class="t2b">Núcleo huésped MODIFICADO</text><text x="340" y="123" text-anchor="middle" class="s2b">sabe que está virtualizado</text><text x="340" y="134" text-anchor="middle" class="s2b">emite llamadas al hipervisor</text>
  <rect x="450" y="92" width="214" height="46" rx="4" fill="#2d8659"/><text x="557" y="109" text-anchor="middle" class="t2b">Núcleo huésped SIN tocar</text><text x="557" y="123" text-anchor="middle" class="s2b">en su anillo 0 (modo no raíz)</text><text x="557" y="134" text-anchor="middle" class="s2b">sin traducción binaria</text>
  <path d="M123 144 L123 158" stroke="#d13c3c" stroke-width="2" marker-end="url(#a2b)"/>
  <path d="M340 144 L340 158" stroke="#d13c3c" stroke-width="2" marker-end="url(#a2b)"/>
  <path d="M557 144 L557 158" stroke="#d13c3c" stroke-width="2" marker-end="url(#a2b)"/>
  <defs><marker id="a2b" markerWidth="7" markerHeight="7" refX="6" refY="3" orient="auto"><path d="M0,0 L6,3 L0,6 z" fill="#d13c3c"/></marker></defs>
  <text x="123" y="155" text-anchor="middle" class="n2b"> </text>
  <rect x="16" y="162" width="214" height="44" rx="4" fill="#d13c3c"/><text x="123" y="180" text-anchor="middle" class="t2b">Traducción binaria</text><text x="123" y="194" text-anchor="middle" class="s2b">reescribe el código privilegiado</text><text x="123" y="204" text-anchor="middle" class="s2b">y lo guarda en caché</text>
  <rect x="233" y="162" width="214" height="44" rx="4" fill="#d13c3c"/><text x="340" y="180" text-anchor="middle" class="t2b">Llamadas al hipervisor</text><text x="340" y="194" text-anchor="middle" class="s2b">interfaz explícita, sin capturas</text><text x="340" y="204" text-anchor="middle" class="s2b">ni emulación costosa</text>
  <rect x="450" y="162" width="214" height="44" rx="4" fill="#d13c3c"/><text x="557" y="180" text-anchor="middle" class="t2b">VM exit / VM entry</text><text x="557" y="194" text-anchor="middle" class="s2b">solo en los eventos definidos</text><text x="557" y="204" text-anchor="middle" class="s2b">en la VMCS / VMCB</text>
  <rect x="16" y="212" width="648" height="34" rx="4" fill="#0055a0"/><text x="340" y="228" text-anchor="middle" class="t2b">HIPERVISOR (anillo 0; con asistencia por hardware, modo raíz VMX / SVM — el llamado «anillo −1»)</text><text x="340" y="241" text-anchor="middle" class="s2b">controla todos los recursos físicos y aísla las máquinas virtuales entre sí</text>
  <rect x="16" y="252" width="648" height="30" rx="4" fill="#333"/><text x="340" y="272" text-anchor="middle" class="t2b">HARDWARE — CPU con VT-x / AMD-V / EL2 · EPT o NPT para memoria · IOMMU (VT-d / AMD-Vi) para E/S</text>
  <rect x="16" y="290" width="648" height="46" rx="4" fill="none" stroke="#0055a0" stroke-width="2"/>
  <text x="340" y="308" text-anchor="middle" class="k2b">Huésped sin modificar: técnicas 1 y 3 · Huésped modificado: técnica 2</text>
  <text x="340" y="326" text-anchor="middle" class="n2b">La paravirtualización del NÚCLEO está desplazada; la paravirtualización de DISPOSITIVOS (virtio) sigue siendo la norma</text>
  <text x="670" y="356" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: POPEK74; ROBIN00; INTEL-SDM; AMD-APM; XEN-DOC]</text>
</svg>
```

---

## D3 · Hipervisor de Tipo 1 frente a hipervisor de Tipo 2

**Sección**: §1.3 — Hipervisores y su clasificación
**Propósito**: Fijar visualmente el criterio de clasificación (sobre qué se ejecuta el hipervisor) y sus consecuencias de rendimiento, seguridad y ámbito de uso.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 332" role="img" aria-label="Comparación entre hipervisor de Tipo 1, que se instala directamente sobre el hardware, e hipervisor de Tipo 2, que se ejecuta como aplicación sobre un sistema operativo anfitrión, con sus consecuencias en número de capas, rendimiento, superficie de ataque y ámbito de uso">
  <style>.t3{font:700 11px system-ui,sans-serif;fill:#fff}.s3{font:9.5px system-ui,sans-serif;fill:#fff}.h3{font:700 13px system-ui,sans-serif;fill:#0055a0}.k3{font:700 11px system-ui,sans-serif;fill:#0055a0}.n3{font:9.5px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h3">Clasificación de Goldberg (1973): sobre qué se ejecuta el hipervisor</text>
  <text x="175" y="42" text-anchor="middle" class="k3">TIPO 1 · NATIVO (bare-metal)</text>
  <text x="505" y="42" text-anchor="middle" class="k3">TIPO 2 · ALOJADO (hosted)</text>
  <rect x="24" y="52" width="96" height="34" rx="4" fill="#888"/><text x="72" y="73" text-anchor="middle" class="t3">VM 1</text>
  <rect x="127" y="52" width="96" height="34" rx="4" fill="#888"/><text x="175" y="73" text-anchor="middle" class="t3">VM 2</text>
  <rect x="230" y="52" width="96" height="34" rx="4" fill="#888"/><text x="278" y="73" text-anchor="middle" class="t3">VM 3</text>
  <rect x="354" y="52" width="150" height="34" rx="4" fill="#888"/><text x="429" y="73" text-anchor="middle" class="t3">VM 1</text>
  <rect x="510" y="52" width="146" height="34" rx="4" fill="#888"/><text x="583" y="73" text-anchor="middle" class="t3">VM 2</text>
  <rect x="24" y="92" width="302" height="36" rx="4" fill="#0055a0"/><text x="175" y="115" text-anchor="middle" class="t3">HIPERVISOR</text>
  <rect x="354" y="92" width="302" height="36" rx="4" fill="#0055a0"/><text x="505" y="115" text-anchor="middle" class="t3">HIPERVISOR (una aplicación más)</text>
  <rect x="354" y="134" width="302" height="36" rx="4" fill="#d13c3c"/><text x="505" y="151" text-anchor="middle" class="t3">SISTEMA OPERATIVO ANFITRIÓN</text><text x="505" y="165" text-anchor="middle" class="s3">capa adicional: latencia y superficie de ataque</text>
  <rect x="24" y="134" width="302" height="36" rx="4" fill="#f2f6fa" stroke="#2d8659" stroke-width="2" stroke-dasharray="4 3"/><text x="175" y="157" text-anchor="middle" style="font:700 10.5px system-ui;fill:#2d8659">— sin capa intermedia —</text>
  <rect x="24" y="176" width="302" height="32" rx="4" fill="#333"/><text x="175" y="197" text-anchor="middle" class="t3">HARDWARE</text>
  <rect x="354" y="176" width="302" height="32" rx="4" fill="#333"/><text x="505" y="197" text-anchor="middle" class="t3">HARDWARE</text>
  <rect x="24" y="218" width="302" height="70" rx="4" fill="#2d8659"/>
  <text x="175" y="236" text-anchor="middle" class="s3">Rendimiento alto · superficie mínima</text>
  <text x="175" y="252" text-anchor="middle" class="s3">Gestión centralizada en clúster</text>
  <text x="175" y="268" text-anchor="middle" class="t3">CENTRO DE DATOS / PRODUCCIÓN</text>
  <text x="175" y="282" text-anchor="middle" class="s3">ESXi · Hyper-V · Xen · KVM · Proxmox VE</text>
  <rect x="354" y="218" width="302" height="70" rx="4" fill="#e89822"/>
  <text x="505" y="236" text-anchor="middle" class="s3">Menor rendimiento · hereda el riesgo del anfitrión</text>
  <text x="505" y="252" text-anchor="middle" class="s3">Si cae el anfitrión, caen todas las VM</text>
  <text x="505" y="268" text-anchor="middle" class="t3">PUESTO / LABORATORIO / PRUEBAS</text>
  <text x="505" y="282" text-anchor="middle" class="s3">VirtualBox · Workstation · Fusion · Parallels</text>
  <text x="340" y="306" text-anchor="middle" class="n3">Casos discutidos: KVM (módulo del núcleo Linux) e Hyper-V (rol de Windows Server) se clasifican como TIPO 1</text>
  <text x="670" y="327" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: GOLDBERG73; NIST-SP800-125]</text>
</svg>
```

---

## D4 · Componentes de un entorno de virtualización de servidores

**Sección**: §2.1 — Arquitectura y componentes
**Propósito**: Mostrar las piezas de un clúster real y la separación de flujos de red, que es la base de las medidas de seguridad de §5.1.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 388" role="img" aria-label="Arquitectura de un entorno de virtualización de servidores: servidor de gestión centralizado, clúster de tres anfitriones con hipervisor y máquinas virtuales, almacenamiento compartido accesible por todos y cuatro redes separadas para gestión, máquinas virtuales, almacenamiento y migración">
  <style>.t4{font:700 10.5px system-ui,sans-serif;fill:#fff}.s4{font:9px system-ui,sans-serif;fill:#fff}.h4{font:700 13px system-ui,sans-serif;fill:#0055a0}.k4{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n4{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h4">Entorno de virtualización de servidores: piezas y flujos</text>
  <rect x="20" y="34" width="180" height="52" rx="5" fill="#0055a0"/><text x="110" y="52" text-anchor="middle" class="t4">SERVIDOR DE GESTIÓN</text><text x="110" y="66" text-anchor="middle" class="s4">inventario · políticas de clúster</text><text x="110" y="79" text-anchor="middle" class="s4">roles (RBAC) · registro de actividad</text>
  <rect x="212" y="34" width="228" height="52" rx="5" fill="#e89822"/><text x="326" y="52" text-anchor="middle" class="t4">CATÁLOGO DE PLANTILLAS E IMÁGENES</text><text x="326" y="66" text-anchor="middle" class="s4">máquinas molde bastionadas y versionadas</text><text x="326" y="79" text-anchor="middle" class="s4">despliegue reproducible</text>
  <rect x="452" y="34" width="208" height="52" rx="5" fill="#888"/><text x="556" y="52" text-anchor="middle" class="t4">CONSOLA DEL ADMINISTRADOR</text><text x="556" y="66" text-anchor="middle" class="s4">acceso privilegiado nominal</text><text x="556" y="79" text-anchor="middle" class="s4">con autenticación reforzada</text>
  <line x1="110" y1="88" x2="110" y2="106" stroke="#0055a0" stroke-width="2"/>
  <line x1="326" y1="88" x2="326" y2="106" stroke="#0055a0" stroke-width="2"/>
  <line x1="556" y1="88" x2="556" y2="106" stroke="#0055a0" stroke-width="2"/>
  <rect x="20" y="106" width="640" height="18" rx="3" fill="#0055a0" opacity="0.15" stroke="#0055a0"/>
  <text x="340" y="119" text-anchor="middle" class="k4">RED DE GESTIÓN — aislada, nunca accesible desde la red de usuarios</text>
  <text x="340" y="140" text-anchor="middle" class="k4">CLÚSTER DE ANFITRIONES</text>
  <rect x="20" y="148" width="206" height="94" rx="5" fill="#f2f6fa" stroke="#0055a0" stroke-width="2"/>
  <rect x="30" y="156" width="60" height="24" rx="3" fill="#888"/><text x="60" y="172" text-anchor="middle" class="s4">VM</text>
  <rect x="94" y="156" width="60" height="24" rx="3" fill="#888"/><text x="124" y="172" text-anchor="middle" class="s4">VM</text>
  <rect x="158" y="156" width="60" height="24" rx="3" fill="#888"/><text x="188" y="172" text-anchor="middle" class="s4">VM</text>
  <rect x="30" y="184" width="188" height="22" rx="3" fill="#0055a0"/><text x="124" y="199" text-anchor="middle" class="t4">Hipervisor Tipo 1</text>
  <rect x="30" y="210" width="188" height="24" rx="3" fill="#333"/><text x="124" y="226" text-anchor="middle" class="t4">Anfitrión 1</text>
  <rect x="237" y="148" width="206" height="94" rx="5" fill="#f2f6fa" stroke="#0055a0" stroke-width="2"/>
  <rect x="247" y="156" width="60" height="24" rx="3" fill="#888"/><text x="277" y="172" text-anchor="middle" class="s4">VM</text>
  <rect x="311" y="156" width="60" height="24" rx="3" fill="#888"/><text x="341" y="172" text-anchor="middle" class="s4">VM</text>
  <rect x="375" y="156" width="60" height="24" rx="3" fill="#888"/><text x="405" y="172" text-anchor="middle" class="s4">VM</text>
  <rect x="247" y="184" width="188" height="22" rx="3" fill="#0055a0"/><text x="341" y="199" text-anchor="middle" class="t4">Hipervisor Tipo 1</text>
  <rect x="247" y="210" width="188" height="24" rx="3" fill="#333"/><text x="341" y="226" text-anchor="middle" class="t4">Anfitrión 2</text>
  <rect x="454" y="148" width="206" height="94" rx="5" fill="#f2f6fa" stroke="#2d8659" stroke-width="2" stroke-dasharray="5 3"/>
  <text x="557" y="180" text-anchor="middle" style="font:700 10.5px system-ui;fill:#2d8659">Anfitrión 3 — capacidad N+1</text>
  <text x="557" y="198" text-anchor="middle" class="n4">reservado para absorber la carga</text>
  <text x="557" y="214" text-anchor="middle" class="n4">del anfitrión que falle (HA)</text>
  <text x="557" y="232" text-anchor="middle" class="n4">y para el modo mantenimiento</text>
  <rect x="20" y="252" width="315" height="18" rx="3" fill="#2d8659" opacity="0.2" stroke="#2d8659"/>
  <text x="177" y="265" text-anchor="middle" style="font:700 9.5px system-ui;fill:#2d8659">RED DE MÁQUINAS VIRTUALES (usuarios)</text>
  <rect x="345" y="252" width="315" height="18" rx="3" fill="#e89822" opacity="0.25" stroke="#e89822"/>
  <text x="502" y="265" text-anchor="middle" style="font:700 9.5px system-ui;fill:#a06000">RED DE MIGRACIÓN EN CALIENTE (dedicada)</text>
  <rect x="20" y="278" width="640" height="18" rx="3" fill="#d13c3c" opacity="0.18" stroke="#d13c3c"/>
  <text x="340" y="291" text-anchor="middle" style="font:700 9.5px system-ui;fill:#a02020">RED DE ALMACENAMIENTO (FC / iSCSI / NFS)</text>
  <rect x="140" y="304" width="400" height="46" rx="5" fill="#0055a0"/>
  <text x="340" y="322" text-anchor="middle" class="t4">ALMACENAMIENTO COMPARTIDO</text>
  <text x="340" y="336" text-anchor="middle" class="s4">accesible desde TODOS los anfitriones — requisito de HA y de migración en caliente</text>
  <text x="340" y="348" text-anchor="middle" class="s4">cabina SAN / NAS o almacenamiento distribuido hiperconvergente (§4.1)</text>
  <text x="340" y="368" text-anchor="middle" class="n4">La separación de los cuatro flujos de red es una buena práctica de seguridad recogida por el NIST y exigible vía ENS</text>
  <text x="670" y="378" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: VMWARE-DOC; HYPERV-DOC; NIST-SP800-125]</text>
</svg>
```

---

## D5 · Memoria virtualizada: doble traducción y recuperación de memoria

**Sección**: §2.2.1 — Planificación de CPU y gestión de memoria virtualizada
**Propósito**: Explicar la doble traducción resuelta por EPT/NPT y ordenar las cuatro técnicas de recuperación de memoria de menos a más dañina.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 340" role="img" aria-label="Doble traducción de memoria en un entorno virtualizado: de dirección virtual del huésped a física del huésped mediante las tablas del huésped, y de física del huésped a física real mediante las tablas extendidas EPT o NPT del hardware; y escala de las cuatro técnicas de recuperación de memoria, de la compartición de páginas al intercambio a disco">
  <style>.t5{font:700 10.5px system-ui,sans-serif;fill:#fff}.s5{font:9px system-ui,sans-serif;fill:#fff}.h5{font:700 13px system-ui,sans-serif;fill:#0055a0}.k5{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n5{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h5">Memoria virtualizada: doble traducción y presión de memoria</text>
  <text x="340" y="40" text-anchor="middle" class="k5">1 · LAS DOS TRADUCCIONES</text>
  <rect x="26" y="50" width="150" height="46" rx="5" fill="#888"/><text x="101" y="70" text-anchor="middle" class="t5">Dirección VIRTUAL</text><text x="101" y="85" text-anchor="middle" class="s5">del proceso del huésped</text>
  <path d="M180 73 L216 73" stroke="#0055a0" stroke-width="2.5" marker-end="url(#a5)"/>
  <defs><marker id="a5" markerWidth="8" markerHeight="8" refX="7" refY="3.5" orient="auto"><path d="M0,0 L7,3.5 L0,7 z" fill="#0055a0"/></marker></defs>
  <text x="198" y="66" text-anchor="middle" style="font:8px system-ui;fill:#0055a0">tablas</text>
  <text x="198" y="89" text-anchor="middle" style="font:8px system-ui;fill:#0055a0">huésped</text>
  <rect x="220" y="50" width="170" height="46" rx="5" fill="#e89822"/><text x="305" y="70" text-anchor="middle" class="t5">Dirección FÍSICA del huésped</text><text x="305" y="85" text-anchor="middle" class="s5">el huésped cree que es real</text>
  <path d="M394 73 L430 73" stroke="#d13c3c" stroke-width="2.5" marker-end="url(#a5b)"/>
  <defs><marker id="a5b" markerWidth="8" markerHeight="8" refX="7" refY="3.5" orient="auto"><path d="M0,0 L7,3.5 L0,7 z" fill="#d13c3c"/></marker></defs>
  <text x="412" y="66" text-anchor="middle" style="font:8px system-ui;fill:#d13c3c">EPT</text>
  <text x="412" y="89" text-anchor="middle" style="font:8px system-ui;fill:#d13c3c">NPT</text>
  <rect x="434" y="50" width="226" height="46" rx="5" fill="#0055a0"/><text x="547" y="70" text-anchor="middle" class="t5">Dirección FÍSICA REAL del anfitrión</text><text x="547" y="85" text-anchor="middle" class="s5">resuelta por la MMU, sin el hipervisor</text>
  <rect x="26" y="104" width="634" height="38" rx="4" fill="#f2f6fa" stroke="#0055a0"/>
  <text x="340" y="120" text-anchor="middle" class="n5">Sin asistencia por hardware había que mantener por software TABLAS DE PÁGINAS SOMBRA: correcto, pero muy costoso.</text>
  <text x="340" y="134" text-anchor="middle" class="n5">EPT (Intel), NPT o RVI (AMD) y la traducción en dos etapas de Arm lo resuelven en la propia MMU.</text>
  <text x="340" y="158" text-anchor="middle" class="k5">2 · RECUPERACIÓN DE MEMORIA — de menos a más dañina</text>
  <rect x="26" y="168" width="152" height="76" rx="5" fill="#2d8659"/>
  <text x="102" y="187" text-anchor="middle" class="t5">1. COMPARTICIÓN</text>
  <text x="102" y="203" text-anchor="middle" class="s5">páginas idénticas entre VM:</text>
  <text x="102" y="217" text-anchor="middle" class="s5">una sola copia física</text>
  <text x="102" y="235" text-anchor="middle" class="s5">muy eficaz en VDI</text>
  <rect x="186" y="168" width="152" height="76" rx="5" fill="#7aa63b"/>
  <text x="262" y="187" text-anchor="middle" class="t5">2. GLOBO (ballooning)</text>
  <text x="262" y="203" text-anchor="middle" class="s5">un controlador reclama</text>
  <text x="262" y="217" text-anchor="middle" class="s5">memoria DENTRO del huésped</text>
  <text x="262" y="235" text-anchor="middle" class="s5">decide el huésped: acierta más</text>
  <rect x="346" y="168" width="152" height="76" rx="5" fill="#e89822"/>
  <text x="422" y="187" text-anchor="middle" class="t5">3. COMPRESIÓN</text>
  <text x="422" y="203" text-anchor="middle" class="s5">páginas poco usadas a una</text>
  <text x="422" y="217" text-anchor="middle" class="s5">caché comprimida en RAM</text>
  <text x="422" y="235" text-anchor="middle" class="s5">más lenta, pero no es disco</text>
  <rect x="506" y="168" width="154" height="76" rx="5" fill="#d13c3c"/>
  <text x="583" y="187" text-anchor="middle" class="t5">4. INTERCAMBIO A DISCO</text>
  <text x="583" y="203" text-anchor="middle" class="s5">el hipervisor elige a ciegas</text>
  <text x="583" y="217" text-anchor="middle" class="s5">qué página saca</text>
  <text x="583" y="235" text-anchor="middle" class="s5">SÍNTOMA DE MAL DIMENSIONADO</text>
  <path d="M26 254 L660 254" stroke="#999" stroke-width="1.5" marker-end="url(#a5c)"/>
  <defs><marker id="a5c" markerWidth="8" markerHeight="8" refX="7" refY="3.5" orient="auto"><path d="M0,0 L7,3.5 L0,7 z" fill="#999"/></marker></defs>
  <text x="340" y="270" text-anchor="middle" class="n5">presión de memoria creciente en el anfitrión →</text>
  <rect x="90" y="282" width="500" height="34" rx="5" fill="none" stroke="#0055a0" stroke-width="2"/>
  <text x="340" y="303" text-anchor="middle" class="k5">Sobreasignar CPU degrada · sobreasignar memoria rompe</text>
  <text x="670" y="332" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: INTEL-SDM; AMD-APM; VMWARE-DOC; KVM-DOC]</text>
</svg>
```

---

## D6 · Los cuatro modelos de entrada/salida en virtualización

**Sección**: §2.2.2 — Entradas y salidas y controladores paravirtualizados
**Propósito**: Comparar emulación, paravirtualización de dispositivo, asignación directa y SR-IOV en rendimiento, requisitos y pérdida de movilidad.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 346" role="img" aria-label="Los cuatro modelos de entrada y salida en virtualización: emulación completa de dispositivo, dispositivo paravirtualizado tipo virtio, asignación directa con passthrough y SR-IOV con funciones virtuales, comparados en rendimiento, requisitos y compatibilidad con la migración en caliente">
  <style>.t6{font:700 10.5px system-ui,sans-serif;fill:#fff}.s6{font:9px system-ui,sans-serif;fill:#fff}.h6{font:700 13px system-ui,sans-serif;fill:#0055a0}.k6{font:700 10px system-ui,sans-serif;fill:#0055a0}.n6{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h6">Modelos de E/S: del más flexible al más rápido</text>
  <rect x="20" y="34" width="152" height="30" rx="4" fill="#888"/><text x="96" y="54" text-anchor="middle" class="t6">1 · EMULACIÓN</text>
  <rect x="182" y="34" width="152" height="30" rx="4" fill="#2d8659"/><text x="258" y="54" text-anchor="middle" class="t6">2 · PARAVIRTUALIZADO</text>
  <rect x="344" y="34" width="152" height="30" rx="4" fill="#e89822"/><text x="420" y="54" text-anchor="middle" class="t6">3 · ASIGNACIÓN DIRECTA</text>
  <rect x="506" y="34" width="154" height="30" rx="4" fill="#0055a0"/><text x="583" y="54" text-anchor="middle" class="t6">4 · SR-IOV</text>
  <rect x="20" y="70" width="152" height="26" rx="3" fill="#f2f6fa" stroke="#888"/><text x="96" y="87" text-anchor="middle" class="n6">Máquina virtual</text>
  <rect x="182" y="70" width="152" height="26" rx="3" fill="#f2f6fa" stroke="#2d8659"/><text x="258" y="87" text-anchor="middle" class="n6">Máquina virtual</text>
  <rect x="344" y="70" width="152" height="26" rx="3" fill="#f2f6fa" stroke="#e89822"/><text x="420" y="87" text-anchor="middle" class="n6">Máquina virtual</text>
  <rect x="506" y="70" width="154" height="26" rx="3" fill="#f2f6fa" stroke="#0055a0"/><text x="583" y="87" text-anchor="middle" class="n6">VM A · VM B · VM C</text>
  <rect x="20" y="102" width="152" height="40" rx="3" fill="#d13c3c"/><text x="96" y="118" text-anchor="middle" class="s6">Dispositivo REAL emulado</text><text x="96" y="133" text-anchor="middle" class="s6">registro a registro</text>
  <rect x="182" y="102" width="152" height="40" rx="3" fill="#2d8659"/><text x="258" y="118" text-anchor="middle" class="s6">Controlador virtio en el</text><text x="258" y="133" text-anchor="middle" class="s6">huésped + colas compartidas</text>
  <rect x="344" y="102" width="152" height="40" rx="3" fill="#e89822"/><text x="420" y="118" text-anchor="middle" class="s6">Controlador NATIVO del</text><text x="420" y="133" text-anchor="middle" class="s6">huésped sobre el hardware</text>
  <rect x="506" y="102" width="154" height="40" rx="3" fill="#0055a0"/><text x="583" y="118" text-anchor="middle" class="s6">Una función virtual (VF)</text><text x="583" y="133" text-anchor="middle" class="s6">por máquina virtual</text>
  <rect x="20" y="148" width="314" height="24" rx="3" fill="#0055a0" opacity="0.85"/><text x="177" y="165" text-anchor="middle" class="t6">EL HIPERVISOR INTERVIENE EN CADA OPERACIÓN</text>
  <rect x="344" y="148" width="316" height="24" rx="3" fill="#333"/><text x="502" y="165" text-anchor="middle" class="t6">EL HIPERVISOR SE APARTA — requiere IOMMU</text>
  <rect x="20" y="178" width="640" height="26" rx="3" fill="#333"/><text x="340" y="196" text-anchor="middle" class="t6">HARDWARE FÍSICO (tarjeta de red, controladora de disco, GPU)</text>
  <text x="96" y="226" text-anchor="middle" class="k6">Rendimiento: BAJO</text>
  <text x="258" y="226" text-anchor="middle" class="k6">Rendimiento: ALTO</text>
  <text x="420" y="226" text-anchor="middle" class="k6">Rendimiento: MUY ALTO</text>
  <text x="583" y="226" text-anchor="middle" class="k6">Rendimiento: MUY ALTO</text>
  <rect x="20" y="234" width="152" height="60" rx="4" fill="#f2f6fa" stroke="#888"/>
  <text x="96" y="251" text-anchor="middle" class="n6">Sin requisitos</text>
  <text x="96" y="266" text-anchor="middle" class="n6">Migración: SÍ</text>
  <text x="96" y="284" text-anchor="middle" class="n6">Instalación y huéspedes antiguos</text>
  <rect x="182" y="234" width="152" height="60" rx="4" fill="#eaf5ec" stroke="#2d8659" stroke-width="2"/>
  <text x="258" y="251" text-anchor="middle" class="n6">Controlador en el huésped</text>
  <text x="258" y="266" text-anchor="middle" class="n6">Migración: SÍ</text>
  <text x="258" y="284" text-anchor="middle" style="font:700 9px system-ui;fill:#2d8659">OPCIÓN POR DEFECTO</text>
  <rect x="344" y="234" width="152" height="60" rx="4" fill="#fdf3e3" stroke="#e89822"/>
  <text x="420" y="251" text-anchor="middle" class="n6">IOMMU obligatoria</text>
  <text x="420" y="266" text-anchor="middle" style="font:700 9px system-ui;fill:#a02020">Migración: NO</text>
  <text x="420" y="284" text-anchor="middle" class="n6">GPU, cifrado, tarjetas especiales</text>
  <rect x="506" y="234" width="154" height="60" rx="4" fill="#e8f0f8" stroke="#0055a0"/>
  <text x="583" y="251" text-anchor="middle" class="n6">IOMMU + tarjeta compatible</text>
  <text x="583" y="266" text-anchor="middle" style="font:700 9px system-ui;fill:#a02020">Migración: NO</text>
  <text x="583" y="284" text-anchor="middle" class="n6">Red de altísimo rendimiento, NFV</text>
  <rect x="120" y="302" width="440" height="24" rx="4" fill="none" stroke="#0055a0" stroke-width="2"/>
  <text x="340" y="318" text-anchor="middle" class="k6">virtio ES paravirtualización, aunque el huésped no esté paravirtualizado</text>
  <text x="670" y="336" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: VIRTIO; PCI-SRIOV; INTEL-VTD]</text>
</svg>
```

---

## D7 · Máquinas virtuales frente a contenedores

**Sección**: §2.3.1 — Comparativa entre hipervisor y contenedores
**Propósito**: Contraponer las dos pilas y dejar claro que no son alternativas excluyentes, sino capas que se estratifican.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 340" role="img" aria-label="Comparación de las pilas de máquinas virtuales y contenedores: la máquina virtual incluye un sistema operativo huésped completo sobre el hipervisor y el hardware, mientras que el contenedor comparte el núcleo del anfitrión a través de un motor de contenedores; abajo se muestra el modelo real combinado, con contenedores ejecutándose dentro de máquinas virtuales">
  <style>.t7{font:700 10.5px system-ui,sans-serif;fill:#fff}.s7{font:9px system-ui,sans-serif;fill:#fff}.h7{font:700 13px system-ui,sans-serif;fill:#0055a0}.k7{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n7{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h7">Qué virtualiza cada uno: el hardware o el sistema operativo</text>
  <text x="175" y="40" text-anchor="middle" class="k7">MÁQUINAS VIRTUALES — virtualizan el HARDWARE</text>
  <text x="505" y="40" text-anchor="middle" class="k7">CONTENEDORES — virtualizan el SISTEMA OPERATIVO</text>
  <rect x="24" y="50" width="96" height="22" rx="3" fill="#888"/><text x="72" y="65" text-anchor="middle" class="s7">App A</text>
  <rect x="127" y="50" width="96" height="22" rx="3" fill="#888"/><text x="175" y="65" text-anchor="middle" class="s7">App B</text>
  <rect x="230" y="50" width="96" height="22" rx="3" fill="#888"/><text x="278" y="65" text-anchor="middle" class="s7">App C</text>
  <rect x="24" y="76" width="96" height="34" rx="3" fill="#d13c3c"/><text x="72" y="91" text-anchor="middle" class="s7">SO huésped</text><text x="72" y="104" text-anchor="middle" class="s7">núcleo propio</text>
  <rect x="127" y="76" width="96" height="34" rx="3" fill="#d13c3c"/><text x="175" y="91" text-anchor="middle" class="s7">SO huésped</text><text x="175" y="104" text-anchor="middle" class="s7">núcleo propio</text>
  <rect x="230" y="76" width="96" height="34" rx="3" fill="#d13c3c"/><text x="278" y="91" text-anchor="middle" class="s7">SO huésped</text><text x="278" y="104" text-anchor="middle" class="s7">núcleo propio</text>
  <rect x="24" y="114" width="302" height="26" rx="3" fill="#0055a0"/><text x="175" y="132" text-anchor="middle" class="t7">HIPERVISOR</text>
  <rect x="24" y="144" width="302" height="24" rx="3" fill="#333"/><text x="175" y="161" text-anchor="middle" class="t7">HARDWARE</text>
  <rect x="354" y="50" width="96" height="22" rx="3" fill="#888"/><text x="402" y="65" text-anchor="middle" class="s7">App A</text>
  <rect x="457" y="50" width="96" height="22" rx="3" fill="#888"/><text x="505" y="65" text-anchor="middle" class="s7">App B</text>
  <rect x="560" y="50" width="96" height="22" rx="3" fill="#888"/><text x="608" y="65" text-anchor="middle" class="s7">App C</text>
  <rect x="354" y="76" width="96" height="34" rx="3" fill="#2d8659"/><text x="402" y="91" text-anchor="middle" class="s7">Dependencias</text><text x="402" y="104" text-anchor="middle" class="s7">(solo librerías)</text>
  <rect x="457" y="76" width="96" height="34" rx="3" fill="#2d8659"/><text x="505" y="91" text-anchor="middle" class="s7">Dependencias</text><text x="505" y="104" text-anchor="middle" class="s7">(solo librerías)</text>
  <rect x="560" y="76" width="96" height="34" rx="3" fill="#2d8659"/><text x="608" y="91" text-anchor="middle" class="s7">Dependencias</text><text x="608" y="104" text-anchor="middle" class="s7">(solo librerías)</text>
  <rect x="354" y="114" width="302" height="26" rx="3" fill="#e89822"/><text x="505" y="132" text-anchor="middle" class="t7">MOTOR DE CONTENEDORES — namespaces + cgroups</text>
  <rect x="354" y="144" width="302" height="24" rx="3" fill="#d13c3c"/><text x="505" y="161" text-anchor="middle" class="t7">NÚCLEO DEL ANFITRIÓN — COMPARTIDO POR TODOS</text>
  <rect x="354" y="172" width="302" height="22" rx="3" fill="#333"/><text x="505" y="187" text-anchor="middle" class="t7">HARDWARE</text>
  <rect x="24" y="174" width="302" height="20" rx="3" fill="#f2f6fa" stroke="#0055a0"/>
  <text x="175" y="188" text-anchor="middle" class="n7">GB · segundos · decenas por anfitrión · cualquier SO</text>
  <rect x="354" y="198" width="302" height="20" rx="3" fill="#f2f6fa" stroke="#2d8659"/>
  <text x="505" y="212" text-anchor="middle" class="n7">MB · milisegundos · cientos · solo el SO del núcleo</text>
  <rect x="24" y="200" width="302" height="18" rx="3" fill="#eaf0f6"/>
  <text x="175" y="213" text-anchor="middle" style="font:700 9px system-ui;fill:#0055a0">Aislamiento FUERTE (frontera de hardware)</text>
  <rect x="354" y="222" width="302" height="18" rx="3" fill="#fdeaea"/>
  <text x="505" y="235" text-anchor="middle" style="font:700 9px system-ui;fill:#a02020">Aislamiento MÁS DÉBIL (frontera de núcleo)</text>
  <text x="340" y="262" text-anchor="middle" class="k7">EN LA PRÁCTICA NO SE ELIGE: SE ESTRATIFICA</text>
  <rect x="90" y="270" width="500" height="46" rx="5" fill="#0055a0"/>
  <text x="340" y="288" text-anchor="middle" class="t7">Contenedores DENTRO de máquinas virtuales DENTRO de un clúster de hipervisores</text>
  <text x="340" y="304" text-anchor="middle" class="s7">es el modelo de toda la nube pública y de la mayoría de instalaciones reales</text>
  <text x="670" y="334" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: OCI-SPEC; NIST-SP800-190; LINUX-NS]</text>
</svg>
```

---

## D8 · Migración en caliente: fases de la precopia iterativa

**Sección**: §2.4 — Alta disponibilidad, balanceo y migración en caliente
**Propósito**: Detallar las cuatro fases de la migración y el momento exacto de la única parada, de milisegundos.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 318" role="img" aria-label="Fases de la migración en caliente por precopia iterativa: preparación del destino, copia iterativa de la memoria mientras la máquina sigue funcionando, parada breve de milisegundos para transferir las últimas páginas sucias y el estado de CPU, y conmutación al destino con aviso a la red">
  <style>.t8{font:700 10.5px system-ui,sans-serif;fill:#fff}.s8{font:9px system-ui,sans-serif;fill:#fff}.h8{font:700 13px system-ui,sans-serif;fill:#0055a0}.k8{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n8{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h8">Migración en caliente: la máquina virtual nunca se apaga</text>
  <rect x="20" y="34" width="200" height="30" rx="4" fill="#333"/><text x="120" y="54" text-anchor="middle" class="t8">ANFITRIÓN ORIGEN</text>
  <rect x="460" y="34" width="200" height="30" rx="4" fill="#333"/><text x="560" y="54" text-anchor="middle" class="t8">ANFITRIÓN DESTINO</text>
  <rect x="240" y="34" width="200" height="30" rx="4" fill="#e89822"/><text x="340" y="54" text-anchor="middle" class="t8">RED DEDICADA DE MIGRACIÓN</text>
  <rect x="20" y="76" width="640" height="40" rx="4" fill="#0055a0"/>
  <text x="34" y="93" style="font:700 10.5px system-ui;fill:#fff">FASE 1 · PREPARACIÓN</text>
  <text x="34" y="108" class="s8">El destino reserva CPU, memoria y red · se comprueba compatibilidad de CPU y acceso al mismo almacenamiento</text>
  <rect x="20" y="122" width="640" height="52" rx="4" fill="#2d8659"/>
  <text x="34" y="139" style="font:700 10.5px system-ui;fill:#fff">FASE 2 · COPIA ITERATIVA DE MEMORIA — la VM SIGUE FUNCIONANDO en el origen</text>
  <text x="34" y="154" class="s8">Pasada 1: se copian TODAS las páginas · Pasada 2: solo las que el huésped ha vuelto a escribir (páginas «sucias»)</text>
  <text x="34" y="168" class="s8">Pasada 3, 4, n: cada vez quedan menos, hasta que el conjunto pendiente es lo bastante pequeño</text>
  <rect x="20" y="180" width="640" height="40" rx="4" fill="#d13c3c"/>
  <text x="34" y="197" style="font:700 10.5px system-ui;fill:#fff">FASE 3 · PARADA BREVE (stun) — ÚNICA INTERRUPCIÓN, DE MILISEGUNDOS</text>
  <text x="34" y="212" class="s8">Se detiene la VM y se transfieren las últimas páginas sucias más el estado de CPU y de dispositivos</text>
  <rect x="20" y="226" width="640" height="40" rx="4" fill="#0055a0"/>
  <text x="34" y="243" style="font:700 10.5px system-ui;fill:#fff">FASE 4 · CONMUTACIÓN</text>
  <text x="34" y="258" class="s8">La VM se reanuda en el destino · aviso a la red (ARP gratuito) para reaprender su MAC · se liberan los recursos del origen</text>
  <rect x="20" y="272" width="640" height="20" rx="3" fill="#f2f6fa" stroke="#0055a0"/>
  <text x="340" y="286" text-anchor="middle" class="n8">Requisitos: CPU compatible · red compartida y dedicada · mismo almacenamiento accesible · SIN dispositivos asignados en passthrough</text>
  <text x="670" y="313" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: VMWARE-DOC; HYPERV-DOC; KVM-DOC]</text>
</svg>
```

---

## D9 · Migración, HA y FT: tres respuestas distintas

**Sección**: §2.4 — Alta disponibilidad y tolerancia a fallos
**Propósito**: Separar de forma inequívoca los tres mecanismos que se suelen confundir, según sean planificados o no y según haya corte o no.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 326" role="img" aria-label="Comparación de migración en caliente, alta disponibilidad y tolerancia a fallos según sean eventos planificados o no planificados y según provoquen o no corte de servicio, con su coste relativo y su caso de uso">
  <style>.t9{font:700 10.5px system-ui,sans-serif;fill:#fff}.s9{font:9px system-ui,sans-serif;fill:#fff}.h9{font:700 13px system-ui,sans-serif;fill:#0055a0}.k9{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n9{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h9">Migración en caliente · Alta disponibilidad · Tolerancia a fallos</text>
  <rect x="20" y="34" width="206" height="28" rx="4" fill="#0055a0"/><text x="123" y="53" text-anchor="middle" class="t9">MIGRACIÓN EN CALIENTE</text>
  <rect x="237" y="34" width="206" height="28" rx="4" fill="#e89822"/><text x="340" y="53" text-anchor="middle" class="t9">ALTA DISPONIBILIDAD (HA)</text>
  <rect x="454" y="34" width="206" height="28" rx="4" fill="#2d8659"/><text x="557" y="53" text-anchor="middle" class="t9">TOLERANCIA A FALLOS (FT)</text>
  <rect x="20" y="70" width="206" height="30" rx="3" fill="#f2f6fa" stroke="#0055a0"/><text x="123" y="82" text-anchor="middle" class="n9">Evento</text><text x="123" y="95" text-anchor="middle" style="font:700 9.5px system-ui;fill:#0055a0">PLANIFICADO</text>
  <rect x="237" y="70" width="206" height="30" rx="3" fill="#fdf3e3" stroke="#e89822"/><text x="340" y="82" text-anchor="middle" class="n9">Evento</text><text x="340" y="95" text-anchor="middle" style="font:700 9.5px system-ui;fill:#a06000">NO PLANIFICADO (fallo)</text>
  <rect x="454" y="70" width="206" height="30" rx="3" fill="#eaf5ec" stroke="#2d8659"/><text x="557" y="82" text-anchor="middle" class="n9">Evento</text><text x="557" y="95" text-anchor="middle" style="font:700 9.5px system-ui;fill:#2d8659">NO PLANIFICADO (fallo)</text>
  <rect x="20" y="106" width="206" height="40" rx="3" fill="#2d8659"/><text x="123" y="124" text-anchor="middle" class="t9">SIN CORTE</text><text x="123" y="139" text-anchor="middle" class="s9">parada de milisegundos</text>
  <rect x="237" y="106" width="206" height="40" rx="3" fill="#d13c3c"/><text x="340" y="124" text-anchor="middle" class="t9">CON CORTE</text><text x="340" y="139" text-anchor="middle" class="s9">la VM SE REINICIA en otro nodo</text>
  <rect x="454" y="106" width="206" height="40" rx="3" fill="#2d8659"/><text x="557" y="124" text-anchor="middle" class="t9">SIN CORTE</text><text x="557" y="139" text-anchor="middle" class="s9">la copia en espejo asume el servicio</text>
  <rect x="20" y="152" width="206" height="42" rx="3" fill="#888"/><text x="123" y="169" text-anchor="middle" class="s9">Estado en memoria: SE CONSERVA</text><text x="123" y="186" text-anchor="middle" class="s9">Se copia página a página</text>
  <rect x="237" y="152" width="206" height="42" rx="3" fill="#888"/><text x="340" y="169" text-anchor="middle" class="s9">Estado en memoria: SE PIERDE</text><text x="340" y="186" text-anchor="middle" class="s9">Corte = arranque de SO + aplicación</text>
  <rect x="454" y="152" width="206" height="42" rx="3" fill="#888"/><text x="557" y="169" text-anchor="middle" class="s9">Estado en memoria: SE CONSERVA</text><text x="557" y="186" text-anchor="middle" class="s9">Copia sincronizada instrucción a instrucción</text>
  <rect x="20" y="200" width="206" height="36" rx="3" fill="#eaf0f6"/><text x="123" y="216" text-anchor="middle" class="n9">Coste: BAJO</text><text x="123" y="230" text-anchor="middle" class="n9">Mantenimiento y balanceo</text>
  <rect x="237" y="200" width="206" height="36" rx="3" fill="#fdf3e3"/><text x="340" y="216" text-anchor="middle" class="n9">Coste: MEDIO (capacidad N+1)</text><text x="340" y="230" text-anchor="middle" class="n9">Política estándar del clúster</text>
  <rect x="454" y="200" width="206" height="36" rx="3" fill="#eaf5ec"/><text x="557" y="216" text-anchor="middle" class="n9">Coste: ALTO (recursos duplicados)</text><text x="557" y="230" text-anchor="middle" class="n9">Solo servicios críticos concretos</text>
  <rect x="20" y="246" width="640" height="26" rx="4" fill="#0055a0"/>
  <text x="340" y="264" text-anchor="middle" class="t9">Con migración en caliente + modo mantenimiento, parchear el hardware DEJA DE REQUERIR VENTANA DE PARADA</text>
  <rect x="20" y="278" width="640" height="24" rx="4" fill="none" stroke="#d13c3c" stroke-width="2"/>
  <text x="340" y="294" text-anchor="middle" style="font:700 10px system-ui;fill:#a02020">Error más frecuente: confundir HA (reinicia, hay corte) con FT (espejo, no hay corte)</text>
  <text x="670" y="316" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: VMWARE-DOC; HYPERV-DOC]</text>
</svg>
```

---

## D10 · Los modelos de virtualización del puesto de usuario

**Sección**: §3.1 — Modelos de virtualización en el cliente
**Propósito**: Separar VDI, escritorios por sesiones y virtualización de aplicaciones por lo que virtualiza cada uno y por su densidad, coste y aislamiento.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 360" role="img" aria-label="Comparación de los modelos de virtualización del puesto de usuario: VDI con una máquina virtual y un sistema operativo de cliente por usuario, escritorios basados en sesiones con un único sistema operativo de servidor compartido por muchas sesiones, y virtualización de aplicaciones que entrega solo la aplicación empaquetada en una burbuja aislada">
  <style>.t10{font:700 10.5px system-ui,sans-serif;fill:#fff}.s10{font:9px system-ui,sans-serif;fill:#fff}.h10{font:700 13px system-ui,sans-serif;fill:#0055a0}.k10{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n10{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h10">Qué se virtualiza en cada modelo de puesto de trabajo</text>
  <rect x="20" y="34" width="206" height="26" rx="4" fill="#0055a0"/><text x="123" y="52" text-anchor="middle" class="t10">VDI — se virtualiza LA MÁQUINA</text>
  <rect x="237" y="34" width="206" height="26" rx="4" fill="#2d8659"/><text x="340" y="52" text-anchor="middle" class="t10">SESIONES — el SISTEMA OPERATIVO</text>
  <rect x="454" y="34" width="206" height="26" rx="4" fill="#e89822"/><text x="557" y="52" text-anchor="middle" class="t10">APLICACIONES — LA APLICACIÓN</text>
  <rect x="26" y="68" width="60" height="52" rx="3" fill="#888"/><text x="56" y="87" text-anchor="middle" class="s10">Usuario 1</text><text x="56" y="102" text-anchor="middle" class="s10">SO cliente</text><text x="56" y="114" text-anchor="middle" class="s10">VM propia</text>
  <rect x="93" y="68" width="60" height="52" rx="3" fill="#888"/><text x="123" y="87" text-anchor="middle" class="s10">Usuario 2</text><text x="123" y="102" text-anchor="middle" class="s10">SO cliente</text><text x="123" y="114" text-anchor="middle" class="s10">VM propia</text>
  <rect x="160" y="68" width="60" height="52" rx="3" fill="#888"/><text x="190" y="87" text-anchor="middle" class="s10">Usuario 3</text><text x="190" y="102" text-anchor="middle" class="s10">SO cliente</text><text x="190" y="114" text-anchor="middle" class="s10">VM propia</text>
  <rect x="26" y="124" width="194" height="24" rx="3" fill="#0055a0"/><text x="123" y="141" text-anchor="middle" class="t10">HIPERVISOR</text>
  <rect x="243" y="68" width="60" height="30" rx="3" fill="#888"/><text x="273" y="87" text-anchor="middle" class="s10">Sesión 1</text>
  <rect x="310" y="68" width="60" height="30" rx="3" fill="#888"/><text x="340" y="87" text-anchor="middle" class="s10">Sesión 2</text>
  <rect x="377" y="68" width="60" height="30" rx="3" fill="#888"/><text x="407" y="87" text-anchor="middle" class="s10">Sesión n</text>
  <rect x="243" y="102" width="194" height="46" rx="3" fill="#2d8659"/><text x="340" y="120" text-anchor="middle" class="t10">UN ÚNICO SO DE SERVIDOR</text><text x="340" y="135" text-anchor="middle" class="s10">compartido por todas las sesiones (RDSH)</text>
  <rect x="460" y="68" width="194" height="30" rx="3" fill="#888"/><text x="557" y="87" text-anchor="middle" class="s10">Escritorio del usuario (físico o virtual)</text>
  <rect x="460" y="102" width="92" height="46" rx="3" fill="#e89822"/><text x="506" y="120" text-anchor="middle" class="s10">Burbuja aislada</text><text x="506" y="135" text-anchor="middle" class="s10">App v1</text>
  <rect x="562" y="102" width="92" height="46" rx="3" fill="#e89822"/><text x="608" y="120" text-anchor="middle" class="s10">Burbuja aislada</text><text x="608" y="135" text-anchor="middle" class="s10">App v2</text>
  <rect x="26" y="156" width="194" height="88" rx="4" fill="#eaf0f6" stroke="#0055a0"/>
  <text x="123" y="174" text-anchor="middle" class="n10">Densidad: BAJA (decenas)</text>
  <text x="123" y="190" text-anchor="middle" class="n10">Coste por puesto: ALTO</text>
  <text x="123" y="206" text-anchor="middle" style="font:700 9px system-ui;fill:#0055a0">Aislamiento: ALTO</text>
  <text x="123" y="222" text-anchor="middle" class="n10">Personalización: ALTA</text>
  <text x="123" y="238" text-anchor="middle" class="n10">Compatibilidad de SO cliente</text>
  <rect x="243" y="156" width="194" height="88" rx="4" fill="#eaf5ec" stroke="#2d8659"/>
  <text x="340" y="174" text-anchor="middle" class="n10">Densidad: ALTA</text>
  <text x="340" y="190" text-anchor="middle" style="font:700 9px system-ui;fill:#2d8659">Coste por puesto: BAJO</text>
  <text x="340" y="206" text-anchor="middle" class="n10">Aislamiento: MEDIO</text>
  <text x="340" y="222" text-anchor="middle" class="n10">Personalización: LIMITADA</text>
  <text x="340" y="238" text-anchor="middle" class="n10">Un cuelgue afecta a TODOS</text>
  <rect x="460" y="156" width="194" height="88" rx="4" fill="#fdf3e3" stroke="#e89822"/>
  <text x="557" y="174" text-anchor="middle" class="n10">No virtualiza el escritorio</text>
  <text x="557" y="190" text-anchor="middle" class="n10">Resuelve CONFLICTOS entre</text>
  <text x="557" y="204" text-anchor="middle" class="n10">versiones de aplicaciones</text>
  <text x="557" y="222" text-anchor="middle" class="n10">Entrega bajo demanda</text>
  <text x="557" y="238" text-anchor="middle" class="n10">Coste inicial: empaquetado</text>
  <rect x="20" y="254" width="640" height="34" rx="4" fill="#0055a0"/>
  <text x="340" y="271" text-anchor="middle" class="t10">NO SON EXCLUYENTES: lo habitual es usar virtualización de APLICACIONES dentro de un escritorio VDI o de sesiones</text>
  <text x="340" y="284" text-anchor="middle" class="s10">y asignar cada colectivo al modelo que le corresponde según su perfil de uso</text>
  <rect x="20" y="296" width="640" height="24" rx="4" fill="none" stroke="#d13c3c" stroke-width="2"/>
  <text x="340" y="312" text-anchor="middle" style="font:700 10px system-ui;fill:#a02020">CLIENTE LIGERO = el dispositivo terminal · VDI = la arquitectura del lado del servidor. No es lo mismo</text>
  <text x="670" y="336" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: HORIZON-DOC; CITRIX-DOC; AVD-DOC; APPV]</text>
  <text x="670" y="352" text-anchor="end" style="font:10px system-ui;fill:#666">Un cuarto modelo, la virtualización en el propio cliente, ejecuta la VM en el equipo del usuario</text>
</svg>
```

---

## D11 · Arquitectura VDI y flujo de conexión con el broker

**Sección**: §3.2 — Componentes de la arquitectura VDI
**Propósito**: Mostrar los siete pasos de una conexión VDI y, en particular, que el broker interviene en el establecimiento pero no en el tráfico del protocolo.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 380" role="img" aria-label="Arquitectura VDI y flujo de conexión numerado en siete pasos: el cliente accede por la pasarela, el broker autentica contra el directorio, comprueba autorizaciones, selecciona o enciende el escritorio del conjunto aprovisionado desde la imagen maestra y redirige al cliente, tras lo cual el protocolo de representación fluye directamente entre cliente y escritorio">
  <style>.t11{font:700 10px system-ui,sans-serif;fill:#fff}.s11{font:8.5px system-ui,sans-serif;fill:#fff}.h11{font:700 13px system-ui,sans-serif;fill:#0055a0}.k11{font:700 10px system-ui,sans-serif;fill:#0055a0}.n11{font:8.5px system-ui,sans-serif;fill:#333}.num11{font:700 11px system-ui,sans-serif;fill:#d13c3c}</style>
  <text x="340" y="18" text-anchor="middle" class="h11">Flujo de una conexión VDI: quién decide y por dónde va el tráfico</text>
  <rect x="16" y="36" width="120" height="60" rx="5" fill="#888"/>
  <text x="76" y="56" text-anchor="middle" class="t11">CLIENTE</text>
  <text x="76" y="71" text-anchor="middle" class="s11">cliente ligero, PC</text>
  <text x="76" y="84" text-anchor="middle" class="s11">o navegador HTML5</text>
  <rect x="156" y="36" width="110" height="60" rx="5" fill="#d13c3c"/>
  <text x="211" y="56" text-anchor="middle" class="t11">PASARELA</text>
  <text x="211" y="71" text-anchor="middle" class="s11">publica hacia fuera</text>
  <text x="211" y="84" text-anchor="middle" class="s11">TLS + doble factor</text>
  <rect x="286" y="36" width="140" height="60" rx="5" fill="#0055a0"/>
  <text x="356" y="56" text-anchor="middle" class="t11">BROKER DE CONEXIONES</text>
  <text x="356" y="71" text-anchor="middle" class="s11">autentica · autoriza · elige</text>
  <text x="356" y="84" text-anchor="middle" class="s11">enciende · redirige · reconecta</text>
  <rect x="446" y="36" width="106" height="60" rx="5" fill="#888"/>
  <text x="499" y="56" text-anchor="middle" class="t11">DIRECTORIO</text>
  <text x="499" y="71" text-anchor="middle" class="s11">identidad y grupos</text>
  <text x="499" y="84" text-anchor="middle" class="s11">del empleado</text>
  <rect x="562" y="36" width="102" height="60" rx="5" fill="#e89822"/>
  <text x="613" y="56" text-anchor="middle" class="t11">PORTAL</text>
  <text x="613" y="71" text-anchor="middle" class="s11">catálogo de recursos</text>
  <text x="613" y="84" text-anchor="middle" class="s11">a los que tiene derecho</text>
  <path d="M136 60 L154 60" stroke="#d13c3c" stroke-width="2" marker-end="url(#a11)"/>
  <defs><marker id="a11" markerWidth="7" markerHeight="7" refX="6" refY="3" orient="auto"><path d="M0,0 L6,3 L0,6 z" fill="#d13c3c"/></marker></defs>
  <text x="145" y="53" text-anchor="middle" class="num11">1</text>
  <path d="M266 60 L284 60" stroke="#d13c3c" stroke-width="2" marker-end="url(#a11)"/>
  <text x="275" y="53" text-anchor="middle" class="num11">2</text>
  <path d="M426 60 L444 60" stroke="#d13c3c" stroke-width="2" marker-end="url(#a11)"/>
  <text x="435" y="53" text-anchor="middle" class="num11">3</text>
  <text x="340" y="122" text-anchor="middle" class="k11">4 · El broker selecciona el escritorio: el suyo (dedicado) o uno libre del conjunto; si está apagado, lo enciende</text>
  <rect x="44" y="128" width="164" height="100" rx="5" fill="#2d8659"/>
  <text x="126" y="147" text-anchor="middle" class="t11">IMAGEN MAESTRA</text>
  <text x="126" y="163" text-anchor="middle" class="s11">plantilla única, parcheada</text>
  <text x="126" y="177" text-anchor="middle" class="s11">bastionada y optimizada</text>
  <text x="126" y="196" text-anchor="middle" class="s11">Se parchea UNA VEZ</text>
  <text x="126" y="210" text-anchor="middle" class="s11">y la heredan todos</text>
  <text x="126" y="223" text-anchor="middle" class="s11">al siguiente reinicio</text>
  <path d="M212 178 L228 178" stroke="#2d8659" stroke-width="2.5" marker-end="url(#a11b)"/>
  <defs><marker id="a11b" markerWidth="8" markerHeight="8" refX="7" refY="3.5" orient="auto"><path d="M0,0 L7,3.5 L0,7 z" fill="#2d8659"/></marker></defs>
  <text x="220" y="170" text-anchor="middle" style="font:700 7.5px system-ui;fill:#2d8659">clona</text>
  <rect x="232" y="128" width="432" height="100" rx="5" fill="#f2f6fa" stroke="#0055a0" stroke-width="2"/>
  <text x="448" y="145" text-anchor="middle" class="k11">CONJUNTO DE ESCRITORIOS VIRTUALES (clúster de hipervisores)</text>
  <rect x="244" y="154" width="94" height="30" rx="3" fill="#0055a0"/><text x="291" y="173" text-anchor="middle" class="s11">Escritorio 1</text>
  <rect x="346" y="154" width="94" height="30" rx="3" fill="#0055a0"/><text x="393" y="173" text-anchor="middle" class="s11">Escritorio 2</text>
  <rect x="448" y="154" width="94" height="30" rx="3" fill="#0055a0"/><text x="495" y="173" text-anchor="middle" class="s11">Escritorio 3</text>
  <rect x="550" y="154" width="102" height="30" rx="3" fill="#888"/><text x="601" y="173" text-anchor="middle" class="s11">Escritorio n (apagado)</text>
  <text x="448" y="200" text-anchor="middle" class="n11">Cada escritorio ejecuta un AGENTE que lo registra en el broker, informa de su estado</text>
  <text x="448" y="215" text-anchor="middle" class="n11">y atiende el protocolo de representación (RDP · ICA/HDX · PCoIP · Blast · SPICE)</text>
  <path d="M356 100 L356 109" stroke="#d13c3c" stroke-width="2" marker-end="url(#a11)"/>
  <path d="M76 100 L76 108 L28 108 L28 254 L286 254" stroke="#0055a0" stroke-width="2.5" fill="none" marker-end="url(#a11c)"/>
  <defs><marker id="a11c" markerWidth="8" markerHeight="8" refX="7" refY="3.5" orient="auto"><path d="M0,0 L7,3.5 L0,7 z" fill="#0055a0"/></marker></defs>
  <text x="150" y="248" text-anchor="middle" class="num11">5-6</text>
  <rect x="290" y="234" width="374" height="42" rx="5" fill="#0055a0"/>
  <text x="477" y="251" text-anchor="middle" class="t11">7 · PROTOCOLO DE REPRESENTACIÓN — DIRECTO cliente ↔ escritorio</text>
  <text x="477" y="266" text-anchor="middle" class="s11">solo viajan imagen, audio y periféricos; los datos NUNCA salen del centro de datos</text>
  <rect x="16" y="288" width="322" height="48" rx="5" fill="#d13c3c"/>
  <text x="177" y="306" text-anchor="middle" class="t11">EL BROKER ES PUNTO ÚNICO DE FALLO</text>
  <text x="177" y="320" text-anchor="middle" class="s11">Si cae, no se pueden abrir sesiones NUEVAS</text>
  <text x="177" y="332" text-anchor="middle" class="s11">→ se despliega siempre redundado y balanceado</text>
  <rect x="346" y="288" width="318" height="48" rx="5" fill="#2d8659"/>
  <text x="505" y="306" text-anchor="middle" class="t11">EL BROKER NO ES CUELLO DE BOTELLA</text>
  <text x="505" y="320" text-anchor="middle" class="s11">Interviene en el ESTABLECIMIENTO, no en el tráfico</text>
  <text x="505" y="332" text-anchor="middle" class="s11">Gestiona además la reconexión e itinerancia de sesión</text>
  <text x="340" y="356" text-anchor="middle" class="n11">Perfil del usuario y datos personales: SIEMPRE fuera del escritorio (contenedor de perfil + redirección de carpetas) — ver D12</text>
  <text x="670" y="374" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: CITRIX-DOC; HORIZON-DOC; AVD-DOC]</text>
</svg>
```

---

## D12 · Escritorio dedicado frente a no dedicado y gestión del perfil

**Sección**: §3.4 — Estrategias de persistencia
**Propósito**: Mostrar la decisión de persistencia y, sobre todo, la condición que el modelo no persistente impone: gestionar el perfil por fuera.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 354" role="img" aria-label="Comparación entre escritorio dedicado o persistente, que conserva los cambios del usuario y hay que parchear uno por uno, y escritorio no dedicado o no persistente, que se descarta al cerrar sesión y se recrea desde la imagen maestra, exigiendo gestionar el perfil del usuario mediante contenedor de perfil, redirección de carpetas, capas de aplicación y directivas">
  <style>.t12{font:700 10.5px system-ui,sans-serif;fill:#fff}.s12{font:9px system-ui,sans-serif;fill:#fff}.h12{font:700 13px system-ui,sans-serif;fill:#0055a0}.k12{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n12{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h12">La decisión que condiciona todo el diseño VDI: ¿persiste el escritorio?</text>
  <rect x="20" y="34" width="316" height="28" rx="4" fill="#e89822"/><text x="178" y="53" text-anchor="middle" class="t12">DEDICADO / PERSISTENTE</text>
  <rect x="344" y="34" width="316" height="28" rx="4" fill="#2d8659"/><text x="502" y="53" text-anchor="middle" class="t12">NO DEDICADO / NO PERSISTENTE (flotante)</text>
  <rect x="20" y="70" width="316" height="34" rx="3" fill="#f2f6fa" stroke="#e89822"/>
  <text x="178" y="84" text-anchor="middle" class="n12">Asignado de forma permanente a un usuario concreto</text>
  <text x="178" y="98" text-anchor="middle" class="n12">Sus cambios SOBREVIVEN al reinicio</text>
  <rect x="344" y="70" width="316" height="34" rx="3" fill="#f2f6fa" stroke="#2d8659"/>
  <text x="502" y="84" text-anchor="middle" class="n12">Se toma del conjunto al iniciar sesión</text>
  <text x="502" y="98" text-anchor="middle" class="n12">SE DESCARTA al cerrarla y se recrea desde la imagen maestra</text>
  <rect x="20" y="112" width="154" height="60" rx="4" fill="#d13c3c"/>
  <text x="97" y="130" text-anchor="middle" class="s12">Parcheo: UNO A UNO</text>
  <text x="97" y="146" text-anchor="middle" class="s12">Se pierde la mayor ventaja</text>
  <text x="97" y="160" text-anchor="middle" class="s12">operativa de VDI</text>
  <rect x="182" y="112" width="154" height="60" rx="4" fill="#d13c3c"/>
  <text x="259" y="130" text-anchor="middle" class="s12">Almacenamiento: ALTO</text>
  <text x="259" y="146" text-anchor="middle" class="s12">Clones completos</text>
  <text x="259" y="160" text-anchor="middle" class="s12">+ desviación de configuración</text>
  <rect x="344" y="112" width="154" height="60" rx="4" fill="#2d8659"/>
  <text x="421" y="130" text-anchor="middle" class="s12">Parcheo: UNA IMAGEN</text>
  <text x="421" y="146" text-anchor="middle" class="s12">Clones enlazados o instantáneos</text>
  <text x="421" y="160" text-anchor="middle" class="s12">Almacenamiento: BAJO</text>
  <rect x="506" y="112" width="154" height="60" rx="4" fill="#2d8659"/>
  <text x="583" y="130" text-anchor="middle" class="s12">Escritorio SIEMPRE LIMPIO</text>
  <text x="583" y="146" text-anchor="middle" class="s12">El software malicioso no</text>
  <text x="583" y="160" text-anchor="middle" class="s12">sobrevive al cierre de sesión</text>
  <rect x="20" y="180" width="316" height="30" rx="4" fill="#fdf3e3" stroke="#e89822"/>
  <text x="178" y="199" text-anchor="middle" class="k12">Encaje: perfiles técnicos y usuarios con software propio</text>
  <rect x="344" y="180" width="316" height="30" rx="4" fill="#eaf5ec" stroke="#2d8659"/>
  <text x="502" y="199" text-anchor="middle" class="k12">Encaje: colectivos numerosos y homogéneos</text>
  <rect x="20" y="222" width="640" height="26" rx="4" fill="#d13c3c"/>
  <text x="340" y="240" text-anchor="middle" class="t12">CONDICIÓN INELUDIBLE DEL MODELO NO PERSISTENTE: gestionar el perfil del usuario POR FUERA del escritorio</text>
  <rect x="20" y="256" width="155" height="58" rx="4" fill="#0055a0"/>
  <text x="97" y="274" text-anchor="middle" class="s12">CONTENEDOR DE PERFIL</text>
  <text x="97" y="290" text-anchor="middle" class="s12">disco virtual que se monta</text>
  <text x="97" y="304" text-anchor="middle" class="s12">al iniciar sesión</text>
  <rect x="183" y="256" width="155" height="58" rx="4" fill="#0055a0"/>
  <text x="260" y="274" text-anchor="middle" class="s12">REDIRECCIÓN DE CARPETAS</text>
  <text x="260" y="290" text-anchor="middle" class="s12">documentos y escritorio</text>
  <text x="260" y="304" text-anchor="middle" class="s12">a un recurso de red central</text>
  <rect x="346" y="256" width="155" height="58" rx="4" fill="#0055a0"/>
  <text x="423" y="274" text-anchor="middle" class="s12">CAPAS DE APLICACIÓN</text>
  <text x="423" y="290" text-anchor="middle" class="s12">discos con las aplicaciones</text>
  <text x="423" y="304" text-anchor="middle" class="s12">de cada colectivo</text>
  <rect x="509" y="256" width="151" height="58" rx="4" fill="#0055a0"/>
  <text x="584" y="274" text-anchor="middle" class="s12">DIRECTIVAS</text>
  <text x="584" y="290" text-anchor="middle" class="s12">la configuración se APLICA</text>
  <text x="584" y="304" text-anchor="middle" class="s12">en cada inicio, no se guarda</text>
  <text x="340" y="330" text-anchor="middle" class="n12">Sin estas cuatro piezas, el escritorio no persistente se percibe como una pérdida de datos del usuario en cada cierre de sesión</text>
  <text x="670" y="346" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: FSLOGIX; HORIZON-DOC; CITRIX-DOC]</text>
</svg>
```

---

## D13 · Almacenamiento: cabina externa frente a hiperconvergencia

**Sección**: §4.1 — Virtualización del almacenamiento e infraestructuras hiperconvergentes
**Propósito**: Contrastar el modelo clásico de cabina y red de almacenamiento con el modelo hiperconvergente, y justificar por qué este último encaja tan bien con VDI.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 326" role="img" aria-label="Comparación entre la arquitectura convergente clásica, con servidores de cómputo conectados por una red de almacenamiento a una cabina externa, y la arquitectura hiperconvergente, donde los discos residen en los propios nodos de cómputo y un software distribuido los agrega y replica presentando un almacén único">
  <style>.t13{font:700 10.5px system-ui,sans-serif;fill:#fff}.s13{font:9px system-ui,sans-serif;fill:#fff}.h13{font:700 13px system-ui,sans-serif;fill:#0055a0}.k13{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n13{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h13">Dónde viven los discos: cabina externa o dentro de los nodos</text>
  <text x="175" y="40" text-anchor="middle" class="k13">CONVERGENTE CLÁSICO</text>
  <text x="505" y="40" text-anchor="middle" class="k13">HIPERCONVERGENTE (HCI)</text>
  <rect x="24" y="50" width="96" height="42" rx="4" fill="#0055a0"/><text x="72" y="68" text-anchor="middle" class="s13">Nodo 1</text><text x="72" y="83" text-anchor="middle" class="s13">solo cómputo</text>
  <rect x="127" y="50" width="96" height="42" rx="4" fill="#0055a0"/><text x="175" y="68" text-anchor="middle" class="s13">Nodo 2</text><text x="175" y="83" text-anchor="middle" class="s13">solo cómputo</text>
  <rect x="230" y="50" width="96" height="42" rx="4" fill="#0055a0"/><text x="278" y="68" text-anchor="middle" class="s13">Nodo 3</text><text x="278" y="83" text-anchor="middle" class="s13">solo cómputo</text>
  <rect x="24" y="100" width="302" height="24" rx="3" fill="#d13c3c"/>
  <text x="175" y="117" text-anchor="middle" class="t13">RED DE ALMACENAMIENTO DEDICADA (FC / iSCSI)</text>
  <rect x="24" y="132" width="302" height="56" rx="4" fill="#333"/>
  <text x="175" y="152" text-anchor="middle" class="t13">CABINA EXTERNA</text>
  <text x="175" y="168" text-anchor="middle" class="s13">RAID, instantáneas, replicación y jerarquización</text>
  <text x="175" y="182" text-anchor="middle" class="s13">gestionada aparte, a menudo por otro equipo</text>
  <rect x="354" y="50" width="96" height="74" rx="4" fill="#0055a0"/><text x="402" y="68" text-anchor="middle" class="s13">Nodo 1</text><text x="402" y="83" text-anchor="middle" class="s13">cómputo</text>
  <rect x="362" y="92" width="80" height="24" rx="3" fill="#2d8659"/><text x="402" y="108" text-anchor="middle" class="s13">discos locales</text>
  <rect x="457" y="50" width="96" height="74" rx="4" fill="#0055a0"/><text x="505" y="68" text-anchor="middle" class="s13">Nodo 2</text><text x="505" y="83" text-anchor="middle" class="s13">cómputo</text>
  <rect x="465" y="92" width="80" height="24" rx="3" fill="#2d8659"/><text x="505" y="108" text-anchor="middle" class="s13">discos locales</text>
  <rect x="560" y="50" width="96" height="74" rx="4" fill="#0055a0"/><text x="608" y="68" text-anchor="middle" class="s13">Nodo 3</text><text x="608" y="83" text-anchor="middle" class="s13">cómputo</text>
  <rect x="568" y="92" width="80" height="24" rx="3" fill="#2d8659"/><text x="608" y="108" text-anchor="middle" class="s13">discos locales</text>
  <rect x="354" y="132" width="302" height="26" rx="3" fill="#2d8659"/>
  <text x="505" y="150" text-anchor="middle" class="t13">SOFTWARE DISTRIBUIDO: agrega y REPLICA entre nodos</text>
  <rect x="354" y="164" width="302" height="24" rx="3" fill="#e89822"/>
  <text x="505" y="181" text-anchor="middle" class="t13">ALMACÉN ÚNICO presentado al clúster</text>
  <rect x="24" y="198" width="302" height="66" rx="4" fill="#f2f6fa" stroke="#0055a0"/>
  <text x="175" y="216" text-anchor="middle" class="n13">Crece cómputo y disco POR SEPARADO</text>
  <text x="175" y="232" text-anchor="middle" class="n13">Funciones muy maduras y alto rendimiento</text>
  <text x="175" y="248" text-anchor="middle" class="n13">Coste y complejidad de la red de almacenamiento</text>
  <text x="175" y="260" text-anchor="middle" style="font:700 9px system-ui;fill:#a02020">Riesgo de concentración en la cabina</text>
  <rect x="354" y="198" width="302" height="66" rx="4" fill="#eaf5ec" stroke="#2d8659"/>
  <text x="505" y="216" text-anchor="middle" class="n13">Crece AÑADIENDO NODOS (escalado horizontal)</text>
  <text x="505" y="232" text-anchor="middle" class="n13">Gestión unificada desde la consola de virtualización</text>
  <text x="505" y="248" text-anchor="middle" class="n13">Cómputo y almacenamiento quedan ACOPLADOS</text>
  <text x="505" y="260" text-anchor="middle" style="font:700 9px system-ui;fill:#a02020">Dependencia fuerte de la red entre nodos</text>
  <rect x="60" y="274" width="560" height="30" rx="4" fill="#0055a0"/>
  <text x="340" y="285" text-anchor="middle" class="s13">HCI encaja especialmente bien con VDI: E/S local rápida sin rodeo por la red de almacenamiento,</text>
  <text x="340" y="298" text-anchor="middle" class="s13">deduplicación de escritorios casi idénticos y crecimiento por bloques de usuarios</text>
  <text x="670" y="316" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: SNIA-SSM; VMWARE-DOC; HYPERV-DOC]</text>
</svg>
```

---

## D14 · Red virtualizada: conmutador virtual, VXLAN y SDN

**Sección**: §4.2 — Virtualización de redes y redes definidas por software
**Propósito**: Encadenar los tres niveles de la red virtualizada (conmutador virtual, superposición y control centralizado) y situar la microsegmentación sobre el tráfico este-oeste.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 368" role="img" aria-label="Tres niveles de la red virtualizada: conmutador virtual en cada anfitrión con etiquetado VLAN y microsegmentación, superposición VXLAN que encapsula tramas de nivel dos sobre UDP con identificador VNI de veinticuatro bits, y arquitectura SDN con separación de plano de control y plano de datos, controlador centralizado e interfaces norte y sur">
  <style>.t14{font:700 10.5px system-ui,sans-serif;fill:#fff}.s14{font:9px system-ui,sans-serif;fill:#fff}.h14{font:700 13px system-ui,sans-serif;fill:#0055a0}.k14{font:700 10px system-ui,sans-serif;fill:#0055a0}.n14{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h14">Los tres niveles de la red virtualizada</text>
  <rect x="20" y="32" width="640" height="20" rx="3" fill="#e89822"/>
  <text x="340" y="46" text-anchor="middle" class="t14">NIVEL 3 · SDN — plano de CONTROL separado del plano de DATOS</text>
  <rect x="20" y="58" width="200" height="46" rx="4" fill="#0055a0"/>
  <text x="120" y="76" text-anchor="middle" class="s14">Aplicaciones y políticas</text>
  <text x="120" y="92" text-anchor="middle" class="s14">(seguridad, red, negocio)</text>
  <path d="M224 81 L252 81" stroke="#e89822" stroke-width="2.5" marker-end="url(#a14)"/>
  <defs><marker id="a14" markerWidth="8" markerHeight="8" refX="7" refY="3.5" orient="auto"><path d="M0,0 L7,3.5 L0,7 z" fill="#e89822"/></marker></defs>
  <text x="238" y="74" text-anchor="middle" style="font:700 8px system-ui;fill:#a06000">NORTE</text>
  <rect x="256" y="58" width="200" height="46" rx="4" fill="#d13c3c"/>
  <text x="356" y="76" text-anchor="middle" class="s14">CONTROLADOR SDN</text>
  <text x="356" y="92" text-anchor="middle" class="s14">visión global de la topología</text>
  <path d="M460 81 L488 81" stroke="#e89822" stroke-width="2.5" marker-end="url(#a14)"/>
  <text x="474" y="74" text-anchor="middle" style="font:700 8px system-ui;fill:#a06000">SUR</text>
  <rect x="492" y="58" width="168" height="46" rx="4" fill="#888"/>
  <text x="576" y="76" text-anchor="middle" class="s14">Conmutadores físicos y</text>
  <text x="576" y="92" text-anchor="middle" class="s14">virtuales: solo reenvían</text>
  <rect x="20" y="112" width="640" height="20" rx="3" fill="#2d8659"/>
  <text x="340" y="126" text-anchor="middle" class="t14">NIVEL 2 · SUPERPOSICIÓN (overlay) — la red lógica VIAJA CON LA MÁQUINA VIRTUAL</text>
  <rect x="20" y="138" width="206" height="50" rx="4" fill="#f2f6fa" stroke="#d13c3c" stroke-width="2"/>
  <text x="123" y="155" text-anchor="middle" style="font:700 10px system-ui;fill:#a02020">VLAN 802.1Q</text>
  <text x="123" y="170" text-anchor="middle" class="n14">12 bits de identificador</text>
  <text x="123" y="183" text-anchor="middle" class="n14">solo 4.094 segmentos útiles</text>
  <rect x="237" y="138" width="206" height="50" rx="4" fill="#eaf5ec" stroke="#2d8659" stroke-width="2"/>
  <text x="340" y="155" text-anchor="middle" style="font:700 10px system-ui;fill:#2d8659">VXLAN (RFC 7348)</text>
  <text x="340" y="170" text-anchor="middle" class="n14">VNI de 24 bits sobre UDP</text>
  <text x="340" y="183" text-anchor="middle" class="n14">~16 millones de segmentos</text>
  <rect x="454" y="138" width="206" height="50" rx="4" fill="#f2f6fa" stroke="#0055a0" stroke-width="2"/>
  <text x="557" y="155" text-anchor="middle" style="font:700 10px system-ui;fill:#0055a0">Geneve (RFC 8926)</text>
  <text x="557" y="170" text-anchor="middle" class="n14">encapsulado extensible</text>
  <text x="557" y="183" text-anchor="middle" class="n14">con opciones TLV</text>
  <rect x="20" y="196" width="640" height="20" rx="3" fill="#0055a0"/>
  <text x="340" y="210" text-anchor="middle" class="t14">NIVEL 1 · CONMUTADOR VIRTUAL EN CADA ANFITRIÓN</text>
  <rect x="20" y="222" width="316" height="66" rx="4" fill="#0055a0"/>
  <text x="178" y="240" text-anchor="middle" class="s14">Funciones de conmutador de nivel 2:</text>
  <text x="178" y="255" text-anchor="middle" class="s14">reenvío por MAC · etiquetado VLAN por grupo de puertos</text>
  <text x="178" y="270" text-anchor="middle" class="s14">agregación de enlaces (NIC teaming)</text>
  <text x="178" y="283" text-anchor="middle" class="s14">políticas de puerto (modo promiscuo, MAC falsificada)</text>
  <rect x="344" y="222" width="316" height="66" rx="4" fill="#d13c3c"/>
  <text x="502" y="240" text-anchor="middle" class="s14">MICROSEGMENTACIÓN</text>
  <text x="502" y="255" text-anchor="middle" class="s14">cortafuegos DISTRIBUIDO con reglas POR MÁQUINA VIRTUAL</text>
  <text x="502" y="270" text-anchor="middle" class="s14">controla el tráfico ESTE-OESTE dentro del centro de datos</text>
  <text x="502" y="283" text-anchor="middle" class="s14">que el cortafuegos perimetral NUNCA ve</text>
  <rect x="20" y="296" width="640" height="26" rx="4" fill="#333"/>
  <text x="340" y="313" text-anchor="middle" class="t14">RED FÍSICA — solo tiene que encaminar paquetes IP entre anfitriones</text>
  <rect x="20" y="330" width="640" height="20" rx="3" fill="none" stroke="#0055a0" stroke-width="2"/>
  <text x="340" y="344" text-anchor="middle" class="k14">Sin virtualizar la red, crear una máquina virtual en minutos no sirve de nada: conectarla seguiría tardando días</text>
  <text x="670" y="358" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: RFC7348; RFC8926; ONF-SDN; NIST-SP800-125]</text>
</svg>
```

---

## D15 · Continuidad: RTO, RPO y regla 3-2-1

**Sección**: §5.3 — Continuidad del negocio, copias de seguridad y recuperación
**Propósito**: Fijar visualmente la diferencia entre RPO (hacia atrás) y RTO (hacia delante) en una línea temporal, y recordar la regla 3-2-1 ampliada.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 328" role="img" aria-label="Línea temporal de un incidente que muestra el RPO como la ventana de datos perdidos entre el último punto de recuperación y el momento del fallo, y el RTO como el tiempo desde el fallo hasta el restablecimiento del servicio, junto con la regla 3-2-1 ampliada con una copia inmutable">
  <style>.t15{font:700 10.5px system-ui,sans-serif;fill:#fff}.s15{font:9px system-ui,sans-serif;fill:#fff}.h15{font:700 13px system-ui,sans-serif;fill:#0055a0}.k15{font:700 10.5px system-ui,sans-serif;fill:#0055a0}.n15{font:9px system-ui,sans-serif;fill:#333}</style>
  <text x="340" y="20" text-anchor="middle" class="h15">RPO mira hacia atrás (cuánto pierdo) · RTO mira hacia delante (cuánto tardo)</text>
  <line x1="40" y1="106" x2="650" y2="106" stroke="#333" stroke-width="2"/>
  <path d="M644 101 L654 106 L644 111 z" fill="#333"/>
  <text x="655" y="122" text-anchor="end" class="n15">tiempo →</text>
  <line x1="210" y1="90" x2="210" y2="122" stroke="#0055a0" stroke-width="3"/>
  <text x="210" y="84" text-anchor="middle" class="k15">Último punto</text>
  <text x="210" y="138" text-anchor="middle" class="n15">de recuperación válido</text>
  <line x1="360" y1="86" x2="360" y2="126" stroke="#d13c3c" stroke-width="3"/>
  <text x="360" y="78" text-anchor="middle" style="font:700 11px system-ui;fill:#a02020">FALLO</text>
  <text x="360" y="142" text-anchor="middle" class="n15">caída del servicio</text>
  <line x1="560" y1="90" x2="560" y2="122" stroke="#2d8659" stroke-width="3"/>
  <text x="560" y="84" text-anchor="middle" style="font:700 10.5px system-ui;fill:#2d8659">Servicio</text>
  <text x="560" y="138" text-anchor="middle" class="n15">restablecido</text>
  <rect x="210" y="152" width="150" height="34" rx="4" fill="#e89822"/>
  <text x="285" y="167" text-anchor="middle" class="t15">RPO</text>
  <text x="285" y="181" text-anchor="middle" class="s15">datos que se pierden</text>
  <rect x="360" y="152" width="200" height="34" rx="4" fill="#0055a0"/>
  <text x="460" y="167" text-anchor="middle" class="t15">RTO</text>
  <text x="460" y="181" text-anchor="middle" class="s15">tiempo hasta volver a dar servicio</text>
  <rect x="40" y="196" width="290" height="42" rx="4" fill="#f2f6fa" stroke="#e89822" stroke-width="2"/>
  <text x="185" y="213" text-anchor="middle" class="n15">RPO de 24 h → copia diaria basta</text>
  <text x="185" y="230" text-anchor="middle" class="n15">RPO ≈ 0 → replicación SÍNCRONA</text>
  <rect x="350" y="196" width="290" height="42" rx="4" fill="#f2f6fa" stroke="#0055a0" stroke-width="2"/>
  <text x="495" y="213" text-anchor="middle" class="n15">RTO de horas → restauración clásica</text>
  <text x="495" y="230" text-anchor="middle" class="n15">RTO de minutos → recuperación instantánea o réplica</text>
  <rect x="40" y="248" width="600" height="22" rx="3" fill="#d13c3c"/>
  <text x="340" y="264" text-anchor="middle" class="t15">Los fija el ANÁLISIS DE IMPACTO (BIA), no el técnico: de ellos se derivan tecnología y coste, nunca al revés</text>
  <rect x="40" y="278" width="145" height="30" rx="4" fill="#2d8659"/><text x="112" y="297" text-anchor="middle" class="s15">3 copias de los datos</text>
  <rect x="192" y="278" width="145" height="30" rx="4" fill="#2d8659"/><text x="264" y="297" text-anchor="middle" class="s15">2 tipos de soporte</text>
  <rect x="344" y="278" width="145" height="30" rx="4" fill="#2d8659"/><text x="416" y="297" text-anchor="middle" class="s15">1 fuera del emplazamiento</text>
  <rect x="496" y="278" width="144" height="30" rx="4" fill="#d13c3c"/><text x="568" y="292" text-anchor="middle" class="s15">+1 INMUTABLE o aislada</text><text x="568" y="304" text-anchor="middle" class="s15">frente a ransomware</text>
  <text x="670" y="318" text-anchor="end" style="font:10px system-ui;fill:#666">[Fuente: NIST-SP800-34; ISO22301; ENS]</text>
</svg>
```

---

## Nota de accesibilidad y de QA

- Todos los diagramas llevan `role="img"` y `aria-label` descriptivo en español, con las tildes correctas.
- La paleta se mantiene legible impresa en blanco y negro porque cada bloque combina color de fondo **y** posición en la composición; ninguna información depende exclusivamente del color.
- Las clases CSS internas de cada SVG llevan **sufijo numérico único** (`.t3`, `.s3`, `.h3`…) para evitar el fallo sistémico de colisión de estilos entre SVG embebidos en la misma página.
- El ancho de referencia es de 680 unidades de `viewBox`, escalable al 100 % del contenedor sin desbordes.
