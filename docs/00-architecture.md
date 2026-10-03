# 00 — Architecture

## The big picture

```mermaid
flowchart TB
    subgraph User
        V[Voice] 
        T[Typed chat]
        Q[Quick actions]
    end

    subgraph App["Android app (com.Lia.assistant)"]
        direction TB
        VS[VoiceSessionManager<br/>singleton, outlives screens]
        FS[VoiceForegroundService<br/>keeps mic alive in background]
        GL[GeminiLiveClient<br/>WebSocket]
        IO[LiveAudioIO<br/>mic 16 kHz / speaker 24 kHz]
        EG[EchoGuard]
        AE[ActionExecutor<br/>per flavor]
        CH[ChatScreen + GeminiTextClient]
        FG[ForgeController<br/>website builder]
        UI[Compose UI + 3D visuals]
    end

    subgraph Cloud
        GLive[(Gemini Live API)]
        GGen[(Gemini generateContent)]
    end

    V --> FS --> VS
    VS --> IO --> EG
    VS <--> GL <--> GLive
    GL -- tool call --> VS
    VS -- build_website --> FG
    VS -- other tools --> AE
    T --> CH --> GGen
    CH -- website request --> FG
    Q --> UI
    FG -- streamGenerateContent --> GGen
    AE --> Phone[(Apps, contacts, dialer, screen)]
    UI --> VS
```

## Three independent engines

1. **Voice engine** — a live, full-duplex WebSocket conversation with Gemini Live. Lives in a singleton so leaving a screen never kills the conversation.
2. **Text engine** — one stateless REST call per chat turn.
3. **Forge engine** — a long-running, streamed website generation with a 3D progress scene.

They share only the Gemini API key, the personality/language settings and the theme.

## Voice engine data flow

```mermaid
sequenceDiagram
    participant U as User
    participant M as Mic (LiveAudioIO)
    participant S as VoiceSessionManager
    participant G as GeminiLiveClient
    participant L as Gemini Live
    participant A as ActionExecutor
    participant P as Speaker

    U->>M: speaks
    M->>S: 16 kHz PCM chunks (if not muted)
    S->>G: realtimeInput.audio
    G->>L: base64 PCM over WebSocket
    L-->>G: inputTranscription + modelTurn audio (24 kHz)
    G-->>S: onAudioChunk
    S->>P: play (USAGE_ASSISTANT)
    Note over S,P: EchoGuard mutes mic while playing + 450 ms tail
    L-->>G: toolCall (e.g. open_app)
    G-->>S: onToolCall(id, name, args)
    S->>A: execute (IO thread, never blocks the socket)
    A-->>S: JSON result
    S->>G: toolResponse
    G->>L: functionResponses
    L-->>G: spoken confirmation audio
```

## Tool routing (who handles which tool)

```mermaid
flowchart LR
    TC[Gemini tool call] --> D{name == build_website?}
    D -- yes --> F[ForgeController.start<br/>returns immediately]
    D -- no --> E[ActionExecutor.execute]
    E --> F1[open_app / call_contact / message_contact]
    E --> F2["screen tools: read_screen, tap_text,<br/>type_text, scroll_screen, go_back, go_home<br/>(direct flavor only)"]
    E --> F3["social / WhatsApp task agents<br/>(direct flavor only)"]
```

## Package map

| Package | Owns |
|---|---|
| `voice` | Gemini Live client, session manager, audio, echo guard, prompt builder, language detector, tools' orchestration, text client |
| `action` (per flavor) | `ActionExecutor` — the single place a tool name becomes a real Android action |
| `data` | API key store, preferences, DataStore-backed personality repository, app state |
| `forge` | Website Forge: stage detection, streaming client, controller, repository, system prompt |
| `ui/theme` `ui/components` `ui/fx` | tokens, reusable components, 3D orb, edge glow, depth |
| `ui/screens/*` | one package per screen |
| `direct/agent/*` (direct only) | visual social-media and WhatsApp agents, state machine, blockers |
| `direct/license` (direct only) | access-key manager |

## Design rules the whole app follows

- **Never block the main thread**: package scans, contacts, network and audio run on IO threads.
- **No hardcoded secrets**: the Gemini key is pasted in Settings and stored locally.
- **No silent sending**: messages open the composer; the user taps Send in the `play` build.
- **One source of truth per concern**: tool dispatch in `ActionExecutor`, theme in `NovaColors`, build progress in `ForgeController.state`.
- **Everything has a reduced-motion path** and uses theme tokens only.
