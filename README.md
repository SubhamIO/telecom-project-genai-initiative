# 📘 GenAI-Powered Customer Network Experience Analysis

## 🎯 Objective
To enhance customer understanding and retention, we developed a GenAI-based system that translates raw network KPI and SHAP values into meaningful **network experience scores** and **human-readable insights** for telecom users. This system enables business stakeholders to interpret customer satisfaction and act on churn risk proactively.

---

## 📌 Business Use Case
Telecom operators frequently struggle to:
- Interpret ML-based churn predictions in customer-centric language
- Connect network KPIs like RSRP/SINR to customer experience
- Explain score rationale to non-technical teams (marketing, support)
- Identify systemic issues causing low scores

Our solution addresses this gap by building an **automated, explainable, and scalable GenAI pipeline**.

---

## 🔧 Features

- **SQL Agent**: Converts user questions to SQL queries
- **Coder Agent**: Runs Python code and generates visualizations
- **Insights Agent**: Summarizes KPIs and SHAP insights in plain English
- **Supervisor Agent**: Routes user queries to the correct agent
- **Memory**: Maintains message history across agents
- **Streaming Output**: Live response streaming from agents

---

## 📁 Files Used

| File                                | Purpose                             |
| ----------------------------------- | ----------------------------------- |
| `customer_features_long_format.csv` | Contains KPI features for customers |
| `customer_scores_long_format.csv`   | Contains score data and SHAP values |
| `nqes.db`                           | SQLite DB created from CSVs         |

---

## 📦 Installation & Setup

```bash
# Clone the repo
git clone <your-repo-url>
cd <repo>

# Set up virtual environment
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows

# Install dependencies
pip install -r requirements.txt

# Set environment variables
cp .env.example .env
# Then edit .env to include GROQ_API_KEY and HF_TOKEN
```

---

## 🗃️ Data Preprocessing

Convert the CSV files to SQLite tables:

```python
csv_to_sqlite('customer_features_long_format.csv', 'nqes.db', 'customer_features_long_format')
csv_to_sqlite('customer_scores_long_format.csv', 'nqes.db', 'customer_scores_long_format')
```

---

## 🧠 Agents Overview

### 1. **Researcher Agent (SQL Agent)**

- Converts natural language questions into SQL queries
- Uses Groq LLaMA3-70B model
- Reads from two SQLite tables
- Custom prompt ensures safe query generation (no INSERT, DELETE, etc.)

### 2. **Coder Agent**

- Uses Python REPL to execute code, generate charts
- Pulls data using researcher agent
- Returns outputs or error messages

### 3. **Insights Generator Agent**

- Extracts values (scores, KPIs, SHAP) from researcher's response
- Generates business insights with an LLM
- Explains score contributors clearly

### 4. **Supervisor Agent**

- Routes user query to researcher, coder, or insights\_generator
- Rejects rude or irrelevant queries with `FINISH`
- Returns JSON: `{ "next": "researcher" }`

---

## 🧩 LangGraph State Definition

```python
class State(TypedDict):
    messages: Annotated[list, add_messages]
    next: str
    coder_task_done: bool
```

### Nodes:

- `researcher`, `coder`, `insights_generator`, `supervisor`

### Flow:

```
START → supervisor → researcher → supervisor
                             → coder → supervisor
                             → insights_generator → supervisor
                             → FINISH / END
```
---
### Langgraph Agent Architecture

<p align="center">
  <img src="https://github.com/SubhamIO/telecom-project-genai-initiative/blob/main/langgraph_agent_graph.png" width="800" alt="architecture">
</p>


---

---

## 🧪 Running the Pipeline

Use this function to interact with the system:

```python
def call_multi_agent_system(agent, prompt):
    events = agent.stream({"messages": [("user", prompt)]},
                          {"recursion_limit": 150},
                          stream_mode="values")

    for event in events:
        event["messages"][-1].pretty_print()

    display(Markdown(event["messages"][-1].content))
```

Example prompt:

```python
call_multi_agent_system(graph, "Show me a bar chart of total customers per experience score decile")
```

---

## 📊 Example Output

The system responds with markdown-formatted tables, insights, or live-generated charts depending on the agent path.

---

## 🧠 LLMs Used

- `llama3-70b-8192` via Groq API
- Groq models power all agents

---

## ✅ Best Practices

- Always validate queries with schema info
- Ensure `.env` is configured with valid keys
- Use prompt examples provided in the supervisor section

---

## 📃 License

MIT License

---

## 👨‍💻 Author

[Your Name]

---

## 📎 Appendix

### .env.example

```
GROQ_API_KEY=your_groq_api_key
HF_TOKEN=your_huggingface_token
```

---

## 📌 Future Enhancements

- Add Streamlit UI
- Include error handling agents
- Integrate feedback loop for agent correction

