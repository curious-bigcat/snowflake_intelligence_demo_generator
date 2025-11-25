# ❄️ Snowflake Intelligence Demo Generator

Build an AI-ready Snowflake demo environment (structured data, unstructured chunks, semantic view, Cortex Search, and Markdown story) in a single Streamlit session.

---

## What you need
- Snowflake account with Cortex, Streamlit, and Native App dev enabled  
- `ACCOUNTADMIN` (or delegated) role + warehouse access  
- Git, [Snow CLI](https://docs.snowflake.com/en/developer-guide/snowflake-cli), and [SnowSQL](https://docs.snowflake.com/en/user-guide/snowsql) on your laptop

Optional keywords: **Snowflake Intelligence Demo - Vehicle Manufacturing**, **Snowflake Intelligence Manufacturing Automotive**

---

## 5-minute setup
```bash
# 1. Clone
git clone https://github.com/<org>/SI_Data_Generator.git
cd SI_Data_Generator

# 2. Prep account once
snowsql -a <acct> -u <user> -r ACCOUNTADMIN \
  -q "ALTER ACCOUNT SET STREAMLIT_UI_ENABLED = TRUE;"
snowsql -q "SHOW PACKAGES LIKE 'snowflake-cortex-*';"

# 3. Save connections (run once)
snowsql config add connection demo_admin --accountname <acct> --username <user> --rolename ACCOUNTADMIN --warehousename <wh>
snow connection add demo_admin --account <acct> --user <user> --role ACCOUNTADMIN --warehouse <wh>

# 4. Deploy everything
snow sql --connection demo_admin -f Setup.sql
snow app deploy --replace --connection demo_admin
snow app run --connection demo_admin
```
The script creates `SI_DEMOS`, wires the Streamlit app to the Git repo, and sets up all roles/warehouses automatically.

---

## Launch & use the app
1. Open the URL printed by `snow app run` (or Snowsight ▸ Data ▸ Streamlit ▸ `SI_DATA_GENERATOR_APP`).  
2. Pick your warehouse when prompted and accept the imported privilege grants.  
3. Follow the on-screen flow:
   - **Step 1 – Customer Brief**: company URL, audience, use cases, industry, record count, typical data sources. Open the **Advanced Table Preferences** expander if you want to set how many structured/unstructured tables to build or describe the source systems/KPIs they should mimic.  
   - **Step 2 – AI Ideas**: click **Generate Demo Ideas**. Cortex returns three scenarios that already reflect your table-count and data-hint choices.  
   - **Step 3 – Provision**: choose the schema name, toggle semantic view / Cortex Search, and press **Create Demo Infrastructure**. The app creates as many tables as you requested, enforces ENTITY_ID joins, publishes search services, and outputs a Markdown “demo story” for sharing.

That’s it—the schema is ready to demo in Snowsight, Streamlit, or Cortex Analyst/Search immediately.

---

## What gets created
- **Structured tables** – Any number you request, each with ENTITY_ID PKs and ~70 % overlapping IDs for meaningful joins  
- **Unstructured tables** – One or more chunk tables with metadata, plus Cortex Search services (one per table)  
- **Semantic view** – Joins the first two structured tables with facts/dimensions, example Analyst questions, and CA extension metadata  
- **Demo story** – Markdown recap with business value bullets, sample Analyst questions, and sample Search prompts

---

## Customize or extend
- Edit fallback scenarios or default table toggles in `native_app/Dashboard.py`.  
- Update `snowflake.yml` / `manifest.yml` if you need different warehouses, privileges, or version labels.  
- Regenerate everything after code changes with:
  ```bash
  snow sql --connection demo_admin -q "
    ALTER GIT REPOSITORY SI_DATA_GENERATOR_REPO FETCH;
  "
  snow app deploy --replace --connection demo_admin
  snow app run --connection demo_admin
  ```

---

## Clean up
```bash
# Drop a per-customer demo schema
snow sql --connection demo_admin -q "
  DROP SCHEMA IF EXISTS SI_DEMOS.<COMPANY>_DEMO_<DATE> CASCADE;
"

# Remove the native app instance (optional)
snow sql --connection demo_admin -q "
  DROP APPLICATION IF EXISTS SI_DATA_GENERATOR_APP;
"
```

---

## Need help?
- `Setup.sql` – creates every object for standalone use  
- `NATIVE_APP_PROVIDER_GUIDE.md` – deeper provider testing & release checklist  
- [Snowflake Cortex docs](https://docs.snowflake.com/en/user-guide/snowflake-cortex) & [Snow CLI reference](https://docs.snowflake.com/en/developer-guide/snowflake-cli) for advanced scenarios  
- Questions? File an issue or ping your Snowflake Technical Account Manager

Happy demoing! 🚀
