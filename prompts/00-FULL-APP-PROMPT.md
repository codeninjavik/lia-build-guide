# FULL PROMPT: Build the complete "Lia AI" Android app

## Ye prompt kya banayega

**Lia AI** naam ka poora Android voice assistant app (Kotlin + Jetpack Compose):

- Hindi / Hinglish / English me **real-time voice baatcheet** (Gemini Live), 10 personalities.
- **Phone ke kaam**: app kholna, call, message. Direct version me screen padhna, tap, type, aur Instagram / Facebook / WhatsApp par post.
- **3D UI**: 3D orb, 3D splash, screen-edge glow, 3D look har screen par.
- **Website Forge**: "Lia, ek cafe ki website banao" bolo, live 3D scene me website banti dikhti hai, phir animated website khulti hai.
- Do versions: **direct** (poori power) aur **play** (Google Play safe).

## Kaise use karein

1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, ya koi bhi bada context wala AI).
3. AI ko **ek ek PART** karke banane do. Har part ke baad build aur check hone dena.
4. Chhote context wale AI ke liye ise todkar chalao: [01](01-project-setup.md), [02](02-state-and-storage.md), ... se [15](15-testing-and-release.md) tak alag prompts hain (index: [README](README.md)).

> Ye prompt lamba hai (15 parts). Agar tumhara AI ek baar me itna na le, to alag alag prompts use karo.

## Copy this prompt

````text
You are a senior Android engineer. Build a complete, compiling, working Android app called "Lia AI".

WHAT YOU ARE BUILDING
Lia AI is a voice-first personal AI companion for Android (package com.Lia.assistant, Kotlin, Jetpack Compose).
- The user talks to it in real time, in Hindi, Hinglish or English, over the Gemini Live API (full duplex, barge-in allowed).
- It has 10 selectable personalities and a user-renamable assistant name.
- It can open apps, call contacts, and prepare messages. In the "direct" build it can also read the screen, tap, type and scroll with an AccessibilityService, and run an Instagram / Facebook / WhatsApp UI agent that always asks before anything irreversible.
- It has a typed chat that uses the same personality.
- It has a premium 3D UI: a GPU-shaded 3D orb, a 3D splash, an edge glow, and a design system called "a pearl of light in the night" (deep indigo night, one marigold accent).
- It has a "Website Forge": when the user says "Lia, ek cafe ki website banao" a live 3D build scene opens, the website streams in from Gemini, then the finished animated website is revealed.
- It ships as two flavors from one codebase: "direct" (website / sideload, full power) and "play" (Google Play, everything the Accessibility policy forbids is removed at compile time).

HOW TO WORK
Build in the PARTS below, in order. For each part: implement it completely (every file in full, no TODO stubs in the core pipeline), then make sure both flavors still build (./gradlew assembleDirectDebug assemblePlayDebug) and the part's tests pass BEFORE starting the next part. Commit after each green part. If a part is marked "direct only", skip it when building only the Play version.

NON-NEGOTIABLE RULES
1. Both flavors must always build. Anything Google Play's Accessibility policy forbids exists ONLY in src/direct; src/play has same-named safe stubs.
2. No secrets in the repository: no API keys, keystores, backend config or project ids. The Gemini key is typed in Settings and stored locally.
3. Never block the main thread. Never log keys, prompts, audio or generated HTML.
4. No SEND_SMS permission, and no silent message sending in the play flavor.
5. Use design tokens only; support dark, light and reduced motion; touch targets at least 48 dp.
6. When something is ambiguous, prefer the behaviour described in the part.

AT THE END
Report what you ran, what you could not verify on a real device, and any deviation from this brief.


==============================================================================
PART 01: Project Setup & Two Build Flavors
==============================================================================
What this part builds: Ek khaali Android project (Kotlin + Jetpack Compose) jo build hota hai. Do versions ek hi code se: direct (poori power) aur play (Google Play ke rules ke hisaab se). Manifest, Gradle, release signing ka setup (secrets git me nahi jate).

You are a senior Android engineer. Create an Android app "Lia AI" (applicationId and namespace
com.Lia.assistant) in Kotlin with Jetpack Compose and Material3. No XML layouts.

Tooling:
- Gradle wrapper, Android Gradle Plugin with built-in Kotlin support (do NOT apply
  org.jetbrains.kotlin.android if the AGP version compiles Kotlin natively), Compose compiler
  plugin org.jetbrains.kotlin.plugin.compose.
- compileSdk 36, targetSdk 36, minSdk 26, Java 17.
- Dependencies: compose-bom, core-ktx, core-splashscreen, activity-compose, lifecycle-runtime-ktx,
  lifecycle-runtime-compose, lifecycle-viewmodel-compose, compose ui / ui-graphics /
  ui-tooling-preview, material3, material-icons-extended, navigation-compose,
  kotlinx-coroutines-android, datastore-preferences, okhttp (WebSocket + SSE), androidx.webkit
  (WebViewAssetLoader). Tests: junit, kotlinx-coroutines-test, org.json (for JVM tests).
- No other networking or JSON libraries: OkHttp + org.json only.

Flavors (dimension "distribution"):
- "direct": full app, including AccessibilityService screen control and visual agents.
- "play": everything Google Play's Accessibility policy forbids is REMOVED AT COMPILE TIME.
Create source sets app/src/main, app/src/direct, app/src/play, plus test, testDirect, testPlay.
Anything that differs per flavor (ActionExecutor, LiaToolCatalog, LiaCapabilityPrompts,
AccessKeyManager, OverlayEdgeGlowController, FlavorRoutes, accessibility settings rows) is declared
as the SAME object/function in both src/direct and src/play, so common code compiles against it.
The play versions are safe stubs (no-ops / "unavailable" results).

Manifest: base manifest in src/main has only INTERNET, RECORD_AUDIO, POST_NOTIFICATIONS,
FOREGROUND_SERVICE, FOREGROUND_SERVICE_MICROPHONE, READ_CONTACTS, CALL_PHONE (never SEND_SMS),
the launcher activity, and the voice foreground service (type microphone). The direct manifest
adds QUERY_ALL_PACKAGES, the AccessibilityService, SYSTEM_ALERT_WINDOW and its own FileProvider.
Release build: minify + shrink resources, signing read from a git-ignored keystore.properties.

Deliver: build.gradle.kts, settings.gradle.kts, gradle.properties, the three manifests,
a Hello-World MainActivity that builds for both flavors. Then print the commands to build
assembleDirectDebug and assemblePlayDebug.

==============================================================================
PART 02: Settings, State & API Key Storage
==============================================================================
What this part builds: Gemini API key paste karne aur phone me save karne ka system. Assistant ka naam, personality, language, voice, theme sab save hota hai. Settings badalte hi chalti voice session turant naye settings le leti hai.

Add the persistence layer for "Lia AI" (package com.Lia.assistant.data).

1. AssistantBrand: NAME="Lia", FULL_NAME="Lia AI", TAGLINE="Your Intelligent AI Companion".
2. ApiKeyStore (object): SharedPreferences "nova_prefs", key "gemini_api_key".
   getKey(ctx), saveKey(ctx, key) (trim), hasKey(ctx). The key is entered by the user in Settings;
   never hardcode it and never write it to logs.
3. NovaPreferences (object): get/put for String, Boolean, Float on the same prefs file, with a
   Keys object: theme_mode, reduced_motion, orb_style, personality, user_name, voice_input_enabled,
   wake_word_enabled, language, has_onboarded, voice_name, response_style, speech_speed,
   voice_volume, assistant_name.
4. LanguagePreference enum(displayName, description): AUTO ("follows whatever you speak"),
   HINDI, HINGLISH, ENGLISH. Default AUTO.
5. Personality enum(displayName, description, icon, systemInstruction) — details in Part 03.
6. PersonalityRepository (singleton, DataStore "assistant_settings"): Flows for personality,
   language, assistant name and voice name (falling back to NovaPreferences), suspend getters,
   and setters that write DataStore AND NovaPreferences, then call
   VoiceSessionManager.refreshInstructions(context) so a live session picks the change up.
7. NovaAppState: a plain class remembered once at the nav root, with Compose mutableStateOf fields
   loaded from NovaPreferences (theme mode default DARK, reducedMotion, orbStyle, personality,
   assistantName, userName, language, voiceName, speechSpeed, voiceVolume, conversations list).
   Every field has an applyX() that saves it. Name them applyX (not setX) to avoid JVM setter
   clashes. applyAssistantName/Language/Personality also update PersonalityRepository on
   Dispatchers.IO.

Rules: no blocking calls on the main thread; DataStore reads in coroutines; unit-test the
fallbacks. Output every file in full.

==============================================================================
PART 03: 10 Personalities & the Hindi / Hinglish / English Brain
==============================================================================
What this part builds: 10 personalities (Normal, Friendly, Funny, Teacher, Developer, Nautanki aur aur). Hindi, Hinglish aur English khud pehchanna aur usi me jawab dena. Reconnect ke baad baat wahin se jaari rakhne ke liye chhoti memory.

Implement the personality system for "Lia AI" in package com.Lia.assistant.voice and .data.

1. enum Personality(displayName, description, icon, systemInstruction) with NORMAL, FRIENDLY,
   PROFESSIONAL, FUNNY, COMPANION, GF_MODE, TEACHER, DEVELOPER, HUNGRY, NAUTANKI. Each
   systemInstruction is an INSTRUCTION to the model starting "Personality: <name>. ..." — never a
   scripted reply. COMPANION and GF_MODE must say: never claim to be human or have a body, no
   sexually explicit content, immediately respect a request to change tone or topic.
   Default NORMAL.
2. LanguageDetector.detect(text): HINDI if any char in U+0900..U+097F; else lowercase, split on
   [^a-zA-Z']+, count hits in a Hinglish marker set (hai, hain, ho, kya, kyun, kaise, nahi, haan,
   mujhe, tumhe, aap, mera, yaar, bhai, accha, theek, matlab, karna, karo, raha, chahiye, batao,
   suno, dekho, abhi, phir, lekin, bahut, thoda, kuch, sab, wala ...). <=4 words: 1 hit -> HINGLISH;
   longer: >=2 hits or ratio >=0.15 -> HINGLISH; else ENGLISH; blank -> UNKNOWN.
3. ConversationMemory: synchronized rolling window of the last 12 turns (role USER/ASSISTANT +
   text). recap() renders "User: ..." / "You: ..." lines capped to the last 1600 chars. In memory
   only; cleared when the session stops.
4. PersonalityPromptBuilder.build(personality, language, detectedLanguage, recap, assistantName)
   joins, with blank lines: (a) base: "You are {name}, a persistent personal voice assistant living
   on the user's phone. Keep replies short and conversational, like spoken conversation."
   (b) LiaCapabilityPrompts.toolInstruction(name) from the flavor, (c) personality instruction,
   (d) language policy (AUTO: match the user turn by turn and never translate what they said;
   fixed modes: explicit line), (e) safety: never claim to be human, no explicit content, stop a
   topic when asked, vary phrasing, (f) only if recap != null: "This is a continuing
   conversation — do not greet again. Recap: ...".
5. Unit tests: every Personality x LanguagePreference builds; recap section appears only with a
   recap; detector cases (Devanagari, "kya haal hai yaar", "what's the weather", blank).

==============================================================================
PART 04: Gemini Live Real-Time Voice Client
==============================================================================
What this part builds: Gemini Live se real-time, dono taraf bolne wali (full-duplex) voice connection. Audio bhejna, audio aur transcript wapas lena, aur beech me tool call handle karna. Wo galtiyan jinse session 'connect hokar chup' ho jata hai, pehle se theek.

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

==============================================================================
PART 05: Microphone, Speaker & Echo Guard
==============================================================================
What this part builds: Saaf mic capture aur low-latency speaker playback. Echo guard: loudspeaker par Lia khud ki awaaz ko user ki awaaz nahi samjhegi. Beech me bolkar Lia ko rok sakte ho (barge-in).

Implement LiveAudioIO and EchoGuard in com.Lia.assistant.voice.

LiveAudioIO:
- Mic: AudioRecord(VOICE_COMMUNICATION, 16000, CHANNEL_IN_MONO, ENCODING_PCM_16BIT,
  max(minBufferSize, 4096)). Attach AcousticEchoCanceler, NoiseSuppressor and AutomaticGainControl
  if available (try/catch each). Read 2048-byte chunks on a dedicated thread; call
  onMicChunk(bytes) and onMicAmplitude(rms) for each.
- Speaker: AudioTrack 24000 Hz mono PCM16, AudioAttributes USAGE_ASSISTANT + CONTENT_TYPE_SPEECH
  (NOT USAGE_VOICE_COMMUNICATION), MODE_STREAM, buffer = minBuf * 4, created lazily on first chunk.
  playChunk(bytes) writes and returns that chunk's RMS. flushPlayback() = pause + flush + play.
  setVolume(float). release() frees everything.
- rms(): little-endian PCM16, sqrt(mean(s^2))/32768 clamped to 0..1.

EchoGuard (singleton object): notifyPlaybackStarted() increments an active-playback counter and
mutes; notifyPlaybackStopped() decrements and when it hits 0, unmutes after ECHO_TAIL_MS = 450 ms.
A 60 s watchdog force-unmutes. isMicMuted(), reset(). Unit-test the counter and tail with a fake
clock (kotlinx-coroutines-test).

Keep these latency fixes: USAGE_ASSISTANT playback, 4x buffer, no logging in the audio path.

==============================================================================
PART 06: Voice Session, Auto-Reconnect & Background Service
==============================================================================
What this part builds: Ek lambi chalne wali conversation jo screen chhodne par bhi nahi rukti. Net toot-ne par khud reconnect, bina dobara 'hello' bole. Notification me Stop button, aur live state (listening / speaking) poore UI ko.

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

==============================================================================
PART 07: Phone Actions: Open Apps, Call, Message, Control Screen
==============================================================================
What this part builds: 'YouTube kholo', 'Mummy ko call karo', 'Rahul ko WhatsApp karo' jaise kaam. play version me message sirf composer kholta hai, Send user dabata hai. direct version me screen padhna, tap, type, scroll (Accessibility se).

Implement phone actions for Lia in com.Lia.assistant.action and .voice, with the flavor split from
Part 01.

Common (src/main): AppLauncher, InstalledAppLabelCache, ContactResolver, CommIntents,
functionDecl(name, description, params, required) helper producing a Gemini function declaration
(all params type STRING).

InstalledAppLabelCache: process-wide map lowercase label -> package for launchable apps, built once
on IO under a mutex, 15-minute TTL, prewarm() fire-and-forget, suspend get().
AppLauncher.findApp: exact label match first; else candidates with key length >= 3 where key
startsWith query or query startsWith key or every query word prefix-matches some key word; pick the
closest label length. Never loose substring matching. launch() uses getLaunchIntentForPackage and
catches ActivityNotFound/SecurityException.
ContactResolver.lookup: if the query looks like a phone number (digits/space/+-(). and >=5 digits)
return it directly; else require READ_CONTACTS, query Phone.CONTENT_URI with DISPLAY_NAME LIKE
%name% (limit 5), rank exact > prefix > contains. Sealed result Found/NoPermission/NotFound.

src/play: ActionExecutor handles open_app, call_contact, message_contact only. call_contact uses
ACTION_CALL when CALL_PHONE is granted ("calling") else ACTION_DIAL
("dialer_opened_no_permission"). message_contact opens SMS (ACTION_SENDTO smsto: + sms_body) or
WhatsApp (https://wa.me/<digits>?text=<encoded>, package com.whatsapp or com.whatsapp.w4b if
installed) and returns "composer_opened" — the USER taps Send. Never SmsManager, never SEND_SMS.
LiaToolCatalog.declarations() lists only these 3 tools; LiaCapabilityPrompts says plainly that
the assistant cannot read or control the screen and cannot post to social apps.

src/direct: the same plus read_screen, tap_text, type_text, scroll_screen, go_back, go_home through
NovaAccessibilityService (static instance set in onServiceConnected, isEnabled(ctx) checks
Settings.Secure.ENABLED_ACCESSIBILITY_SERVICES). Results: accessibility_not_enabled, tapped /
element_not_found, typed / no_focused_field, scrolled / nothing_scrollable, done / failed.
message_contact additionally auto-taps Send: up to 12 tries, wait 400 ms, (WhatsApp) tap "Continue
to chat" if present, tap "Send" -> "sent", else "composer_opened_send_not_found".
readScreen(maxElements=40, maxLabelLength=40): BFS, unique lines like "[input] Search".
Service config: eventTypes windowStateChanged|windowContentChanged, canRetrieveWindowContent,
canPerformGestures, notificationTimeout 100.

All tools return a JSONObject; an unknown tool returns {"error": "Unknown tool: <name>"}.
Unit-test AppLauncher matching, ContactResolver ranking and the no-device fallbacks.

==============================================================================
PART 08: Text Chat with Gemini
==============================================================================
What this part builds: Type karke baat karne wala chat screen, voice wali hi personality ke saath. Model ka naam khud dhoondhta hai, isliye Google model rename kare to bhi chalta hai. 'ek cafe ki website banao' likhne par website builder khulta hai.

Implement text chat (common to both flavors) in com.Lia.assistant.voice and ui/screens/chat.

GeminiTextClient (object, OkHttp REST):
- resolveModel(apiKey): GET https://generativelanguage.googleapis.com/v1beta/models?key=...; choose the
  first model whose supportedGenerationMethods contains "generateContent" and whose name contains
  "flash" but not "live", "image" or "tts"; fall back to the first generateContent model. Cache
  once per process (@Volatile). Never hardcode a model id.
- reply(apiKey, systemInstruction, history: List<ChatTurn(isUser, text)>): ChatReplyResult
  (Success(text) | Error(message)). POST models/<model>:generateContent with systemInstruction.parts
  and contents[] of role "user"/"model". Parse candidates[0].content.parts[0].text. Handle: blank key,
  empty history, HTTP errors (log status + Gemini's own reason ONLY, never the key or the user's
  text), IOException ("Couldn't reach Gemini..."), empty/blocked reply.
- Expose the OkHttp client and resolveModel as internal so the Forge can reuse them.

ChatPromptBuilder.build(personality, language, assistantName): base instruction + "this is typed
chat, never mention tapping, listening or speaking" + personality + language policy + safety.
No tool instructions.

ChatScreen: header (back, small live orb, name, "Typing..." / "Here to help"), LazyColumn of
NovaMessageBubble with auto-scroll, floating glass input bar with send on IME action, assistant
bubbles with copy / retry / speak (TextToSpeech created once and shut down on dispose). Show the
reply with a word-by-word reveal (20 ms per word). Before calling Gemini, run
ForgeIntent.extract(text): if it matches, start the Website Forge and add an assistant bubble
("Opening the Forge ...") instead of a Gemini reply.

==============================================================================
PART 09: Design System & Theme
==============================================================================
What this part builds: Poore app ka ek jaisa look: gehri indigo raat + ek marigold accent. Dark aur light theme, fonts (Instrument Serif + Manrope), spacing, shapes. Glass cards, buttons, text fields jaise reusable components.

Build the design system for Lia AI in com.Lia.assistant.ui.theme and .components. Use Compose and
tokens only; no hardcoded colours in screens.

Identity: "a pearl of light in the night". Deep indigo night background (not flat black), ONE warm
marigold accent for all actionable things, lotus (#FF6FAE) and lagoon (#3FE0D0) reserved for the orb.

NovaColorScheme (data class) with: background, backgroundGradientTop, surface, surfaceGlass,
surfaceBorder, surfaceRaised, accent, accentSecondary, accentGlow, onAccent, textPrimary,
textSecondary, textTertiary, success, warning, error, userBubble, assistantBubble, depthShadow,
rimLight, lagoon, lotus. Provide Dark (background #06050E, gradientTop #15113A, surface #17143A,
raised #221E4D, accent #FFB547, accentSecondary #FF8A3D, text #EEE9FF, textSecondary #A7A0CC) and a
well-contrasted Light scheme (background #F3F0FB, surface #FFFFFF, accent #B86A00, text #15112E).
NovaTheme(mode DARK/LIGHT/SYSTEM, reducedMotion) maps to a Material3 colour scheme (no dynamic
colour), sets status/nav bar colours and icon contrast, provides LocalNovaColors and
LocalNovaReducedMotion.

Typography: bundle Instrument Serif (regular + italic) and Manrope (variable, weights 400-800) as
res/font and ship their OFL licence texts in assets. Serif for display/headline/"voice" lines,
Manrope for title/body/label/caption (title 17/24 semibold, body 15/22, label 13/18, caption 12/16).
Spacing 4/8/12/16/24/32/48, shapes 12/20/28/pill, motion 150/300/500 ms.

Depth.kt: Modifier.nightSky() (gradient + sparse star field, optional tilt parallax),
Modifier.depthSurface(shape, elevation) (coloured ambient shadow + top rim light), and a
LocalBottomBarInset so content can run under the floating nav bar.

Components: NovaGlassCard, NovaButton(PRIMARY/SECONDARY/TEXT, press scale 0.96), NovaTextField,
NovaTopBar, NovaSectionHeader, NovaSettingsRow(icon, title, subtitle, value, switch, onClick),
NovaMessageBubble(ChatMessage(id,text,isUser,isStreaming,isError)), pickers/dialogs, NovaEmptyState,
NovaVoiceVisualizer, NovaTypingDots. Every component honours LocalNovaReducedMotion and exposes
content descriptions. Touch targets >= 48 dp.

==============================================================================
PART 10: 3D Orb, 3D Splash & Edge Glow
==============================================================================
What this part builds: Lia ka asli 3D golak (GPU shader se), jo phone tilt par roshni badalta hai aur awaaz par lehrata hai. 7 Canvas orb styles (purane phones ke liye bhi). 3D splash intro aur screen ke kinare par glow jab Lia active ho.

Build Lia's 3D presence in com.Lia.assistant.ui.fx, ui.components and ui.screens.splash.

1) LiaOrb3D(state, size, amplitude): an AGSL RuntimeShader (Android 13+/API 33) drawing a lit 3D
sphere per pixel: analytic sphere normals from the pixel coordinate, a slowly rotating fbm noise
interior used as an energy field, diffuse + specular lighting from a light direction that follows
the device tilt (SensorManager accelerometer, low-pass filtered, unregistered on dispose), a fresnel
rim, and a soft halo outside the sphere. Pass uniforms: time, resolution, colours (core / rim /
halo), amplitude, tilt. Voice amplitude displaces the silhouette radius and brightens the core.
Palettes per NovaOrbState: IDLE calm lotus/violet, LISTENING lagoon teal with a breathing pulse,
THINKING faster swirl, SPEAKING warm marigold-pink reacting to amplitude, CONNECTING amber slow
spin, ERROR red dim. API < 33: fall back to NovaOrb unchanged. Respect LocalNovaReducedMotion
(freeze time, no tilt).

2) NovaOrb (Canvas fallback + personality styles): enum NovaOrbStyle {NOVA, AURORA, PLASMA, GLASS,
ENERGY, MINIMAL, ARCHER}. Pure Canvas with infinite transitions: breathing (4.2 s sine, +-3.5 %),
rotation (9 s; 2.6 s thinking; 1.1 s connecting). Layers: radial outer glow, two expanding fading
rings while LISTENING, a rotating 100-degree arc while CONNECTING, orbiting particles (ENERGY 8,
PLASMA 6, GLASS 3, else 5; none for MINIMAL/ERROR), a radial-gradient core, a white highlight arc
for GLASS, a pulse ring for ERROR. ARCHER draws ~400 particles on a golden-spiral sphere with
depth-scaled alpha/size, slow rotation and amplitude jitter. contentDescription "<name> status:
<state>".

3) LiaSplash3D: a once-per-process 3D intro (SplashSession.played flag) rendered over the app while
it loads, ending with a callback that removes it. Never replays on rotation or resume.

4) ListeningEdgeGlow: a slim ~26 dp border composable (violet #8B5CF6 resting, pink #FF6FAE speaking,
red #FF7A85 error), drawn with no opinion on hosting. Host it in a full-screen Dialog window in
MainActivity above every screen. Flavor: src/direct OverlayEdgeGlowController shows the same content
in a WindowManager TYPE_APPLICATION_OVERLAY window (needs Settings.ACTION_MANAGE_OVERLAY_PERMISSION,
silently does nothing if not granted), started/stopped from VoiceForegroundService; src/play has a
no-op object with the same signature.

==============================================================================
PART 11: All Screens & Navigation
==============================================================================
What this part builds: Home, Voice, Chat, History, Settings, Personality, Orb Style, Permissions, Profile, Privacy, About, Debug. Neeche floating bar jiske beech me Talk orb hai. Onboarding aur Quick Actions.

Build the app shell and screens for Lia AI (com.Lia.assistant.MainActivity and ui/screens/*). Use the
design system from Part 09 and the 3D visuals from Part 10. Use the 3D UI prompts in
Part 11b (3D look prompts) for each screen's look.

MainActivity: installSplashScreen(); setContent { NovaTheme(mode, reducedMotion) { Box { NovaApp(appState);
if (!SplashSession.played) LiaSplash3D(...) ; ListeningEdgeGlow() } } }. Also handle onNewIntent for the
EXTRA_OPEN_FORGE extra by calling ForgeController.requestOpen().

NovaApp: rememberNavController; Scaffold with Modifier.nightSky() and the floating
LiaBottomNavigation (Home, Chat, centre Talk orb -> Voice, History, Settings) visible only on
those 4 routes, providing LocalBottomBarInset to content. NavHost routes: splash, onboarding, home,
chat, history, settings, voice, quick_actions, profile, personality, orb_style, privacy, permissions,
about, debug, forge, plus a flavor route (accessibility_disclosure) registered via
registerAccessibilityDisclosureRoute (direct) / no-op (play). Start at HOME if has_onboarded else
ONBOARDING. LaunchedEffect on ForgeController.pendingOpen: consume it and navigate to forge with
launchSingleTop.

Screens: Onboarding (4-page pager), Home (greeting by time of day in the serif voice font, live
orb, Talk + Type buttons, "Try saying" cards, header with logo + profile), Voice Mode (permission
chain, 240 dp orb, status text, mute/stop/close, starts VoiceForegroundService and does NOT stop it
on dispose), Chat (Part 08), History, Quick Actions (tiles that start real tools only), Settings
(sections as in the table), Personality, Orb Style, Permissions (live status rows), Profile,
Privacy, About, Debug.

Rules: dark and light both work via tokens; reduced motion respected; touch targets >= 48 dp;
no screen blocks the main thread; every Lia string uses the saved assistant name.

==============================================================================
PART 11b: 3D Look for Every Screen
==============================================================================
What this part builds: Har screen ko 3D depth wala look dene ke prompts (Home, Voice, Chat, Settings, Forge...). Ye Part 11 ke saath ya uske baad chalana hai.

Apply the following 3D look to the Lia AI screens built in Part 11. Use theme tokens only. Follow the global rules first, then each screen's prompt.

### Global 3D rules (paste first)
Design language for Lia AI: "a pearl of light in the night". Every screen is a stage with depth.

DEPTH MODEL (5 layers, back to front)
 0 night sky: indigo gradient + sparse stars, slow parallax from phone tilt
 1 ambient light fields: large soft radial glows tinted by the live state (lagoon = listening,
   marigold/lotus = speaking)
 2 content surfaces: glass cards with rim light and coloured ambient shadow (Modifier.depthSurface)
 3 the hero object: the 3D orb (AGSL shader) or the Forge scene
 4 floating controls: nav bar, buttons, chips - they sit above everything and cast soft shadows

MOTION
 - Tilt parallax: layers shift 2-12 dp by the accelerometer, deeper layers less. Disabled with
   reduced motion.
 - Entrances: one orchestrated sequence per screen (<= 600 ms, staggered by depth), no per-element
   fade-and-slide on every card.
 - Press feedback: scale 0.96 + light haptic. State changes animate colour and glow, never snap.
 - Use graphicsLayer (rotationX/rotationY with cameraDistance) for card tilt. Do NOT put
   AndroidView WebViews inside graphicsLayer / AnimatedVisibility (they render black).

RULES: only theme tokens (no hardcoded colours), 48 dp touch targets, 4.5:1 text contrast on glass,
LocalNovaReducedMotion respected everywhere, 60 fps on a mid-range phone, no overdraw-heavy blur
stacks (max 2 blurred layers per screen), dark and light both designed.

### Home
Build the Home screen as a 3D stage. Layer 0-1: night sky with a glow field that changes colour with
VoiceSessionManager.state. Layer 3: LiaOrb3D at ~55 % of the screen width, centred, tilting its light
with the phone, ripples with amplitude, tap = open Voice. Above it, a time-based greeting in the serif
"voice" font ("Good morning, <name>") that fades in first. Under the orb: the caption "Tap me to talk"
in italic serif. Layer 4: two floating pill buttons "Talk" (marigold, primary) and "Type" (glass),
then a "Try saying" row of glass cards that tilt slightly in 3D when scrolled past (rotationY +-6
degrees by scroll position). Header: logo and profile button as floating glass circles. The floating
bottom bar has the Talk orb docked in its centre, slightly raised above the bar.

### Voice Mode
Voice Mode is full-bleed: the 3D orb at 70 % width on the night sky, the ListeningEdgeGlow border in
the state colour, and nothing else competes. Status text in serif italic under the orb
("Lia is listening..."). A frosted control row at the bottom: Mute, Stop (large, red-tinted), Close.
The orb's palette and pace follow state (calm lotus idle, lagoon breathing when listening, warm
marigold-pink reacting to amplitude when speaking, amber slow spin when connecting). Add a thin
voice-visualizer ring of 48 bars around the orb scaled by amplitude on the speaking state only.
Permission and no-key states reuse the same stage with an error-tinted dim orb and one clear action.

### Chat
Chat is a calm 3D room: the night sky stays visible behind translucent bubbles. Assistant bubbles are
glass surfaces with a top rim light; user bubbles are warm marigold-tinted raised surfaces. A tiny
live orb (LiaOrb3D, 40 dp) in the header breathes when "Typing...". New bubbles rise from slightly
below with a 4 dp depth shadow that settles. The input bar floats above the keyboard as a raised
glass pill with a marigold send button that scales on press. A website request ("website bana do")
shows an assistant bubble with a mini Forge preview chip that opens the Forge.

### Settings, Personality, Orb Style
Settings: sections are stacked glass cards at slightly different depths (card i sits i*2 dp deeper) with
a profile card on top showing the avatar over a soft glow. Rows have 48 dp targets, icons in tinted
discs, switches that glow marigold when on.
Personality: a grid of 10 glass cards; the selected card lifts (translateZ via scale 1.04 + stronger
shadow + marigold rim) while the others dim. Each card shows its icon and a one-line description; a
live line at the top says "Changes take effect immediately".
Orb Style: a large live-preview LiaOrb3D/NovaOrb with Idle / Listening / Speaking toggle chips, and a
horizontally snapping carousel of the 7 styles where the centred item scales up and tilts toward the
centre (coverflow, rotationY +-35 degrees).

### History, Quick Actions, Permissions, Profile, About
History: search field as a floating glass bar; list items are thin glass rows that compress and tilt
(rotationX) as they scroll off the top. Empty state: a dim orb with "No conversations yet" and one button.
Quick Actions: a 2-column grid of tiles; each tile is a glass card with a tinted icon disc and an example
phrase ("YouTube kholo"); press = depth push (scale 0.96, shadow shrinks). Tiles start real tools only,
including "Build a website".
Permissions: rows with a status light (green lagoon / amber / red) that pulses once when it changes;
tap-to-grant or Open Settings; re-check on resume.
Profile / Privacy / About: quiet screens - same stage, fewer effects, an orb of 120 dp on About.

### Onboarding and Splash
Splash: a once-per-process 3D intro: stars streak past, the orb condenses from particles at the centre,
the name appears in serif, then the intro dissolves into Home (<= 2.2 s, skippable).
Onboarding: 4 pages in a pager. Each page has the orb in a different pose (large centre, orbiting,
split into two halves, shielded by a ring), with page content parallaxing at 0.6x the pager scroll.
Primary button in marigold; Skip as a text button.

### Website Forge (build screen) and result
Forge build screen = the 3D "Forge Reactor" scene (step 14) full-bleed, immersive (system bars hidden),
with only two floating glass HUD pieces: a top prompt chip and a bottom status card (stage name, big
serif percent, six-segment stage track, four stats, Cancel). Never show a terminal, log box or matrix
rain. The scene is the star; the HUD stays quiet.
Result viewer = the generated page full-screen with an auto-hiding floating toolbar (glass pill at the
top) and a small always-visible menu handle at the bottom right.

### 3D prompt for the *generated websites* (what Lia builds for users)
Every website Lia generates uses 3D, not flat layouts: a Three.js scene in the hero (themed object or
particle field reacting to pointer and scroll), a second Three.js scene or scroll-driven 3D transform in
the final call to action, and CSS 3D everywhere else (perspective tilt cards, translateZ depth layers,
flip cards, rotating cubes). Photos in at most two sections, with a dark gradient overlay for text.
Content is always visible without waiting for animation.

==============================================================================
PART 12: Instagram / Facebook / WhatsApp Visual Agent (direct only) (DIRECT ONLY)
==============================================================================
What this part builds: Lia app ki screen dekhkar khud buttons dabati hai: post, reel, story, WhatsApp message. Hamesha pehle puchti hai, phir hi post karti hai. Login/CAPTCHA par ruk jati hai. Sirf direct version me. Google Play wale version me ye hota hi nahi.

In src/direct only, implement a visual UI agent for Android using the AccessibilityService from Part 07.
Package com.Lia.assistant.agent.

agent/core:
- UiElement, ScreenObservation(observationId, package, window class, elements, screen size,
  windowStamp), CapturedScreen, ScreenObserver (turns a capture into an observation).
- ScreenDriver interface = the ONLY boundary with the device: capture(), clickElement(obs, element),
  gestureTap(x,y), setText(text), scroll, back, home. Results: OK, FAILED, STALE (observation no
  longer latest), NOT_FOUND, UNSUPPORTED. Production impl AccessibilityScreenDriver; tests use a
  scripted FakeScreenDriver.
- TargetResolver(spec -> best element), UiActionExecutor and TextInputExecutor that verify an
  expectation after every action (target gone, text present...) and report ActionResult
  Success / Failed(reason) / Blocked(blocker).
- Enums: ExecPhase, ActionMethod {ACCESSIBILITY_CLICK, GESTURE_ON_ELEMENT_BOUNDS, VISION_TAP,
  SET_TEXT}, Blocker {LOGIN, CAPTCHA, TWO_FACTOR, SECURITY_CHECK, PERMISSION_PROMPT}, FailureReason.

agent/social: Platform {INSTAGRAM, FACEBOOK}, SocialAction {CREATE_POST, CREATE_REEL, CREATE_STORY,
TEXT_POST}, TaskMode {PUBLISH, DRAFT}, TaskStatus, SocialTaskRequest.parse() that validates the model's
arguments BEFORE a task exists, per-platform selectors, MediaResolver (only content:// URIs shared to
LIA through ShareToLiaActivity), BlockerDetector, PlanStep, TaskReport, ControlCommand {CONFIRM,
REJECT, RESUME, CANCEL, STATUS}.

TaskStateMachine: states IDLE, OPENING_APP, OBSERVING, RESOLVING_TARGET, VALIDATING_TARGET,
EXECUTING_ACTION, WAITING_FOR_UI, VERIFYING, NEXT_STEP, RETRY, REOBSERVE, WAITING_FOR_CONFIRMATION,
WAITING_FOR_USER, COMPLETED, FAILED, CANCELLED. moveTo() throws on an illegal transition; FAILED and
CANCELLED are reachable from every non-terminal state; terminal states never exit; keep a history.

Rules: PUBLISH mode pauses at WAITING_FOR_CONFIRMATION before the irreversible tap; DRAFT never
publishes. A blocker pauses at WAITING_FOR_USER. If success cannot be verified, finish as
SUBMITTED_UNVERIFIED and tell the user so. Tool declarations execute_social_media_task(platform,
action, media_uri, caption, mode, target_account) and social_media_task_control(command, task_id).
The description must say: only call confirm after the user explicitly said yes.

agent/whatsapp: WhatsAppAgent with actions READ_UNREAD (via a notification listener, never opens the
app), SEND_MESSAGE (go straight to the chat by phone number), READ_CHAT, SEARCH_CHAT, MUTE_CHAT,
UNMUTE_CHAT, MARK_READ; ambiguity (two matching contacts) fails with "contact_ambiguous" instead of
guessing. Tools execute_whatsapp_task and whatsapp_task_control.

Write a scripted test world (FakeScreenDriver) and unit tests for: every legal and illegal state
transition, blocker pauses, DRAFT never publishing, stale observations, confirm without a prior
yes being refused, and unverified submission reporting.

==============================================================================
PART 13: Access-Key Licensing (direct only) (DIRECT ONLY)
==============================================================================
What this part builds: Bina valid access key ke direct app voice aur tools nahi chalata. Key admin se block ho to chalti session ruk jati hai; net na ho to purani state chalti hai. Sirf direct version me.

In src/direct, implement an access-key gate. Package com.Lia.assistant.license. Do not include any
real project ids, URLs or secrets in the repository — read backend configuration from a git-ignored
config file (the way google-services.json is handled) and make the app build and report
"licensing not configured" when it is missing.

- LicenseInfo(active, plan, expiresAt, status text, blocked, hasRememberedKey).
- AccessKeyRules (pure Kotlin): decide active/expired/blocked from plan, expiry timestamp and the
  blocked flag; fully unit-tested, no Android types.
- AccessKeyManager (object): refreshInfo(ctx) loads local state from private SharedPreferences
  ("lia_access_key": key, plan, expires_at, active, blocked, last_remote_check) into a StateFlow;
  activate(ctx, key) validates a key against the remote backend and binds it to a device id;
  liveCheck(ctx) re-checks at most once per 3 hours and FAILS OPEN on network errors;
  an admin "blocked" result makes the key inactive immediately.
- Enforcement: VoiceSessionManager.start and ActionExecutor.execute refuse when the key is not
  active (clear message the model can speak). MainActivity re-runs liveCheck on ON_RESUME and sends
  ACTION_STOP to VoiceForegroundService if a running session's key became blocked.
- UI: AccessKeyBanner (Home) and AccessKeySection (Profile) in src/direct; the same-named
  composables and a no-op AccessKeyManager in src/play so common code compiles for both.
- The play flavor must contain no licensing code paths and no Firebase dependencies.

==============================================================================
PART 14: Website Forge: Live 3D Website Builder
==============================================================================
What this part builds: 'Lia, ek cafe ki website banao' bolne par 3D Forge scene khulta hai. Build ke stages, sections aur log cards Gemini ki asli stream se chalte hain. Aakhir me animated website full-screen khulti hai (share, save, edit, delete).

Implement the "Website Forge" feature (common to both flavors) in packages com.Lia.assistant.forge and
com.Lia.assistant.ui.screens.forge, plus assets/forge. Read Part 04 / 06 / 08 / 11 first.

BEHAVIOUR
- Triggers: Gemini Live tool build_website(prompt) (declared in BOTH flavors' LiaToolCatalog, handled
  in VoiceSessionManager.onToolCall before ActionExecutor, returns {"result":"forge_started"}
  immediately), a typed chat command (ForgeIntent: needs a website/web page/landing page subject AND
  a build verb such as build/create/make/bana/banao; ignores questions), and a Quick Actions tile.
- ForgeController (process-wide object, own CoroutineScope): StateFlow<ForgeUiState(phase IDLE/
  BUILDING/DONE/FAILED/CANCELLED, prompt, isEdit, stage, progress, metrics(chars, sections,
  elapsedSec, tokensPerSec), sections, log + logSeq, buildId, file, error)>; pendingOpen flag;
  start(), edit(change), retry(), cancel(), reset(), openSaved(file), partialHtml(). start() returns
  Started/MissingKey/Offline/Busy/NothingToEdit with a spoken-friendly message.
- WebsiteForgeClient: streamGenerateContent?alt=sse on the model resolved by GeminiTextClient's
  ListModels lookup (header x-goog-api-key, read timeout 180 s). For model names containing "2.5"
  send generationConfig.thinkingConfig.thinkingBudget = 0 (otherwise the first token takes a minute).
  Parse SSE "data:" lines (SseParser.extractText / extractError / extractFinishReason), cancel the
  call when the collector is cancelled, log finishReason when it is not STOP (never log keys, prompts
  or HTML).
- Robustness: one automatic retry for overloaded (503/UNAVAILABLE) after 4 s; one retry with the hint
  "write completely original markup, copy and code; finish the whole page" for output that is < 1500
  chars, or ends without </html> (e.g. finishReason RECITATION); accept a truncated page only if >= 14000
  chars (repair open script/style/body/html tags), else fail with "Gemini stopped before finishing".
  Strip ```html fences. Save to filesDir/Websites/website_<timestamp>.html. If the screen is not visible,
  post a "Your website is ready" notification (tap re-opens via EXTRA_OPEN_FORGE).
- ForgeStage enum and ForgeStageDetector.detect(partialHtml) exactly as in the stage table above;
  throttle state emission to every 120 ms; log lines: stage labels, "Laying out <Section>", every 10 KB.
  ForgeHtml: stripFences, looksLikeHtml, repairTruncated, withSafetyNet (idempotent injected script
  that fades in content stuck at opacity 0 inside the viewport for 1.4 s, skipping fixed /
  pointer-events none / aria-hidden / canvas elements).
- ForgeSystemPrompt: the website system prompt described above (rules for Tailwind CDN, GSAP,
  picsum/pravatar, hero flair, magnetic buttons, bento, theming, 5-6 sections, mobile-first, reduced
  motion, <= 2 photo sections, Three.js hero + CTA, CSS 3D elsewhere, photo overlays, visible canvases,
  never hide content waiting for animation, hamburger nav). forNewSite(prompt) and
  forEdit(currentHtml, change).

3D SCENE (assets/forge/forge.html + forge.js, Three.js r160 ES modules + EffectComposer, RenderPass,
UnrealBloomPass, ShaderPass, OutputPass bundled locally; importmap {"three":"./three.module.min.js"})
- Expose the Forge.* API listed above. Scene: gradient + nebula + stars backdrop sphere; floor grid
  shader with up to 6 expanding ripples; reactor core (fresnel shader sphere + wire icosahedron + 3
  gyro torus rings + halo sprite); 1800 instanced-style glyph particles using a canvas glyph atlas
  ({ } < > / = ; ( ) 0 1 # $ * & %) that spiral into the core; 22 pooled data beams; 4 pooled
  shockwave sprites; up to 10 glass slabs (rounded-rect SDF shader, scan-line birth, faux content bars
  after Styling, shimmer after Motion, browser dots on the first); a ring of 6 stage nodes with an
  energy arc shader and labels; up to 4 floating glass log cards; dust. Post: bloom (strength
  ~0.34 + power*0.14, threshold 0.55, radius 0.42, half resolution) -> vignette + chromatic
  aberration -> OutputPass. Camera: fly-in over 2 s, slow orbit, touch drag + deviceorientation
  parallax, stage "kick" (dolly + FOV + shake). Reveal over ~1.5 s: flash, shockwave, particle burst,
  slabs fuse into one panel that flies into the camera with an FOV punch, then call
  ForgeBridge.onRevealFinished(). Failure: grayscale CSS filter, slabs drift apart, core dims.
- Colours come only from the Nova theme JSON passed to Forge.init (no second palette). Pixel ratio
  cap 2 (1.5 low RAM), adaptive quality, pause on visibilitychange, dispose everything, reduced-motion
  mode that renders only when dirty. No network access from the scene.

ANDROID SIDE
- ForgeWebGlScene: WebView with LayoutParams MATCH_PARENT, opaque background = theme background,
  javaScriptEnabled, domStorage off, file/content access off, WebViewAssetLoader serving /assets/ on
  appassets.androidplatform.net, shouldOverrideUrlLoading = true (nothing else may load), a JS
  bridge exposing ONLY onReady / onRevealFinished / onWebGLFailed, lifecycle onPause/onResume,
  Forge.dispose() + destroy() on leave. Push state diffs every 60 ms. Do NOT wrap this WebView in
  AnimatedVisibility/graphicsLayer (it renders black).
- ForgeFallbackScene (Compose Canvas) for low-RAM devices or WebGL failure, driven by the same state.
- GeneratedSiteView: separate sandboxed WebView (JS on, NO JavascriptInterface, no file/content
  access, mixed content blocked, geolocation off, external links to the system browser,
  MATCH_PARENT), loadDataWithBaseURL("https://localhost/", html). During the build refresh it with the
  partial HTML at most every 1.5 s after >= 4 KB of growth (skip on low-RAM); on DONE load the final page,
  wait until it has painted (max 2.5 s), then run the reveal.
- ForgeHud (Compose): prompt chip, stage label with slide animation, percent in the serif font, six-segment
  stage track, stats row (KB, sections, tokens/s, elapsed), Cancel (>= 48 dp), haptics on stage change,
  TalkBack live region, reduced-motion path.
- ResultToolbar: back, share (FileProvider subclass with its own authority, files-path Websites/),
  save (CreateDocument text/html), open in browser, edit with text, rebuild, my websites list, delete
  with confirmation; auto-hides after 4 s, always-visible handle to bring it back.
- Immersive mode while BUILDING; navigation route "forge"; NovaApp observes pendingOpen.

TESTS: SSE parsing (text, multi-part, errors, finishReason, [DONE]), fence stripping, truncation
repair, looksLikeHtml, stage detection (head-only stays at the start; staged progression; canvas counts
as visuals), section naming, safety net idempotency, error mapping (overloaded -> friendly message),
ForgeIntent matches/ignores.

==============================================================================
PART 15: Tests & Release
==============================================================================
What this part builds: Saare pure logic ke unit tests. Real phone par smoke-test ki script. Signed release build aur Play Store checklist.

Add the test suites and release setup for Lia AI.
1) JVM unit tests (JUnit4, kotlinx-coroutines-test, org.json) for every pure component listed in the
   table, split into src/test (common), src/testDirect and src/testPlay. Use fakes instead of mocks
   for ScreenDriver and clocks. Run with ./gradlew testDirectDebugUnitTest testPlayDebugUnitTest.
2) Release: proguard-rules.pro keeping org.json and Compose needs; release buildType with
   isMinifyEnabled and isShrinkResources; signingConfig read from keystore.properties if present
   (git-ignored); versionCode/versionName in defaultConfig.
3) A docs/PLAYSTORE_LISTING.md with: artifact to upload (play AAB), listing copy, data-safety
   answers, permission justifications (microphone foreground service, contacts, call), and the
   submission checklist.
4) A DebugScreen ("Voice Pipeline Debugger") that shows connection state, selected personality,
   configured and detected language, VAD, mic state and per-turn latency from VoiceLatencyTracker.
Do not add any secrets to the repository.
````
