
# BriefGauge AI

### Benchmarking AI Agents for Pre-Meeting Intelligence

BriefGauge AI is an AI-powered benchmarking platform that evaluates how effectively different AI agents prepare company research briefings before important business meetings.

It gives two AI agents the same research evidence and meeting objective, then compares their outputs using transparent, task-specific evaluation metrics.

The goal is simple: help users understand not just what an AI agent produces, but how effectively it prepares them for a real-world business conversation.

---

## 1. Problem Statement

Preparing for an important business meeting often requires researching a company's recent developments, leadership changes, product launches, partnerships, opportunities and potential risks.

This research can be time-consuming, and AI-generated summaries may overlook important developments, rely on weak evidence or produce repetitive meeting questions.

Although multiple AI agents can perform the same research task, users often lack a consistent way to compare their performance.

**BriefGauge AI addresses this problem by benchmarking AI agents using the same research evidence, meeting objective and evaluation framework.**

## 2. Proposed Solution

BriefGauge AI uses Tavily to collect recent company information and Bifrost to route AI inference requests.

Two agents independently analyze the same evidence:

- **Agent Alpha: Coverage Researcher**  
  Focuses on broad company coverage, business developments, product launches, partnerships, growth opportunities and strategic developments.

- **Agent Beta: Critical Analyst**  
  Focuses on overlooked developments, potential risks, contradictions, weak evidence and unanswered questions.

Both agents generate a pre-meeting briefing and five relevant meeting questions.

The platform then evaluates their outputs using five benchmarking metrics and presents a comparison.

## 3. Key Features

### Dual-Agent Benchmarking
Runs two specialized AI agents on the same research evidence and meeting objective.

### Tavily-Powered Research
Retrieves recent company information and filters dated sources according to the meeting's research window.

### Blind Benchmarking
Allows users to compare agent performance without revealing agent identities until the results are revealed.

### Transparent Scoring
Evaluates both agents using five measurable benchmarking dimensions.

### Citation Verification
Checks whether cited URLs correspond to the retrieved research sources.

### Meeting-Ready Briefings
Generates executive summaries, key developments, evidence-backed insights, limitations and meeting questions.

### Demo Mode
Allows users to explore the interface and sample benchmarking results without making live AI inference requests.

### Health Monitoring
Provides information about the configured Tavily and Bifrost connections.

## 4. Benchmarking Methodology

Each agent is evaluated using five metrics.

| Metric | Weight | Purpose |
|---|---:|---|
| Source Coverage | 20 | Evaluates research coverage |
| Citation Coverage | 20 | Evaluates supporting citations |
| Freshness | 20 | Evaluates the recency of research |
| Meeting Usefulness | 20 | Evaluates relevance to the meeting objective |
| Latency | 20 | Evaluates response time |

**Maximum score: 100 points per agent.**

The platform calculates heuristic scores using the available research and generated outputs.

These scores are intended for comparative benchmarking. They do not establish factual accuracy or guarantee that an agent's conclusions are correct.

Token usage is displayed when the inference provider returns usage information.

## 5. System Architecture

```text
                USER INPUT
                     |
        Company / Meeting Date / Purpose
                     |
                     v
              EXPRESS SERVER
                     |
                     v
                TAVILY API
                     |
           Recent Company Research
                     |
              Shared Evidence
                /        \
               /          \
              v            v
        AGENT ALPHA    AGENT BETA
        Researcher     Critical Analyst
              |            |
              v            v
           BIFROST GATEWAY
              |            |
              v            v
           GROQ MODEL / CONFIGURED MODEL
              |            |
              v            v
          BRIEFING       BRIEFING
              |            |
              +-----+------+
                    |
                    v
             SCORING ENGINE
                    |
                    v
           BENCHMARK COMPARISON
                    |
                    v
              REACT FRONTEND
```

### Architecture Components

- **Frontend:** React interface for input, briefings, scores and comparison.
- **Backend:** Express server handles requests and coordinates research and inference.
- **Research:** Tavily retrieves company-related information.
- **Inference Gateway:** Bifrost routes requests to the configured AI provider.
- **AI Provider:** Groq is the configured provider in the demonstrated setup.
- **Scoring Engine:** Calculates comparative benchmarking metrics.
- **Testing:** Automated tests validate important research, parsing and scoring behaviour.

Both agents receive the same research evidence to make the comparison more consistent.

## 6. Technology Stack

| Technology | Purpose |
|---|---|
| React 19 | Frontend interface |
| TypeScript | Application development |
| Vite 6 | Frontend development and build |
| Node.js | JavaScript runtime |
| Express 4 | Backend API server |
| Tavily API | Web research |
| Bifrost | AI inference gateway |
| Groq | AI model provider |
| OpenAI-compatible API | Model request format |
| Lucide React | Interface icons |
| TSX | TypeScript execution |
| Node Test Runner | Automated testing |

## 7. Prerequisites

Before running BriefGauge AI, install:

1. Node.js and npm.
2. Git (optional, for cloning the repository).
3. A Tavily API account and API key.
4. A locally running Bifrost instance.
5. A Groq account and API key, configured through Bifrost.

The demonstrated configuration uses:

- Node.js 26.13.1
- Bifrost running locally on port 8080
- Groq model: `openai/gpt-oss-20b`

Other compatible models may require configuration changes and testing.

## 8. Installation on Windows

The following instructions use Windows PowerShell.

### Step 1: Install Node.js

Download Node.js from:

https://nodejs.org/

Install it and restart PowerShell.

Verify installation:

```powershell
node -v
npm -v
```

### Step 2: Clone the repository

```powershell
git clone YOUR_GITHUB_REPOSITORY_URL
```

Navigate into the project:

```powershell
cd BriefGaugeAI
```

Replace the repository URL with the actual GitHub repository URL.

Alternatively, download the repository as a ZIP file, extract it and open PowerShell inside the project folder.

### Step 3: Install dependencies

```powershell
npm.cmd install
```

This installs the dependencies defined in `package.json`.

## 9. Tavily Configuration

BriefGauge AI uses Tavily to retrieve company research.

### Step 1: Create a Tavily account

Visit:

https://tavily.com/

Create an account and obtain an API key.

### Step 2: Configure the API key

The Tavily API key is configured in the application's environment file.

Do not publish your actual API key.

## 10. Bifrost and Groq Configuration

BriefGauge AI uses Bifrost as its AI inference gateway.

**Important:** The current application configuration expects a Bifrost instance running locally. The application does not automatically host or deploy Bifrost.

### Step 1: Start Bifrost

Install and start Bifrost using the official instructions:

https://docs.getbifrost.ai/

The demonstrated local startup command is:

```powershell
npx.cmd -y @maximhq/bifrost
```

Keep the Bifrost terminal running while using BriefGauge AI.

The configured local gateway address is:

```text
http://localhost:8080
```

### Step 2: Configure Groq

1. Create an account at https://console.groq.com/
2. Generate a Groq API key.
3. Open the Bifrost interface.
4. Configure Groq as an AI provider.
5. Add your Groq API key through Bifrost's provider configuration.
6. Select a supported model.

The demonstrated model is:

```text
openai/gpt-oss-20b
```

### Step 3: Create a Bifrost Virtual Key

Create a Virtual Key in Bifrost and configure the application to use it.

The application uses the Virtual Key to authenticate requests to the Bifrost gateway.

Keep this key private.

## 11. Environment Configuration

Create a file named:

```text
.env.local
```

Place it in the project's root directory.

Add the following configuration:

```env
TAVILY_API_KEY=your_tavily_api_key

BIFROST_BASE_URL=http://localhost:8080/v1
BIFROST_VIRTUAL_KEY=your_bifrost_virtual_key
BIFROST_MODEL=groq/openai/gpt-oss-20b
```

Replace the placeholder values with your own credentials.

### Environment Variables

| Variable | Description |
|---|---|
| TAVILY_API_KEY | Your Tavily API key |
| BIFROST_BASE_URL | Bifrost OpenAI-compatible API endpoint |
| BIFROST_VIRTUAL_KEY | Your Bifrost Virtual Key |
| BIFROST_MODEL | Model identifier configured in Bifrost |

**Security Notes**

- Never commit `.env.local` to GitHub.
- Never publish API keys, passwords or Virtual Keys.
- Do not share screenshots containing credentials.
- Use `.env.example` for sharing configuration templates.
- Each developer must configure their own API credentials.

## 12. Running the Application

Make sure:

- Dependencies are installed.
- `.env.local` is configured.
- Bifrost is running locally.

Open PowerShell inside the project directory and execute:

```powershell
npm.cmd run dev
```

This command starts:

- Express backend using TSX watch.
- Vite frontend development server.

Open the application in your browser:

http://localhost:5173

Keep the terminal running while using the application.

### Stopping the Application

Press:

```text
Ctrl + C
```

Stop the Bifrost process separately when it is no longer required.

## 13. Testing

BriefGauge AI includes automated tests.

### Run automated tests

```powershell
npm.cmd test
```

### Run TypeScript type checking

```powershell
npm.cmd run typecheck
```

### Create a production build

```powershell
npm.cmd run build
```

The production build is generated in the `dist` directory.

The project has been tested with:

- 17 automated tests passing.
- Successful TypeScript compilation.
- Successful Vite production build.

These results describe the tested development version and do not imply that every external API configuration will work without setup.

## 14. Demo Mode

Demo Mode allows users to explore BriefGauge AI without making live research and AI inference requests.

It uses predefined sample benchmarking data to demonstrate:

- Agent briefings.
- Benchmark scores.
- Agent comparison.
- Winner visualization.
- Research source presentation.

Demo Mode is particularly useful for:

- Hackathon presentations.
- Interface demonstrations.
- Exploring the benchmarking workflow.
- Demonstrating the application when external API services are unavailable.

**Important:** Demo results are illustrative and should not be interpreted as live AI-generated research.

## 15. Screenshots

Add application screenshots to a `docs/screenshots/` directory.

Suggested screenshots:

| Screenshot | Description |
|---|---|
| `dashboard.png` | Main application dashboard |
| `research.png` | Retrieved company research |
| `briefings.png` | Alpha and Beta agent briefings |
| `comparison.png` | Benchmark comparison and scoring |
| `blind-mode.png` | Blind benchmarking interface |
| `demo-mode.png` | Demo Mode interface |

Example Markdown:

```markdown
![BriefGauge AI Dashboard](docs/screenshots/dashboard.png)
```

Screenshots should not contain API keys, passwords or private configuration information.

## 16. Known Limitations

- Live benchmarking requires valid Tavily credentials.
- Live inference requires a locally running Bifrost instance and a configured AI provider.
- The demonstrated Groq configuration is subject to provider rate limits and free-tier usage restrictions.
- When provider limits are reached, live benchmarking may temporarily fail.
- AI-generated briefings may contain incomplete or inaccurate interpretations. Always verify important claims against their original sources.
- Undated research sources cannot receive the same freshness credit as dated sources.
- Benchmark scores are heuristic measurements and do not prove factual accuracy.
- Token usage depends on whether the configured inference provider returns usage information.
- Demo Mode uses sample data and does not perform live research.
- Public cloud deployment has not been implemented or verified as part of this project.

## 17. Project Structure

```text
BriefGaugeAI/
|
|-- server/
|   |-- index.ts
|   |-- bifrost.ts
|   |-- tavily.ts
|   |-- scoring.ts
|   |-- demoData.ts
|   |-- types.ts
|
|-- src/
|   |-- components/
|   |-- App.tsx
|   |-- main.tsx
|   |-- index.css
|   |-- types.ts
|
|-- test/
|   |-- benchmark.test.ts
|
|-- .env.example
|-- .gitignore
|-- package.json
|-- tsconfig.json
|-- vite.config.ts
|-- README.md
```

## 18. Developer Setup Summary

To run the project using your own credentials:

1. Install Node.js.
2. Clone the repository.
3. Install dependencies using `npm.cmd install`.
4. Create a Tavily account and obtain an API key.
5. Start a local Bifrost instance.
6. Configure your AI provider and model in Bifrost.
7. Create a Bifrost Virtual Key.
8. Add your credentials to `.env.local`.
9. Start the application using `npm.cmd run dev`.
10. Open `http://localhost:5173`.

No API credentials are included in the repository.

## 19. Future Improvements

Potential future development areas include:

- Additional AI model and provider comparisons.
- More advanced benchmarking metrics.
- Historical benchmark tracking.
- Expanded source-quality evaluation.
- Improved handling of provider rate limits.
- Additional research and reporting capabilities.
- Verified hosted deployment options.

## 20. Hackathon Project

**Project:** BriefGauge AI

**Category:** AI Agent Benchmarking and Research Intelligence

**Research Integration:** Tavily

**Inference Gateway:** Bifrost

**AI Provider:** Groq (demonstrated configuration)

BriefGauge AI explores a practical question in the growing AI-agent ecosystem:

**When two AI agents receive the same task and evidence, which one prepares a more useful, well-supported and meeting-ready briefing?**

The project aims to make AI-agent evaluation more transparent, comparable and task-oriented.
