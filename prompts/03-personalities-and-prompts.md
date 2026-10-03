# PROMPT 03: 10 Personalities & the Hindi / Hinglish / English Brain

## Ye prompt kya banayega

- 10 personalities (Normal, Friendly, Funny, Teacher, Developer, Nautanki aur aur).
- Hindi, Hinglish aur English khud pehchanna aur usi me jawab dena.
- Reconnect ke baad baat wahin se jaari rakhne ke liye chhoti memory.

**Pehle ye prompts ho chuke hone chahiye:** [02](02-state-and-storage.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] Switching personality mid-conversation changes tone without a new greeting.
- [ ] "Kya haal hai yaar" gets a Hinglish reply; an English question gets English.
- [ ] Prompt-builder tests pass for all 40 combinations.

Logic, diagrams aur samjhane wali detail: [`docs/03-personalities-and-prompts.md`](../docs/03-personalities-and-prompts.md)

**Agla:** [PROMPT 04: Gemini Live Real-Time Voice Client](04-gemini-live-client.md)
