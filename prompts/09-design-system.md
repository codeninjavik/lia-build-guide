# PROMPT 09: Design System & Theme

## Ye prompt kya banayega

- Poore app ka ek jaisa look: gehri indigo raat + ek marigold accent.
- Dark aur light theme, fonts (Instrument Serif + Manrope), spacing, shapes.
- Glass cards, buttons, text fields jaise reusable components.

**Pehle ye prompts ho chuke hone chahiye:** [01](01-project-setup.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
Build the design system for Lia AI in com.Lia.assistant.ui.theme and .components. Use Compose and
tokens only; no hardcoded colours in screens.

Identity: "a pearl of light in the night". Deep indigo night background (not flat black), ONE warm
marigold accent for all actionable things, lotus (#FF6FAE) and lagoon (#3FE0D0) reserved for the orb.

NovaColorScheme (data class) with: background, backgroundGradientTop, surface, surfaceGlass,
surfaceBorder, surfaceRaised, accent, accentSecondary, accentGlow, onAccent, textPrimary,
textSecondary, textTertiary, success, warning, error, userBubble, assistantBubble, depthShadow,
rimLight, lagoon, lotus. Provide Dark (background #06050E, gradientTop #15113A, surface #17143A,
raised #221E4D, accent #FFB547, accentSecondary #FF8A3D, text #EEE9FF, textSecondary #A7A0CC) and a
well-contrasted Light scheme (background #F3F0FB, surface #FFFFFF, accent #B86A00, text #15112E).
NovaTheme(mode DARK/LIGHT/SYSTEM, reducedMotion) maps to a Material3 colour scheme (no dynamic
colour), sets status/nav bar colours and icon contrast, provides LocalNovaColors and
LocalNovaReducedMotion.

Typography: bundle Instrument Serif (regular + italic) and Manrope (variable, weights 400-800) as
res/font and ship their OFL licence texts in assets. Serif for display/headline/"voice" lines,
Manrope for title/body/label/caption (title 17/24 semibold, body 15/22, label 13/18, caption 12/16).
Spacing 4/8/12/16/24/32/48, shapes 12/20/28/pill, motion 150/300/500 ms.

Depth.kt: Modifier.nightSky() (gradient + sparse star field, optional tilt parallax),
Modifier.depthSurface(shape, elevation) (coloured ambient shadow + top rim light), and a
LocalBottomBarInset so content can run under the floating nav bar.

Components: NovaGlassCard, NovaButton(PRIMARY/SECONDARY/TEXT, press scale 0.96), NovaTextField,
NovaTopBar, NovaSectionHeader, NovaSettingsRow(icon, title, subtitle, value, switch, onClick),
NovaMessageBubble(ChatMessage(id,text,isUser,isStreaming,isError)), pickers/dialogs, NovaEmptyState,
NovaVoiceVisualizer, NovaTypingDots. Every component honours LocalNovaReducedMotion and exposes
content descriptions. Touch targets >= 48 dp.
```

## Check karo ki ban gaya

- [ ] A screen built only from components looks right in dark and light.
- [ ] With "reduce motion" on, nothing loops or springs.
- [ ] `grep -rn "Color(0x" ui/screens` returns (almost) nothing.

Logic, diagrams aur samjhane wali detail: [`docs/09-design-system.md`](../docs/09-design-system.md)

**Agla:** [PROMPT 10: 3D Orb, 3D Splash & Edge Glow](10-3d-visuals.md)
