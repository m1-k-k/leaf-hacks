# NeuroLearn

Adaptive revision for learners who need control over how much information they see at once. Enter a topic, adjust a 1–10 sensory-load slider, and NeuroLearn generates an explanation with a matching level of detail and presentation.

This repository, `leaf-hacks`, contains a Next.js prototype designed with SEN and neurodiverse learners in mind. It combines streamed Gemini responses, follow-up chat, visual accessibility settings, and a calming view in one learning workspace.

## The learning experience

| Sensory load | Presentation |
| --- | --- |
| 1–3 | Detailed explanations with paragraphs and examples |
| 4–6 | Short bullet points in plain language |
| 7–9 | A focused view with one idea at a time and a shorter, gentler response |
| 10 | Learning content gives way to a calming message and visual breathing guide; the page does not request a new explanation |

Changing the load or sand-mode setting regenerates the active topic after a short debounce. The interface also includes:

- Follow-up questions with streamed replies that use the current topic and load settings.
- Copy and regenerate controls, plus browser text-to-speech where supported.
- A **deaf mode** setting that hides the listen control and adds visual completion cues.
- Deuteranopia, protanopia, and monochromacy palettes, with shape-coded load indicators.
- **Sand mode**, which applies a warm palette and asks the model for an unhurried tone.
- Six recent topics, accessibility preferences, and a small companion interaction stored locally in the browser.

The load bands guide model prompts and rendering; generated explanations can vary. The project demonstrates an accessibility-oriented learning interface and does not establish a measured learning outcome or an accessibility certification.

## Run locally

Use **Node.js 22 or newer** and npm. The checked-in AI SDK dependencies require Node 22.

```bash
git clone https://github.com/m1-k-k/leaf-hacks.git
cd leaf-hacks
npm ci
```

Create `.env.local` from [env.example](env.example):

```bash
# macOS / Linux
cp env.example .env.local
```

```powershell
# Windows PowerShell
Copy-Item env.example .env.local
```

Create a key in [Google AI Studio](https://aistudio.google.com/) and set it in `.env.local`:

```dotenv
GOOGLE_GENERATIVE_AI_API_KEY=your_key_here
```

Start the application:

```bash
npm run dev
```

Open [localhost:3000](http://localhost:3000). The interface can load without a key, but explanation and chat requests require a valid key and network access. Restart the development server after changing environment variables.

## Configuration and data flow

| Item | Location | Behaviour |
| --- | --- | --- |
| Gemini credentials | `GOOGLE_GENERATIVE_AI_API_KEY` | Server-only environment variable; keep it out of `NEXT_PUBLIC_` variables |
| Model | [src/lib/gemini.ts](src/lib/gemini.ts) | Defaults to `gemini-2.5-flash` |
| Learning endpoint | [src/app/api/learn/route.ts](src/app/api/learn/route.ts) | Streams explanations or chat replies through the Vercel AI SDK |
| Load bands | [src/lib/sensoryLoad.ts](src/lib/sensoryLoad.ts) | Maps slider values to four presentation bands |
| Local preferences | [src/context/AccessibilityContext.tsx](src/context/AccessibilityContext.tsx) | Stores accessibility settings in `localStorage` |

The browser sends the topic, load level, and sand-mode setting to `/api/learn`. Follow-up requests also include the question and conversation history; the server uses up to the last 12 history entries. These inputs are sent to Google's model service. Recent topics and preferences use browser storage; the repository has no account system or database for syncing learning history.

## Development

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start the Next.js development server |
| `npm run build` | Create a production build |
| `npm run start` | Serve the production build |
| `npx eslint src` | Run the checked-in ESLint configuration against the source |

The current `npm run lint` script uses the older `next lint` command. Use the direct ESLint command above with this repository's flat configuration. There is no automated test suite checked in.

```text
src/
  app/
    page.tsx                  Learning workspace, settings, and chat
    api/learn/route.ts        Streamed AI response endpoint
  components/
    Pet.tsx                   Interactive companion
    ui/BreathingCircle.tsx    Visual breathing guide
  context/
    AccessibilityContext.tsx  Shared preferences and browser storage
  lib/
    gemini.ts                 Model selection
    sensoryLoad.ts            Load-band mapping
    colourBlindPalettes.ts    Alternative colour palettes
```

The stack is Next.js 15, React 19, TypeScript, Tailwind CSS 4, Framer Motion, and the Vercel AI SDK with `@ai-sdk/google`.

## Deployment and project notes

Deploy to a host that supports Next.js server routes and streaming responses. Set `GOOGLE_GENERATIVE_AI_API_KEY` in the server environment. The learning endpoint currently has no authentication or application-level rate limiting, so a public deployment needs usage controls appropriate to its API budget.

[PRODUCT_PLAN.md](PRODUCT_PLAN.md) and [PROJECT_GUIDE.md](PROJECT_GUIDE.md) provide additional design context. Some supporting documents, including [CONTRIBUTING.md](CONTRIBUTING.md), retain earlier NeuroDev Therapy branding and structure; use this README and the current source for setup and implemented behaviour.

## Licence and attribution

MIT — see [LICENSE](LICENSE). The existing licence credits Dhruba Bhattacharyya, and the repository retains materials from NeuroDev Therapy.
