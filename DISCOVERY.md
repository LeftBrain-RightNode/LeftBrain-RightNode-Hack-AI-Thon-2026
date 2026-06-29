# Signal to Campaign 

---

## 1. Problem Framing

### The Problem
Modern enterprise marketing organizations operate under a structural latency that makes capturing fleeting market opportunities functionally impossible. High-impact external triggers—such as flash weather events, hyper-local sports outcomes, sudden transit disruptions, and regional search velocity spikes—create short-lived "micro-moments" where consumer attention and purchasing intent are significantly elevated. 

While these windows represent optimal conversion opportunities, traditional marketing workflows are too slow. Navigating the manual chain of copywriting, graphic design, audience query building, channel selection, brand compliance reviews, and multi-layered executive sign-offs typically takes 24 to 48 hours. By the time a campaign launches, the contextual window has closed, resulting in wasted ad spend and lost revenue.

### Target Users & Enterprise Context
1. **The Campaign Manager / Growth Marketer:** Responsible for real-time campaign performance, CTRs, and ROAS across quick-commerce, retail, or CPG sectors. They need a tool that eliminates manual construction latency.
2. **The Brand Administrator / Compliance Officer:** Tasked with safeguarding brand reputation. They require deterministic guardrails to ensure that automated copy meets legal, compliance, and stylistic guidelines before any campaign is shipped.

### Primary Value Hypothesis
> **We believe that integrating an automated real-time signal-sensing ingestion pipeline with an LLM-driven campaign-generation agent and a marketer-in-the-loop approval interface will reduce the campaign generation-to-approval cycle from 48 hours to under 10 minutes for Growth Marketers, resulting in significantly higher contextual ad engagement (CTRs) and capturing time-sensitive customer demand during regional micro-moments.**

---

## 2. MVP Scope

To deliver a production-grade, highly reliable solution within the intensive 3-week Hack-AI-Thon timeline, we have defined strict boundaries regarding what is **in scope** and what is explicitly **out of scope**.

```
┌────────────────────────────────────────────────────────────────────────┐
│                              SYSTEM BOUNDARY                           │
│                                                                        │
│   [IN SCOPE]                                           [OUT OF SCOPE]  │
│  ┌─────────────────────────┐                         ┌──────────────┐  │
│  │ ⚡ Ingestion Adapters   │                         │  Direct Ad   │  │
│  │ ⚡ Relevance Scoring    │                         │  Publishing  │  │
│  │ ⚡ Generative Agent     ├─────────❌ Blocked ❌──>│  APIs        │  │
│  │ ⚡ Approval Dashboard   │                         │  (No Auto)   │  │
│  │ ⚡ Schema/JSON Export   │                         └──────────────┘  │
│  └─────────────────────────┘                                           │
└────────────────────────────────────────────────────────────────────────┘
```

### In Scope
* **Signal Ingestion Adapters (Simulated/Mocked APIs):** Standardized ingestion adapters pulling real-time telemetry for localized weather patterns (precipitations, temperature shifts), search trend spikes (simulated Google Trends RSS/JSON feeds), and sports telemetry (event completions, scores).
* **Sensing & Correlation Engine:** A statistical execution layer that groups concurrent regional signals, calculates statistical deviations against rolling 30-day baselines, and generates a unified **Relevance Score ($R_c$)** based on a multi-factor mathematical scoring model.
* **AI-Powered Generative Loop (Agentic):** A structured generative pipeline that translates signal metadata into a complete campaign brief consisting of context-aware headlines and CTAs, structured target audience segments, and urgency-based channel recommendations (e.g., Push, SMS, or Paid Social).
* **Marketer-in-the-Loop Approval Dashboard:** A responsive Next.js frontend allowing growth marketers to review active campaign opportunities, inline-edit generated text, adjust audience parameters, and sign off.
* **Structured Export Pipeline:** A one-click export mechanism that outputs the finalized, signed-off campaign brief as a schema-compliant JSON file, ready for direct ingestion by downstream marketing platforms (e.g., Salesforce Marketing Cloud, Adobe Experience Manager), or dispatches a webhook alert to Slack/Teams.

### Out of Scope
* **Direct Auto-Publishing API Integration:** Integrating live write-back publishing APIs (e.g., Meta Graph API, Google Ads API) to buy programmatic media or publish live ads autonomously is explicitly **out of scope**. Retaining a "human-in-the-loop" is mandatory to eliminate financial exposure and brand safety risks.
* **Custom Trend Predictive Models:** Training bespoke neural nets (e.g., LSTM, Transformers) to predict trends is **out of scope**; the MVP will utilize deterministic statistical modeling (Z-score deviation) on historical API baselines.
* **Multi-Language Ad Generation:** The generation pipeline will strictly support English copy for the initial MVP.

### Future Roadmap (R2 / R3)
* **Direct CMS & Ad-Tech Connectors:** Developing secure, authenticated OAuth integrations with Adobe Experience Manager (AEM) and HubSpot to push drafts directly into live workflows.
* **Adaptive Reinforcement Learning:** Tracking click-through rates (CTRs) of approved campaigns to continuously fine-tune the generative agent’s prompt weights, optimizing copy variations based on historical performance.

---

## 3. MVP Backlog

All user stories are structured to ensure testability and mapped to clear MoSCoW priorities.

### [STORY-01] Weather Signal Ingestion (Must-Have)
* **User Story:** As a Sensing Engine, I want to ingest localized weather data telemetry, so that I can capture sudden environmental changes in targeted regions.
* **Acceptance Criteria:**
  1. The system successfully parses standard OpenWeatherMap-style JSON payloads.
  2. A local trigger event is dispatched to the event bus when rainfall intensity shifts by $\ge 0.2$ inches/hour or temperature shifts by $\ge 15^\circ\text{F}$ within a 1-hour window.
* **Test Approach:** Run unit tests passing mock weather payloads representing a sudden heavy downpour in Phoenix; assert that a normalized telemetry event is generated with correct timestamps and geographical coordinates.

### [STORY-02] Search Trend Correlation (Must-Have)
* **User Story:** As a Sensing Engine, I want to ingest search query velocity data, so that I can detect spikes in topic-specific consumer intent.
* **Acceptance Criteria:**
  1. The engine ingests weekly baseline keyword data and real-time search velocity feeds.
  2. An alert triggers when real-time search volume rises more than 3 standard deviations ($3\sigma$) above the rolling baseline.
* **Test Approach:** Pass a historical baseline mock database to the engine and stream a simulated $5\sigma$ surge for "same-day delivery"; verify that a search trend event is dispatched.

### [STORY-03] Relevance Scoring Engine (Must-Have)
* **User Story:** As a Growth Marketer, I want the system to calculate a Relevance Score for incoming signals against our specific brand profile, so that I am not flooded with irrelevant alerts.
* **Acceptance Criteria:**
  1. The system evaluates active signal clusters against a brand configuration JSON (containing brand keywords, locations, and category weights).
  2. The calculated score ($R_c$) is normalized between $0.0$ and $1.0$.
  3. Only clusters with a score $\ge 0.75$ trigger the downstream Generative Copy Agent.
* **Test Approach:** Execute automated integration tests using a "Grocery Delivery" brand profile. Verify that a (Heavy Rain + Traffic Spike + "grocery delivery" search) cluster yields $R_c \ge 0.80$, while a (Blizzard + "snowboards" search) cluster yields $R_c \le 0.30$.

### [STORY-04] Campaign Copy Generation (Must-Have)
* **User Story:** As a Growth Marketer, I want the AI engine to write situational headline copy and a highly relevant Call to Action (CTA), so that I don't have to draft messaging from scratch.
* **Acceptance Criteria:**
  1. The agent produces three distinct headline variations and one recommended CTA.
  2. Generated headlines must not exceed 100 characters and must dynamically incorporate the localized trigger context (e.g., "Phoenix rain").
* **Test Approach:** Execute LLM integration assertions validating that outputs contain non-empty strings, a clear "CTA" field, and that all text variants conform to character-length boundaries.

### [STORY-05] Audience & Channel Recommendation (Must-Have)
* **User Story:** As a Growth Marketer, I want the AI to propose a targeted audience segment and channel mix, so that I can target the right users in the right channels.
* **Acceptance Criteria:**
  1. The output recommendation includes a clear channel mix (Push, SMS, Paid Social) weighted by signal urgency.
  2. The audience recommendation outputs a structured, human-readable segment definition (e.g., "Active grocery users in Phoenix, Zone A/B, active in last 14 days").
* **Test Approach:** Assert that the generated output JSON conforms strictly to the target Pydantic schema, containing non-empty arrays for `channels` and `audience_definition`.

### [STORY-06] Marketer Review Dashboard (Must-Have)
* **User Story:** As a Growth Marketer, I want to view active campaign generation cards in a clean user interface, so that I can easily browse high-relevance opportunities.
* **Acceptance Criteria:**
  1. The UI displays pending campaigns sorted by Relevance Score.
  2. Each card visualizes the underlying signal data (e.g., weather intensity, search volume), generation timestamp, and workflow status (`Pending`, `Approved`, `Exported`).
* **Test Approach:** Run Playwright end-to-end UI tests to verify that mock campaign cards render correctly in grid and detail views.

### [STORY-07] Marketer Inline Editing (Must-Have)
* **User Story:** As a Growth Marketer, I want to edit the AI-generated copy, CTA, and target audience on the dashboard, so that I can ensure alignment with real-time conditions.
* **Acceptance Criteria:**
  1. Text elements inside the campaign review card are editable via responsive text inputs.
  2. Saving edits updates the component state and database record without refreshing the page.
* **Test Approach:** Simulate user typing events in the headline input field, trigger the "Save" action, and verify that the updated headline is written to the database.

### [STORY-08] Structured Kit Export (Must-Have)
* **User Story:** As a Growth Marketer, I want to export the finalized campaign brief as a structured JSON file, so that I can easily import it into downstream marketing platforms.
* **Acceptance Criteria:**
  1. Clicking "Approve & Export" generates a schema-compliant JSON payload containing all edited copy, audience, and channel metadata.
  2. The system initiates an immediate local file download of the JSON file.
* **Test Approach:** Validate the exported JSON output against the master marketing-integration schema using a JSONSchema validator.

### [STORY-09] Real-Time Alert Dispatch (Should-Have)
* **User Story:** As a Growth Marketer, I want to receive a Slack/Teams notification when a high-relevance trigger is detected, so that I can react instantly without sitting in front of the dashboard.
* **Acceptance Criteria:**
  1. Triggering an event with $R_c \ge 0.75$ posts a rich, formatted card to a configured Slack Webhook containing the relevance score, triggers, and a direct hyperlink to the dashboard card.
* **Test Approach:** Mock the webhook responder, fire a high-relevance trigger, and assert that the outbound POST payload contains valid Slack Block Kit formatting.

### [STORY-10] Automated Brand Alignment Guardrails (Should-Have)
* **User Story:** As a Brand Administrator, I want the system to run automated brand alignment checks on the generated copy, so that we minimize policy violations and protect brand equity.
* **Acceptance Criteria:**
  1. The generation pipeline executes a secondary LLM checker to scan copy against brand guidelines (banned phrases, compliance terms, competitor mentions).
  2. Copy that fails the alignment check is automatically flagged with a compliance warning in the UI.
* **Test Approach:** Pass mock campaign copy containing banned keywords (e.g., competitor names) and verify that the compliance engine successfully flags the content as "Failed Brand Safety".

---

## 4. High-Level Design

### Architecture Diagram
The architecture illustrates a unidirectional data flow from real-time external telemetry to the final marketer approval interface.

```mermaid
graph TD
    %% Telemetry Sources
    subgraph Sensing Layer (Ingestion & Normalization)
        A1[NOAA / OpenWeather API] -->|Local Weather Shifts| B1(Weather Ingestion Adapter)
        A2[Google Trends API] -->|Search Volume Velocity| B2(Search Ingestion Adapter)
        A3[Sportradar API] -->|Live Sports Outcomes| B3(Sports Ingestion Adapter)
        
        B1 -->|Normalize to JSON| C[FastAPI Event Ingestion Bus]
        B2 -->|Normalize to JSON| C
        B3 -->|Normalize to JSON| C
    end

    %% Intelligence Engine
    subgraph Central Intelligence Engine (Correlation & Scoring)
        C --> D[Signal Clustering & Correlation Service]
        D --> E[Relevance Scoring Engine]
        E -->|Calculate Rc Score| F{Rc >= 0.75?}
        F -->|No| G[Log to PostgreSQL Baseline]
        F -->|Yes| H[Dispatched State Trigger]
    end

    %% Responding Layer
    subgraph Responding Layer (Generative AI Pipeline)
        H --> I[LangGraph Multi-Agent System]
        I -->|Prompt Engineering| I1[Copy Specialist Agent]
        I -->|Prompt Engineering| I2[Audience Selector Agent]
        I1 --> J[Structured Output Aggregator]
        I2 --> J
        J --> K[Brand Safety & Compliance Agent]
    end

    %% User Interaction & Downstream
    subgraph Presentation & Integration Layer
        K -->|Validated Campaign Kit| L[Supabase Database]
        L --> M[Next.js Marketer Dashboard]
        M -->|Inline Edits & Manual Sign-off| N{Marketer Approval}
        N -->|Approve & Export| O[Structured JSON Download]
        N -->|Webhook| P[Slack Alert Dispatch]
    end

    classDef sense fill:#f9f,stroke:#333,stroke-width:1px;
    classDef engine fill:#bbf,stroke:#333,stroke-width:1px;
    classDef respond fill:#bfb,stroke:#333,stroke-width:1px;
    classDef ui fill:#ffb,stroke:#333,stroke-width:1px;

    class B1,B2,B3,C sense;
    class D,E,F,G,H engine;
    class I,I1,I2,J,K respond;
    class L,M,N,O,P ui;
```

---

### Deep Dive: The Relevance Scoring Engine

To prevent "alert fatigue" and ensure that marketing budgets are directed only toward highly contextual opportunities, EventPulse uses a multi-factor mathematical scoring model. The system calculates a **Relevance Score ($R_c$)** for each incoming cluster of signals.

The core algorithm is defined as:

$$R_c = \left( \sum_{i=1}^{n} (w_i \cdot S_i) \right) \times A_b \times R_a \times D_t$$

Where:
* **$S_i$ (Signal Intensity Factor):** The normalized strength of signal $i$ (ranging from $0.0$ to $1.0$).
* **$w_i$ (Signal Weight):** The statistical weight assigned to signal type $i$ (such that $\sum w_i = 1.0$).
* **$A_b$ (Brand Affinity Coefficient):** A brand-specific relevance match ($0.0$ to $1.0$) indicating how aligned the event category is to the brand's core offerings.
* **$R_a$ (Audience Impact Factor):** A density scale ($0.0$ to $1.0$) representing the proportion or critical mass of targetable active users physically or behaviorally impacted by the event.
* **$D_t$ (Temporal Decay Factor):** A time-decay multiplier ($0.0 \le D_t \le 1.0$) that discounts the signal's value as time passes from initial detection.

#### 1. Signal Intensity Factor ($S_i$)
Signals are rarely binary; they represent fluctuations from a baseline. EventPulse calculates $S_i$ using a rolling Z-score over a 30-day historical baseline:
$$Z_i = \frac{x_i - \mu_i}{\sigma_i}$$
Where $x_i$ is the current real-time metric, $\mu_i$ is the historical mean, and $\sigma_i$ is the standard deviation. This Z-score is then normalized to a $0.0$–$1.0$ range using a sigmoid function:
$$S_i = \frac{1}{1 + e^{-k(Z_i - \theta)}}$$
*(where $k = 1.5$ is the scaling slope and $\theta = 2.0$ is the activation offset)*.

#### 2. Temporal Decay Factor ($D_t$)
Micro-moments decay quickly. The decay factor follows an exponential half-life decay curve:
$$D_t = e^{-\lambda \cdot t}$$
Where $\lambda$ represents the decay constant (higher for viral social trends, lower for regional weather patterns) and $t$ is the elapsed time in minutes since the trigger was detected.

---

### Tech Stack Decisions

| Component | Choice | One-Sentence Rationale |
| :--- | :--- | :--- |
| **Backend Framework** | FastAPI (Python 3.11) | Provides native asynchronous handling, outstanding speed, and automatic Swagger documentation, optimal for real-time telemetry ingestion. |
| **Agentic Framework** | LangGraph | Enables deterministic state management over multi-agent loops, ensuring reliable execution sequences from generation to brand compliance checks. |
| **Frontend Framework** | Next.js & Tailwind CSS | Facilitates lightning-fast rendering of interactive dashboard views with real-time state synchronization via client-side caching. |
| **Database** | PostgreSQL (Supabase) | Offers relational schema enforcement for audit logs, brand voice rules, and active campaigns, combined with instantaneous cloud provisioning. |
| **Model APIs** | Claude 3.5 Sonnet / GPT-4o | Balances advanced reasoning capabilities, structured brand safety logic, and native JSON mode schema compliance. |
| **Runtime & Deploy** | Docker / AWS ECS | Ensures completely identical local development and cloud staging environments via standardized container builds. |

### Architectural Trade-offs
1. **Rule-Based Pre-Filtering vs. Multi-Agent Semantic Scoring:** For the ingestion pipeline, we employ deterministic rule-based pre-filtering (e.g., geographic intersection and statistical Z-score thresholds) before triggering LLM-based processing. While a fully semantic model would support open-ended reasoning, pre-filtering drastically reduces API token costs and ensures processing latency remains well under our 10-second threshold.
2. **Real-time WebSockets vs. Optimized Database Polling:** To sync state between the backend and Next.js, we chose optimized SWR (Stale-While-Revalidate) REST polling. This maintains a lightweight server footprint, is incredibly robust against intermittent connectivity, and easily fits within our latency budget.

---

## 5. AI Workflow Log

To demonstrate the rigorous, iterative evolution of our AI workflow, this section logs five actual development stages of our generative prompts and schemas, with detailed commentary on revisions and enhancements.

### Iteration 1: Initial Simple Text Prompt (Ad Copy Generation)
* **Goal:** Test basic LLM capacity to draft headlines based on regional weather alerts.
* **Prompt:**
```text
Write a marketing headline for a grocery delivery brand. 
The current weather in Phoenix is heavy rain. Keep it catchy.
```
* **Output:**
```text
Don't let the rain wash away your appetite! Phoenix, stay warm inside and 
order your groceries with us today! Quick delivery guaranteed.
```
* **Why it was revised:** The output was conversational but far too long, did not follow any structured JSON format required by our Next.js UI, and lacked audience segmentation or channel recommendations.

---

### Iteration 2: Enforcing Structured JSON Formatting
* **Goal:** Direct the model to output a structured JSON schema containing copy and metadata.
* **Prompt:**
```text
Generate a campaign based on the following trigger.
Trigger: Rain in Phoenix, Arizona.
Output a JSON object with the following fields:
- headline (string under 100 chars)
- cta (string under 20 chars)
- target_audience (string description)
- recommended_channel (string)
```
* **Output:**
```json
{
  "headline": "Rainy Day in Phoenix? We've Got Your Groceries Covered!",
  "cta": "Order Now",
  "target_audience": "People who are currently at home during the rain in Phoenix.",
  "recommended_channel": "Push Notification"
}
```
* **Why it was revised:** While the structure is better, the target audience parameter is too abstract. It cannot be mapped directly to transactional or database attributes in a real Customer Data Platform (CDP) or Data Management Platform (DMP).

---

### Iteration 3: Contextual Audience Segments & Channel Mix Constraints
* **Goal:** Force the model to generate specific, database-actionable audience segments and an urgency-weighted channel mix.
* **Prompt:**
```text
You are a senior marketing automation assistant. Generate a campaign launch kit JSON.
Trigger Event: Rainfall > 0.5 inches/hour, Phoenix.
Rules:
1. Target audience must be described in actionable terms of user database attributes (e.g., recency, activity state, location zones).
2. Recommend a list of channels ranked by immediacy.
3. Keep the headline length strictly under 80 characters.

JSON Schema:
{
  "headline": "str",
  "cta": "str",
  "audience": {
    "geographic_reach": "str",
    "behavioral_criteria": "str"
  },
  "channel_mix": ["str"]
}
```
* **Output:**
```json
{
  "headline": "Rainy day in Phoenix? Groceries at your door in 30 mins!",
  "cta": "Order Now",
  "audience": {
    "geographic_reach": "Phoenix Metro, Zones A and B",
    "behavioral_criteria": "Users active in-app within last 14 days"
  },
  "channel_mix": ["Mobile Push Notification", "Paid Social (Meta)"]
}
```
* **Why it was revised:** This text output is highly functional. However, in our Python backend, parsing raw string outputs can lead to runtime crashes if the LLM output deviates slightly in syntax. We must enforce complete type safety using a strict Pydantic model.

---

### Iteration 4: Standardizing Structured Outputs via Pydantic
* **Goal:** Implement programmatic schema validation utilizing Pydantic and JSON schema-mode APIs.
* **Prompt:**
```python
from pydantic import BaseModel, Field
from typing import List

class AudienceSegment(BaseModel):
    geographic_reach: str = Field(description="Actionable target zones or ZIP ranges.")
    behavioral_criteria: str = Field(description="App interaction recency and user segments.")

class CampaignLaunchKit(BaseModel):
    headline: str = Field(description="Situation-appropriate copy under 80 chars.")
    cta: str = Field(description="Urgency-driven Call to Action under 20 chars.")
    audience: AudienceSegment
    channel_mix: List[str] = Field(description="Urgency-based delivery channels.")

# Prompt passed to Claude 3.5 Sonnet:
"""
Generate a CampaignLaunchKit matching the specified Pydantic schema for the following event:
Trigger: Rain alert in Phoenix + traffic slowdown + rising search trend 'same-day grocery delivery'.
Ensure compliance with all types, field descriptions, and length boundaries.
"""
```
* **Output:**
```json
{
  "headline": "Rainy Day? Groceries at your door in 30 mins",
  "cta": "Order Now",
  "audience": {
    "geographic_reach": "Phoenix Zone A/B",
    "behavioral_criteria": "Users active in last 14 days"
  },
  "channel_mix": ["Push Notification", "Paid Social"]
}
```
* **Why it was revised:** The schema generation is flawless and robustly parsed. However, to operate safely in an enterprise environment, we must add an automated brand safety check. This prevents the generative agent from outputting inappropriate phrases or competitor terms.

---

### Iteration 5: Multi-Agent Collaboration (Brand Safety & Compliance Check)
* **Goal:** Formulate a secondary compliance validation step within a LangGraph state loop, designed to evaluate the generated output before showing it to the marketer.
* **Prompt:**
```text
You are a Brand Compliance and Legal Evaluator Agent.
Review the following generated campaign assets against our Brand Guidelines:
- Banned Competitor Terms: "Walmart", "Instacart", "UberEats", "Amazon"
- Safety Guidelines: Do not refer to extreme weather as "fun" or make light of major disasters.
- Guidelines: Headline must be under 80 characters.

Evaluate the following campaign kit:
{
  "headline": "Rainy Day? Groceries at your door in 30 mins",
  "cta": "Order Now",
  "audience": {
    "geographic_reach": "Phoenix Zone A/B",
    "behavioral_criteria": "Users active in last 14 days"
  },
  "channel_mix": ["Push Notification", "Paid Social"]
}

Output JSON schema:
{
  "status": "PASS" | "FAIL",
  "reason": "str (explain if FAIL)",
  "corrected_headline": "str (optional, if modification required)"
}
```
* **Output:**
```json
{
  "status": "PASS",
  "reason": "The headline is 44 characters, contains no competitor terms, references localized rain in a helpful context, and does not violate any brand guidelines.",
  "corrected_headline": null
}
```
* **Why it was chosen:** This final multi-agent loop provides an automated legal safety net, ensuring high quality and protecting client brand equity without delaying deployment speeds.

---

## 6. Risk Notes

### Sensitive Data & PII Management
Our solution will utilize strictly synthetic and anonymized user data. No real client consumer records, credit card numbers, or PII will ever enter our system, nor will they be sent to public LLM endpoints. Location data will be processed strictly at the aggregate zip-code, zone, or city level.

### Secrets and Credentials Protection
All API credentials (including OpenAI, Anthropic, Supabase, and Weather platform tokens) will be stored in localized, git-ignored `.env` files and referenced inside the code via environment variables (`os.getenv(...)`). We will deploy pre-commit hooks (like gitleaks) to ensure no keys are committed to the public or internal GitHub enterprise repository.

### Agentic Boundaries and Human-in-the-Loop Safeguards
To guarantee complete brand safety, the system operates on a strict Human-in-the-Loop model. While the agentic workflow is fully autonomous in sensing signals and generating templates, it is completely blocked from directly deploying or spending marketing funds on live media channels. A human marketer must review, optionally edit, and click a manual "Approve & Export" action to release the campaign payload. This mitigates prompt injection risks and protects the brand from rogue algorithmic spends.
