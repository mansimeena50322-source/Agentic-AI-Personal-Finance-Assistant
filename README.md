#  Agentic AI Personal Finance Assistant

An **agentic AI-powered personal finance assistant** that uses a multi-agent architecture to analyze financial data, retrieve market information, and generate real-time, structured financial insights.

The system is built around **Model Context Protocol (MCP)**, enabling specialized agents to independently access financial tools and collaborate through a **Planner → Executor → Aggregator** pipeline.

---

#  Key Highlights

*  5-agent MCP architecture** for specialized financial analysis
*  Parallel agent execution** using a Planner–Executor–Aggregator workflow
*  Provider-agnostic LLM orchestration** supporting:

  * Groq — LLaMA 3.3
  * OpenAI — GPT-4o
  * Ollama — local LLM inference
*  Strict structured outputs validated using Pydantic
*  SSE-based MCP servers for real-time agent communication
*  Financial data retrieval using yFinance
*  Web-based financial/news retrieval using DuckDuckGo
*  Semantic retrieval using FAISS
*  Designed to support 100+ concurrent agent executions
*  Combines market data, financial context, and LLM reasoning into actionable insights



  # System Architecture


                         ┌──────────────────────┐
                         │      User Query      │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │       Planner        │
                         │  Task Decomposition  │
                         └──────────┬───────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    │               │               │
                    ▼               ▼               ▼
             ┌───────────┐   ┌───────────┐   ┌───────────┐
             │ Agent 1   │   │ Agent 2   │   │ Agent 3   │
             │  Market   │   │ Research  │   │ Retrieval │
             └─────┬─────┘   └─────┬─────┘   └─────┬─────┘
                   │               │               │
                   └───────────────┼───────────────┘
                                   │
                            Parallel Execution
                                   │
                    ┌──────────────┼──────────────┐
                    ▼              ▼              ▼
               ┌─────────┐   ┌──────────┐   ┌─────────┐
               │ yFinance│   │DuckDuckGo│   │  FAISS  │
               └─────────┘   └──────────┘   └─────────┘
                    │              │              │
                    └──────────────┼──────────────┘
                                   ▼
                         ┌──────────────────────┐
                         │      Aggregator      │
                         │  Result Synthesis    │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Structured Response  │
                         │   Pydantic Output    │
                         └──────────────────────┘


# Multi-Agent Architecture

The assistant divides financial analysis into specialized tasks rather than relying on a single LLM call.

 1. Planner Agent

Responsible for:

* Understanding the user's financial query
* Breaking complex queries into smaller tasks
* Identifying which agents/tools are required
* Creating an execution plan

 2. Specialized Analysis Agents

Multiple agents independently perform specific financial tasks.

Examples include:

* Market Analysis Agent — analyzes stock and market data
* Financial Research Agent — retrieves relevant financial information
* Retrieval Agent — performs semantic search over indexed information
* News/Context Agent — retrieves current financial/news information

Agents can execute independently whenever their tasks do not depend on one another.

3. Aggregator Agent

The aggregator receives outputs from the parallel agents and:

* Combines the individual results
* Resolves conflicting information
* Generates a unified analysis
* Produces the final structured response



Planner–Executor–Aggregator Pipeline

A major design goal of the system is reducing unnecessary sequential LLM calls.

Instead of:

Query
  ↓
Agent 1
  ↓
Agent 2
  ↓
Agent 3
  ↓
Agent 4
  ↓
Final Response

the system uses parallel execution:


                Query
                  ↓
               Planner
                  ↓
       ┌──────────┼──────────┐
       ↓          ↓          ↓
    Agent 1    Agent 2    Agent 3
       ↓          ↓          ↓
       └──────────┼──────────┘
                  ↓
              Aggregator
                  ↓
            Final Response


This allows independent financial tasks to execute concurrently, improving response latency and scalability.


#  Provider-Agnostic LLM Layer

The project includes an abstraction layer between the application and the underlying LLM provider.


                Application
                     │
                     ▼
          ┌─────────────────────┐
          │  LLM Orchestration  │
          │       Layer         │
          └─────────┬───────────┘
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
     Groq        OpenAI       Ollama
   LLaMA 3.3      GPT-4o     Local LLM

This makes it possible to switch models without changing the core agent logic.

### Supported Providers

| Provider | Model        | Use Case                  |
| -------- | ------------ | ------------------------- |
| Groq     | LLaMA 3.3    | Low-latency inference     |
| OpenAI   | GPT-4o       | General-purpose reasoning |
| Ollama   | Local models | Local/private inference   |



# Structured & Validated Outputs

Agent responses are represented using **Pydantic models** rather than relying on unrestricted text generation.

LLM Response
     │
     ▼
Pydantic Schema
     │
     ├── Valid
     │     ↓
     │  Continue
     │
     └── Invalid
           ↓
      Validation Error

This provides:

* Type-safe agent communication
* Consistent response formats
* Easier downstream processing
* Validation of required fields
* Reduced risk of malformed agent outputs

---

# MCP Infrastructure

The project uses **Model Context Protocol (MCP)** to expose financial capabilities as reusable tools.

MCP servers are implemented using **Server-Sent Events (SSE)** for communication between agents and tool servers.

Agent
  │
  │ MCP / SSE
  ▼
┌──────────────────┐
│    MCP Server    │
├──────────────────┤
│                  │
│  yFinance Tool   │
│  FAISS Tool      │
│  Search Tool     │
│                  │
└──────────────────┘

This separates:

* Agent reasoning
* Tool execution
* Data retrieval
* Application orchestration

and makes individual tools reusable across different agents.

# Technology Stack

# AI & LLM

* Python
* LLaMA 3.3
* GPT-4o
* Ollama
* Provider-agnostic LLM orchestration

# Agent Infrastructure

* MCP
* SSE
* Multi-agent orchestration
* Planner–Executor–Aggregator architecture
* Parallel execution

# Financial & Retrieval Tools

* yFinance — market and stock data
* FAISS — vector similarity search
* DuckDuckGo — web/financial information retrieval

# Data Validation

* **Pydantic**
* Structured outputs
* Schema validation

---

# Project Structure

agentic-finance-assistant/
│
├── agents/
│   ├── planner.py
│   ├── executor.py
│   ├── aggregator.py
│   └── ...
│
├── mcp_servers/
│   ├── finance_server.py
│   ├── search_server.py
│   ├── retrieval_server.py
│   └── ...
│
├── llm/
│   ├── provider.py
│   ├── groq.py
│   ├── openai.py
│   └── ollama.py
│
├── models/
│   └── schemas.py
│
├── tools/
│   ├── yfinance_tools.py
│   ├── search_tools.py
│   └── faiss_tools.py
│
├── config/
│   └── settings.py
│
├── main.py
├── requirements.txt
└── README.md

> Adapt the structure above to match the actual repository structure.

---

# Example Workflow

A user asks:

> "Should I invest in NVIDIA right now? Analyze its recent performance and major news."

The system can decompose the query into:

Planner
   │
   ├──→ Market Agent
   │       └──→ yFinance
   │
   ├──→ News Agent
   │       └──→ DuckDuckGo
   │
   ├──→ Research Agent
   │       └──→ FAISS
   │
   └──→ Analysis Agent
           └──→ LLM

The outputs are then aggregated:

Market Data
     +
News
     +
Retrieved Context
     +
LLM Analysis
     ↓
Aggregator
     ↓
Structured Financial Insight

# Scalability

The architecture is designed around asynchronous and parallel agent execution.

Instead of waiting for each independent task to finish sequentially, the executor can dispatch multiple agents concurrently.

This enables the system to support 100+ concurrent agent executions while keeping the architecture modular and horizontally scalable.

---

# Installation

# 1. Clone the repository

```bash
git clone https://github.com/<your-username>/<repository-name>.git
cd <repository-name>
```

# 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it:

**Windows**

```bash
venv\Scripts\activate
```

**Linux/macOS**

```bash
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a `.env` file:

```env
GROQ_API_KEY=your_groq_api_key
OPENAI_API_KEY=your_openai_api_key
```

Ollama can be configured for local model inference without requiring a cloud API key.

---

# Running the Application

```bash
python main.py
```

If the project uses separate MCP servers, start the required servers before launching the agent orchestration layer.

```bash
python mcp_servers/finance_server.py
python mcp_servers/search_server.py
python main.py
```

> Update these commands according to the actual entry points in the repository.

---

# Environment Variables

| Variable         | Description                          |
| ---------------- | ------------------------------------ |
| `GROQ_API_KEY`   | API key for Groq inference           |
| `OPENAI_API_KEY` | API key for OpenAI models            |
| `OLLAMA_HOST`    | Optional Ollama server configuration |

**Never commit API keys or `.env` files to GitHub.**

Add:

```text
.env
venv/
__pycache__/
```

to `.gitignore`.

---

# Design Goals

The project was built around four major principles:

### 1. Modularity

Each agent and MCP tool has a well-defined responsibility.

# 2. Model Independence

The application should not be tightly coupled to a single LLM provider.

# 3. Parallelism

Independent financial tasks should execute concurrently instead of sequentially.

# 4. Reliability

Agent communication should use validated, structured data rather than unrestricted natural-language outputs.

---

#Future Improvements

* [ ] Portfolio tracking and personalized risk profiling
* [ ] Expense categorization and budgeting
* [ ] Real-time portfolio alerts
* [ ] Historical backtesting
* [ ] More financial data providers
* [ ] Persistent user profiles and preferences
* [ ] Authentication and authorization
* [ ] Observability and agent tracing
* [ ] Evaluation framework for agent responses
* [ ] Docker-based deployment
* [ ] Cloud-native horizontal scaling

---

# Disclaimer

This project is intended for **educational and informational purposes only**.

The financial insights generated by the system should not be considered professional investment, financial, or trading advice. Always verify financial information using reliable sources and consult a qualified financial professional before making investment decisions.



# Author
Mansi Meena

Built as a self-project to explore:

* Agentic AI
* Multi-agent systems
* Model Context Protocol (MCP)
* LLM orchestration
* Parallel AI workflows
* Financial data retrieval
* Structured AI outputs



