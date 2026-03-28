# 🦈 Wireshark — Análisis de Tráfico de Red (Malware)

## ¿Para qué sirve?

**Wireshark** es un analizador de protocolos de red (sniffer) que **captura y muestra en tiempo real todo el tráfico** que pasa por una interfaz de red.

En análisis de malware sirve para:

- Ver a qué **IPs o dominios** se conecta el malware (C2 / Command & Control)
- Detectar **exfiltración de datos** (archivos, credenciales, screenshots enviados)
- Analizar **protocolos usados** (HTTP, DNS, FTP, IRC, TCP raw, etc.)
- Ver si el malware descarga **payloads adicionales**
- Identificar **beaconing** (conexiones periódicas al C2)
- Reconstruir **archivos transferidos** por la red
- Ver **queries DNS** sospechosas (fast-flux, DGA domains)

> En resumen: te muestra todo lo que el malware habla por la red mientras corre.

---

## 📥 Descarga

| Plataforma | Link |
|------------|------|
| **Windows** (instalador) | https://www.wireshark.org/download.html |
| **Linux** (Debian/Ubuntu) | `sudo apt install wireshark` |
| **Linux** (Fedora/RHEL) | `sudo dnf install wireshark` |
| **macOS** (Homebrew) | `brew install --cask wireshark` |
| **Versión portable** | https://www.wireshark.org/download.html → *Portable* |

> ⚠️ En Windows, durante la instalación instalar también **Npcap** (captura de paquetes).  
> En Linux, agregar tu usuario al grupo `wireshark`: `sudo usermod -aG wireshark $USER`

---

## 🖥️ Uso básico

1. Abrir Wireshark
2. Seleccionar la **interfaz de red** activa (ej: `eth0`, `Wi-Fi`)
3. Hacer clic en el botón **azul de tiburón** (Start capture)
4. Ejecutar el malware en el entorno aislado
5. Detener la captura con el botón **rojo**
6. Guardar: `File` → `Save As` → formato `.pcap` o `.pcapng`

---

## 🔎 Filtros de captura (antes de capturar)

Se configuran en `Capture` → `Options` → campo **BPF filter**.  
Reducen el tráfico capturado desde el inicio.

```bash
# Solo tráfico HTTP y HTTPS
port 80 or port 443

# Solo tráfico DNS
port 53

# Excluir tráfico local
not host 127.0.0.1

# Solo tráfico de una IP específica
host 192.168.1.100

# Solo TCP
tcp

# Solo UDP
udp

# Rango de puertos
portrange 1024-65535
```

---

## 🔬 Filtros de visualización (después de capturar)

Se escriben en la **barra de filtro verde/azul** arriba del listado de paquetes.  
Son los más usados en análisis de malware.

### 🌐 Por protocolo

```wireshark
http
dns
ftp
smtp
tcp
udp
icmp
ssl
tls
```

### 🌍 Por IP o host

```wireshark
# Tráfico hacia/desde una IP
ip.addr == 185.220.101.5

# Solo tráfico saliente hacia esa IP
ip.dst == 185.220.101.5

# Excluir una IP
ip.addr != 192.168.1.1

# Rango de IPs
ip.addr >= 10.0.0.0 and ip.addr <= 10.0.0.255
```

### 🚪 Por puerto

```wireshark
# Puerto destino específico
tcp.port == 4444

# Puerto origen
tcp.srcport == 443

# Puertos no estándar (posible C2)
tcp.port != 80 and tcp.port != 443 and tcp.port != 53
```

### 🔗 HTTP / HTTPS

```wireshark
# Todas las requests HTTP
http.request

# Solo GET
http.request.method == "GET"

# Solo POST (exfiltración común)
http.request.method == "POST"

# Por User-Agent sospechoso
http.user_agent contains "Mozilla"

# Por URL que contenga string
http.request.uri contains ".php"
http.request.uri contains "gate"
http.request.uri contains "upload"

# Respuestas HTTP con ejecutables
http.content_type contains "application/octet-stream"
```

### 📡 DNS (muy útil para malware)

```wireshark
# Todas las consultas DNS
dns

# Queries (preguntas)
dns.flags.response == 0

# Respuestas DNS
dns.flags.response == 1

# Buscar dominio específico
dns.qry.name contains "malware-domain"

# Dominios largos/random (posible DGA)
dns.qry.name matches "[a-z]{15,}"

# Consultas tipo A
dns.qry.type == 1

# Consultas tipo TXT (exfiltración via DNS)
dns.qry.type == 16
```

### 🔒 TLS / SSL

```wireshark
# Todo el tráfico TLS
tls

# Handshakes (ver a qué se conecta)
tls.handshake

# Server Name Indication (dominio destino)
tls.handshake.extensions_server_name contains "dominio"

# Certificados autofirmados / sospechosos
tls.handshake.type == 11
```

### 🔴 Comportamiento sospechoso

```wireshark
# Conexiones a puertos RAT clásicos
tcp.port == 4444 or tcp.port == 1337 or tcp.port == 31337

# Beaconing: muchos paquetes similares en el tiempo
# Filtrar por IP C2 y ordenar por tiempo

# Escaneo de puertos (muchos SYN sin ACK)
tcp.flags.syn == 1 and tcp.flags.ack == 0

# Tráfico IRC (malware botnet viejo)
tcp.port == 6667 or tcp.port == 6668 or tcp.port == 6669

# Tráfico en puertos no estándar altos
tcp.dstport > 1024 and tcp.dstport != 3389 and tcp.dstport != 8080
```

---

## 🛠️ Funciones útiles de Wireshark

### Seguir un flujo completo
Click derecho en un paquete → **Follow** → **TCP Stream** (o HTTP Stream)  
→ Ver toda la conversación completa en texto legible

### Exportar objetos (archivos descargados por el malware)
`File` → `Export Objects` → `HTTP`  
→ Lista todos los archivos transferidos por HTTP (ejecutables, scripts, etc.)

### Ver estadísticas de conexiones
`Statistics` → `Conversations`  
→ Muestra todas las IPs con las que hubo comunicación, bytes enviados/recibidos

### Resolver IPs a hostnames
`View` → `Name Resolution` → activar todo  
→ Muestra dominios en lugar de IPs crudas

### Colorear por protocolo / sospecha
`View` → `Coloring Rules`  
→ Podés agregar reglas para resaltar tráfico sospechoso en rojo

---

## 🧪 Flujo típico de análisis con Wireshark

```
1. Preparar entorno aislado (VM sin acceso real a Internet
   o con INetSim/FakeNet-NG simulando servicios)
        │
        ▼
2. Iniciar captura en Wireshark
        │
        ▼
3. Ejecutar el malware
        │
        ▼
4. Esperar 2–5 minutos, detener captura
        │
        ▼
5. Filtrar: http.request / dns / tcp.port == XXXX
        │
        ▼
6. Identificar IPs/dominios C2
        │
        ▼
7. Buscar IoCs en:
   - VirusTotal (virustotal.com)
   - AbuseIPDB (abuseipdb.com)
   - Shodan (shodan.io)
   - Threat Intelligence Platforms
        │
        ▼
8. Exportar .pcap para documentar evidencia
```

---

## 🔄 Herramientas complementarias

| Herramienta | Uso |
|-------------|-----|
| **NetworkMiner** | Reconstruye archivos y sesiones del .pcap |
| **FakeNet-NG** | Simula servicios de red para que el malware "crea" que está conectado |
| **INetSim** | Similar a FakeNet, muy usado en Linux |
| **Zeek (Bro)** | Análisis de .pcap con scripts, genera logs estructurados |
| **Suricata** | IDS — detecta patrones de malware en el tráfico |
| **tshark** | Wireshark en línea de comandos |

### tshark (CLI de Wireshark)

```bash
# Capturar en interfaz eth0 y guardar
tshark -i eth0 -w captura.pcap

# Leer un .pcap y filtrar HTTP
tshark -r captura.pcap -Y "http.request"

# Extraer solo campos específicos
tshark -r captura.pcap -Y "dns" -T fields -e dns.qry.name

# Extraer todas las IPs destino
tshark -r captura.pcap -T fields -e ip.dst | sort | uniq -c | sort -rn
```

---

## 📚 Recursos para profundizar

- [Descarga oficial Wireshark](https://www.wireshark.org/download.html)
- [Wiki de filtros Wireshark](https://wiki.wireshark.org/DisplayFilters)
- [Sample PCAPs para practicar](https://www.malware-traffic-analysis.net/)
- [PCAP análisis — Brad Duncan](https://www.malware-traffic-analysis.net/training-exercises.html)
- [FakeNet-NG (Mandiant)](https://github.com/mandiant/flare-fakenet-ng)
- [NetworkMiner](https://www.netresec.com/?page=NetworkMiner)

---

> ⚠️ **Siempre analizá malware en una VM aislada** (sin acceso real a Internet o con red controlada).  
> Nunca ejecutes muestras en tu máquina host.
