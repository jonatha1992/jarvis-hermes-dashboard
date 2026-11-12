# Diagramas

## Contexto del sistema

```mermaid
flowchart LR
    U[Persona usuaria] --> B[Navegador / UI estática]
    B -->|HTTP local: status, run, jobs| S[Servidor JARVIS]
    B -->|audio / texto| V[API de voz JARVIS]
    S --> C[Comandos locales y estado JSON]
    S --> R[Runtime Hermes]
    R -->|CLI o API persistente| H[Hermes Agent / perfil activo]
    V -->|si configurado| E[ElevenLabs]
    V -->|si instalado| L[Edge TTS / faster-whisper]
    B -->|capacidades de voz disponibles| W[APIs de voz del navegador]
```

**Evidencia:** `server.py` (rutas HTTP, `main()`), `runtime.py` (`run()`), `commands.py` (comandos/estado), `voice.py` (proveedores), `ui/app.js` (interacción browser). La opción de API persistente está en `runtime.py` (`_api_conf()`, `run_api()`).

## Ejecución de una solicitud

```mermaid
sequenceDiagram
    actor U as Usuario
    participant B as Navegador (ui/app.js)
    participant S as server.py
    participant C as commands.py
    participant R as runtime.py
    participant H as Hermes
    U->>B: Escribe o dicta solicitud
    B->>S: POST /api/run + token
    S->>S: Valida host, origen, tipo y tamaño
    S->>C: handle(mensaje)
    alt Comando local que se responde sin modelo
        C-->>S: respuesta local
        S-->>B: eventos NDJSON delta + complete
    else Solicitud para Hermes
        C-->>S: mensaje a ejecutar
        S->>R: run(mensaje, sesión, contexto)
        R->>H: CLI o API persistente
        H-->>R: eventos/texto
        R-->>S: status, delta, complete/error
        S-->>B: stream NDJSON
    end
```

## Voz (simplificado)

```mermaid
flowchart LR
    Mic[Micrófono] --> UI[ui/app.js]
    UI -->|POST /api/listen, audio| Server[server.py]
    Server --> Voice[voice.py: transcribe]
    Voice -->|STT según configuración| Transcript[Texto reconocido]
    Transcript --> Run[POST /api/run]
    Run --> Answer[Respuesta Hermes]
    Answer -->|POST /api/speak| Voice2[voice.py: speak]
    Voice2 -->|audio| UI
    UI -->|alternativa| Browser[SpeechRecognition / speechSynthesis]
```

La selección real depende de proveedor configurado, módulos instalados y capacidades del navegador. [Verificado: `voice.py`, `server.py`, `ui/app.js`]
