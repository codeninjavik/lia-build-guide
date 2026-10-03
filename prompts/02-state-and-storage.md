# PROMPT 02: Settings, State & API Key Storage

## Ye prompt kya banayega

- Gemini API key paste karne aur phone me save karne ka system.
- Assistant ka naam, personality, language, voice, theme sab save hota hai.
- Settings badalte hi chalti voice session turant naye settings le leti hai.

**Pehle ye prompts ho chuke hone chahiye:** [01](01-project-setup.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] Changing the personality in Settings while a voice session is live reconnects without the assistant greeting again.
- [ ] Killing and reopening the app keeps every setting.
- [ ] `grep -r "AIza"` finds no key in the repo.

Logic, diagrams aur samjhane wali detail: [`docs/02-state-and-storage.md`](../docs/02-state-and-storage.md)

**Agla:** [PROMPT 03: 10 Personalities & the Hindi / Hinglish / English Brain](03-personalities-and-prompts.md)
