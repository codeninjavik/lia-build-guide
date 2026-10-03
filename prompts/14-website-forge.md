# PROMPT 14: Website Forge: Live 3D Website Builder

## Ye prompt kya banayega

- 'Lia, ek cafe ki website banao' bolne par 3D Forge scene khulta hai.
- Build ke stages, sections aur log cards Gemini ki asli stream se chalte hain.
- Aakhir me animated website full-screen khulti hai (share, save, edit, delete).

**Pehle ye prompts ho chuke hone chahiye:** [04](04-gemini-live-client.md), [06](06-voice-session.md), [08](08-text-chat.md), [11](11-screens-and-navigation.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

````text
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
````

## Check karo ki ban gaya

- [ ] Saying the command opens the 3D scene instantly; stages advance with the real stream.
- [ ] The finished page scrolls, animates and has **no black sections**; only two sections contain photos.
- [ ] Cancel, offline and missing-key cases each show a clear message.
- [ ] No `file://` loads, no JS bridge on the generated page, both flavors build.

Logic, diagrams aur samjhane wali detail: [`docs/14-website-forge.md`](../docs/14-website-forge.md)

**Agla:** [PROMPT 15: Tests & Release](15-testing-and-release.md)
