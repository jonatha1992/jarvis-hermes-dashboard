# Análisis del repositorio

> Base documental: `README.md` y código existente. No se encontró un PRD confirmado; por eso, las conclusiones deducidas se etiquetan **[Inferido]** y lo que requiere validación queda **[Pendiente]**.

## Índice del análisis ampliado

- [Descripción global](00-descripcion-global.md)
- [Actores y roles](01-actores-y-roles.md)
- [Requerimientos funcionales y no funcionales](02-requerimientos.md)
- [Reglas de negocio observadas](03-reglas-de-negocio.md)
- [Diagramas Mermaid](04-diagramas.md)
- [Límites del sistema](05-limites-del-sistema.md)

Los documentos amplían esta base, no la reemplazan. La información se contrasta con el README y con las rutas citadas del código. **[Inferido]** identifica interpretación; **[Pendiente]** señala decisiones/datos no especificados.

## Propósito y alcance

JARVIS es una interfaz web local, con soporte de voz, para interactuar con un perfil ya configurado de Hermes Agent. No es un runtime de agente independiente: delega las solicitudes al Hermes CLI o, si está configurada, a la API persistente de Hermes. La interfaz incluye comandos slash, continuidad de sesión, estado y cola local de misiones.

**Confirmado por README/código:** el servidor se enlaza a `127.0.0.1`; la UI se sirve desde `ui/`; las integraciones de Hermes se heredan del perfil seleccionado; ElevenLabs es opcional.

**[Pendiente]** No se especifican en el README usuarios objetivo, requisitos de producto medibles, política de soporte, plan de versiones ni criterios formales de aceptación.

## Arquitectura y flujo

```text
Navegador (ui/index.html + app.js + styles.css)
    ├─ POST /api/run ──> server.py ──> runtime.py ──> Hermes CLI/API
    ├─ POST /api/listen ──> voice.py ──> STT configurado
    └─ POST /api/speak ──> voice.py ──> TTS configurado
```

- `server.py` sirve la UI y expone estado, ejecución en streaming NDJSON, voz, reinicio/cancelación y consulta de trabajos.
- `runtime.py` prepara las opciones de Hermes (perfil, modelo, toolsets, directorio de trabajo) y mantiene el identificador de sesión válido para la conversación.
- `commands.py` resuelve comandos locales como `/goal`, `/profile`, `/mission`, `/background`, `/tools` y `/status`; guarda datos en `state.json` por defecto, o en la ruta indicada por `JARVIS_STATE`.
- `voice.py` conecta proveedores opcionales ElevenLabs, Edge TTS y faster-whisper cuando están disponibles; el navegador puede ofrecer alternativas de voz.
- `persona.md` y el contexto local de comandos se agregan a las solicitudes al agente.

## Configuración y ejecución

El README documenta instalación en macOS/Linux con Python 3, Hermes Agent y un navegador Chromium moderno. La configuración de ejemplo está en `.env.example`: perfil Hermes, directorio de trabajo, puerto, permisos, timeout y credenciales/voz opcionales. El instalador y lanzador son `install.sh` y `start.sh`; la URL predeterminada documentada es `http://127.0.0.1:8730`.

**[Pendiente]** El repositorio no documenta una matriz de versiones de Python/Chromium ni un procedimiento de instalación para Windows.

## Seguridad y privacidad

El README declara que el servicio solo escucha en loopback, que las operaciones mutables requieren token por ejecución y que se validan origen, host, tipo y tamaño del cuerpo. También documenta serialización de ejecuciones, logging de prompts desactivado por defecto y exclusión de secretos/estado local de Git. En `server.py` se observan comprobaciones de host/origen/token y límites de payload; `runtime.py` admite logging optativo con permisos restrictivos.

- Mantén las claves de proveedor en el servidor y fuera del JavaScript/HTML entregado al navegador.
- No añadas registro de prompts, secretos ni información privada sin una justificación explícita y controles adecuados.
- **[Pendiente]** No se encontró una auditoría de seguridad independiente ni documentación de modelo de amenazas; no interpretar estas medidas como garantía de seguridad exhaustiva.

## Datos y persistencia

`commands.py` persiste notas de perfil, objetivo, tono, tareas y misiones en `state.json` (o `JARVIS_STATE`). El historial de la vía API se mantiene en memoria; la sesión CLI se conserva durante la vida del servidor hasta que se reinicia o se solicita una sesión nueva.

**[Inferido]** El contenido de perfil/objetivos/cola puede ser incluido en el contexto de solicitudes posteriores; tratar el archivo de estado como dato privado. La retención, copia de seguridad y borrado de datos no están especificados formalmente.

## Comandos documentados

La matriz del README describe `/new`, `/goal`, `/tools`, `/browser`, `/background`, `/mission`, `/personality`, `/commands`, `/toolsets`, `/connectors`, `/connect` y `/status`. El código también acepta `/profile`, `/kanban`, `/missions`, `/clear` y `/help`. Los comandos desconocidos se envían a Hermes.

**[Pendiente]** Hay diferencias entre la lista breve del README y los alias/comandos implementados; decidir si se debe sincronizar el README con la interfaz y la implementación.

## Verificación y riesgos conocidos

- No se encontró manifiesto de dependencias ni suite de pruebas en los archivos versionados examinados.
- **[Pendiente]** Identificar comandos de prueba/lint soportados y añadir pruebas para endpoints, sesiones, comandos y flujos de voz antes de dar por validado un cambio funcional.
- **[Inferido]** Los flujos de voz dependen de capacidades y permisos del navegador, proveedor disponible y configuración local; validar cada combinación objetivo antes de afirmar compatibilidad.
- Cambios en autenticación local, ejecución de procesos, datos de usuario o streaming requieren revisión cuidadosa y comprobaciones de regresión.

## Archivos principales

| Ruta | Responsabilidad observada |
|---|---|
| `server.py` | Servidor HTTP y API local |
| `runtime.py` | Integración con Hermes y ciclo de ejecución |
| `commands.py` | Comandos dashboard, estado y misiones |
| `voice.py` | Síntesis y transcripción de voz |
| `ui/` | Aplicación web estática |
| `persona.md` | Persona de respuesta |
| `.env.example` | Variables de configuración de ejemplo |
| `README.md` | Instalación, uso, arquitectura y seguridad declarada |
