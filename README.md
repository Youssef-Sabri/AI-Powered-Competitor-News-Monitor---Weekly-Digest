# AI-Powered Competitor News Monitor & Weekly Digest

An automated, production-grade **n8n workflow** that monitors competitor and industry news on a recurring schedule, leverages **Google Gemini AI** to synthesize raw articles into executive-ready market intelligence, and delivers styled, dark-mode-compatible HTML digests via email. Built with native **Model Context Protocol (MCP)** integration for seamless AI agent orchestration.

🔗 **Live Public n8n Workflow Link:** [https://youssefwael.app.n8n.cloud/workflow/MxKqBeKoOp7CER34](https://youssefwael.app.n8n.cloud/workflow/MxKqBeKoOp7CER34)

---

## 📐 Workflow Architecture

```
┌──────────────────────────┐
│  Weekly Schedule Trigger │  (Every Monday 9:00 AM)
└────────────┬─────────────┘
             │
        ┌────┴────┐
        │         │
        ▼         ▼
┌──────────┐  ┌───────────────┐
│ Fetch VB │  │ Fetch TechCrunch│
│ AI Feed  │  │    AI Feed    │
└────┬─────┘  └──────┬────────┘
     │               │
     └───────┬───────┘
             ▼
      ┌──────────────┐
      │Combine Feeds │  (Merge - append)
      └──────┬───────┘
             ▼
      ┌──────────────┐
      │Parse & Filter│  (Code - CDATA, HTML entities, dedup, 7-day window)
      └──────┬───────┘
             ▼
      ┌──────────────┐
      │ Check Count  │  (If - articleCount > 0)
      └──────┬───────┘
             │ (True)
             ▼
      ┌──────────────┐      ┌───────────────────────────┐
      │  AI Agent    │◄─────│ Gemini 3.5 Flash Lite     │
      │(Intelligence)│      │  (temp: 0.2)              │
      └──────┬───────┘      └───────────────────────────┘
             ▼
      ┌──────────────┐
      │Format Digest │  (Light + Dark Mode HTML + Markdown + Plain Text)
      └──────┬───────┘
             ▼
      ┌──────────────┐
      │ Send Email   │  (Gmail OAuth2)
      └──────┬───────┘
```

---

## ✨ Key Features

1. **Automated Multi-Source Content Ingestion**:
   - Ingests official news and RSS feeds from premier industry publications.
   - Built-in request resiliency with automatic retry logic and redirect handling.

2. **Intelligent Data Parsing & Deduplication**:
   - Custom parsing engine that cleans raw XML/HTML markup and normalizes content.
   - Enforces a rolling 7-day window and automatically deduplicates syndicated articles.

3. **AI-Driven Executive Intelligence (Google Gemini)**:
   - Leverages Google Gemini configured with a Senior Analyst persona for high-accuracy, data-driven synthesis.
   - Generates 5 structured sections: Executive Summary, Key Trends with citations, Funding & M&A, Competitive Watchlist, and Actionable Recommendations.

4. **Responsive & Dark-Mode Optimized Email Delivery**:
   - Built with a universal, table-based HTML template for cross-client compatibility.
   - Fully optimized for clear text readability in both Light and Dark mode across all desktop and mobile devices.

5. **Model Context Protocol (MCP) Integration**:
   - Protocol-enabled endpoints allow AI agents to invoke, inspect, and manage workflow executions programmatically.

---

## 🛠️ Node Reference

| Node Name | Type | Purpose |
| :--- | :--- | :--- |
| **Weekly Schedule Trigger** | `n8n-nodes-base.scheduleTrigger` v1.2 | Runs automatically every Monday at 9:00 AM. |
| **Fetch VentureBeat AI Feed** | `n8n-nodes-base.httpRequest` v4.2 | Fetches VentureBeat AI RSS XML (retry 3 attempts). |
| **Fetch TechCrunch AI Feed** | `n8n-nodes-base.httpRequest` v4.2 | Fetches TechCrunch AI RSS XML (retry 3 attempts). |
| **Combine RSS Feeds** | `n8n-nodes-base.merge` v3.2 | Appends both feed streams into a single execution. |
| **Parse & Filter Articles** | `n8n-nodes-base.code` v2 | Cleans CDATA/entities, extracts pubDate, caps at 25 items, deduplicates. |
| **Check Article Count** | `n8n-nodes-base.if` v2.2 | Halts execution if 0 articles were found to prevent empty emails. |
| **AI Market Intelligence Agent**| `@n8n/n8n-nodes-langchain.agent` v3.1 | Prompts Gemini to synthesize news into strategic C-suite insights. |
| **Google Gemini 3.5 Flash Lite Model** | `@n8n/n8n-nodes-langchain.lmChatGoogleGemini` v1.1 | Attached model (`models/gemini-3.5-flash-lite`, temp `0.2`). |
| **Format Professional Digest** | `n8n-nodes-base.code` v2 | Converts Markdown to mobile-dark-mode-proof HTML email. |
| **Send Digest Email** | `n8n-nodes-base.gmail` v2.2 | Sends HTML digest to recipient email. |

---

## 🤖 Prompts & AI Architecture

### System Prompt (Persona & Role)
```text
You are a senior market intelligence analyst at a competitive technology company. You specialize in synthesizing high volumes of news into concise, actionable strategic intelligence for executive decision-makers. Your outputs are always data-driven, cite sources, and avoid speculation. You write in a professional but readable tone suitable for a C-suite audience.
```

### User Prompt (Task & Data Template)
```text
You are acting as a senior market intelligence analyst reviewing this week's competitor and industry news.

METADATA:
- Articles collected: {{ $json.articleCount }}
- Date range: {{ $json.dateRange.from }} to {{ $json.dateRange.to }}
- Sources: {{ $json.sources.join(", ") }}

ARTICLES:
{{ JSON.stringify($json.articles, null, 2) }}

YOUR TASK:
Produce a structured weekly intelligence digest with the following sections:

## 1. Executive Summary
## 2. Key Trends & Themes (3-5 items with [Title](link) citations)
## 3. Notable Funding & M&A
## 4. Competitive Watchlist
## 5. Action Items

FORMAT RULES: Clean Markdown, 400-600 words total, every claim cites a source.
```

---

## 🚀 Setup & Installation Guide

1. **Import Workflow**:
   - Open your n8n instance (v1.0+).
   - Go to **Workflows** > **Import from File**.
   - Select `AI-Powered Competitor News Monitor & Weekly Digest.json`.

2. **Configure Credentials**:
   - **Google Gemini API**: Add your Google AI Studio API key.
   - **Gmail OAuth2**: Connect your Gmail account for digest delivery.

3. **Activate**:
   - Toggle workflow status to **Active**.

