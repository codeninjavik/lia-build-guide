# PROMPT 07: Phone Actions: Open Apps, Call, Message, Control Screen

## Ye prompt kya banayega

- 'YouTube kholo', 'Mummy ko call karo', 'Rahul ko WhatsApp karo' jaise kaam.
- play version me message sirf composer kholta hai, Send user dabata hai.
- direct version me screen padhna, tap, type, scroll (Accessibility se).

**Pehle ye prompts ho chuke hone chahiye:** [01](01-project-setup.md), [06](06-voice-session.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] "YouTube kholo" opens YouTube; an unknown app returns `not_found` and Lia says so.
- [ ] "Mummy ko call karo" dials (or opens the dialer without permission).
- [ ] In `play`, a message only ever opens the composer.

Logic, diagrams aur samjhane wali detail: [`docs/07-phone-actions.md`](../docs/07-phone-actions.md)

**Agla:** [PROMPT 08: Text Chat with Gemini](08-text-chat.md)
