Las Startup folders (carpetas de inicio automático) en Windows son las ubicaciones donde se colocan accesos directos o programas para que se ejecuten automáticamente al iniciar sesión.

Tenés dos principales:

🧑‍💻 Startup del usuario actual

Solo afecta a tu usuario:

C:\Users\<TU_USUARIO>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup

👉 Forma rápida:

Presioná Win + R
Escribí:
shell:startup
🏢 Startup global (todos los usuarios)

Afecta a todos los usuarios del sistema:

C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup

👉 Forma rápida:

Win + R
Escribí:
shell:common startup
🔥 Tip SOC / IR (esto te sirve mucho)

En análisis de malware, estas carpetas son clave porque:

Son persistencia básica (MITRE ATT&CK T1547)
Muchos malwares dropean .lnk o ejecutables acá

👉 Cosas a revisar:

.lnk sospechosos
Ejecutables con nombres raros
Paths que apuntan a %AppData%, %Temp%, etc.
