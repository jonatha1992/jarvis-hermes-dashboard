# Requerimientos

Los requerimientos funcionales y no funcionales siguientes describen comportamiento observable en el README y en el código; no constituyen una especificación aprobada. Cada cita apunta a archivos/versiones del repositorio y la evidencia se comprobó en código.

## Requerimientos funcionales (RF)

| ID | Requerimiento observado | Evidencia verificable |
|---|---|---|
| RF-01 | Servir la interfaz web estática desde el servidor local. | `server.py`: `Handler.do_GET()`, `UI`, resolución de archivos y `/` → `index.html`. |
| RF-02 | Exponer estado del runtime, perfil, directorio de trabajo, modelo, herramientas y capacidades/estado de voz. | `server.py`: `GET /api/status`; `runtime.py`: `runtime_kind()`, `hermes_tools_snapshot()`; `voice.py`: `stt_engine()`, `tts_engine()`. |
| RF-03 | Aceptar solicitudes de agente y transmitir eventos como NDJSON. | `server.py`: `POST /api/run`, `_stream_run()`; `runtime.py`: `run()`. |
| RF-04 | Mantener identificador de sesión válido tras una ejecución completada y permitir iniciar sesión nueva/cancelar. | `server.py`: `SESSION`, `_stream_run()`, `POST /api/new`, `POST /api/cancel`; `runtime.py`: `valid_session()`, `cancel_active()`. |
| RF-05 | Resolver comandos locales de dashboard y reenviar mensajes no reconocidos al runtime Hermes. | `commands.py`: `_CMD`, `handle()`; `server.py`: `_stream_run()`. |
| RF-06 | Persistir notas de perfil, objetivo, tono, tareas y misiones en JSON, con ruta configurable por `JARVIS_STATE`. | `commands.py`: `STATE_FILE`, `load()`, `save()`, `update_state()`. |
| RF-07 | Lanzar misiones en segundo plano y consultar trabajos activos/finalizados. | `commands.py`: `start_background()`, `jobs_snapshot()`, `take_finished()`; `server.py`: `GET /api/jobs`. |
| RF-08 | Proporcionar síntesis de voz y transcripción por proveedor configurable/disponible. | `server.py`: `POST /api/speak`, `POST /api/listen`; `voice.py`: `speak()`, `transcribe()`. |
| RF-09 | Usar el perfil Hermes seleccionado y permitir configurar comando, modelo, toolsets, permiso y directorio de trabajo. | `runtime.py`: constantes de configuración, `_hermes_base()`, `build_command()`; `.env.example`. |
| RF-10 | Responder a solicitudes de UI con token de ejecución y admitir solo solicitudes mutables de origen/host local permitido. | `server.py`: `API_TOKEN`, `_host_ok()`, `_origin_ok()`, `_token_ok()`, `do_POST()`. |

## Requerimientos no funcionales (RNF)

| ID | Requerimiento/propiedad observada | Evidencia verificable y límite |
|---|---|---|
| RNF-01 | Escuchar en loopback, no en todas las interfaces. | `server.py`: `ThreadingHTTPServer(("127.0.0.1", PORT), Handler)`. No equivale a una auditoría de seguridad. |
| RNF-02 | Acotar cuerpos de solicitud: JSON y audio tienen límites distintos. | `server.py`: `MAX_JSON_BODY`, `MAX_AUDIO_BODY`, `_read()`; error 413 en `do_POST()`. |
| RNF-03 | Evitar ejecuciones de foreground concurrentes en el servidor y serializar procesos Hermes compartidos. | `server.py`: `RUN_LOCK`; `runtime.py`: `EXECUTION_LOCK`. El alcance preciso de sincronización entre procesos distintos no se ha validado. |
| RNF-04 | No registrar tráfico HTTP salvo modo verbose; logging de transcripciones Hermes desactivado por defecto. | `server.py`: `log_message()` condicionado a `JARVIS_VERBOSE`; `runtime.py`: `RAW_LOG`. Con `JARVIS_RAW_LOG`, el archivo se abre con permisos restrictivos cuando el SO los soporta. |
| RNF-05 | Mantener la clave ElevenLabs en servidor, no en la UI. | `voice.py` construye peticiones con la clave; `server.py` expone bytes/resultados; README declara clave fuera del frontend. |
| RNF-06 | No requerir proveedor de voz externo para uso básico según README; disponibilidad efectiva depende de navegador o módulos instalados. | `README.md`; `voice.py` detecta `edge_tts`/`faster_whisper`; `ui/app.js` inspecciona APIs del navegador. **[Pendiente]** matriz reproducible por navegador/plataforma. |
| RNF-07 | Requisitos de plataforma declarados: macOS/Linux, Python 3 y Chromium moderno. | `README.md`; `install.sh`/`start.sh` son Bash. **[Pendiente]** versiones mínimas y soporte Windows. |

## Fuera de especificación formal

**[Pendiente]** No se halló PRD, definición de disponibilidad/rendimiento, SLA, retención/borrado de datos, matriz de compatibilidad, esquema versionado de API ni suite de aceptación. La presencia de un endpoint no especifica por sí sola su contrato completo.
