# Wazuh — Agente y SIEM


---

## Arquitectura general

```
[ Servidor / Endpoint ]
         ↓
  [ Wazuh Agent ]         ← instalado en cada máquina a monitorear
         ↓
  [ Wazuh Manager ]       ← recibe, procesa y correlaciona eventos
         ↓
  [ Wazuh Indexer ]       ← almacena los datos (basado en OpenSearch)
         ↓
  [ Wazuh Dashboard ]     ← visualización, alertas y reportes
```

---

## Wazuh Agent


El agente es el componente que se instala **directamente en el endpoint** (servidor, PC, VM). Es el encargado de recopilar toda la información de seguridad y enviarla al Manager (SIEM).

### ¿Qué monitorea el agente?

| Capacidad | Descripción |
|---|---|
| **FIM** | Detecta cambios en archivos y directorios críticos |
| **Logs** | Recopila logs del sistema, aplicaciones y servicios |
| **Procesos** | Monitorea procesos en ejecución y detecta comportamiento anómalo |
| **Red** | Registra conexiones de red entrantes y salientes |
| **Vulnerabilidades** | Escanea CVEs en el SO y paquetes instalados |
| **Rootkits** | Detecta rootkits y malware oculto |
| **Active Response** | Ejecuta acciones automáticas ante amenazas (bloquear IP, matar proceso) |



### Configuración del agente

El agente se configura en `/var/ossec/etc/ossec.conf`:

```xml
<ossec_config>

  <!-- Conexión al Manager -->
  <client>
    <server>
      <address>IP_DEL_MANAGER</address>
      <port>1514</port>
      <protocol>tcp</protocol>
    </server>
  </client>

  <!-- Monitoreo de archivos (FIM) -->
  <syscheck>
    <frequency>43200</frequency>
    <directories>/etc,/usr/bin,/usr/sbin</directories>
    <directories>/var/www,/var/ossec/etc</directories>
  </syscheck>

  <!-- Recolección de logs -->
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/syslog</location>
  </localfile>

  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/auth.log</location>
  </localfile>

</ossec_config>
```

---


### ¿Qué hace el SIEM con los datos?

```
Evento recibido del agente
        ↓
Decodificación del log
        ↓
Aplicación de reglas (MITRE ATT&CK)
        ↓
Generación de alerta con nivel de severidad
        ↓
Almacenamiento en Indexer
        ↓
Visualización en Dashboard
```

### Niveles de alerta en Wazuh

| Nivel | Severidad | Ejemplo |
|---|---|---|
| 0 - 3 | Informativo | Login exitoso |
| 4 - 7 | Bajo / Medio | Múltiples intentos de login |
| 8 - 11 | Alto | Ataque de fuerza bruta detectado |
| 12 - 15 | Crítico | Rootkit o malware detectado |

---

## Flujo completo: Agente → SIEM

```
1. El agente detecta un evento (ej: login fallido)
          ↓
2. Envía el evento al Manager por puerto 1514 (TCP/UDP)
          ↓
3. El Manager decodifica y aplica reglas
          ↓
4. Si coincide con una regla → genera alerta
          ↓
5. La alerta se indexa en Wazuh Indexer
          ↓
6. Visible en el Dashboard con nivel de severidad
          ↓
7. Si hay Active Response → acción automática (bloquear IP, etc.)
```

---

## Puertos importantes

| Puerto | Protocolo | Uso |
|---|---|---|
| **1514** | TCP/UDP | Comunicación Agente → Manager |
| **1515** | TCP | Registro de nuevos agentes |
| **55000** | TCP | API REST del Manager |
| **9200** | HTTPS | Wazuh Indexer (OpenSearch) |
| **443** | HTTPS | Wazuh Dashboard |

---

## Integraciones del SIEM

Wazuh puede recibir datos de fuentes externas además de sus agentes:

- **Syslog** — Firewalls, switches, routers
- **VirusTotal** — Análisis de archivos sospechosos
- **Slack / PagerDuty** — Notificaciones de alertas
- **Suricata / Zeek** — IDS/IPS de red
- **Docker** — Monitoreo de contenedores
- **AWS / Azure / GCP** — Logs de nube

---
## Documentación
https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html


