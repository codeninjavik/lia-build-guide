# PROMPT 04: Gemini Live Real-Time Voice Client

## Ye prompt kya banayega

- Gemini Live se real-time, dono taraf bolne wali (full-duplex) voice connection.
- Audio bhejna, audio aur transcript wapas lena, aur beech me tool call handle karna.
- Wo galtiyan jinse session 'connect hokar chup' ho jata hai, pehle se theek.

**Pehle ye prompts ho chuke hone chahiye:** [02](02-state-and-storage.md), [03](03-personalities-and-prompts.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
Implement GeminiLiveClient (package com.Lia.assistant.voice) with OkHttp WebSocket and org.json.

Constants: MODEL = "models/gemini-3.1-flash-live-preview" (check the Live API docs for the current
native-audio Live model name and keep it in ONE constant); WS_URL =
wss://generativelanguage.googleapis.com/ws/google.ai.generativelanguage.v1beta.GenerativeService.BidiGenerateContent?key=<API_KEY>
OkHttp: readTimeout 0, connectTimeout 20 s, pingInterval 20 s.

API: connect(systemInstruction, voiceName = "Aoede"), sendAudio(pcmBytes), sendToolResponse(id,
name, resultJson), disconnect() (close code 1000). A Listener interface: onSetupComplete,
onAudioChunk(bytes), onInputTranscript(text), onOutputTranscript(text), onToolCall(id, name, args),
onTurnComplete, onInterrupted, onError(message), onClosed.

On open send setup: model, generationConfig.responseModalities ["AUDIO"],
speechConfig.voiceConfig.prebuiltVoiceConfig.voiceName, systemInstruction.parts[0].text,
inputAudioTranscription {}, outputAudioTranscription {}, and tools[0].functionDeclarations from
LiaToolCatalog.declarations() (flavor specific; all params type STRING, parameters type OBJECT).
Do NOT set realtimeInputConfig.automaticActivityDetection.

Outgoing audio: {"realtimeInput":{"audio":{"mimeType":"audio/pcm;rate=16000","data":"<base64 NO_WRAP>"}}}
Tool reply: {"toolResponse":{"functionResponses":[{"id":..,"name":..,"response":{..}}]}}

Incoming: handle BOTH text and binary frames (bytes.utf8()). Parse setupComplete, toolCall.functionCalls[],
serverContent.modelTurn.parts[].inlineData (audio/pcm -> base64 decode -> onAudioChunk, 24 kHz PCM16),
serverContent.inputTranscription.text, outputTranscription.text, turnComplete, interrupted.
Ignore sessionResumptionUpdate and goAway silently. Never log raw server messages or the API key.

Add ToolCallDeduplicator (LinkedHashSet of seen call ids, bounded) owned by the client: a call id seen
before is skipped. Unit-test it. Output every file in full.
```

## Check karo ki ban gaya

- [ ] You can say "hello" and hear a spoken reply.
- [ ] The transcripts of both sides arrive.
- [ ] Sending the same tool-call id twice runs the tool once.

Logic, diagrams aur samjhane wali detail: [`docs/04-gemini-live-client.md`](../docs/04-gemini-live-client.md)

**Agla:** [PROMPT 05: Microphone, Speaker & Echo Guard](05-audio-and-echo-guard.md)
