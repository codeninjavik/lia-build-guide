# PROMPT 08: Text Chat with Gemini

## Ye prompt kya banayega

- Type karke baat karne wala chat screen, voice wali hi personality ke saath.
- Model ka naam khud dhoondhta hai, isliye Google model rename kare to bhi chalta hai.
- 'ek cafe ki website banao' likhne par website builder khulta hai.

**Pehle ye prompts ho chuke hone chahiye:** [02](02-state-and-storage.md), [03](03-personalities-and-prompts.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] With a valid key, chat replies in the chosen personality and language.
- [ ] Removing the key shows "No Gemini API key yet - add one in Settings."
- [ ] Renaming the model on the server does not break chat.

Logic, diagrams aur samjhane wali detail: [`docs/08-text-chat.md`](../docs/08-text-chat.md)

**Agla:** [PROMPT 09: Design System & Theme](09-design-system.md)
