# Tema 28 — Fuentes

> **Título oficial**: Virtualización de sistemas y virtualización de puestos de usuario.
>
> **Criterio**: todo dato del contenido cita un **ID** inline (p. ej. `[POPEK74]`). Tier 1 = literatura fundacional, especificaciones de arquitectura y de estándares abiertos, y normativa vigente (Popek y Goldberg, Intel, AMD, Arm, PCI-SIG, OASIS, OCI, IETF, ONF, ETSI, NIST, ISO, BOE). Tier 2 = documentación de productos y proyectos concretos, citada para ilustrar sin atar el tema a un único fabricante. Tier 3 = material de contexto no citado como contenido técnico.

---

## Tier 1 — Literatura fundacional, especificaciones y normativa

| ID | Referencia |
|---|---|
| `[POPEK74]` | Popek, G. J. y Goldberg, R. P. *Formal Requirements for Virtualizable Third Generation Architectures*. Communications of the ACM, vol. 17, n.º 7 (julio de 1974). Define las tres propiedades exigibles a un monitor de máquina virtual (equivalencia, control de recursos y eficiencia) y el teorema de virtualizabilidad. Referencia canónica del concepto. |
| `[GOLDBERG73]` | Goldberg, R. P. *Architectural Principles for Virtual Computer Systems*. Harvard University / ESD-TR-73-105 (1973). Origen de la clasificación de los monitores en **Tipo 1** y **Tipo 2**. |
| `[ROBIN00]` | Robin, J. S. y Irvine, C. E. *Analysis of the Intel Pentium's Ability to Support a Secure Virtual Machine Monitor*. 9th USENIX Security Symposium (2000). Identifica las **17 instrucciones sensibles no privilegiadas** que impedían virtualizar x86 de forma clásica. |
| `[INTEL-SDM]` | Intel Corporation. *Intel 64 and IA-32 Architectures Software Developer's Manual, Volume 3C: System Programming Guide* — extensiones **VMX** (Intel VT-x): modos VMX raíz y no raíz, estructura **VMCS**, transiciones *VM entry* / *VM exit*, tablas de páginas extendidas (**EPT**). |
| `[INTEL-VTD]` | Intel Corporation. *Intel Virtualization Technology for Directed I/O (VT-d) Architecture Specification*. Unidad de gestión de memoria de E/S (**IOMMU**): reasignación de DMA y de interrupciones, requisito de la asignación directa de dispositivos. |
| `[AMD-APM]` | Advanced Micro Devices. *AMD64 Architecture Programmer's Manual, Volume 2: System Programming* — extensión **SVM** (AMD-V), estructura **VMCB**, paginación anidada (**NPT/RVI**) y AMD-Vi (IOMMU). |
| `[ARM-ARM]` | Arm Limited. *Arm Architecture Reference Manual for A-profile architecture* — nivel de excepción **EL2** para el hipervisor y traducción en **dos etapas** (*Stage-1* / *Stage-2*). |
| `[PCI-SRIOV]` | PCI-SIG. *Single Root I/O Virtualization and Sharing Specification (SR-IOV)*. Reparto de un dispositivo PCI Express en una **función física (PF)** y varias **funciones virtuales (VF)** asignables a máquinas virtuales. |
| `[VIRTIO]` | OASIS. *Virtual I/O Device (VIRTIO) Specification, Version 1.2*. Estándar abierto de dispositivos **paravirtualizados** (virtio-net, virtio-blk, virtio-scsi, virtio-fs) y de sus colas virtuales. |
| `[OCI-SPEC]` | Open Container Initiative. *Runtime Specification* e *Image Format Specification*. Estandarización del formato de imagen y del ciclo de vida de un contenedor. |
| `[RFC7348]` | IETF. *RFC 7348: Virtual eXtensible Local Area Network (VXLAN)* (2014). Superposición de red de capa 2 sobre capa 3 con identificador **VNI de 24 bits** encapsulado en UDP. |
| `[RFC8926]` | IETF. *RFC 8926: Geneve — Generic Network Virtualization Encapsulation* (2020). Encapsulado de superposición extensible mediante opciones TLV. |
| `[RFC7364]` | IETF. *RFC 7364: Problem Statement — Overlays for Network Virtualization*. Justificación de las redes de superposición en centros de datos multiinquilino. |
| `[RFC7143]` | IETF. *RFC 7143: Internet Small Computer System Interface (iSCSI) Protocol*. Transporte de SCSI sobre TCP/IP, base del almacenamiento en bloque compartido de bajo coste. |
| `[RFC6143]` | IETF. *RFC 6143: The Remote Framebuffer Protocol (RFB)*. Protocolo abierto en el que se basa VNC. |
| `[ONF-SDN]` | Open Networking Foundation. *SDN Architecture (TR-521)* y *OpenFlow Switch Specification*. Separación de plano de control y plano de datos, controlador centralizado e interfaces norte/sur. |
| `[ETSI-NFV]` | ETSI. *GS NFV 002 — Network Functions Virtualisation (NFV): Architectural Framework* y serie **NFV-MANO**. Virtualización de funciones de red y su orquestación. |
| `[SNIA-SSM]` | SNIA. *Shared Storage Model* y *Storage Virtualization Tutorial*. Modelo de referencia de la virtualización del almacenamiento en bloque, fichero y objeto. |
| `[NIST-SP800-125]` | NIST. *SP 800-125: Guide to Security for Full Virtualization Technologies*; *SP 800-125A Rev. 1: Security Recommendations for Server-based Hypervisor Platforms*; *SP 800-125B: Secure Virtual Network Configuration for Virtual Machine (VM) Protection*. Referencia de seguridad del hipervisor y de la red virtual. |
| `[NIST-SP800-190]` | NIST. *SP 800-190: Application Container Security Guide*. Riesgos y contramedidas propios de los contenedores (imágenes, registros, orquestador, tiempo de ejecución). |
| `[NIST-SP800-34]` | NIST. *SP 800-34 Rev. 1: Contingency Planning Guide for Federal Information Systems*. Origen de los objetivos **RTO** y **RPO** y de la clasificación de emplazamientos alternativos. |
| `[ISO27001]` | ISO/IEC 27001:2022 e ISO/IEC 27002:2022. Sistema de gestión de seguridad de la información y catálogo de controles. |
| `[ISO22301]` | ISO 22301:2019. *Security and resilience — Business continuity management systems — Requirements*. Marco del plan de continuidad de negocio. |
| `[ISO30134]` | ISO/IEC 30134-2. *Information technology — Data centres — Key performance indicators — Part 2: Power usage effectiveness (PUE)*. Definición normalizada del indicador PUE. |
| `[ENS]` | **Real Decreto 311/2022, de 3 de mayo**, por el que se regula el Esquema Nacional de Seguridad (BOE de 4 de mayo de 2022). Categorías, dimensiones de seguridad, marcos de medidas del Anexo II, auditoría y conformidad. |
| `[ENI]` | **Real Decreto 4/2010, de 8 de enero**, por el que se regula el Esquema Nacional de Interoperabilidad. |
| `[LEY40-2015]` | **Ley 40/2015, de 1 de octubre**, de Régimen Jurídico del Sector Público. Título preliminar, capítulo V (funcionamiento electrónico del sector público) y arts. 156-158 (reutilización y transferencia de tecnología, medios de la Administración electrónica). |
| `[LEY39-2015]` | **Ley 39/2015, de 1 de octubre**, del Procedimiento Administrativo Común de las Administraciones Públicas. |
| `[RGPD]` | **Reglamento (UE) 2016/679** General de Protección de Datos. Arts. 5, 24, 25, 28, 32, 33-34, 35 y 44-49. |
| `[LOPDGDD]` | **Ley Orgánica 3/2018, de 5 de diciembre**, de Protección de Datos Personales y garantía de los derechos digitales. Disposición adicional primera (medidas de seguridad en el sector público, por remisión al ENS). |
| `[LCSP]` | **Ley 9/2017, de 8 de noviembre**, de Contratos del Sector Público. Art. 1.3 (incorporación de criterios sociales y medioambientales) y art. 145 (criterios de adjudicación). |
| `[EED2023]` | **Directiva (UE) 2023/1791** relativa a la eficiencia energética (refundición). Artículo 12: obligación de información sobre el rendimiento energético de los centros de datos a partir de un determinado umbral de potencia instalada. |
| `[CCN-STIC]` | Centro Criptológico Nacional. Serie de guías **CCN-STIC** de configuración segura y de cumplimiento del ENS (entre ellas la CCN-STIC-823 sobre utilización de servicios en la nube y los perfiles de cumplimiento específicos). Referencia de bastionado en el sector público español. |

## Tier 2 — Productos, proyectos y documentación de fabricante

| ID | Referencia |
|---|---|
| `[VMWARE-DOC]` | VMware / Broadcom. *vSphere Documentation* — ESXi, vCenter Server, vMotion y Storage vMotion, HA, DRS, DPM, Fault Tolerance, VMFS, conmutador distribuido y vSAN. |
| `[HYPERV-DOC]` | Microsoft. *Hyper-V documentation*, *Failover Clustering*, *Live Migration*, *Storage Spaces Direct*, *Cluster Shared Volumes*. learn.microsoft.com. |
| `[KVM-DOC]` | Proyecto KVM/QEMU y *libvirt*. Hipervisor integrado en el núcleo Linux (módulo `kvm`), emulación de dispositivos con QEMU y gestión con libvirt. |
| `[XEN-DOC]` | Xen Project. *Xen Hypervisor Documentation* — arquitectura `dom0`/`domU`, paravirtualización (PV), HVM y modo PVH, *hypercalls*. |
| `[OPENSTACK]` | OpenStack Foundation. *OpenStack Documentation* — Nova (cómputo), Neutron (red), Cinder (bloque), Glance (imágenes), Keystone (identidad). |
| `[OVIRT]` | Proyecto oVirt / Red Hat Virtualization. Gestión centralizada de clústeres KVM. |
| `[PROXMOX]` | Proxmox Server Solutions. *Proxmox VE Administration Guide* — KVM y contenedores LXC en una misma plataforma de gestión de código abierto. |
| `[DOCKER-DOC]` | Docker Inc. *Docker Documentation* — imágenes por capas, `Dockerfile`, redes y volúmenes, `containerd`. |
| `[K8S-DOC]` | The Kubernetes Authors. *Kubernetes Documentation* — pod, nodo, `kubelet`, plano de control, controladores y planificador. |
| `[LINUX-NS]` | Documentación del núcleo Linux: `namespaces(7)`, `cgroups(7)`, `capabilities(7)`, `seccomp(2)`, `overlayfs`. Primitivas de aislamiento sobre las que se construyen los contenedores. |
| `[MS-RDPBCGR]` | Microsoft. *[MS-RDPBCGR]: Remote Desktop Protocol — Basic Connectivity and Graphics Remoting*, especificación abierta. Protocolo RDP y su canal de transporte. |
| `[CITRIX-DOC]` | Citrix. *Citrix Virtual Apps and Desktops Documentation* — protocolo **ICA/HDX**, *Delivery Controller*, *StoreFront*, *Machine Creation Services* y *Provisioning Services*. |
| `[HORIZON-DOC]` | VMware / Omnissa. *Horizon Documentation* — *Connection Server*, protocolo **Blast Extreme**, clones instantáneos (*instant clones*), *App Volumes*. |
| `[AVD-DOC]` | Microsoft. *Azure Virtual Desktop* y *Remote Desktop Services (RDS)* — escritorios y aplicaciones publicadas, sesiones múltiples, *host pool*. |
| `[FSLOGIX]` | Microsoft. *FSLogix profile containers* — contenedor de perfil de usuario en disco virtual para escritorios no persistentes. |
| `[APPV]` | Microsoft *App-V*, *MSIX app attach*; VMware *ThinApp*. Virtualización de aplicaciones mediante empaquetado aislado. |
| `[SPICE]` | Proyecto SPICE (Red Hat). *Simple Protocol for Independent Computing Environments*. |
| `[PCOIP]` | Teradici / HP Anyware. *PCoIP Technology* — protocolo de representación remota sobre UDP con compresión progresiva de imagen. |
| `[NVIDIA-VGPU]` | NVIDIA. *Virtual GPU Software Documentation* — reparto de GPU entre máquinas virtuales para puestos con carga gráfica. |
| `[TERRAFORM]` | HashiCorp *Terraform* y Red Hat *Ansible*. Infraestructura como código y automatización de la configuración. |
| `[PROMETHEUS]` | Cloud Native Computing Foundation. *Prometheus* y *Grafana*. Recolección de métricas y cuadros de mando de la infraestructura virtualizada. |

## Tier 3 — Contexto no citado como contenido técnico

| ID | Referencia |
|---|---|
| `[HIST-IBM]` | Documentación histórica de IBM sobre CP-40, CP-67/CMS y VM/370 (1967-1972), primeros sistemas de máquinas virtuales completas en tiempo compartido. Citado solo como antecedente histórico. |
| `[MERCADO]` | Informes de analistas de mercado sobre cuotas de hipervisores y plataformas VDI. **No citados** como fuente de contenido: los datos de cuota de mercado son volátiles y no forman parte del temario. |
| `[FABRICANTES-COMERCIAL]` | Material comercial de fabricantes (fichas de producto, notas de prensa). **No citado**: las cifras de rendimiento y de ahorro de origen comercial no se consideran fuente técnica. |

---

## Nota sobre la volatilidad del tema

Los **conceptos** de este tema (los principios de Popek y Goldberg, las tres técnicas de virtualización, la clasificación de hipervisores, el modelo VDI, la separación de planos de SDN, los objetivos RTO/RPO) son **estables**. Los **productos** que los implementan cambian de nombre, de propietario y de licencia con frecuencia. Por eso el contenido apoya cada afirmación técnica en fuente Tier 1 y usa los productos de Tier 2 únicamente como ilustración, indicando siempre a qué concepto general corresponden.
