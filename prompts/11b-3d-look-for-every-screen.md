# PROMPT 11b: 3D Look for Every Screen

## Ye prompt kya banayega

- Har screen ko 3D depth wala look dene ke prompts (Home, Voice, Chat, Settings, Forge...).
- Ye Part 11 ke saath ya uske baad chalana hai.

**Pehle ye prompts ho chuke hone chahiye:** [09](09-design-system.md), [10](10-3d-visuals.md), [11](11-screens-and-navigation.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] Home, Voice and Chat feel like a 3D stage (orb in front, glass layers behind).
- [ ] With 'reduce motion' on, no looping animation remains.

**Agla:** [PROMPT 12: Instagram / Facebook / WhatsApp Visual Agent (direct only)](12-visual-agent.md)
