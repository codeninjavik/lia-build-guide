# PROMPT 05: Microphone, Speaker & Echo Guard

## Ye prompt kya banayega

- Saaf mic capture aur low-latency speaker playback.
- Echo guard: loudspeaker par Lia khud ki awaaz ko user ki awaaz nahi samjhegi.
- Beech me bolkar Lia ko rok sakte ho (barge-in).

**Pehle ye prompts ho chuke hone chahiye:** [04](04-gemini-live-client.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] On the loudspeaker Lia does not answer herself.
- [ ] Speaking over her cuts the audio quickly.
- [ ] The orb reacts to your voice level.

Logic, diagrams aur samjhane wali detail: [`docs/05-audio-and-echo-guard.md`](../docs/05-audio-and-echo-guard.md)

**Agla:** [PROMPT 06: Voice Session, Auto-Reconnect & Background Service](06-voice-session.md)
