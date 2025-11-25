# 🎓 School Research Assistant (LangChain Refactor)

**Version 2.0** - Refactored with LangChain for better scalability, maintainability, and Databricks integration.

## What This App Does

Helps sales consultants at Supporting Education Group make smarter calls by providing:

1. **Pre-loaded school data** (28 Camden schools) - no waiting for web scraping
2. **Searchable dropdown** - find any school instantly
3. **AI-generated conversation starters** - personalized talking points for each school
4. **Financial insights** - see agency spend, cost comparisons
5. **Contact information** - headteacher details ready to use

---

## Quick Start

### 1. Install Dependencies

```bash
pip install -r requirements_v2.txt
```

### 2. Set Up API Keys

Copy the example env file and add your keys:

```bash
cp .env.example .env
```

Edit `.env`:
```
# For Claude (recommended):
ANTHROPIC_API_KEY=sk-ant-...

# OR for GPT:
OPENAI_API_KEY=sk-...
```

### 3. Choose Your LLM

Edit `config_v2.py` line 17:
```python
# For Claude:
LLM_PROVIDER = "anthropic"

# For GPT:
LLM_PROVIDER = "openai"
```

### 4. Run the App

```bash
streamlit run streamlit_app_v2.py
```

Open http://localhost:8501 in your browser.

**Default password:** `SEG2025AI!`

---

## Project Structure

```
school_research_assistant/
├── data/
│   └── camden_schools_llm_ready.csv    # Your 28 schools dataset
│
├── chains/
│   └── conversation_chain.py            # LangChain chain for AI generation
│
├── prompts/
│   └── templates.py                      # All prompt templates in one place
│
├── models_v2.py                          # Pydantic data models
├── config_v2.py                          # Settings (LLM choice, etc.)
├── data_loader.py                        # Loads CSV (or Databricks later)
├── school_intelligence_service.py        # Main orchestration
├── streamlit_app_v2.py                   # The web app
│
├── requirements_v2.txt                   # Python dependencies
├── .env.example                          # Example environment variables
└── README.md                             # This file
```

---

## How It Works

### Data Flow

```
App Starts
    ↓
Load CSV (28 schools) ← Instant, no API calls
    ↓
User selects school from dropdown
    ↓
School data displays immediately
    ↓
User clicks "Generate Insights"
    ↓
LangChain sends data to Claude/GPT
    ↓
AI returns conversation starters
    ↓
Results cached for 24 hours
```

### Key Files Explained

| File | Purpose |
|------|---------|
| `models_v2.py` | Defines what a School, Contact, FinancialData looks like. Validates data automatically. |
| `data_loader.py` | Reads CSV file and creates School objects. Will connect to Databricks later. |
| `prompts/templates.py` | All the prompts sent to the AI. Edit here to change how starters are generated. |
| `chains/conversation_chain.py` | The LangChain code that calls Claude/GPT and parses results. |
| `school_intelligence_service.py` | Connects everything together. This is what the UI calls. |
| `streamlit_app_v2.py` | The web interface. |
| `config_v2.py` | Settings - change LLM provider, enable/disable features. |

---

## Switching to Databricks (Phase 2)

When your Databricks connection is ready:

### 1. Add credentials to `.env`:
```
DATABRICKS_HOST=your-workspace.cloud.databricks.com
DATABRICKS_TOKEN=dapi...
DATABRICKS_WAREHOUSE_ID=abc123
```

### 2. Change data source in `config_v2.py`:
```python
DATA_SOURCE = "databricks"  # Instead of "csv"
```

### 3. Update Databricks config in `config_v2.py`:
```python
DATABRICKS_CONFIG = {
    "catalog": "main",
    "schema": "schools",
    "table": "edco_schools"
}
```

### 4. Install Databricks connector:
```bash
pip install databricks-sql-connector
```

The app will now load from Databricks instead of CSV!

---

## Switching Between Claude and GPT

### To use Claude (Anthropic):

1. Get an API key from https://console.anthropic.com
2. Add to `.env`: `ANTHROPIC_API_KEY=sk-ant-...`
3. In `config_v2.py`: `LLM_PROVIDER = "anthropic"`

### To use GPT (OpenAI):

1. Get an API key from https://platform.openai.com
2. Add to `.env`: `OPENAI_API_KEY=sk-...`
3. In `config_v2.py`: `LLM_PROVIDER = "openai"`

---

## Customizing Prompts

All prompts are in `prompts/templates.py`. 

To change how conversation starters are generated, edit:

```python
CONVERSATION_STARTERS_SYSTEM = """..."""
CONVERSATION_STARTERS_HUMAN = """..."""
```

The system prompt defines the AI's role and guidelines.
The human prompt defines what specific output we want.

---

## Caching

Results are cached for 24 hours to:
- Save money on API calls
- Speed up repeat lookups
- Reduce latency

Cache is stored in the `cache/` folder as JSON files.

To clear cache:
- Click "Clear Cache" in the sidebar
- Or delete files in `cache/` folder

To disable caching, set in `config_v2.py`:
```python
ENABLE_CACHE = False
```

---

## Troubleshooting

### "No module named 'langchain'"
```bash
pip install -r requirements_v2.txt
```

### "Invalid API key"
Check your `.env` file has the correct key for your chosen provider.

### "School not found"
The CSV file must be in `data/camden_schools_llm_ready.csv`

### Slow generation
First call may take 3-5 seconds. Subsequent calls use cache.

---

## What's Not Included (Future Phases)

- **Ofsted PDF analysis** - Would require Firecrawl/web scraping
- **Vacancy detection** - Would require live web search
- **Vector search** - Needed for 24K schools semantic search
- **MLflow tracking** - For Phase 4 ML features

These can be added incrementally without changing the core architecture.

---

## Files from Old Version (Can Be Removed)

These files from the original version are NOT used in v2:

```
ai_engine_premium.py          → Replaced by chains/conversation_chain.py
processor_premium.py          → Replaced by school_intelligence_service.py
streamlit_app.py              → Replaced by streamlit_app_v2.py
models.py                     → Replaced by models_v2.py
config.py                     → Replaced by config_v2.py
ofsted_analyzer_v2.py         → Not needed for POC (data pre-loaded)
financial_data_engine.py      → Not needed for POC (data pre-loaded)
vacancy_detector.py           → Not needed for POC
cache.py                      → Replaced by SimpleCache in service
```

Keep them as reference, or remove once v2 is working.

---

## Support

Contact the IT team for:
- API key issues
- Databricks connection setup
- Feature requests

---

**Built with LangChain + Streamlit** 🚀
