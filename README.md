# BriefGauge AI ⚡

> **Pre-Meeting AI Agent Research Benchmarking Dashboard**
> Evaluates and scores two AI agents preparing pre-meeting company research briefings using real search evidence from Tavily and inference through the Bifrost OpenAI-compatible gateway.

---
## Problem Statement

Preparing for an important business meeting often requires researching a company's recent developments, leadership changes, product launches, partnerships, opportunities and potential risks.

This research can be time-consuming, and AI-generated summaries may overlook important developments, rely on weak evidence or produce repetitive meeting questions.

Although multiple AI agents can perform the same research task, users often lack a consistent way to compare their performance.

**BriefGauge AI addresses this problem by benchmarking AI agents using the same research evidence, meeting objective and evaluation framework.**

## 🎯 Proposed Solution (Overview)

**BriefGauge AI** benchmarks two specialized AI agent personas that prepare pre-meeting company research briefings:

1. **Agent Alpha (Coverage Researcher)**: Focuses on broad, holistic coverage of recent company news, executive leadership changes, hiring, launches, and core business momentum.
2. **Agent Beta (Critical Analyst)**: Focuses on critical analysis, identifying overlooked risks, contradictions, unsubstantiated hype, weak evidence, and probing questions.

Both agents receive the **same evidence**, run against the **same model via Bifrost**, and are evaluated across **5 transparent metrics** out of 100 points:
* **Source Coverage** (20 pts, 20% weight)
* **Citation Coverage** (20% weight, 20 pts)
* **Freshness & Temporal Alignment** (20% weight, 20 pts)
* **Meeting Usefulness & Probing Questions** (20% weight, 20 pts)
* **Response Latency** (20% weight, 20 pts)

---
## 3. Key Features

###Dual-Agent Benchmarking
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

- **Frontend:** React interfce for input, briefings, scores and comparison.
- **Backend:** Express server handles requests and coordinates research and inference.
- **Research:** Tavily retrieves company-related information.
- **Inference Gateway:** Bifrost routes requests to the configured AI provider.
- **AI Provider:** Groq is the configured provider in the demonstrated setup.
- **Scoring Engine:** Calculates comparative benchmarking metrics.
- **Testing:** Automated tests validate important research, parsing and scoring behaviour.

Both agents receive the same research evidence to make the comparison more consistent.

---
## 🏗️ Tech Stack

* **Frontend**: React 19, TypeScript, Tailwind CSS, Lucide Icons, Vite
* **Backend**: Node.js, Express, `tsx`
* **Research Engine**: [Tavily Search API](https://tavily.com) (retrieves recent 30-day developments)
* **AI Model Gateway**: [Bifrost](https://github.com/maximumpush/bifrost) OpenAI-compatible Chat Completions API with virtual key authentication (`x-bf-vk`)
* **No paid AI SDKs or Gemini**: Uses Bifrost proxy and Tavily only.

---

## 🚀 Quick Start (Windows PowerShell)

### 1. Prerequisites
* **Node.js** v18+ or higher installed (`node -v`)
* Windows PowerShell or Command Prompt

### 2. Configure Environment Variables
A `.env.local` file is already created in the project root. You can review or update it:

```env
# Tavily Search API Key
TAVILY_API_KEY=tvly-your-tavily-api-key

# Bifrost Gateway Configuration
BIFROST_BASE_URL=http://localhost:8080/v1
BIFROST_VIRTUAL_KEY=sk-bf-your-bifrost-virtual-key
BIFROST_MODEL=groq/openai/gpt-oss-20b

# Backend Port
PORT=3001
```

> 🔒 **Security Note**: All API keys and secrets stay strictly on the Node.js backend. No credentials are ever exposed to frontend client code. `.env.local` is ignored by Git.

### 3. Install Dependencies
In Windows PowerShell, run:

```powershell
npm.cmd install
```

*(Note: On Windows systems where PowerShell script execution policy disables `npm.ps1`, using `npm.cmd` avoids `PSSecurityException`.)*

### 4. Run Automated Tests
Execute the 14-point automated test suite (mocked API tests with zero quota consumption):

```powershell
npm.cmd test
```

### 5. Start the Application
To run both backend (Express on port 3001) and frontend (Vite on port 5173) concurrently:

```powershell
npm.cmd run dev
```

Alternatively, you can run them in two separate PowerShell windows:
* **Terminal 1 (Backend Server)**:
  ```powershell
  npm.cmd run dev:server
  ```
* **Terminal 2 (Frontend Client)**:
  ```powershell
  npm.cmd run dev:client
  ```

### 5. Open in Browser
Open your browser and navigate to:
```
http://localhost:5173
```

---

## 🧪 Testing in Demo Mode

If you don't have Bifrost running locally on port 8080 or if your Tavily search quota is exhausted, BriefGauge AI includes a **1-Click Demo Mode**:

1. Click **"Load Quick Demo (Stripe)"** on the input card, or
2. Toggle the **"Demo Mode"** button in the header bar.
3. The application will immediately run a full benchmark on realistic synthetic company data for Stripe, demonstrating the complete briefing comparison, scoring formulas, and winner determination without consuming live quota.

---

## 🩺 System Health Check

BriefGauge AI provides a real-time health check endpoint:

```
GET http://localhost:3001/api/health
```

Returns JSON:
```json
{
  "status": "ok",
  "tavily": {
    "configured": true,
    "hasKey": true,
    "status": "connected",
    "message": "Tavily API key configured."
  },
  "bifrost": {
    "configured": true,
    "baseUrl": "http://localhost:8080/v1",
    "model": "groq/openai/gpt-oss-20b",
    "hasKey": true,
    "status": "connected",
    "message": "Bifrost configured for groq/openai/gpt-oss-20b via http://localhost:8080/v1"
  },
  "demoModeAvailable": true
}
```

You can also click the **"API Status"** pill in the frontend header to inspect connectivity and configuration.

---

## 📊 Benchmark Scoring Methodology

Each agent briefing is scored from **0 to 100** across 5 distinct dimensions (20 points max each):

| Metric | Weight | Formula & Definition |
| :--- | :---: | :--- |
| **Source Coverage** | 20% | `(Unique Sources Utilized / Total Available) × 20`. Evaluates what ratio of retrieved research documents were integrated. |
| **Citation Coverage** | 20% | `(Cited Claims / Total Assertions) × 20`. Rewards statements backed by direct source URLs and penalizes unsubstantiated claims. |
| **Freshness** | 20% | Evaluates recency within the 30-day pre-meeting window (3+ dated items = 20 pts). |
| **Meeting Usefulness** | 20% | Evaluates 5 structured probing questions with strategic objectives (11 pts), executive summary depth (5 pts), and limitation transparency (4 pts). |
| **Response Latency** | 20% | Measures inference round-trip time (`<2.5s`: 20 pts, `2.5-5s`: 18 pts, `5-8s`: 16 pts, `8-12s`: 13 pts, `>12s`: 7-10 pts). |

> ⚠️ **Factual Disclaimer**: Automated heuristic benchmark scores evaluate structural completeness, citation density, recency alignment, and latency. They do not claim or prove ground-truth factual accuracy.

---

## 🕶️ Blind Benchmark Mode

Want an unbiased evaluation?
* Toggle **"Blind Benchmark: ON"** in the top navigation bar.
* Agent Alpha and Agent Beta will be masked as **"Agent 1"** and **"Agent 2"**, hiding personality cues until you finish reviewing the briefings.
* Click **"Reveal"** on any briefing card to uncover which agent produced the analysis.

---

## 📁 Project Structure

```
BriefGaugeAI/
├── .env.example             # Template for environment configuration
├── .env.local               # Private local environment variables (ignored by Git)
├── .gitignore               # Git ignore rules
├── index.html               # Main HTML entrypoint with dark navy styling
├── package.json             # Scripts & simple dependencies
├── tsconfig.json            # TypeScript configuration
├── vite.config.ts           # Vite bundler & API proxy configuration
├── server/
│   ├── types.ts             # Shared data models & TypeScript interfaces
│   ├── tavily.ts            # Tavily Search API client (30-day pre-meeting window)
│   ├── bifrost.ts           # Bifrost OpenAI Chat Completions client & Agent prompts
│   ├── scoring.ts           # 100-point transparent benchmark scoring engine
│   ├── demoData.ts          # High-fidelity synthetic data for offline Demo Mode
│   └── index.ts             # Express REST API (port 3001)
└── src/
    ├── App.tsx              # Main dashboard view & benchmark orchestration
    ├── main.tsx             # React entrypoint
    ├── index.css            # Dark theme, glassmorphic styling & glowing accents
    ├── types.ts             # Frontend TypeScript types
    └── components/
        ├── Header.tsx       # Navbar, Blind Mode toggle, Health check indicator
        ├── InputForm.tsx    # Inputs for Company, Date, Purpose & Run trigger
        ├── ResearchSources.tsx # Verified search evidence from Tavily
        ├── BriefingCard.tsx # Detailed briefing card for Agent Alpha & Beta
        ├── ScoreVisualization.tsx # Progress meters for all 5 benchmark metrics
        ├── WinnerBanner.tsx # Winner announcement & token usage telemetry
        ├── ComparisonTable.tsx # Side-by-side comparison table
        ├── LimitationsSection.tsx # Methodology caveats and disclaimers
        ├── MetricDefinitionsModal.tsx # Transparent metric definitions & weights
        └── HealthModal.tsx  # API connection health modal
```

---

## 🛠️ Build for Production

To create a production build:

```powershell
npm.cmd run build
```

This compiles TypeScript and builds optimized static assets into the `dist/` directory.

To start the production server:

```powershell
npm.cmd run start
```
The Express server on port 3001 will serve the built frontend at `http://localhost:3001`.
