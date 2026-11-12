# Descripción global

## Identidad y propósito

JARVIS es un dashboard web local, con interfaz de voz, que actúa como interfaz para una instalación de Hermes Agent ya configurada. No implementa un runtime de agente separado: delega las solicitudes al CLI Hermes o a su API persistente cuando está disponible. [Verificado: `README.md`; `runtime.py`, `build_command()` y `run()`]

El README presenta la interfaz como una consola de misión personal, con conversación, voz, continuidad de sesión, comandos y acceso al perfil Hermes activo. La orientación de producto y los criterios medibles de éxito no están formalizados. **[Pendiente]** confirmar usuarios objetivo, objetivos de producto y criterios de aceptación.

## Alcance funcional observado

- UI estática servida desde `ui/`; el servidor Python ofrece API HTTP local. [Verificado: `server.py`, `UI` y `Handler.do_GET()`]
- Solicitudes y respuestas del agente, incluidas respuestas en streaming NDJSON. [Verificado: `server.py`, `_stream_run()`]
- Comandos locales para estado, perfil, objetivo, tono, cola de misiones y tareas en segundo plano. [Verificado: `commands.py`, `_CMD`, `handle()`]
- Integración de voz configurable: ElevenLabs en servidor; Edge TTS y faster-whisper si están instalados; la interfaz consulta capacidades de voz del navegador. [Verificado: `voice.py`; `ui/app.js`]
- Herencia de herramientas, skills, memoria, browser y conectores del perfil Hermes: intención declarada en README y comandos de invocación en `runtime.py`. El alcance exacto depende de la instalación/configuración Hermes local.

## Límites explícitos

- El servidor se crea enlazado a `127.0.0.1`. [Verificado: `server.py`, `main()`]
- El dashboard no gestiona por separado las credenciales/conexiones de aplicaciones integradas en Hermes; usa el perfil seleccionado. [Verificado: `runtime.py`; `commands.py`, `/connectors`]
- El instalador y lanzador versionados son scripts Bash (`install.sh`, `start.sh`) y validan Python 3 y Hermes. README limita prerrequisitos documentados a macOS/Linux. No hay instalador Windows en el repositorio. [Verificado: scripts y README]

## Fuentes y certeza

La fuente de propósito y despliegue es `README.md`; los detalles funcionales se contrastan con los módulos Python y `ui/app.js`. Las garantías de seguridad no se amplían más allá de lo observado. Donde la documentación no define el comportamiento, se indica **[Inferido]** o **[Pendiente]** en los documentos de esta carpeta.
