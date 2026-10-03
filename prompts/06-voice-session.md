# PROMPT 06: Voice Session, Auto-Reconnect & Background Service

## Ye prompt kya banayega

- Ek lambi chalne wali conversation jo screen chhodne par bhi nahi rukti.
- Net toot-ne par khud reconnect, bina dobara 'hello' bole.
- Notification me Stop button, aur live state (listening / speaking) poore UI ko.

**Pehle ye prompts ho chuke hone chahiye:** [04](04-gemini-live-client.md), [05](05-audio-and-echo-guard.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
Implement the voice session layer in com.Lia.assistant.voice.

VoiceSessionManager (singleton object, independent of any screen) exposing StateFlows: state
(NovaOrbState IDLE/LISTENING/THINKING/SPEAKING/CONNECTING/ERROR), amplitude, muted, statusText,
isActive, lastError, currentPersonality, currentLanguage, detectedLanguage, vadActive.

- start(ctx): if already active return; reset state, clear ConversationMemory, reset reconnect count,
  prewarm InstalledAppLabelCache, create LiveAudioIO, then under a Mutex call
  connectSession(isResuming=false) on an IO scope.
- connectSession: increment sessionEpoch, disconnect the previous client, read personality /
  language / assistant name / voice name from PersonalityRepository, build the prompt (recap only
  when resuming), map UI voice names to Gemini voices (default "Aoede"), connect.
- Listener (every callback checks epoch): setupComplete -> LISTENING and start the mic once;
  audio chunk -> first chunk of a turn calls EchoGuard.notifyPlaybackStarted(), play it, state
  SPEAKING with amplitude, and re-arm a 300 ms playback-silence watchdog that ends the turn;
  turnComplete / interrupted (also flushPlayback) -> EchoGuard.notifyPlaybackStopped(), LISTENING;
  transcripts -> ConversationMemory (user transcript also updates detectedLanguage);
  toolCall -> if name == "build_website" start the Forge and return {result:"forge_started"}
  immediately, else ActionExecutor.execute on IO and sendToolResponse if the epoch is current;
  error/close while active -> scheduleReconnect.
- Mic forwarding: send a chunk only if !muted && !EchoGuard.isMicMuted().
- scheduleReconnect: single-flight (job + mutex), delay 1200 ms, connectSession(isResuming=true);
  after 5 attempts set ERROR with "Connection lost - tap Stop and reopen Voice Mode".
- refreshInstructions(ctx): if active, reconnect with isResuming=true (Live cannot change the system
  instruction mid-session).
- toggleMute(), stop() (cancel jobs, bump epoch, disconnect, release audio, reset EchoGuard, clear
  memory, isActive=false).

VoiceForegroundService: channel "<name> voice session" (IMPORTANCE_LOW), ongoing notification
"<name> is listening" with a Stop action (ACTION_STOP). onStartCommand: STOP -> stop session,
stopForeground(REMOVE), stopSelf; else startForeground + VoiceSessionManager.start(); START_STICKY.

VoiceLatencyTracker: per-turn timestamps and derived deltas, plus reconnectCount StateFlow.
Unit-test the tracker (in-order marks give right deltas, out-of-order marks ignored).
```

## Check karo ki ban gaya

- [ ] Leaving Voice Mode keeps the conversation running; the notification's Stop ends it.
- [ ] Turning off Wi-Fi for 3 seconds reconnects without a re-greeting.
- [ ] After 5 failed reconnects the UI shows the error state.

Logic, diagrams aur samjhane wali detail: [`docs/06-voice-session.md`](../docs/06-voice-session.md)

**Agla:** [PROMPT 07: Phone Actions: Open Apps, Call, Message, Control Screen](07-phone-actions.md)
