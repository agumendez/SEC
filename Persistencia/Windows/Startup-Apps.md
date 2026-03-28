🟢 Startup Folders en Windows

Las Startup folders son ubicaciones donde Windows ejecuta automáticamente programas al iniciar sesión.

🧑‍💻 Startup del usuario actual

Afecta únicamente al usuario logueado.

Path:

C:\Users\<TU_USUARIO>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup

Acceso rápido:

Win + R → shell:startup
🏢 Startup global (todos los usuarios)

Afecta a todos los usuarios del sistema.

Path:

C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup

Acceso rápido:

Win + R → shell:common startup
🔥 Uso en Ciberseguridad (SOC / Incident Response)

Estas carpetas son relevantes porque permiten persistencia en el sistema.

MITRE ATT&CK:

T1547 - Boot or Logon Autostart Execution
🔍 Qué revisar
Archivos .lnk sospechosos
Ejecutables desconocidos
Rutas que apunten a:
%AppData%
%Temp%
%Public%
Nombres que simulan procesos legítimos
⚠️ Indicadores comunes
Archivos con nombres similares a procesos de Windows (ej: svchost.lnk)
Accesos directos que ejecutan scripts (.bat, .ps1, .vbs)
Binarios ubicados fuera de Program Files o Windows
🧠 Nota

Las Startup folders son solo una de las múltiples técnicas de persistencia.
Siempre complementar con análisis de:

Run Keys (Registry)
Scheduled Tasks
Servicios
WMI Subscriptions
