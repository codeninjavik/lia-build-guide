# PROMPT 13: Access-Key Licensing (direct only)

> Ye part **sirf direct version** ke liye hai. Sirf Google Play wala app bana rahe ho to ise chhod do.

## Ye prompt kya banayega

- Bina valid access key ke direct app voice aur tools nahi chalata.
- Key admin se block ho to chalti session ruk jati hai; net na ho to purani state chalti hai.
- Sirf direct version me.

**Pehle ye prompts ho chuke hone chahiye:** [01](01-project-setup.md), [06](06-voice-session.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] `direct` without a key: voice refuses to start and the banner explains how to activate.
- [ ] Offline with a previously valid key still works.
- [ ] Blocking a key remotely stops a running session at the next resume.
- [ ] `play` builds without any Firebase / licensing dependency.

Logic, diagrams aur samjhane wali detail: [`docs/13-access-key-licensing.md`](../docs/13-access-key-licensing.md)

**Agla:** [PROMPT 14: Website Forge: Live 3D Website Builder](14-website-forge.md)
