# BriefGauge AI - End-to-End Audit & Remediation Report

**Date**: November 14, 2026  
**Auditor**: Senior Full-Stack Engineer, AI Evaluation Researcher, QA & Security Auditor  
**Overall Audit Verdict**: **PASSED** (All 14 automated test suites passed; 0 compilation errors; clean production build)

---

## 1. Executive Summary

A comprehensive, end-to-end audit was conducted on **BriefGauge AI**, a full-stack pre-meeting research benchmarking system comparing two AI agent personas (**Agent Alpha: Coverage Researcher** and **Agent Beta: Critical Analyst**) utilizing Tavily Search API research and the Bifrost OpenAI-compatible Chat Completions proxy.

Critical bugs across date filtering, question repetition/hallucination, identity leaks in Blind Benchmark mode, unverified citation scoring, and health checks were identified, root-caused, and completely resolved without breaking architectural continuity or deleting working functionality.

---

## 2. Problems Discovered & Root Causes

### 1. September 20 Article Ingestion for November Meetings (Tavily Research)
- **Problem**: Search results for a November meeting included an article dated September 20 (older than the mandated 30-day pre-meeting window).
- **Root Cause**: In `server/tavily.ts`, `rawResults.slice(0, 6)` was being returned directly without applying any temporal boundaries or comparing `published_date` against the pre-meeting 30-day window (`[windowEnd - 30 days, windowEnd]`).
- **Fix**: Implemented `calculateResearchWindow()` and `processRawTavilyResults()`.
  - Defined explicit window: `windowEnd = min(meetingDate, today)` and `windowStart = windowEnd - 30 days`.
  - Date parser validates ISO, YYYY-MM-DD, and RFC formats.
  - Strictly rejects any publication where `pubDate < windowStart` or `pubDate > windowEnd`.
  - Clearly tags sources as `dateStatus: 'verified_recent'` or `dateStatus: 'undated'`.
  - Undated sources do not receive false freshness credit.
  - Added URL normalization and deduplication by URL and normalized title.

### 2. Repetitive / Near-Duplicate Meeting Questions
- **Problem**: Both agents generated generic, templated questions, and when fewer than 5 questions were returned, the backend hardcoded fallback questions by merely appending an index number (`Question ${idx}: How do recent market shifts...`).
- **Root Cause**: `server/bifrost.ts` lacked duplicate detection and question validation, and had a loop injecting hardcoded fallback templates.
- **Fix**:
  - Implemented `calculateQuestionSimilarity()` (Jaccard token similarity with suffix stemming) and `areQuestionsNearDuplicates()`.
  - Created `validateMeetingQuestions()` enforcing exactly 5 distinct questions with length >= 15 chars, ending in `?`, with substantive context and strategic objectives.
  - Implemented automatic Bifrost question regeneration (up to 2 attempts) when duplicates are caught.
  - Injected Alpha's generated questions into Beta's prompt to actively prevent cross-agent question duplication.
  - Removed all hardcoded fallback question strings. If duplicates persist after 2 retries, the system transparently flags `questionsValidation: { isValid: false, warnings }` and penalizes the benchmark score instead of fabricating fake successes.

### 3. Blind Benchmark Identity Leaks
- **Problem**: When Blind Benchmark was set to ON, the interface leaked the identities of Agent Alpha and Agent Beta in multiple locations:
  1. `WinnerBanner.tsx` displayed `"Winner: Agent 1 (Alpha)"` and `"Alpha vs Beta"`.
  2. `winner.summary` and `winner.keyAdvantages` hardcoded the strings `"Agent Alpha"` and `"Agent Beta"`.
  3. `ScoreVisualization.tsx` legend displayed `(Alpha)` and `(Beta)`.
  4. `ComparisonTable.tsx` displayed the archetype titles and leaked candidate identities.
- **Root Cause**: The scoring engine generated only one winner summary containing literal agent names, and frontend components included hardcoded parentheses.
- **Fix**:
  - `server/scoring.ts` now creates two distinct winner summaries: `winner.summary` (for live mode) and `winner.blindSummary` (strictly sanitized to `"Agent 1"` and `"Agent 2"`).
  - Sanitized `blindKeyAdvantages` in `BenchmarkWinner`.
  - Updated `WinnerBanner.tsx`, `ScoreVisualization.tsx`, `ComparisonTable.tsx`, and `BriefingCard.tsx` to completely hide all mentions of Alpha, Beta, Coverage Researcher, and Critical Analyst while `blindMode && !revealed`.
  - Added a global, synchronized **"Reveal Agent Identities"** toggle button allowing the user to unmask identities only when ready.

### 4. Unverified Citation Scoring Flaw
- **Problem**: Agents could cite fabricated or non-existent URLs and receive full Citation Coverage points as long as `sourceUrl.length > 5`.
- **Root Cause**: `server/scoring.ts` did not cross-reference citation URLs against the actual URLs retrieved by Tavily.
- **Fix**:
  - Implemented `validateBriefingCitations()` which checks every claim against the normalized `Set` of retrieved Tavily URLs.
  - Valid citations are tagged `citationValid: true`.
  - Hallucinated or unresolvable URLs are tagged `citationValid: false` and penalized in the Citation Coverage metric.

### 5. Freshness Metric Baseline Inflation
- **Problem**: An agent with 0 dated articles in the 30-day window was awarded a default baseline of 14/20 points for Freshness.
- **Root Cause**: Fixed fallback score in `scoring.ts`.
- **Fix**: Freshness now strictly starts at 4 points and awards scaled points (9, 13, 17, 20) only for verified dated sources occurring within `[windowStart, windowEnd]`. Undated sources receive 0 freshness bonus.

### 6. Health Check Inaccuracy
- **Problem**: `/api/health` returned `"connected"` simply because environment variables existed as non-empty strings, without testing actual service reachability.
- **Root Cause**: No active connection probe was performed.
- **Fix**:
  - Implemented safe, minimal GET probe to `${bifrostBaseUrl}/models` with `x-bf-vk` authentication and a 2.5s abort timeout.
  - Distinguishes between `'connected'`, `'offline'`, `'auth_failed'`, and `'unconfigured'`.
  - Validates `TAVILY_API_KEY` format without leaking secrets.

### 7. Input Validation & Silent Fallback Risk
- **Problem**: Backend did not validate date string formatting, and if credentials were missing, live mode silently switched to demo mode without notifying the user.
- **Root Cause**: Missing regex validation and automatic fallback logic in `server/index.ts`.
- **Fix**:
  - Added strict input validation: `companyName` (2-100 chars), `meetingDate` (`/^\d{4}-\d{2}-\d{2}$/`), and `meetingPurpose` (3-300 chars).
  - Live mode returns HTTP 400 Bad Request with an informative explanation if keys are missing; synthetic data is only loaded when `forceDemo: true` is explicitly requested.

---

## 3. Files Modified

| File | Purpose of Modifications |
| :--- | :--- |
| `server/types.ts` | Added `dateStatus`, `isDated`, `publishedDateIso`, `questionsValidation`, `ResearchWindowInfo`, `blindSummary`, and updated health check types. |
| `src/types.ts` | Synchronized frontend types with backend types. |
| `server/tavily.ts` | Implemented 30-day window computation, date parsing, URL normalization, deduplication, and strict out-of-window filtering. |
| `server/bifrost.ts` | Enhanced persona prompts, implemented Jaccard semantic question similarity, 5-question validation, Bifrost regeneration, and cross-agent duplication prevention. |
| `server/scoring.ts` | Ground-truth citation cross-referencing, strict 0-100 bounded scoring, honest freshness and usefulness grading, and blind summary generation. |
| `server/demoData.ts` | Updated synthetic benchmark to use realistic dated records, distinct questions, and verified metric structures. |
| `server/index.ts` | Input validation, reachability-based `/api/health` probes, no silent fallback in live mode, and error handling. |
| `src/components/WinnerBanner.tsx` | Complete Blind Mode identity protection (no leaked names in title, score delta, telemetry, or advantages) + global Reveal button. |
| `src/components/ScoreVisualization.tsx` | Sanitized candidate labels and legends in Blind Mode. |
| `src/components/ComparisonTable.tsx` | Sanitized evaluation matrix dimensions and headers in Blind Mode. |
| `src/components/BriefingCard.tsx` | Masked persona in Blind Mode; visual indicators for verified vs unverified citations; question duplicate warnings. |
| `src/components/ResearchSources.tsx` | Display of 30-day research window boundaries, dated vs undated badges. |
| `src/components/Header.tsx` | Added Blind Mode state indicator and verified live gateway status. |
| `src/App.tsx` | Synchronized `revealedIdentities` state, debounced benchmark submission, demo mode watermark banner. |
| `package.json` | Added `"test": "tsx --test test/**/*.test.ts"` script. |
| `test/benchmark.test.ts` | 14 automated unit and integration tests covering all requirements. |
| `README.md` | Updated documentation with Windows PowerShell run commands, architecture, and testing instructions. |

---

## 4. Test Execution & Verification

### Automated Test Suite (`npm.cmd test`)
All 14 test suites executed using Node.js native test runner via `tsx`:

```
▶ BriefGauge AI - Comprehensive Automated Test Suite
  ✔ 1. Tavily result parsing extracts titles, snippets, domains and domains correctly
  ✔ 2. Publication date filtering strictly rejects out-of-window sources (e.g. Sept 20 for Nov 20 meeting)
  ✔ 3. Duplicate source detection removes identical URLs and tracking query variants
  ✔ 4. Bifrost configuration handles headers, base URL and virtual key
  ✔ 5. Agent response parsing extracts valid JSON even with markdown fences or preambles
  ✔ 6. Five-question validation passes for 5 distinct, well-formed questions
  ✔ 7. Duplicate question detection identifies near-duplicate and template questions
  ✔ 8. Citation validation verifies authentic source URLs and flags hallucinated links
  ✔ 9. Score calculation bounds scores strictly within [0, 100] and rewards lower latency
  ✔ 10. Blind benchmark winner summary masks identities as Agent 1 and Agent 2 without leaking Alpha/Beta
  ✔ 11. Blind benchmark provides both masked and unmasked representations
  ✔ 12. Demo mode result explicitly tags isDemoMode as true with synthetic disclaimer
  ✔ 13. URL validator accepts valid http/https URLs and rejects malformed strings
  ✔ 14. Validates date parsing and rejects impossible dates
✔ BriefGauge AI - Comprehensive Automated Test Suite (282.5752ms)

ℹ tests 14
ℹ suites 1
ℹ pass 14
ℹ fail 0
```

### TypeScript Static Analysis (`npm.cmd run typecheck`)
- Command: `tsc --noEmit`
- Result: **0 errors** across both `server/` and `src/`.

### Production Frontend Build (`npm.cmd run build`)
- Command: `tsc && vite build`
- Result: **Success**
  - `dist/index.html` (1.79 kB)
  - `dist/assets/index-4py_XTco.css` (1.72 kB)
  - `dist/assets/index-ClzECf-t.js` (303.85 kB)

---

## 5. Remaining Considerations & Next Steps

1. **Local Bifrost Proxy**:
   - To execute live AI benchmarks, ensure your Bifrost gateway container or service is running at `http://localhost:8080/v1` with your virtual key.
   - If Bifrost is offline, BriefGauge AI will clearly notify you and allow seamless **1-Click Demo Mode** execution with synthetic data.
2. **Start the Application**:
   Run in Windows PowerShell:
   ```powershell
   npm.cmd run dev
   ```
   Access the dashboard at `http://localhost:5173`.
