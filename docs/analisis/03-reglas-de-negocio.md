# Reglas de negocio observadas

Estas reglas describen decisiones existentes en la implementación, no políticas de negocio aprobadas externamente.

| ID | Regla | Evidencia |
|---|---|---|
| RN-01 | Se usa el perfil `default` cuando no se especifica `HERMES_PROFILE` (con compatibilidad de variable `JARVIS_PROFILE`). | `runtime.py`: inicialización de `PROFILE`. |
| RN-02 | La API persistente Hermes se intenta cuando existe credencial; si no, se usa el camino CLI. Si falla antes de emitir texto, se permite fallback al CLI; tras emitir deltas, se emite error sin reproducir salida duplicada. | `runtime.py`: `_api_conf()`, `run_api()`, `run()` final. |
| RN-03 | Se conserva una sesión Hermes solo cuando el evento de ejecución es `complete` y el ID satisface el formato validado; un evento de error limpia la sesión. | `runtime.py`: `valid_session()`; `server.py`: `_stream_run()`. |
| RN-04 | `/new` reinicia sesión; `/cancel` intenta terminar proceso activo y reinicia la sesión; `/clear` limpia el panel de respuesta sin modificar la sesión Hermes. | `server.py`: rutas POST; `commands.py`: rama `clear`. |
| RN-05 | Los comandos listados en `commands.py` se procesan localmente; las entradas que no coinciden con la matriz local continúan hacia Hermes. | `commands.py`: `_CMD`, `handle()`; `server.py`: `_stream_run()`. |
| RN-06 | Notas de perfil, objetivo, tono y tareas abiertas se incorporan al contexto construido para solicitudes. Se agregan hasta 40 notas de perfil y 20 tareas abiertas. | `commands.py`: `context_block()`; `server.py`: `persona()`. |
| RN-07 | Las misiones de fondo quedan registradas en estado local y mantienen resultado/estado de proceso en memoria para consulta; la finalización se reporta una vez mediante `take_finished()`. | `commands.py`: `start_background()`, `JOBS`, `take_finished()`; `server.py`: `/api/jobs`. |
| RN-08 | Solicitudes mutables requieren token generado al inicio del proceso; se comprueban host y origen antes de operar. Cuerpos JSON/audio tienen máximos configurados. | `server.py`: `API_TOKEN`, `do_POST()`, `_read()`. |
| RN-09 | La voz prioriza ElevenLabs si está configurado; sin clave se seleccionan Edge TTS/faster-whisper cuando los módulos están instalados, con alternativa de voz del navegador disponible en UI. No se asume disponibilidad idéntica entre clientes. | `voice.py`: `available()`, `tts_engine()`, `stt_engine()`; `ui/app.js`. |

## Decisiones por validar

- **[Pendiente]** El alcance de “limpiar” datos locales no incluye una política formal de retención/borrado; `/clear` solo aclara que limpia el panel, no el estado persistente.
- **[Pendiente]** Acordar comportamiento esperado si dos pestañas inician comandos locales/background mientras hay una ejecución foreground.
- **[Pendiente]** Confirmar si el fallback de voz del navegador es requisito soportado en cada navegador declarado.
