# Límites del sistema

## Dentro del sistema

- Aplicación web estática de `ui/` y su API HTTP implementada en `server.py`.
- Coordinación de solicitudes, token por ejecución, stream NDJSON y acceso a estado de trabajos.
- Interpretación de comandos locales y almacenamiento configurable de perfil/objetivos/cola en `state.json` o `JARVIS_STATE`.
- Adaptación de solicitudes y procesos a Hermes CLI o API persistente, incluyendo perfil/modelo/toolsets según configuración.
- Adaptación de voz a ElevenLabs y a módulos locales opcionales; la UI también comprueba APIs del navegador.

**Citas verificadas:** `server.py` (`Handler`, `main()`), `commands.py` (`STATE_FILE`, `handle()`), `runtime.py` (`build_command()`, `run()`), `voice.py` (`speak()`, `transcribe()`), `ui/app.js`.

## Fuera del sistema

- Instalación, autenticación, configuración y operación interna de Hermes Agent y de su perfil. El dashboard los invoca, pero no es propietario de esas capacidades.
- Servicios externos de ElevenLabs y capacidades externas del navegador.
- Gestión independiente de credenciales/conectores de Google, MCP y terceros; README y `/connectors` indican que pertenecen a la configuración Hermes.
- Sistema operativo, navegador y red local, salvo las restricciones de escucha y validación implementadas por el servidor.

## Fronteras e incertidumbres

- **[Inferido]** `state.json` es dato local potencialmente sensible; no se debe publicar su contenido. `commands.py` lo lee/escribe localmente.
- La configuración `.env` la carga `server.py` y los instaladores preservan/crean el archivo; `.env.example` es plantilla, no estado efectivo.
- **[Pendiente]** No está definida una política formal de backup, expiración o borrado de sesiones/estado.
- **[Pendiente]** La API persistente Hermes se conecta por defecto a `http://127.0.0.1:8642` (configurable mediante `HERMES_API_URL`), pero el despliegue/seguridad de ese servicio externo no pertenece al dashboard. [Verificado: `runtime.py`, `_api_conf()`]

## Supuestos que no deben confundirse con garantías

- “Local” y loopback reducen exposición de red, pero no constituyen por sí solos una auditoría de seguridad.
- La lista de herramientas mostrada es una instantánea de salida de `hermes tools list`; no demuestra que cada integración sea funcional en toda solicitud.
- El soporte declarado macOS/Linux no implica compatibilidad probada en todas sus versiones. Windows queda **[Pendiente]**.
