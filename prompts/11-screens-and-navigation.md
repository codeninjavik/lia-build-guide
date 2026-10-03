# PROMPT 11: All Screens & Navigation

## Ye prompt kya banayega

- Home, Voice, Chat, History, Settings, Personality, Orb Style, Permissions, Profile, Privacy, About, Debug.
- Neeche floating bar jiske beech me Talk orb hai.
- Onboarding aur Quick Actions.

**Pehle ye prompts ho chuke hone chahiye:** [02](02-state-and-storage.md), [06](06-voice-session.md), [08](08-text-chat.md), [09](09-design-system.md), [10](10-3d-visuals.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
Build the app shell and screens for Lia AI (com.Lia.assistant.MainActivity and ui/screens/*). Use the
design system from Part 09 and the 3D visuals from Part 10. Use the 3D UI prompts in
Part 11b (3D look prompts) for each screen's look.

MainActivity: installSplashScreen(); setContent { NovaTheme(mode, reducedMotion) { Box { NovaApp(appState);
if (!SplashSession.played) LiaSplash3D(...) ; ListeningEdgeGlow() } } }. Also handle onNewIntent for the
EXTRA_OPEN_FORGE extra by calling ForgeController.requestOpen().

NovaApp: rememberNavController; Scaffold with Modifier.nightSky() and the floating
LiaBottomNavigation (Home, Chat, centre Talk orb -> Voice, History, Settings) visible only on
those 4 routes, providing LocalBottomBarInset to content. NavHost routes: splash, onboarding, home,
chat, history, settings, voice, quick_actions, profile, personality, orb_style, privacy, permissions,
about, debug, forge, plus a flavor route (accessibility_disclosure) registered via
registerAccessibilityDisclosureRoute (direct) / no-op (play). Start at HOME if has_onboarded else
ONBOARDING. LaunchedEffect on ForgeController.pendingOpen: consume it and navigate to forge with
launchSingleTop.

Screens: Onboarding (4-page pager), Home (greeting by time of day in the serif voice font, live
orb, Talk + Type buttons, "Try saying" cards, header with logo + profile), Voice Mode (permission
chain, 240 dp orb, status text, mute/stop/close, starts VoiceForegroundService and does NOT stop it
on dispose), Chat (Part 08), History, Quick Actions (tiles that start real tools only), Settings
(sections as in the table), Personality, Orb Style, Permissions (live status rows), Profile,
Privacy, About, Debug.

Rules: dark and light both work via tokens; reduced motion respected; touch targets >= 48 dp;
no screen blocks the main thread; every Lia string uses the saved assistant name.
```

## Check karo ki ban gaya

- [ ] Fresh install → onboarding → Home; second launch goes straight to Home.
- [ ] The bottom bar hides on Voice, Personality and other detail screens.
- [ ] Saying "ek cafe ki website banao" from anywhere opens the Forge.

Logic, diagrams aur samjhane wali detail: [`docs/11-screens-and-navigation.md`](../docs/11-screens-and-navigation.md)

**Agla:** [PROMPT 11b: 3D Look for Every Screen](11b-3d-look-for-every-screen.md)
