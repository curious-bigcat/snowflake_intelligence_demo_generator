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

Follow this single, end-to-end path to bring the demo generator online, validate the Streamlit UI, and (optionally) publish it as a Native App for broader distribution.

### Step 0. Confirm prerequisites
- Snowflake account on an edition that supports Cortex LLM, Cortex Analyst, Cortex Search, and Streamlit.  
- Role with `ACCOUNTADMIN`-level powers (or equivalent) to create databases, warehouses, Git integrations, and Streamlit apps.  
- Local workstation with `git`, [Snow CLI](https://docs.snowflake.com/en/developer-guide/snowflake-cli), and [SnowSQL](https://docs.snowflake.com/en/user-guide/snowsql) (all ≥ latest GA).

Recommended pre-checks (run once via SnowSQL or Snow CLI):
```
snowsql -a <account> -u <user> -r ACCOUNTADMIN -q "ALTER ACCOUNT SET STREAMLIT_UI_ENABLED = TRUE;"
snowsql -q "SHOW PACKAGES LIKE 'snowflake-cortex-*';"
```

Add a reusable SnowSQL connection profile so subsequent commands stay concise:
```
snowsql config add connection demo_admin \
  --accountname <account_locator> \
  --username <username> \
  --rolename ACCOUNTADMIN \
  --warehousename <warehouse>
snowsql -c demo_admin -q "SELECT CURRENT_ROLE();"
```

### Step 1. Clone the repository, review configuration, and wire up Snow CLI
```
git clone https://github.com/<org>/SI_Data_Generator.git
cd SI_Data_Generator
```
- Open `Setup.sql` to understand the objects that will be created (databases, warehouses, application roles, Git integration, Streamlit app definition).  
- Inspect `native_app/app_config.yml`, `native_app/environment.yml`, and `native_app/Dashboard.py` if you plan to modify the Streamlit behavior before deployment.  
- Note the optional packaging artifacts (`snowflake.yml`, `manifest.yml`, `native_app/app_config.yml`) that the script references.
- Create a Snow CLI connection that mirrors the SnowSQL profile so all subsequent steps can run headless:
  ```
  snow connection add demo_admin \
    --account <account_locator> \
    --user <username> \
    --role ACCOUNTADMIN \
    --warehouse <warehouse>
  snow connection test demo_admin
  ```

### Step 2. Create or update the Snowflake Git integration via Snow CLI
The Streamlit app pulls its UI code from the Snowflake Git repository created in `SI_DEMOS.APPLICATIONS`. Run (adjust repo URL/branch as needed):
```
snow sql --connection demo_admin <<'SQL'
USE ROLE ACCOUNTADMIN;
CREATE OR REPLACE GIT REPOSITORY SI_DATA_GENERATOR_REPO
  ORIGIN = 'https://github.com/<org>/SI_Data_Generator.git';
ALTER GIT REPOSITORY SI_DATA_GENERATOR_REPO FETCH;
SQL
```
If your governance model requires a network/security integration for Git, add it here with `snow sql --connection demo_admin -q "CREATE OR REPLACE SECURITY INTEGRATION ... TYPE = GIT ..."` and re-run the fetch.

### Step 3. Execute `Setup.sql` using Snow CLI
```
snow sql --connection demo_admin -f Setup.sql
```
The script performs the following automatically:
- Creates `SI_DEMOS` (database), `APPLICATIONS` (schema), warehouses, stages, and service roles.  
- Registers the Git repo and pins the Streamlit app to `native_app/Dashboard.py`.  
- Grants the Streamlit app least-privilege access to warehouses and Cortex features.  
- Seeds helper procedures (`SETUP_NATIVE_APP`, `CREATE_DEMO_SCHEMA`) and loads the packaged assets into `SI_DEMOS.APPLICATIONS`.

CLI output shows each block; expect a 2–4 minute run. Re-run the command anytime you need to refresh the environment—`Setup.sql` is idempotent.

### Step 4. Discover and open the Streamlit experience from Snow CLI
1. List apps to confirm registration:
   ```
   snow streamlit list --connection demo_admin \
     --database SI_DEMOS --schema APPLICATIONS
   ```
   Copy the `app_url` (Snowsight link) from the output.
2. Open the URL in your browser (or run `snow streamlit open SI_DEMOS.APPLICATIONS.SI_DATA_GENERATOR_APP --connection demo_admin` if your CLI version supports direct launch).  
3. When the browser prompts for a warehouse, select the alias created in Step 3 (default: `SI_DEMOS_WH`) and accept any privilege dialogs.  
4. The Streamlit UI always pulls the latest Git commit; rerun Step 2’s `ALTER ... FETCH` when you push new code.

### Step 5. Generate and validate your first demo
1. On the Streamlit **Customer Brief** page (opened from Step 4), supply:
   - Company URL (real or internal), audience, top use cases, and data sources.  
   - Industry segment (Retail, Banking, etc.) and desired record count for structured tables.  
   - Whether to include unstructured data, semantic views, or Cortex Search services.
2. Click **Generate Demo Ideas**. Cortex will synthesize three industry-specific playbooks. Review the structured/unstructured table definitions, metrics, and recommended questions.  
3. Select the scenario that best fits the engagement. Provide a schema name (ex: `ACME_DEMO_JAN2025`) or keep the suggested `[COMPANY]_DEMO_[DATE]`.  
4. Press **Create Demo Infrastructure**. The app orchestrates warehouse usage, Snowpark data generation, semantic view registration, and (optionally) Cortex Search indexing.  
5. Download or copy the generated “demo story” Markdown. To double-check objects via CLI:
   ```
   snow sql --connection demo_admin -q "
     USE SCHEMA SI_DEMOS.ACME_DEMO_JAN2025;
     SHOW TABLES;
     SHOW VIEWS;
   "
   ```
   You should see `TABLE_1`, `TABLE_2`, `CONTENT_CHUNKS`, optional semantic views, and search services for the target schema.

### Step 6. (Optional) Package and distribute as a Native App
1. Install Snow CLI (`pip install snowflake-cli-labs`) and authenticate with a key pair or OAuth.  
2. Update `snowflake.yml` with your target account, role, and warehouse names (reuse connection `demo_admin` or create a provider-specific one).  
3. Run:
   ```
   snow app run --connection demo_admin     # Builds, uploads, and tests the app package
   snow app deploy --replace --connection demo_admin
   ```
4. Follow `NATIVE_APP_PROVIDER_GUIDE.md` to publish the package to consumers, ensuring the manifest references the assets generated in Step 3.  
5. Grant `USAGE` and `OPERATE` on the consumer warehouse to the installed application role as part of your rollout checklist.

### Step 7. Share outputs and perform ongoing maintenance
- Encourage sellers to attach the Markdown “demo story” to their opportunity notes or solution briefs.  
- Use the cleanup SQL snippet below when a demo is no longer needed:
  ```
  snow sql --connection demo_admin -q "
    USE ROLE ACCOUNTADMIN;
    DROP SCHEMA IF EXISTS SI_DEMOS.ACME_DEMO_JAN2025 CASCADE;
  "
  ```
- Schedule periodic `snow sql --connection demo_admin -q "ALTER GIT REPOSITORY SI_DATA_GENERATOR_REPO FETCH;"` (or rerun `Setup.sql`) to keep the Streamlit app synchronized with the latest commit.  
- Monitor warehouse auto-suspend settings, Cortex usage limits, and app logs via Snowsight’s **Activity ➜ Logs** page to ensure predictable cost controls.

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
