# 🔁 Autoruns — Análisis de Persistencia de Malware

## ¿Para qué sirve?

**Autoruns** (de Sysinternals / Microsoft) es una herramienta que **muestra TODO lo que se ejecuta automáticamente en Windows**: al inicio, al login, al abrir ciertos programas, etc.

En análisis de malware sirve para:

- Detectar **mecanismos de persistencia** que el malware instaló
- Ver entradas sospechosas en el **registro de Windows** (Run, RunOnce, etc.)
- Identificar **servicios, drivers o tareas programadas** creadas por el malware
- Detectar **DLLs inyectadas** o hijacking de DLLs legítimas
- Ver **extensiones de browser** maliciosas instaladas
- Comparar el estado del sistema **antes y después** de ejecutar malware
- Encontrar **rootkits** que se cargan como drivers

> En resumen: si el malware quiere sobrevivir a un reinicio, Autoruns lo va a mostrar.

---

## 📥 Descarga

| Recurso | Link |
|---------|------|
| **Descarga oficial (Sysinternals)** | https://learn.microsoft.com/en-us/sysinternals/downloads/autoruns |
| **Suite completa Sysinternals** | https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite |
| **Directo ZIP** | https://download.sysinternals.com/files/Autoruns.zip |

> No requiere instalación — ejecutar `Autoruns.exe` (GUI) o `autorunsc.exe` (CLI) directamente.  
> Ejecutar siempre como **Administrador** para ver todas las entradas.

---

## 🖥️ Interfaz — Pestañas principales

| Pestaña | Qué muestra |
|---------|-------------|
| **Everything** | Todo junto (mejor para análisis completo) |
| **Logon** | Claves Run/RunOnce del registro, carpetas Startup |
| **Explorer** | Shell extensions, context menu handlers |
| **Internet Explorer** | BHOs, toolbars, extensiones IE |
| **Scheduled Tasks** | Tareas programadas |
| **Services** | Servicios de Windows |
| **Drivers** | Drivers del kernel (rootkits aquí) |
| **Boot Execute** | Ejecutados antes de que cargue Windows |
| **Image Hijacks** | IFEO — Image File Execution Options (técnica de persistencia) |
| **AppInit** | DLLs cargadas en TODOS los procesos |
| **Known DLLs** | DLLs del sistema que pueden ser hijackeadas |
| **Winlogon** | Hooks en el proceso de login |
| **Winsock Providers** | LSPs — pueden interceptar tráfico de red |
| **Print Monitors** | Persistencia via monitores de impresión |
| **Sidebar Gadgets** | Gadgets del escritorio |

---

## 🎨 Sistema de colores

| Color | Significado |
|-------|-------------|
| **Blanco** | Entrada normal verificada |
| **Amarillo** | El archivo referenciado **no existe** (entrada huérfana) |
| **Rojo** | Sin firma digital o firma no verificada — **sospechoso** |
| **Rosa/Fucsia** | Sin imagen (el archivo fue borrado pero la entrada queda) |
| **Verde** | Deshabilitado por el usuario |

> 💡 En malware: prestá atención a todo lo que esté en **rojo** o **amarillo**.

---

## ⚙️ Opciones clave (menú Options)

```
Options → Scan Options:
  ✅ Check VirusTotal.com          ← consulta hashes en VT automáticamente
  ✅ Submit Unknown Images          ← sube binarios no conocidos a VT
  ✅ Hide VirusTotal Clean Entries  ← oculta lo limpio, muestra solo sospechoso

Options → Filter Options:
  ✅ Hide Microsoft Entries         ← oculta entradas firmadas por Microsoft
  ✅ Hide Windows Entries           ← oculta entradas del sistema
```

> Activar **Hide Microsoft Entries** + **Check VirusTotal** es la combo más potente  
> para análisis rápido de malware: solo ves lo no-Microsoft y con score de VT.

---

## 🔎 ¿Dónde mira Autoruns? (ubicaciones clave)

### 🔑 Registro — Autostart más comunes

```
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon
HKLM\System\CurrentControlSet\Services
HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options
HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders
HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders
```

### 📁 Carpetas de inicio

```
C:\Users\<usuario>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
```

### 🛠️ Servicios y drivers

```
HKLM\System\CurrentControlSet\Services\
```

### ⏰ Tareas programadas

```
C:\Windows\System32\Tasks\
C:\Windows\SysWOW64\Tasks\
HKLM\Software\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache
```

---

## ⌨️ Autorunsc — Línea de comandos

`autorunsc.exe` es la versión CLI, ideal para scripting y automatización.

```cmd
REM Ver todas las entradas (formato texto)
autorunsc.exe -a *

REM Exportar todo a CSV
autorunsc.exe -a * -c > autoruns_output.csv

REM Solo entradas de autostart de login (Run/RunOnce)
autorunsc.exe -a l

REM Solo servicios
autorunsc.exe -a s

REM Solo tareas programadas
autorunsc.exe -a t

REM Solo drivers
autorunsc.exe -a d

REM Ocultar entradas de Microsoft (solo terceros)
autorunsc.exe -a * -m

REM Ocultar entradas verificadas por VT (solo sospechosas)
autorunsc.exe -a * -vt

REM Modo silencioso + exportar a CSV sin banner
autorunsc.exe -nobanner -a * -c > clean_output.csv

REM Escanear un usuario específico
autorunsc.exe -a * -u NombreUsuario

REM Verificar contra VirusTotal
autorunsc.exe -a * -vt -c > output_con_vt.csv
```

### Parámetros de `-a` (categorías)

| Flag | Categoría |
|------|-----------|
| `*`  | Todo |
| `b`  | Boot execute |
| `d`  | Appinit DLLs |
| `e`  | Explorer addons |
| `h`  | Image hijacks |
| `i`  | IE addons |
| `l`  | Logon (Run/RunOnce) |
| `m`  | WMI entries |
| `n`  | Winsock providers |
| `o`  | Codecs |
| `p`  | Printer monitors |
| `r`  | LSA providers |
| `s`  | Services y drivers |
| `t`  | Scheduled tasks |
| `w`  | Winlogon entries |

---

## 🧪 Técnica: Comparar antes y después

La forma más efectiva de detectar persistencia de malware:

### Paso 1 — Snapshot limpio

```cmd
REM Antes de ejecutar el malware, exportar estado limpio
autorunsc.exe -nobanner -a * -c -m > C:\lab\antes.csv
```

### Paso 2 — Ejecutar el malware

Correr la muestra y esperar unos minutos.

### Paso 3 — Exportar estado post-infección

```cmd
autorunsc.exe -nobanner -a * -c -m > C:\lab\despues.csv
```

### Paso 4 — Comparar los CSV

```cmd
REM En PowerShell: ver líneas nuevas (lo que agregó el malware)
Compare-Object (Get-Content C:\lab\antes.csv) (Get-Content C:\lab\despues.csv) | Where-Object {$_.SideIndicator -eq "=>"}
```

```bash
# En Kali/Linux si compartís los archivos:
diff antes.csv despues.csv | grep "^>"
```

---

## 🚩 Red flags — qué buscar

### Rutas sospechosas
```
%TEMP%\
%APPDATA%\
C:\Users\Public\
C:\ProgramData\   (sin subcarpeta conocida)
C:\Windows\Temp\
```

### Nombres sospechosos
```
nombres random: xkdjfh.exe, 8f3a2b.exe
imitando sistema: svchost32.exe, lsass_.exe, explorer32.exe
nombres de office: winword_.exe, excel_.exe
```

### Técnicas de persistencia conocidas

| Técnica | Dónde verla en Autoruns |
|---------|------------------------|
| Run Key | Pestaña Logon |
| Scheduled Task | Pestaña Scheduled Tasks |
| Service install | Pestaña Services |
| Driver rootkit | Pestaña Drivers |
| DLL hijacking | Pestaña Known DLLs / AppInit |
| Image File Execution Options | Pestaña Image Hijacks |
| Winlogon hijack | Pestaña Winlogon |
| COM hijacking | Pestaña Everything → buscar CLSID |
| Startup folder | Pestaña Logon → Startup folders |
| WMI subscription | Pestaña WMI |

---

## 🔄 Herramientas complementarias

| Herramienta | Uso |
|-------------|-----|
| **Process Monitor** (Sysinternals) | Ver en tiempo real qué claves de registro toca el malware |
| **Process Explorer** (Sysinternals) | Ver procesos activos y sus DLLs cargadas |
| **RegShot** | Comparar el registro antes/después (similar a la técnica de CSV) |
| **Sysmon** | Loggear persistencia automáticamente con reglas |
| **MalwareBytes** | Detectar y remover entradas maliciosas |

---

## 🧪 Flujo típico con Autoruns

```
1. VM Windows limpia → sacar snapshot
        │
        ▼
2. Exportar estado limpio con autorunsc → antes.csv
        │
        ▼
3. Ejecutar malware
        │
        ▼
4. Esperar (1-5 min) → malware instala persistencia
        │
        ▼
5. Abrir Autoruns GUI:
   - Options → Hide Microsoft Entries ✅
   - Options → Check VirusTotal ✅
   - Buscar entradas rojas/amarillas
        │
        ▼
6. Exportar estado post-infección → despues.csv
        │
        ▼
7. Comparar CSVs → ver exactamente qué agregó el malware
        │
        ▼
8. Documentar: ruta del binario, clave de registro, score VT
        │
        ▼
9. Revertir snapshot
```

---

## 📚 Recursos

- [Descarga oficial Autoruns](https://learn.microsoft.com/en-us/sysinternals/downloads/autoruns)
- [Sysinternals Suite completa](https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite)
- [Video oficial Microsoft — Autoruns](https://learn.microsoft.com/en-us/shows/sysinternals-videos/)
- [MITRE ATT&CK — Persistence techniques](https://attack.mitre.org/tactics/TA0003/)
- [Flare-VM (Windows para análisis)](https://github.com/mandiant/flare-vm)

---

> 💡 **Tip pro:** Combiná Autoruns con **Process Monitor** — mientras Autoruns te muestra  
> *qué* quedó instalado, Process Monitor te muestra *en qué momento exacto* y *qué proceso*  
> escribió esa clave de registro o creó ese archivo.

<img width="851" height="434" alt="image" src="https://github.com/user-attachments/assets/110e5d43-cebb-4105-8b92-5ef91bb626c7" />


<img width="908" height="435" alt="image" src="https://github.com/user-attachments/assets/701437c1-5006-4c4e-b531-1233c1e92a86" />

Correrlo previo a ejecucion de archivo.

Se puede guardar el resultado.
<img width="340" height="301" alt="image" src="https://github.com/user-attachments/assets/219071cf-f0a4-42b2-b517-15dcf50f8a07" />


Ejecutar el archivo y luego compararlo

<img width="1007" height="532" alt="image" src="https://github.com/user-attachments/assets/9dcd09b4-ebbb-42e8-977c-eddd8e0f9a0b" />
<img width="935" height="326" alt="image" src="https://github.com/user-attachments/assets/a9c66918-0ef0-4880-8bcf-9943b788c62b" />
