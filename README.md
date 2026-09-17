# Agentic AI - Data Agent

A sophisticated multi-agent system for intelligent data processing and analysis using LangGraph. This project demonstrates a complete implementation of an agentic architecture with specialized sub-agents for SQL operations and ETL workflows.


##  Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [Agent Descriptions](#agent-descriptions)
- [Data Models](#data-models)


---

##  Overview

**Agentic AI Data Agent** is an intelligent system that processes natural language queries and routes them to specialized agents for execution. The main agent acts as an intelligent router that understands user intent and delegates tasks to either the **SQL Analyst Agent** (for database queries) or the **ETL Analyst Agent** (for data extraction and transformation operations).

This project showcases modern AI engineering practices including:
- Multi-agent orchestration with LangGraph
- Intelligent routing based on natural language understanding
- Safety validation for SQL queries
- Tool-based agent architecture
- Dynamic LLM selection based on task complexity

---
## Architecture

The system follows a hierarchical agent architecture:

```
┌─────────────────────────────────────────────────────────────┐
│                    Data Agent (Router)                      │
│         Routes user queries to appropriate sub-agents       │
└────────────────────┬────────────────────────────────────────┘
                     │
         ┌───────────┴───────────┐
         │                       │
         ▼                       ▼
    ┌──────────────┐        ┌──────────────┐
    │ SQL Analyst  │        │ ETL Analyst  │
    │   Agent      │        │   Agent      │
    └──────────────┘        └──────────────┘
         │                       │
         ├─► Query Curation      ├─► Extract Load
         ├─► Schema Context      ├─► Transform Load
         ├─► SQL Generation      └─► Code Execution
         ├─► Safety Validation   
         ├─► Query Execution     
         └─► Answer Generation   
```

### State Flow

1. **User Input** → Natural language query
2. **Router Node** → Classifies query as SQL or ETL
3. **Agent Dispatch** → Routes to appropriate sub-agent
4. **Processing** → Each agent processes the task
5. **Output** → Returns structured result to user

---

##  Features

### Core Capabilities

- **Intelligent Query Routing**: Automatically classifies user queries as SQL or ETL operations
- **SQL Analysis Agent**:
  - Natural language to SQL query conversion
  - Automatic schema context gathering
  - SQL safety validation (prevents harmful operations)
  - Query execution on PostgreSQL database
  - Intelligent query refinement

- **ETL Agent**:
  - API data extraction (JSON to structured formats)
  - Data transformation using Pandas
  - Multi-format support (CSV, JSON, Parquet)
  - Dynamic code generation based on user requirements
  - Safe code execution

- **Multi-LLM Support**:
  - Low-complexity queries: Faster, cost-effective LLM
  - Medium-complexity queries: Balanced LLM
  - High-complexity queries: Premium LLM (Claude)

- **Safety & Validation**:
  - SQL query safety checking
  - Protection against database modifications (INSERT, UPDATE, DELETE, DROP, etc.)
  - Input validation and sanitization
  - Structured output validation using Pydantic

---

##  Prerequisites

- Python 3.12+
- PostgreSQL database (for SQL operations)
- API keys for LLM providers (Claude / OpenAI / Google Gemini Api)

---

##  Installation

### 1. Clone and Setup Project

```bash
cd Data_Agent
python -m venv .venv

# Activate virtual environment
# On Windows:
.\.venv\Scripts\Activate.ps1
# On macOS/Linux:
source .venv/bin/activate
```

### 2. Install Dependencies

```bash
uv pip install -r requirements.txt
# or
pip install -e .
```

This installs:
- **langchain**: Core LLM framework
- **langgraph**: Multi-agent orchestration
- **langchain-anthropic**: Claude AI integration
- **langchain-openai**: OpenAI integration
- **pandas**: Data processing
- **psycopg2**: PostgreSQL driver
- **pydantic**: Data validation
- **python-dotenv**: Environment configuration

### 3. Environment Configuration

Create a `.env` file in the root directory:

```env
# LLM Configuration
ANTHROPIC_API_KEY=your_claude_api_key
OPENAI_API_KEY=your_openai_api_key

# Database Configuration
host=localhost
port=5432
user=postgres
password=your_password
database=data_agent_db

# Optional: LLM Model Selection
LLM_MODEL_LOW=gpt-3.5-turbo
LLM_MODEL_MEDIUM=gpt-4-turbo
LLM_MODEL_HIGH=claude-3-opus
```

---

##  Project Structure

```
Data_Agent/
├── agents/                          # Agent implementations
│   ├── __init__.py
│   ├── data_agent.py               # Main router agent
│   ├── sql_analyst.py              # SQL query agent
│   └── etl_analyst.py              # ETL operations agent
│
├── Models/                          # Data models
│   ├── __init__.py
│   └── schema.py                   # Pydantic schemas for state management
│
├── utils/                           # Utility modules
│   ├── __init__.py
│   ├── database.py                 # PostgreSQL utilities
│   ├── etl_tools.py                # ETL operations toolkit
│   ├── llm_pick.py                 # LLM selection logic
│
├── data/                            # Data directory
│   ├── extract/                     # Extracted data storage
│   ├── transform/                   # Transformed data storage
│   ├── payments.csv                 # Sample dataset
│   ├── ratings.csv                  # Sample dataset
│   ├── rides.csv                    # Sample dataset
│   ├── users.csv                    # Sample dataset
│   └── vehicles.csv                 # Sample dataset
│
├── main.py                          # Entry point
├── feed_db.py                       # Database initialization script
├── pyproject.toml                   # Project metadata and dependencies
└── README.md                         # This file
```

---

##  Configuration

### LLM Selection (`utils/llm_pick.py`)

The `pick_llm()` function intelligently selects the appropriate LLM based on complexity:

```python
from utils.llm_pick import pick_llm

# Select based on complexity
llm_fast = pick_llm("low")        # Cost-effective for simple queries
llm_balanced = pick_llm("medium") # Balanced performance and cost
llm_powerful = pick_llm("claude") # Premium model for complex tasks
```

### Database Configuration (`utils/database.py`)

```python
from utils.database import DatabaseUtil

conn_details = {
    "host": "localhost",
    "port": 5432,
    "user": "postgres",
    "password": "password",
    "dbname": "data_agent_db"
}

db = DatabaseUtil(conn_details)
schema_info = db.schema_details("public")
```

---




##  Agent Descriptions

### 1. **Data Agent (Main Router)**
**File:** `agents/data_agent.py`

**Responsibility:** 
- Receives natural language user queries
- Classifies queries as either SQL or ETL operations
- Routes queries to appropriate sub-agents
- Aggregates results and returns to user

**Components:**
- **Router Node**: Uses structured output to classify query intent
- **Conditional Routing**: Routes to SQL or ETL based on classification
- **Graph Orchestration**: Manages workflow using LangGraph

---

### 2. **SQL Analyst Agent**
**File:** `agents/sql_analyst.py`

**Responsibility:**
- Converts natural language queries to SQL
- Handles all database query operations
- Validates query safety
- Executes queries and returns results

**Workflow:**
1. **Query Curation** - Refines user question for clarity
2. **Context Gathering** - Fetches database schema details
3. **Prompt Construction** - Creates detailed context for LLM
4. **SQL Generation** - Generates SQL query using LLM
5. **Safety Check** - Validates query safety
6. **Query Execution** - Executes validated query on database
7. **Answer Generation** - Formats and returns results

**Safety Features:**
- Prevents execution of dangerous commands (INSERT, UPDATE, DELETE, DROP, ALTER)
- Validates query before execution
- Automatic result limiting to 10 rows (unless specified)
- Schema validation against database

---

### 3. **ETL Analyst Agent**
**File:** `agents/etl_analyst.py`

**Responsibility:**
- Handles data extraction from APIs
- Performs data transformation using Pandas
- Manages data loading to various formats
- Executes code safely in controlled environment

**Workflow:**
1. **Tool Binding** - Attaches ETL tools to LLM
2. **User Intent Understanding** - Analyzes transformation requirements
3. **Tool Selection** - Chooses appropriate ETL operation
4. **Code Generation** - Generates Pandas code for transformation
5. **Safe Execution** - Executes generated code in sandboxed environment
6. **Result Reporting** - Returns execution status and generated code

**Supported Tools:**
- **extract_load_tool**: Extract from API → Load to storage
- **transform_load_tool**: Transform data using Pandas → Load result

**Supported Formats:**
- CSV (default)
- JSON (Lines or Records)
- Parquet

---

##  Data Models

### AgentSchema (SQL Agent State)
```python
class AgentSchema(BaseModel):
    messages: List                    # Conversation messages
    user_question: str                # Original user query
    curated_ques: str                 # Refined question
    prompt_query_context: str         # Database context + prompt
    generated_sql_query: str          # Generated SQL
    is_safe: Literal["Yes", "No"]     # Safety validation result
    comments: str                     # Safety check comments
    sql_query_execution_result: str   # Query result
    final_answer: str                 # Final formatted answer
```

### ETLAgentSchema (ETL Agent State)
```python
class ETLAgentSchema(BaseModel):
    messages: List                    # Conversation messages
```

### RouterSchema (Query Classification)
```python
class RouterSchema(BaseModel):
    answer: Literal["sql", "etl"]     # Query classification
    comments: str                     # Reasoning for classification
```

### DataAgentSchema (Main Agent State)
```python
class DataAgentSchema(BaseModel):
    messages: List                    # All conversation messages
    route_response: str               # Router decision (sql/etl)
```

---

