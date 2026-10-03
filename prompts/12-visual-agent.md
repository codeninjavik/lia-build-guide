# PROMPT 12: Instagram / Facebook / WhatsApp Visual Agent (direct only)

> Ye part **sirf direct version** ke liye hai. Sirf Google Play wala app bana rahe ho to ise chhod do.

## Ye prompt kya banayega

- Lia app ki screen dekhkar khud buttons dabati hai: post, reel, story, WhatsApp message.
- Hamesha pehle puchti hai, phir hi post karti hai. Login/CAPTCHA par ruk jati hai.
- Sirf direct version me. Google Play wale version me ye hota hi nahi.

**Pehle ye prompts ho chuke hone chahiye:** [07](07-phone-actions.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] With a shared photo, "post this on Instagram" prepares the post and **asks before publishing**.
- [ ] A login screen pauses the task and Lia asks you to log in.
- [ ] The state-machine tests prove illegal transitions throw.

Logic, diagrams aur samjhane wali detail: [`docs/12-visual-agent.md`](../docs/12-visual-agent.md)

**Agla:** [PROMPT 13: Access-Key Licensing (direct only)](13-access-key-licensing.md)
