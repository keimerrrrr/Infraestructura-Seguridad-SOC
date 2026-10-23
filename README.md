# 🛡️ Infraestructura-Seguridad-SOC

## 🎯 Objetivo del Proyecto
El objetivo principal de este proyecto es diseñar, desplegar y operar un Centro de Operaciones de Seguridad (SOC) empresarial en un entorno virtualizado. A través de la integración de Wazuh SIEM, telemetría avanzada de Sysmon y un entorno de Directorio Activo (Windows Server 2022), el laboratorio busca cazar tácticas avanzadas de atacantes mapeadas al framework MITRE ATT&CK (como evasión de defensas y técnicas Living off the Land), automatizando el análisis de malware mediante Inteligencia de Amenazas (CTI) con VirusTotal.

## 🏗️ Topología y Arquitectura de Red (bancolab.local)
* **Controlador de Dominio (Tier 0):** Windows Server 2022 (`DC-BANCO-01` | IP: `192.168.100.10`)
* **Endpoint de Usuario (Tier 2):** Windows 11 (`PC-CAJA-01` | IP: `192.168.100.20`)
* **Cerebro SIEM / Manager:** Ubuntu Server / Wazuh 4.8.2 (`siem-server` | IP: `192.168.100.30`)

## 🛠️ Stack Tecnológico y Hardening
* **SIEM / XDR:** Wazuh (Ingesta de logs, análisis de comportamiento y alertas).
* **Telemetría Avanzada:** Sysmon implementado con reglas corporativas de **SwiftOnSecurity** para reducción de ruido e identificación de Indicadores de Compromiso (IoCs).
* **Inteligencia de Amenazas (CTI):** Integración de **VirusTotal API** al módulo File Integrity Monitoring (FIM).
* **Directorio Activo:** Implementación de políticas de grupo (GPOs) para habilitar auditorías de seguridad avanzadas en los endpoints.

## 🚀 Threat Hunting y Simulaciones de Ataque
Durante este laboratorio, se simuló el rol de *Red Team* para evaluar la capacidad de detección del *Blue Team*:

1. **Evasión de Defensas y Descubrimiento (T1059.001 / T1087):** 
   Detección exitosa de ejecución de comandos de PowerShell ofuscados (`-ExecutionPolicy Bypass -WindowStyle Hidden`) intentando hacer enumeración de la red y cuentas de administrador.
   
2. **Living off the Land / Ingress Tool Transfer (T1105):** 
   Identificación de evasión del antivirus nativo (Windows Defender) al utilizar binarios legítimos del sistema (`certutil.exe -urlcache -split -f`) para descargar *payloads* desde servidores externos.

---
*Nota: Este repositorio se encuentra en construcción. Se estarán subiendo los archivos de configuración (`ossec.conf`, XML) y las evidencias en captura de pantalla del panel de alertas.*

```mermaid
graph TD
    %% Estilos de Nodos
    classDef attacker fill:#5e0000,stroke:#000,stroke-width:2px,color:#fff;
    classDef siem fill:#0f4c75,stroke:#000,stroke-width:2px,color:#fff;
    classDef windows fill:#1b5e20,stroke:#000,stroke-width:2px,color:#fff;
    classDef intel fill:#303030,stroke:#000,stroke-width:2px,color:#fff;

    %% Actores Externos
    A["☠️ Red Team / Atacante<br>(Simulación LotL)"]:::attacker
    E["🌐 VirusTotal API<br>(Threat Intelligence)"]:::intel

    subgraph SOC ["🛡️ Security Operations Center (Blue Team)"]
        B("🧠 Wazuh Manager (SIEM/XDR)<br>Ubuntu Server 22.04 | 192.168.100.30"):::siem
    end

    subgraph LAN ["🏢 Corporate Network (bancolab.local)"]
        C["🏰 Domain Controller<br>Windows Server 2022 | 192.168.100.10"]:::windows
        D["💻 Endpoint de Usuario<br>Windows 11 | 192.168.100.20"]:::windows
        
        %% Relación en la LAN
        C -.-|"GPOs & LAPS"| D
    end

    %% Flujos de Ataque y Defensa
    A -.->|"Inyección de Tácticas<br>(T1105 / T1087)"| D
    C ==>|"Logs (Sysmon + Eventos AD)"| B
    D ==>|"Logs (Sysmon + FIM)"| B
    B <-->|"Análisis de Hashes / IoCs"| E

    %% Estilos de Enlaces
    linkStyle 1 stroke:#ff0000,stroke-width:2px,stroke-dasharray: 5 5;
