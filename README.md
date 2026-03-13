# 🤖 AI-Powered Customer Support Sentiment Analysis
### Snowflake Cortex AI · Power BI · SQL · Star Schema

---

## Project Overview

A SaaS company serving **100 construction companies across Canada** receives **1,000 support tickets per year** across 5 products. Leadership had no visibility into which products were failing, which customers were at risk of churning, or how many workers on job sites were being impacted.

This project builds a **fully automated AI analytics pipeline** — raw customer complaint text goes in, business-ready insights come out — with zero manual labelling.

---

## Pipeline Architecture

```
Claude AI          Snowflake            Cortex AI              Power BI
──────────         ─────────            ─────────              ────────
Generate     →     Load into      →     Enrich with      →     3-Page
1,000 tickets      star schema          4 AI functions         Dashboard
                   (SQL scripts)        (auto-labelling)       + Insights
```

---

## Tech Stack

| Tool | Purpose |
|---|---|
| **Claude AI** | Generated 1,000 realistic support ticket texts |
| **Snowflake** | Data warehouse — star schema, SQL scripts |
| **Snowflake Cortex AI** | 4 AI enrichment functions (see below) |
| **Power BI** | 3 analytical dashboards + insights page |
| **SQL / DAX** | Data modelling, transformations, measures |

---

## Snowflake Cortex AI Functions

All 4 AI columns were generated automatically — **no manual reading or labelling of any ticket.**

| Function | Column Created | What It Does |
|---|---|---|
| `CORTEX.SENTIMENT()` | `SENTIMENT_SCORE` | Scores ticket tone from −1.0 to +1.0 |
| `CORTEX.CLASSIFY_TEXT()` | `TICKET_CATEGORY` | Assigns one of 6 business categories automatically |
| `CORTEX.EXTRACT_ANSWER()` | `AFFECTED_WORKER_NUMBER` | Pulls worker count from unstructured complaint text |
| `CORTEX.COMPLETE()` | `TICKET_SUMMARY` | Generates a one-sentence summary using mistral-7b LLM |

> 📖 Explore more Cortex AI functions: https://docs.snowflake.com/en/sql-reference/functions/ai_classify

---

## Data Model

```
DIM_CUSTOMER (100)          DIM_PRODUCT (5)
      │                           │
      └──────── FACT_SUPPORT_TICKET (1,000) ────────┘
                      │
              🤖 AI Enrichment Columns
              ├── SENTIMENT_SCORE
              ├── SENTIMENT_LABEL
              ├── TICKET_CATEGORY
              ├── AFFECTED_WORKER_NUMBER
              └── TICKET_SUMMARY
```

Full schema details in [`notes/data_model.md`](notes/data_model.md)

---

## SQL Files — Run in Order

| File | Purpose |
|---|---|
| `sql/01_create_tables.sql` | Creates DIM and FACT tables |
| `sql/02_load_data.sql` | Inserts DIM data (100 customers, 5 products) |
| `sql/fact_support_ticket_1000.sql` | Inserts 1,000 fact rows |
| `sql/03_cortex_ai_enrichment.sql` | Runs all 4 Cortex AI functions |
| `sql/04_views_and_analysis.sql` | Creates VW_TICKET_ANALYSIS + analytical queries |

---

## Power BI Dashboard — 3 Pages

### Page 1 — Executive Overview
- 5 KPI cards: Total Tickets, Avg Sentiment Score, Negative %, Avg Workers Affected, Avg Resolution Time
- Sentiment Distribution by Product (stacked bar)
- Ticket Volume by Category (treemap)
- Monthly Volume vs Avg Sentiment (dual-axis line)
- Overall Sentiment Split (donut)

### Page 2 — Product Deep Dive
- Category vs Sentiment Heatmap (matrix with conditional formatting)
- Ticket Volume by Category (bar)
- Lowest Sentiment Tickets table (sorted by AI score)

### Page 3 — Customer Intelligence
- Avg Sentiment by Province (bar — color rule: green above 0, red below)
- Avg Workers Affected by Product (bar — powered by EXTRACT_ANSWER)
- At-Risk Customer Watchlist (table — negative + unresolved tickets)

---

## Key Insights Found by AI

🔴 **92% of Bug Reports are Negative** — spotted automatically across 1,000 tickets. Would be resource-costly to find manually.

🔴 **The 10 worst-scoring tickets share one crisis** — payroll failing mid-cycle, leaving 29–50 field workers without direct deposit. Surfaced by AI summaries.

🔴 **FieldForce averages 24 affected workers per ticket** — this number did not exist in the data. EXTRACT_ANSWER() pulled it from plain complaint text.

🔴 **Alberta and BC are the most dissatisfied provinces** — home to the majority of Platinum and Gold tier accounts.



