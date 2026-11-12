# Actores y roles

| Actor | Rol observado | Evidencia |
|---|---|---|
| Persona usuaria del dashboard | Envía texto/voz, usa comandos locales, consulta estado y controla sesión desde el navegador. | `ui/app.js`; `server.py`, rutas `/api/*` |
| Navegador | Presenta la UI, adjunta el token de ejecución a solicitudes protegidas y puede grabar audio o sintetizar voz según capacidades disponibles. | `ui/app.js`; `server.py`, `Handler` |
| Servidor JARVIS | Sirve archivos UI, valida solicitudes, coordina comandos, voz y ejecución Hermes, y transmite eventos NDJSON. | `server.py`, `Handler`, `main()` |
| Hermes Agent | Ejecuta solicitudes con perfil/herramientas configurados y devuelve texto/eventos al runtime. | `runtime.py`, `build_command()`, `run_hermes()` |
| Proveedores de voz | ElevenLabs opcional para STT/TTS; Edge TTS y faster-whisper se detectan si están instalados. | `voice.py`, `speak()`, `transcribe()`, `tts_engine()`, `stt_engine()` |
| **[Inferido]** Operador del equipo | Instala/ejecuta el dashboard, configura `.env` y mantiene Hermes. El código no define roles administrativos separados. | `README.md`, `install.sh`, `.env.example` |

## Roles y autorización

No hay un sistema de cuentas ni roles múltiples documentado. El token aleatorio `API_TOKEN` se genera por proceso y se inyecta en `index.html`; las rutas POST requieren token y comprobaciones de host/origen. `/api/jobs` GET también requiere token. `/api/status` GET solo valida host. [Verificado: `server.py`, `API_TOKEN`, `Handler.do_GET()` y `do_POST()`]

**[Pendiente]** Definir si el uso multiusuario o acceso remoto forman parte del producto. La implementación observada escucha en loopback y no sustenta afirmar soporte remoto.
