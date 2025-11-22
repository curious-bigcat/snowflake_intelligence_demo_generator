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

## 🔖 Tags & Keywords

- Snowflake Intelligence Demo - Vehicle Manufacturing  
- Snowflake Intelligence Manufacturing Automotive  
- Comprehensive demonstration of Snowflake Intelligence capabilities for Vehicle Manufacturing  
- License Snowflake Python

---

## 📚 Table of Contents

1. [Why This App](#why-this-app)  
2. [Architecture Overview](#architecture-overview)  
3. [Implementation Guide](#implementation-guide)  
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

## Implementation Guide

Use this provider-ready playbook to deploy, test, and release the SI Data Generator with the same rigor described in the Native App Provider Guide.

### What This App Provides
- **Intelligent Demo Generation** – AI-crafted scenarios powered by Cortex LLM.  
- **Structured Tables** – Two ENTITY_ID-aware datasets with realistic measures/dimensions.  
- **Unstructured Content** – Chunked documents that are ready for Cortex Search indexing.  
- **Semantic Views & Search Services** – Optional assets to showcase Cortex Analyst and Cortex Search end-to-end.  
- **Demo Storytelling** – Markdown summaries that capture value props, questions, and next steps.

### App Package Installation

#### Prerequisites
- Snowflake account (Enterprise+ or similar) with Cortex, Streamlit, and Native App dev enabled.  
- `ACCOUNTADMIN` (or delegated) role for installation/testing.  
- Latest `git`, [Snow CLI](https://docs.snowflake.com/en/developer-guide/snowflake-cli), and [SnowSQL](https://docs.snowflake.com/en/user-guide/snowsql).  
- Network access for Snowflake Git integrations (if restricting egress).

Run these once to prep the account and local tooling:
```
snowsql -a <account> -u <user> -r ACCOUNTADMIN \
  -q "ALTER ACCOUNT SET STREAMLIT_UI_ENABLED = TRUE;"
snowsql -q "SHOW PACKAGES LIKE 'snowflake-cortex-*';"

snowsql config add connection demo_admin \
  --accountname <account_locator> \
  --username <username> \
  --rolename ACCOUNTADMIN \
  --warehousename <warehouse>

snow connection add demo_admin \
  --account <account_locator> \
  --user <username> \
  --role ACCOUNTADMIN \
  --warehouse <warehouse>
snow connection test demo_admin
```

#### Quick Installation (Snow CLI)
```bash
# Clone and explore
git clone https://github.com/<org>/SI_Data_Generator.git
cd SI_Data_Generator

# Hydrate the Snowflake objects (databases, schemas, app roles)
snow sql --connection demo_admin -f Setup.sql

# Ensure the bundled Git repository stays in sync
snow sql --connection demo_admin -q "
  USE ROLE ACCOUNTADMIN;
  ALTER GIT REPOSITORY SI_DATA_GENERATOR_REPO FETCH;
"

# Build & deploy the Native App package defined in snowflake.yml
snow app deploy --replace --connection demo_admin

# Launch a provider-owned test instance
snow app run --connection demo_admin
```
`snowflake.yml` wires each artifact automatically: `manifest.yml`, `setup_script.sql`, `native_app/Dashboard.py`, and `native_app/environment.yml`.

### Testing Your App Instance

#### Deploy Test Instance
After `snow app run`, copy the URL returned by Snow CLI (or open from Snowsight):
```
https://app.snowflake.com/<account>/#/apps/application/SI_DATA_GENERATOR_APP
```

#### Test Scenarios
1. **Basic Flow** – Input company context and ensure a demo generates end-to-end.  
2. **AI Integration** – Confirm Cortex LLM produces three differentiated scenarios.  
3. **Data Creation** – Verify structured/unstructured tables, semantic views, and search services exist in the target schema.  
4. **Permissions** – Swap warehouses/roles to ensure reference requests and privilege prompts behave correctly.

#### Validation Checklist
- Streamlit app loads without stack traces.  
- Customer brief accepts inputs and persists session state.  
- AI returns 3 industry-aligned ideas.  
- Provisioning step creates schemas, ENTITY_ID primary keys, semantic view, and Cortex Search service when toggled.  
- Generated Markdown “demo story” reflects the chosen scenario.  
- Logs show Cortex calls, Git fetches, and Snowpark jobs succeeding.

### Releasing New Versions

#### Version Management
Update `manifest.yml` with semantic versions:
```yaml
version:
  name: V1_1
  label: "1.1"
  comment: "Enhanced demo generation with new industry templates"
```

#### Release Process
1. **Update Code** – Change `native_app/Dashboard.py`, data templates, or scripts.  
2. **Test Locally** – `snow app deploy && snow app run` using `demo_admin`.  
3. **Update Manifest** – Increment `version` metadata and note release highlights.  
4. **Deploy Package** – `snow app deploy --connection demo_admin`.  
5. **Create Version** – Promote the stage to a version and set it as default:
   ```bash
   snow app version create V1_1
   snow app release-directive set --version V1_1 --default
   snow app release-directive list
   ```
   One-liner: `snow app version create V1_1 --set-as-default`.

#### Advanced Release Management
Use `snow app version drop`, multiple stages, or rollback directives per the [Native Apps versioning guide](https://docs.snowflake.com/en/developer-guide/native-apps/versioning-apps).

### Architecture Overview
```
SI Data Generator Native App
├── APPLICATION ROLE: app_instance_role
├── APPLICATIONS (schema)
│   └── SI_DATA_GENERATOR_APP (Streamlit)
├── CONFIG (schema)
│   └── REGISTER_SINGLE_REFERENCE (callback procedure)
└── Consumer warehouse reference
    ├── USAGE
    └── OPERATE
```

### Customization for Providers
- **Demo templates** – Edit `get_fallback_demo_ideas` in `native_app/Dashboard.py` to inject vertical-specific storylines.  
- **Warehouse sizing** – Adjust `references` within `manifest.yml` to enforce minimum warehouse classes.  
- **Semantic/search toggles** – Default the Streamlit switches inside `Dashboard.py` to match your go-to-market narrative.  
- **Setup automation** – Extend `setup_script.sql` if you need extra roles, stages, or masking policies.

### Consumer Management
```sql
-- Consumer installs your shared/package app
CREATE APPLICATION si_data_generator_app
  FROM APPLICATION PACKAGE provider_account.si_data_generator_pkg;

-- Grant warehouse access expected by the app
GRANT USAGE, OPERATE ON WAREHOUSE consumer_warehouse
  TO APPLICATION si_data_generator_app;
```
Cleanup commands:
```sql
DROP APPLICATION si_data_generator_app;
DROP SCHEMA IF EXISTS demo_schema_name;
```

### Monitoring, Analytics, & Troubleshooting
- **Provider analytics** – Track installs, active sessions, and errors via Native App telemetry views.  
- **Performance tuning** – Watch Cortex usage, Snowpark job durations, and warehouse time via `ACCOUNT_USAGE`.  
- **Deployment validation** – `snow app deploy --validate-only` for manifest checks, `snow app deploy --debug` for verbose logs.  
- **Privilege issues** – Ensure `setup_script.sql` grants `USAGE/OPERATE` on warehouses and that consumers accept reference prompts.  
- **Consumer errors** – Validate `manifest.yml` references and callback procedures; confirm Git fetch succeeds.

### Success Indicators
- Test instances deploy without errors and create all assets.  
- Sellers can run end-to-end demos immediately after installation.  
- Cleanup scripts drop schemas/app instances cleanly.  
- Documentation (this README + provider guide) reflects the latest release.

### Support Resources
- `NATIVE_APP_PROVIDER_GUIDE.md` (detailed provider checklist)  
- [Snowflake Native Apps docs](https://docs.snowflake.com/en/developer-guide/native-apps/)  
- [Snow CLI reference](https://docs.snowflake.com/en/developer-guide/snowflake-cli/)  
- [Cortex documentation](https://docs.snowflake.com/en/user-guide/snowflake-cortex)

### Maintenance Cheatsheet
```
# Refresh Git repo without redeploying package
snow sql --connection demo_admin -q "
  ALTER GIT REPOSITORY SI_DATA_GENERATOR_REPO FETCH;
"

# Drop per-customer schemas from CLI
snow sql --connection demo_admin -q "
  DROP SCHEMA IF EXISTS SI_DEMOS.ACME_DEMO_JAN2025 CASCADE;
"
```

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
