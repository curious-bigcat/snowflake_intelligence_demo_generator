# ❄️ Snowflake Intelligence Demo Generator

Snowflake | Cortex Analyst | Cortex Search | Streamlit | Python

Build complete Snowflake Intelligence demos—structured data, unstructured content, semantic views, and Cortex Search services—in a few clicks. The Streamlit experience guides sellers, architects, and partners through collecting customer context, generating AI-crafted scenarios, and provisioning all required database objects automatically.

---

## 🚦 Quick Facts

| Category | Details |
| --- | --- |
| Primary audience | Snowflake Sales Engineers, Solutions Architects, Partners |
| Core services | Cortex LLM, Cortex Analyst, Cortex Search, Streamlit |
| Deployment modes | Standalone (Setup.sql) or Native App (`snowflake.yml`) |
| Supported industries | Manufacturing, Automotive, Banking, Insurance, Securities, Asset Manager, Retail, CPG, Pharma, Healthcare, Generic |
| Tech stack | Python, Streamlit, Snowflake Snowpark, Cortex APIs |

---

## 📚 Table of Contents

1. [Why This App](#why-this-app)  
2. [Architecture Overview](#architecture-overview)  
3. [Setup Paths](#setup-paths)  
4. [Guided Demo Flow](#guided-demo-flow)  
5. [Generated Assets](#generated-assets)  
6. [Reference Data Scenarios](#reference-data-scenarios)  
7. [Operations & Troubleshooting](#operations--troubleshooting)  
8. [Documentation & Support](#documentation--support)

---

## Why This App

- **Cortex-powered ideation** – Snowflake Cortex LLM composes three industry-specific scenarios using the customer’s URL, audience, use cases, and typical data sources.  
- **Complete infrastructure** – Two structured tables (with primary keys and realistic joins), one unstructured content table, optional semantic view, and optional Cortex Search service are deployed together in a schema you choose.  
- **Self-guided Streamlit UI** – Sellers can collect requirements, review AI-generated ideas, and create demo schemas without authoring SQL.  
- **Documentation on autopilot** – The app emits a “demo story” that summarizes assets, business value, and example Cortex Analyst questions.

---

## Architecture Overview

```
SI_DEMOS (Database)
├── APPLICATIONS (Schema)
│   ├── SI_DATA_GENERATOR_REPO         -- Git-integrated repo
│   └── SI_DATA_GENERATOR_APP          -- Streamlit front-end
└── [COMPANY]_DEMO_[DATE] (per-customer schemas)
    ├── TABLE_1 / TABLE_2              -- Structured data with ENTITY_ID PK
    ├── CONTENT_CHUNKS                 -- Unstructured searchable text
    ├── [COMPANY]_SEMANTIC_VIEW        -- Cortex Analyst semantic layer
    └── [TABLE]_SEARCH_SERVICE         -- Cortex Search service
```

---

## Setup Paths

### Option A – Standalone Snowflake objects
1. Run `Setup.sql` as `ACCOUNTADMIN`.  
2. This creates `SI_DEMOS`, the `APPLICATIONS` schema, warehouses, Git integration, and the Streamlit app pointing at `native_app/Dashboard.py`.  
3. Open Snowsight ➜ **Data > Streamlit** ➜ launch `SI_DATA_GENERATOR_APP`.

### Option B – Native App packaging
1. Install [Snow CLI](https://docs.snowflake.com/en/developer-guide/snowflake-cli).  
2. From the repo root, run `snow app deploy` followed by `snow app run`.  
3. Customize references via `snowflake.yml` (warehouse references, privileges) and distribute through the Snowflake Native Apps framework.  
4. Documentation for providers lives in `NATIVE_APP_PROVIDER_GUIDE.md`.

**Prerequisites**
- Snowflake account with Cortex enabled and Streamlit feature turned on.  
- Warehouse usage rights for the app (grant via `GRANT USAGE, OPERATE ON WAREHOUSE ... TO APPLICATION ...`).  
- Git integration if deploying via Setup.sql.

---

## Guided Demo Flow

1. **Customer Brief** – Provide company URL, audience, use cases, industry segment, typical datasets, and desired record count.  
2. **AI Scenario Generation** – Click “Generate Demo Ideas.” Cortex returns three fully described scenarios with structured/unstructured table definitions, business value, and search intents.  
3. **Review & Select** – Each scenario is shown in a tab with industry focus, value proposition, and table breakdowns (Structured 1, Structured 2, Unstructured).  
4. **Provision** – Choose or override the schema name, toggle semantic view and/or Cortex Search service, and click “Create Demo Infrastructure.”  
5. **Demo Story** – The app posts a Markdown report summarizing objects, record counts, example Cortex Analyst questions, and Cortex Search queries.

---

## Generated Assets

| Asset | Description |
| --- | --- |
| Structured tables (2) | ENTITY_ID primary key, realistic measures/dimensions, ~70% overlapping IDs for meaningful joins. |
| Unstructured table | `CONTENT_CHUNKS` with chunk text, metadata, timestamps, ready for Cortex Search. |
| Semantic view (optional) | Declares relationships, facts, dimensions, synonyms, and Cortex Analyst extension metadata. |
| Cortex Search service (optional) | Points to the chunk table for natural-language lookup. |
| Demo documentation | Generated Markdown “story” summarizing the environment and recommended Cortex Analyst/ Search questions. |

---

## Reference Data Scenarios

- **E-commerce / Retail** – Sales transactions + customer profiles + product review corpus.  
- **Financial Services** – Transaction monitoring + compliance event tables + regulatory policy text.  
- **Healthcare / Pharma** – Patient outcomes + treatment protocols + clinical notes.  
- **Manufacturing / CPG** – Production line metrics + supplier performance + maintenance/quality logs.  
- **Generic** – If no segment is selected, robust fallback templates keep the workflow usable offline.

You can customize fallback templates in `Dashboard.py` by editing `get_fallback_demo_ideas`.

---

## Operations & Troubleshooting

| Scenario | Resolution |
| --- | --- |
| Streamlit app missing | Re-run `Setup.sql` or redeploy the Native App package; ensure the Git repo is accessible. |
| Cortex privileges missing | Grant imported privileges via `snowflake.permissions` prompt or run `USE ROLE ACCOUNTADMIN; GRANT ...`. |
| Warehouse not found | The app asks for a `consumer_warehouse` reference; grant USAGE/OPERATE on the desired warehouse to the app. |
| Cleanup | Drop the per-customer schema: `DROP SCHEMA IF EXISTS SI_DEMOS.[COMPANY]_DEMO_[DATE];`. |
| Cost monitoring | Track warehouse auto-resume, table sizes, and Cortex usage via standard Snowflake views. |

---

## Documentation & Support

- `NATIVE_APP_PROVIDER_GUIDE.md` – installation, testing, release guidance for the Native App channel.  
- `Setup.sql` – one-stop SQL script that provisions everything for the standalone deployment.  
- [Snowflake Cortex Documentation](https://docs.snowflake.com/en/user-guide/snowflake-cortex) – detailed reference for Analyst, Search, and Agents.  
- Questions or issues? Open an issue in this repository or contact your Snowflake Technical Account Manager.

---

Ready to create stunning Snowflake Intelligence demos? **Clone the repo, deploy the app, and impress your customers.** 🚀
