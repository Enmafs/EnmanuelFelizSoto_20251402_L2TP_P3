# 🔐 Lab 9 — Client-to-Site VPN — L2TP/IPSec (Windows Nativo)

**Estudiante:** Enmanuel Feliz Soto | **Matrícula:** 2025-1402  
**Institución:** Instituto Tecnológico de Las Américas (ITLA)  
**Curso:** Seguridad en Redes | **Sección:** 5  Lunes
**Docente:** Jonathan Esteban Rondón Corniel

---

## 📋 Descripción

VPN de acceso remoto (Client-to-Site) para usuarios individuales. Utiliza L2TP para el encapsulamiento de túnel de Capa 2 y PPP para la autenticación de usuarios (MS-CHAPv2), asegurado por IPsec para cifrar la carga útil en Modo Transporte.

| Campo | Valor |
|-------|-------|
| **Tipo de VPN** | Client-to-Site (Acceso Remoto) |
| **Protocolo** | L2TP + IPSec (IKEv1 ESP-AES256-SHA) |
| **Mecanismo** | VPDN + Crypto Map Dinámico + Virtual-Template |
| **Routing** | Asignación IP dinámica por DHCP (Pool Local) al cliente |
| **Pre-shared Key** | `PskSecreto123` |

---

## 🗺️ Topología

> 📸 <img width="1376" height="733" alt="image" src="https://github.com/user-attachments/assets/e246d7e9-9be4-42c2-a61b-561599650346" />


**Entorno:** PNetLab (Proxmox VE) — Cisco IOL ↔ Host Físico (Windows 10/11)  
**Peers:** Cliente Nativo Windows (10.0.0.153) ↔ R1 Gateway (10.0.0.173)

### Tabla de Direccionamiento

| **Dispositivo** | **Rol** | **IP WAN / Física** | **Interfaz WAN** | **IP VPN** | **Interfaz VPN** |
|-----------------|---------|---------------------|------------------|------------|------------------|
| Windows PC      | Cliente / Iniciador | 10.0.0.153/24 | Wi-Fi / Ethernet | 172.16.10.10 | Adaptador PPP |
| R1              | Gateway / Respondedor | 10.0.0.173/24 | Ethernet0/0 | Pool Asignado | Virtual-AccessX |

### Dirección Túnel (Pool Asignado)
| Endpoint | Rango IP L2TP |
|----------|---------------|
| R1 Pool_VPN | `172.16.10.10` al `172.16.10.50` |

---

## ⚙️ Configuración

El script completo de configuración se encuentra en:  
📄 [`EnmanuelFelizSoto_2025-1402_ClientToSite_L2TP_IPSec.txt`](./EnmanuelFelizSoto_2025-1402_ClientToSite_L2TP_IPSec.txt)

### Parámetros IKE/IPSec y PPP

| Parámetro | Valor |
|-----------|-------|
| IPsec Mode | Transport Mode |
| IKE Encryption | AES-256 |
| IKE Hash/Integrity| SHA-1 *(Requerido por cliente Windows nativo)* |
| DH Group | 14 (2048-bit) |
| IPsec Transform | ESP-AES-256, ESP-SHA-HMAC |
| Auth Method (Fase 1)| Pre-Shared Key |
| Auth Method (PPP) | MS-CHAPv2 *(Contraseña local en plaintext)* |
| NAT-T | udp-encapsulation (Soportado) |

---

## ▶️ Procedimiento de Ejecución

### 1. Cargar configuración en PNetLab
# Aplicar script en el R1 Gateway
# 1. Configurar interfaces y routing base.
# 2. Pegar script de IKEv1, VPDN, Transform-Set y Virtual-Template.

**2. Configurar Cliente Windows**
# 1. Crear VPN: Servidor 10.0.0.173, Tipo "L2TP/IPsec", PSK: PskSecreto123
# 2. Forzar Seguridad: ncpa.cpl > Propiedades VPN > Seguridad > MS-CHAPv2
# 3. Validar Parche NAT-T (Si aplica): AssumeUDPEncapsulationContextOnSendRule = 2
# 4. Conectar usando credenciales: vpncliente / cisco123

**3. Verificar la VPN en R1**
show crypto isakmp sa
show crypto ipsec sa
show vpdn
show ip int br

---

**🔍 Análisis y ComparativaVentajas y Consideraciones de esta VPN**
Ver documentación técnica y apuntes de "Troubleshooting Windows L2TP Error 691/789" en el informe PDF.Diferencias con otros labs
Ver tabla comparativa en el README principal.

---

| Recurso | Enlace |
|---------|--------|
| Repositorio Principal | [Enmafs/NetSec](https://github.com/Enmafs/NetSec) |
| Script de configuración | [`EnmanuelFelizSoto_20251402_L2TP_P3.txt`](./EnmanuelFelizSoto_20251402_L2TP_P3.txt) |
| Video demostración | 🎬 [Aquí](https://www.youtube.com/watch?v=-JKA2c4Z7Uo) |

---

> ⚠️ *Laboratorio realizado en entorno controlado (PNetLab). Fines exclusivamente académicos.*
