# Lenspilot

**An AI operating layer for your phone — it sees your screen, understands your goal, and either guides you or does the work for you.**

Lenspilot is an Android app, backed by a cloud AI service, that turns a plain-language goal ("turn off notifications for this app", "find where to apply for this service", "teach me this chapter", "reply to this message") into precise, guided action.

It is built for the next billion smartphone users: people who are fluent in their own language but not in menus, settings, forms, and developer tooling. It works in any language the user writes in, with a mobile-first interface and a reliable loop that checks every step against the real screen before it shows the answer.

> Created by Hemel — solo founder, designed and built end to end.

---

## Why Lenspilot

Software is getting more capable and more complicated at the same time. Most people don't need another chatbot; they need something that can *look at the thing in front of them and help them finish it*.

- **Chat assistants can't see your screen.** They describe steps you then have to translate into taps.
- **Agents are brittle.** They hallucinate buttons, loop forever, or confidently report success on tasks that failed.
- **The long tail is ignored.** Most AI products assume English, fast networks, a laptop, and technical literacy.

Lenspilot is built around the opposite assumptions: a phone-only user, a low-bandwidth network, a non-English language, and a task that has to actually get done.

---

## What Lenspilot Does

Lenspilot is one app with several modes that share a common AI backend, a common screen-understanding stack, and a common token-based usage model.

### 1. Screen Guide — "show me what to tap"

The user describes a goal; Lenspilot reads the current screen and highlights exactly where to tap next, one step at a time, adapting after every screen change.

- **Goal-plus-screen loop.** Each turn the AI receives the goal, a short history of what has already been shown, and a compact description of the current screen. It returns a structured decision (a note, a status, a message, and an action), and the app verifies that action against the screen before showing it.
- **Highlights, arrows, and deep links.** Where an app or a specific settings page can be opened directly, Lenspilot uses system intents and deep links instead of making the user navigate. If the target is hidden or hard to reach, it explains and highlights the right path.
- **Teaching mode.** If the user doesn't understand the screen itself, Lenspilot highlights several elements at once in distinct colours and explains each in plain language, then waits for the next step.
- **Hallucination guards.** Every AI decision is checked against what is actually on screen before the user sees it: non-existent elements, mismatched labels, disabled controls, and unsupported "task complete" states are rejected.
- **Floating control bar and bubble.** A draggable overlay and a floating AI Agent bubble let the user ask for help on whatever screen they are currently on, with no separate "run" step.
- **Quick Settings tile.** One tap from anywhere to scan the current screen.

### 2. Four selectable AI engines

The same task can be run through four different "brains" so users and the service can trade cost, speed, and depth:

| Engine | Design |
|---|---|
| **Super Lite** | One strong-model plan up front, then a small, inexpensive model executes each step. Lowest cost per task. |
| **Super 1·2** | Fresh reasoning on every screen with a lightweight model. Balanced default. |
| **ELA 1st** | A stronger single-model engine with on-demand vision and note-backed planning. Free-form conversational answers. |
| **ELA 4N** | Highest accuracy. Every answer is grounded in a live web search first; actions are decided by two independent models in parallel with a judge resolving disagreements, and a supervisor checks the final result. |

All four work across Screen Guide, the floating Agent bubble, and the AI Browser.

### 3. Flexible screen understanding

Lenspilot can see a screen in several ways, selectable in Settings and chosen automatically as a fallback:

- **Accessibility tree** — fast and exact, filtered to clickable items plus visible text and popups so the model gets a clean picture without noise.
- **AI Vision** — screenshots interpreted by a vision model, for apps with no usable accessibility data.
- **On-device icon detector** (YOLO-style TFLite model) — offline, fast, fixed icon vocabulary.
- **On-device OCR** — text-only, cheapest.
- **Hybrid** — detector and OCR together for the strongest on-device reading.
- **Server vision-language model** — slower but recognises the widest range of icons.

If accessibility permission is not granted, Lenspilot automatically falls back to the vision pipeline, requesting screen capture only when needed.

### 4. AI Browser — an agent with a real browser

A full in-app browser (tabs, bookmarks, history, downloads, find-in-page, desktop-site, multiple search engines) that an AI agent can drive.

- **Autonomous multi-step tasks** across websites: filling forms step by step, replying to multiple comments, submitting multiple applications, researching, and comparing.
- **Research-first behaviour.** Asked "where do I apply for X?", the agent searches the web, finds the right official site, works out what information is needed before applying, and falls back to the user's confirmation when a required field is uncertain.
- **Browser as guide, not only automation.** When a screen has several options it asks the user which to take; when a form needs input it asks field by field, and moves on if the user fills a step themselves.
- **Live chat with the agent.** A glass-style chat overlay lets the user redirect, add instructions, answer the agent's questions, pause, resume, or stop — by typing, by voice, or by sending an image.
- **Human-in-the-loop handoffs.** Captchas, logins, one-time passcodes, and payments are never bypassed: the agent pauses, hands control to the user with clear guidance, and resumes in the same session afterwards.
- **Cross-app one-time-code flow.** The agent can work out where a verification code will arrive, open that app, guide the user to copy it, return to the exact same browser session, and continue.
- **One-time Google sign-in.** The agent can ask once for permission to sign in to Google in the browser and then use "Continue with Google" across sites during a task.
- **Separate history** for browser sessions.

### 5. ELORA Tools Mode — a general-purpose automation agent

ELORA is the AI persona inside Tools Mode: a goal-driven system that plans and executes work across **three workspaces**, designed to handle any kind of task rather than a fixed list.

- **Browser workspace** — the AI Browser above; downloads land directly in Storage.
- **Shell workspace** — a terminal-style workspace running Git (clone, commit, push, pull, branch, merge, rebase, and more) and basic navigation, so projects can be pushed to GitHub straight from the phone.
- **Storage workspace** — an in-app file manager where user uploads and browser downloads meet. ELORA can create, move, zip, unzip, and export files.

How it works:

1. **Plan.** For a big goal, the server researches candidate pipelines, scores them against time, quality, cost, and reliability, critiques the result, and returns a step-by-step plan. Verified pipelines are kept for reuse.
2. **Execute.** Each step names its workspace, action, expected completion signal, timeout, and risk level.
3. **Detect completion without polling the AI.** An on-device runner watches for events — a file appearing, a web endpoint returning a value, a build finishing, a command exiting, specific page text appearing, or a matching result in storage.
4. **Schedule.** Steps can wait until a given time (for example, until a service's usage limit resets) and resume automatically through system alarms.
5. **Ask before risky actions.** High-risk steps (spending money, deleting, publishing, pushing, changing account settings) require explicit approval every time.
6. **Learn.** Outcomes are fed back so successful pipelines can be reused.

Every job is a **session** with its own folder, state (running, waiting, needs you, done, failed), and step log, so long tasks can be left running and revisited.

### 6. Learning Mode — an AI teacher

A study companion that behaves like a classroom, not a chatbot.

- **Chat first, class when you ask.** Small talk gets a short answer; study requests produce a class plan; a class starts only when the student taps Class.
- **Research before teaching.** Before writing a class, an independent web-research agent and the student's own knowledge base are consulted in parallel, and the lesson can pull in fresh research as it goes.
- **Visual teaching.** With no picture, an animated robot teacher explains in speech bubbles. When a picture or diagram is relevant, the robot steps aside and teaches with pointers and highlights on the screen.
- **Voice.** Spoken lessons in the student's language, with multilingual text-to-speech (Bengali, Hindi, Arabic, Japanese, Korean, Chinese, Thai, Tamil, Telugu, and more) and voice input with quick language switching.
- **My Syllabus.** Students paste or photograph their own syllabus; the AI transcribes it faithfully, flags anything unclear, and stores it so future classes follow *their* curriculum.
- **Library and notes.** Past classes are kept in a library, with exportable class notes (PDF) and class recordings.
- **Own dark, premium learning theme** that makes entering the mode feel like walking into a different space.

### 7. Discussion Mode — rich answers with annotated screenshots

For "how do I do this on my phone/computer?" questions, Lenspilot answers like a skilled friend: numbered step cards with **marked screenshots** (red circles, arrows, numbered points), tap-to-zoom images, and friendly explanations.

### 8. Multi-Agent Research

A research mode where 2–5 independent AI agents search the web in parallel — mixing different models and search methods — and a final judge reconciles their answers into one verified, source-cited summary.

### 9. Lenspilot Keyboard

A full custom keyboard that works in any app:

- Regular typing plus a ✨ AI button for generating text in place.
- Bengali layouts, including a traditional consonant-plus-vowel-sign layout.
- **Live translation:** translate what you typed in place before sending, and translate on-screen text into your language.
- Adjustable key size and a tools menu.

### 10. Voice-first experience

Open the app and speak — a large voice ball starts a task immediately. Voice input defaults to Bengali and switches between Bengali, English, Hindi, and Arabic with a long-press.

### 11. AI Automation of the Real World (ESP32 devices)

Lenspilot extends the same agent to physical hardware. Any ESP32-based machine can describe its own capabilities — actions, safe limits, sensors, and a safe-stop action — and the app's AI operates it within those limits.

- Connects over Wi-Fi, Bluetooth, or USB-OTG, with the phone able to host its own access point.
- Hardware-enforced limits: the device always applies its own safety bounds regardless of what the AI requests.
- A **marketplace-style hub** of downloadable automation "agents" with cover art, categories, and one-tap install.
- **Firmware flashing from the phone** over USB, with checksum verification.
- A reference firmware is included under `firmware/`.

### 12. Personal context

Users can store personal information (including by photographing documents), which the AI uses to fill forms and personalise answers — kept under the user's control.

### 13. Reports and diagnostics

An in-app support-report flow, a temporary AI-call history viewer for transparency during guide sessions, and a guided history screen for past conversations.

---

## Accessibility and Inclusion by Design

- **Language-neutral AI.** The assistant answers in the language the user starts the chat in, for the whole chat, and switches only if asked. The interface itself is professional English.
- **Bengali-first quality** across speech recognition, text-to-speech, keyboard, and teaching.
- **Voice-first entry** for users who prefer speaking.
- **Works on modest hardware and networks** — an on-device detector and OCR for offline operation, small-model execution paths, and robustness for slow connections.
- **No computer required.** The entire development and delivery workflow for this project itself runs from a phone (see below) — the product is built the way its users live.

---

## Business Model

- **Token wallet.** Usage is metered in input/output tokens per user.
- **Rewarded ads** top up tokens, so a free tier is sustainable in price-sensitive markets.
- **Bring-your-own-key** unlimited mode for power users.
- **Subscriptions and per-user limits** controlled server-side.
- **Provider switching** between model vendors from an operator console, so cost and quality can be tuned without shipping an app update.

---

## How It Works

The hard part of a screen-aware AI agent is not calling a language model. It is making the model *reliable*: no invented buttons, no false "done", no infinite loops, low cost per task, and graceful fallback when the world does not match the assumptions.

### 1. The core loop: Observe → Decide → Validate → Act → Verify

```
   ┌────────┐   goal + last notes + screen    ┌────────┐
   │ OBSERVE│ ───────────────────────────────▶│ DECIDE │  (LLM, small prompt)
   └───▲────┘                                 └───┬────┘
       │                                          │ {note, status, say, do}
       │ next screen                              ▼
   ┌───┴────┐        rejected + reason       ┌──────────┐
   │ VERIFY │◀───────────────────────────────│ VALIDATE │  (deterministic code)
   └───▲────┘   (model retries, max 2)       └───┬──────┘
       │                                         │ accepted
       │                                         ▼
       └──────────────────────────────────── ┌──────┐
                                             │  ACT │  highlight · intent · deep link · explain
                                             └──────┘
```

**Observe.** On every turn the app builds a compact picture of the current screen:
- In accessibility mode, only the elements that matter reach the model: clickable items, plus visible text and popups. Each element carries a position zone (top bar, bottom navigation, floating button, etc.) so the LLM can reason about context without reading every pixel.
- In vision mode, a screenshot is sent with sensitive fields (password, one-time code, card inputs) masked in black before it leaves the device. In hybrid mode, the model sees the image *and* the elements found by the detector and OCR.

**Decide.** The model receives only the goal, a short rolling history of what has already been shown, and the current screen. It answers in a fixed structure: a private note, a status, a message for the user, and an action to take.

**Validate.** Before anything is shown to the user, plain server code checks the decision against the real screen. It rejects ids that are not on screen, labels that do not match the chosen id, controls that are disabled, and instructions that would violate the app's rules.

**Act.** Depending on the decision, Lenspilot highlights the exact control for the user to tap, launches an app or a settings page directly through a system intent or deep link, or switches to teaching mode for explanation.

**Verify.** The next observation shows whether the screen actually changed as expected. If the same pick repeats three times, the loop escalates to a screenshot and then to asking the user. Six or more loops without progress escalate to a clear failure state.

**Measured the hard way.** The loop is tested against a simulated phone with a dozen interlinked screens (including popups that leave background elements in the tree, as real Android does) and a deliberately faulty model to ensure the safety nets trigger when they should.

### 2. Four engines, one pipeline shape

Every engine follows *plan → execute one small step → check progress → repair the plan → learn*. They differ in where the intelligence is spent:

| Engine | Where the plan comes from | Per-step model | Screenshots | Notes |
|---|---|---|---|---|
| Super Lite | One strong-model call with no screen | Small, cheap executor, one call | Never | Lowest cost |
| Super 1·2 | Piggybacks on the existing planning call | Light model, one call | Once per step, only if the AI asks | Default |
| ELA 1st | Derived from the reply (one small call) | Stronger model | On demand | Free-form conversation |
| ELA 4N | From a *verified*, web-grounded reply plus previously saved successful paths | Two independent models in parallel, judge on disagreement | On irreversible steps and at "task complete" | Highest reliability |

If a screen contradicts the plan, the plan is patched rather than abandoned. When a task succeeds, the path is saved and used as a candidate the next time a similar goal appears, so common tasks get cheaper and more reliable over time.

### 3. The AI Browser agent

The browser agent separates *planning* from *acting* to keep each call small:

1. **Plan once** at the start of a run, with the goal and the first page.
2. **Observe in a single call.** A small injected script returns a pruned, token-budgeted list of the interactive elements on the page, including icons in bottom bars that naive scrapers miss.
3. **Act and report the outcome.** A second script performs the action and reports what changed (navigation, new content, no effect), so the model reasons about results, not guesses.
4. **Check relevance after every navigation.** If the new page does not serve the goal, the agent goes back instead of continuing.
5. **Detect blockers.** Captchas, logins, one-time codes, and payments are recognised and trigger a handoff to the user. The agent pauses, shows what is needed, and resumes in the same session afterwards.
6. **Supervise (ELA 4N).** A separate model reviews each step of long runs.

**Live chat as a router.** Messages typed, spoken, or sent as images during a run are classified *by meaning* into one of: start a task, add an instruction, answer the agent's question, pause, resume, stop, or ask for a different approach.

**Research before action.** For questions like "where do I apply for X?", the agent searches, identifies the official source, determines what information the application needs, and tells the user before asking for anything sensitive.

### 4. ELORA: plan, execute, detect, wake

For goals larger than a single screen or site, ELORA runs a different loop built around *not* paying for the model while waiting.

```
goal ──▶ PLANNER (server) ──▶ steps[] ──▶ EXECUTOR ──▶ DETECTOR ──▶ next step / fix
                                              │            ▲
                           browser · shell · storage · wait · ask_user
                                              │            │
                                         RUNNER (on device, zero LLM tokens)
                          wakes the model only on: done · failed · stuck · needs you
```

**Planner (server).** Constraints → weighted criteria → at least five candidate pipelines, including unconventional combinations the user did not name → separate research per candidate (official docs, current UI, provider constraints) → a critic scores and chooses the best path.

**Step contract.** Every step is structured data: workspace, action, arguments, a completion detector, a timeout, a risk level, and what to do on failure.

**Runner (device).** Instead of asking the model "is it done yet?" on a timer, the runner subscribes to concrete signals: a file appears in storage, a web endpoint returns a given value, a build run completes, or a command exits with a status code.

**Scheduler.** Steps can wait until a timestamp (for instance, when a service's usage limit resets) and resume through exact alarms, with a background-job fallback.

**Shell.** Android cannot execute downloaded native binaries, so the shell is built on a pure-Java Git implementation plus a small set of navigation built-ins. This covers the workflow that matters most for code and content work from a phone.

**Learning from outcomes.** Verified pipelines are stored as *data* in a retrieval index, not as prompt text. A pipeline is marked unverified until it succeeds twice, so one lucky run does not become a permanent rule.

**Risk is decided by rules, not by the AI.** A declarative rule list classifies each step. High-risk steps always need explicit approval; a timeout counts as a refusal; a refused step is not retried automatically.

### 5. Learning Mode pipeline

1. **Route.** Each message is classified as small talk, a direct question, a request needing clarification, or a study request. Greetings are handled with zero model tokens.
2. **Plan in chat.** Study requests produce a class plan inside the conversation; no class starts until the student taps Class.
3. **Research.** An independent web-research agent and the student's own knowledge base run in parallel, with a time limit; a lesson never blocks if one source is slow.
4. **Compose in batches.** Lessons are written in parts. Between parts the model can request fresh research for the next part, up to a fixed budget per class.
5. **Teach.** The board renders the lesson; a robot explains by voice and bubbles, or steps aside to point at a diagram. Subject names and example lists are never hard-coded into prompts, so the system can teach almost any topic.
6. **Personalise.** A student's own syllabus is transcribed faithfully (unclear parts flagged for review), saved as embeddings, and retrieved during later classes. With no syllabus saved, nothing changes.

### 6. Discussion cards with marked screenshots

The model locates the relevant region in a screenshot (or in a reference image found on the web and ranked by a vision model), and the *app* draws the circles, arrows, and numbers locally. If the match is weak or the image is noisy, the app asks for a clearer reference instead of making a confident claim.

### 7. Cost and latency engineering

- **Flat context.** Each call carries the goal plus a few compact lines of history, so per-call cost does not grow with task length.
- **Smallest model that works.** Planning uses strong models; per-step execution uses small ones where the engine allows.
- **Filtered observations.** Sending only actionable elements cuts input tokens and reduces hallucination at the same time.
- **Zero-token waiting** through the on-device runner.
- **Reuse.** Saved successful paths and retrieval over notes replace repeated reasoning.
- **Metered billing per call,** so heavier engines honestly cost more tokens and users can choose.
- **Provider and model switching** from the operator console, with fallbacks when a model's quota is exhausted.

### 8. Honest constraints

- Accessibility-tree mode needs the user to grant the accessibility permission; without it, Lenspilot falls back to vision, which is slower and costs more.
- Sign-in with some providers can be blocked inside an embedded browser; in those cases the agent hands over to the user rather than trying to work around it.
- Android does not allow running arbitrary native programs, so Tools Mode's shell is intentionally limited to Git and basic navigation.
- Real-model reliability varies by model and app; the included simulations validate the safety nets, and live testing on real devices is ongoing.

---

## Architecture

```
┌────────────────────────────── Android app (Kotlin) ─────────────────────────────┐
│  Chat · Guide overlay · Floating Agent · Learning · Keyboard · Voice             │
│  Screen reader (Accessibility) · Vision (screenshot, OCR, on-device detector)    │
│  AI Browser (WebView + injected observers/actions) · ELORA Tools Mode            │
│     ├─ Browser workspace   ├─ Shell workspace (Git)   └─ Storage workspace       │
│  Runner (event detectors) · Scheduler · Permission gates · Device hub (ESP32)    │
└───��───────────────────────────────────┬────────────────────────────────────────┘
                                        │ streaming (SSE) over HTTPS
┌───────────────────────────────────────▼────────────────────────────────────────┐
│                         Backend (Python / Flask)                                 │
│  Guide loop & validators · Four engine pipelines · Browser agent planner/actions │
│  Learning (curriculum, lessons, research) · Discussion cards · Multi-agent       │
│  Tools planner / decider / learner · Embedding-based RAG · Token billing         │
│  Operator console (provider & model switching, pricing, users)                   │
└───────────────┬───────────────────────────┬──────────────────────────────────────┘
                │                           │
        Gemini · Groq models          Firebase (Auth, Firestore)
```

**Key design principles**

- **Small prompts, no hard-coded examples.** System prompts never name specific apps, sites, or subjects; the model works them out itself, which reduces hallucination on unfamiliar tasks.
- **Server-side validation of every AI decision** before it reaches the user.
- **Event-driven waiting.** The AI is called when something changes, not on a timer.
- **Retrieval over memorisation.** Notes, syllabi, and verified pipelines are retrieved with embeddings instead of being stuffed into prompts.
- **Fail safe, then ask.** Unclear or risky situations stop the loop and ask the user.

### Tech stack

| Layer | Technology |
|---|---|
| Mobile | Kotlin, Android SDK (min 26, target 34), AndroidX, Material, WorkManager |
| On-device ML | TensorFlow Lite icon detector, ML Kit OCR |
| Networking | OkHttp, server-sent events |
| Git on device | JGit |
| Hardware | USB-serial, Bluetooth SPP, Wi-Fi, ESP32 reference firmware |
| Backend | Python, Flask |
| AI | Gemini and Groq model families, Google-grounded search, embeddings (768-dim) for RAG, multilingual TTS |
| Auth & data | Firebase Authentication, Firestore |
| Monetisation | Rewarded-ad networks, token wallet |
| CI/CD | GitHub Actions (APK builds) |

### Codebase at a glance

- ~176 Kotlin source files (~42,000 lines) in the Android app
- ~21,500-line Python backend
- Simulation and regression suites for the guide loop, browser agent, and Tools Mode planner (fake phone, fake sites, and deliberately faulty models used to test the safety nets)

---

## Repository Layout

```
.
├── app/                        Android application
│   └── src/main/java/…/
│       ├── guide/              Goal + screen guide loop
│       ├── accessibility/      Screen reader and service
│       ├── vision/             Screenshot, OCR, on-device detector, fallback guide
│       ├── overlay/ chat/      Highlights, glass chat, guide cards
│       ├── browser/            AI Browser and live chat
│       ├── tools/              ELORA: agent, runner, shell, storage, permissions
│       ├── learning/           Learning Mode, syllabus, library, class notes
│       ├── keyboard/           Lenspilot Keyboard
│       ├── automation/         ESP32 hub, transports, flasher
│       ├── audio/              Voice input and text-to-speech
│       └── ads/                Token wallet and rewarded ads
├── firmware/                   ESP32 reference firmware
├── tests/                      Simulation and regression tests
├── .github/workflows/          CI (APK build, browser tests)
└── docs/ *.md                  Design notes per feature
```

---

## Building

The project builds in the cloud so it can be developed entirely from a phone.

1. Add your own Firebase configuration file and backend URL in the app configuration (not included in this repository).
2. Push to GitHub; the included workflow assembles a debug APK.
3. Download the APK from the workflow artifacts and install it.

The backend is a standard Flask app and needs your own AI provider credentials and Firebase project, supplied through environment configuration.

---

## Status and Roadmap

Lenspilot is under active development by a solo founder, shipping iteratively.

**Working today:** Screen Guide with four engines, floating Agent bubble, AI Browser with live chat and handoffs, Learning Mode with syllabus and notes, Discussion cards, Multi-Agent research, custom keyboard, voice input, and a working app foundation.

**In progress:** ELORA Tools Mode — shell, storage, runner, scheduler, planner, and session management are implemented and being hardened on real devices.

**Next:**
- Fully general automation: any goal, any tool, with the planner discovering the best pipeline itself
- Richer GPT-style answer layouts with images and option boxes across all modes
- A new, distinctive product name and brand
- Broader language coverage and on-device models for lower cost and offline use

---

## Vision

Make the phone do what people mean, not what menus demand — in their language, on their network, with their permission. Lenspilot starts by guiding people through their own screens and ends with an AI that can operate across apps, websites, a shell, and physical devices when allowed.

---

## License

All rights reserved unless a license file is added to this repository.

## Contact

Built by **Hemel**.
