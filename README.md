# 🛡️ CYBER Tools — Knowledge Base

## 🦠 Malware Analysis
- [BinText](Malware-Analisis/Bintext.md) — Extrae strings de binarios para encontrar URLs, rutas y APIs hardcodeadas sin ejecutar el archivo.
- [CFF Explorer](Malware-Analisis/CFF-Explorer.md) — Inspecciona y edita la estructura PE de ejecutables (headers, imports, exports, secciones).
- [INetSim](Malware-Analisis/InetSim.md) — Simula servicios de Internet (DNS, HTTP, SMTP) para capturar el comportamiento de red del malware en un entorno aislado.
- [PEstudio](Malware-Analisis/PEstudio.md) — Analiza estáticamente ejecutables PE mostrando imports, strings, indicadores y score de sospecha.
- [Process Hacker](Malware-Analisis/Process-Hacker.md) — Monitor avanzado de procesos que detecta inyección de código, memoria RWX y conexiones de red por proceso.
- [Process Monitor (ProcMon)](Malware-Analisis/ProcMon.md) — Registra en tiempo real toda la actividad de procesos: archivos tocados, claves de registro modificadas y conexiones de red.

## 🔁 Persistencia
- [Autoruns](Persistencia/Windows/Autoruns.md) — Muestra todo lo que se ejecuta automáticamente en Windows para detectar mecanismos de persistencia del malware.
- [Startup Apps](Persistencia/Windows/Startup-Apps.md) — Lista las aplicaciones configuradas para iniciar con Windows desde la interfaz nativa del sistema.

## 🌐 Captura y Análisis de Tráfico
- [Wireshark](Captura-y-Analisis-de-Trafico/Wireshark.md) — Captura y analiza tráfico de red en tiempo real para identificar conexiones C2, exfiltración y protocolos usados por el malware.

## 🗂️ Forense
- [Autopsy](Forense/Autopsy.md) — Plataforma de análisis forense digital para examinar discos, recuperar archivos eliminados y analizar artefactos del sistema.
- [Velociraptor](Forense/Velociraptor.md) — Framework de respuesta a incidentes y forense en vivo que permite consultar endpoints en escala con VQL.

## 📊 SIEM
- [Splunk](siem/splunk.md) — Plataforma de análisis de logs y eventos de seguridad con búsqueda, correlación y dashboards en tiempo real.
