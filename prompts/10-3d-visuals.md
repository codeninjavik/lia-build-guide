# PROMPT 10: 3D Orb, 3D Splash & Edge Glow

## Ye prompt kya banayega

- Lia ka asli 3D golak (GPU shader se), jo phone tilt par roshni badalta hai aur awaaz par lehrata hai.
- 7 Canvas orb styles (purane phones ke liye bhi).
- 3D splash intro aur screen ke kinare par glow jab Lia active ho.

**Pehle ye prompts ho chuke hone chahiye:** [06](06-voice-session.md), [09](09-design-system.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
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
```

## Check karo ki ban gaya

- [ ] On Android 13+ the orb is a lit sphere that tilts with the phone and ripples with your voice.
- [ ] On older devices the Canvas orb appears with no crash.
- [ ] The edge glow shows while a session is live and disappears on Stop.

Logic, diagrams aur samjhane wali detail: [`docs/10-3d-visuals.md`](../docs/10-3d-visuals.md)

**Agla:** [PROMPT 11: All Screens & Navigation](11-screens-and-navigation.md)
