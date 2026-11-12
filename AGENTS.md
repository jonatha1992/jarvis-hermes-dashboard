# Guía para agentes — JARVIS Hermes Dashboard

## Alcance y fuentes

- Este repositorio es un dashboard local para Hermes Agent, no un runtime de agente independiente. Deriva el comportamiento de `README.md` y el código fuente; no hay PRD confirmado.
- No inventes requisitos, compatibilidad, pruebas o garantías. Marca la documentación no confirmable como **[Inferido]** y la información que requiera validación como **[Pendiente]**.
- Conserva los cambios existentes del usuario. En particular, `docs/DECISIONS.md` puede contener trabajo local ajeno a tu tarea.

## Arquitectura

- `server.py`: servidor HTTP local, endpoints `/api/*`, token por ejecución y streaming NDJSON.
- `runtime.py`: invocación de Hermes CLI/API, perfil, sesión y procesos.
- `commands.py`: comandos slash, estado local y misiones en segundo plano.
- `voice.py`: integración opcional de voz ElevenLabs/Edge/Whisper.
- `ui/`: interfaz estática sin dependencias de frontend.
- `persona.md`: instrucciones de tono agregadas a las solicitudes.
- La configuración se documenta en `.env.example`; no publiques secretos ni valores locales de `.env`.

## Reglas de cambio

- Mantén la integración con el perfil Hermes existente; no dupliques credenciales, herramientas ni lógica de runtime en el dashboard.
- El servidor debe permanecer limitado a loopback según el comportamiento documentado. Trata autenticación, origen, límites de cuerpo, sesiones y cancelación como áreas sensibles.
- Mantén secretos fuera del frontend, de logs y de la documentación. No muestres contenido privado de `.env` ni archivos de estado.
- No añadas dependencias ni cambies compatibilidad de plataforma sin justificarlo: el README declara macOS/Linux, Python 3 y Chromium moderno.
- Conserva el estilo y las convenciones del código existente; evita refactors no necesarios para el cambio solicitado.
- No afirmes que se ejecutaron pruebas si no se ejecutaron. Si no hay suite o instrucciones de pruebas claras, indícalo como [Pendiente].

## Verificación

- Revisa el diff y el estado de Git antes de concluir; no sobrescribas cambios preexistentes.
- Ejecuta las comprobaciones pertinentes al cambio y registra exactamente el resultado.
- Para documentación, comprueba enlaces/rutas citados contra los archivos disponibles y evita describir como confirmado aquello que solo se infiere del README o código.
