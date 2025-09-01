---
layout: post
title: "Building Your First Agent API with Smol Agents + FastAPI"
date: 2025-09-01 22:41:30 +0530
categories: ai agents crypto
image: /assets/images/agent-tutorial-1.png
---

![Building Your First Agent API with Smol Agents + FastAPI](/assets/images/agent-tutorial-1.png)
# 🧮 Building Your First Agent API with Smol Agents + FastAPI

AI agents don’t need to be heavy or complicated.  
With [smolagents](https://huggingface.co/docs/smolagents) + FastAPI you can build a small, working agent in minutes.  

In this guide, we’ll build a **Maths Agent API** that accepts a math problem and returns the answer.  


## 1. Setting up the environment

We’ll use **UV** as the package manager (faster and cleaner than pip).  

### Install UV

```bash
# On macOS and Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# On Windows
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Or, from PyPI:

```bash
# With pip
pip install uv

# Or with pipx
pipx install uv
```

Update to the latest version:

```bash
uv self update
```

### Create a virtual environment

```bash
uv venv .venv
source .venv/bin/activate
```

### Install dependencies

```bash
uv pip install "smolagents[toolkit]"
uv pip install fastapi uvicorn
```

- **smolagents** → the library that helps us create agents.  
- **fastapi** → the web framework to expose our agent as an API.  
- **uvicorn** → the server to run our FastAPI app.  


## 2. Writing the agent logic (`agent.py`)

```python
from smolagents import CodeAgent, InferenceClientModel

def build_agent(question):
    # Load a default model (Hugging Face Inference API)
    model = InferenceClientModel()

    # Create a CodeAgent with no extra tools (just pure reasoning)
    agent = CodeAgent(tools=[], model=model)

    # Run the agent on the user’s problem
    result = agent.run(question.problem)
    return result
```

### What’s happening here?
- `InferenceClientModel()` → connects to Hugging Face’s hosted models (no local weights needed).  
- `CodeAgent()` → a basic agent that can reason through problems.  
- `tools=[]` → we didn’t add any external tools yet (e.g., APIs, calculators). The agent only uses the model.  
- `agent.run()` → executes the reasoning loop to solve the problem.  


## 3. Building the API (`app.py`)

```python
from agent import build_agent
from pydantic import BaseModel
from fastapi import FastAPI

app = FastAPI(title="Maths Agent API")

# Define the input schema
class Query(BaseModel):
    problem: str

# Define the /solve endpoint
@app.post("/solve")
def solve(q: Query):
    result = build_agent(q)
    return {"question": q, "answer": result}
```

### Breakdown:
- `FastAPI(title=...)` → gives our API a name.  
- `Query` → defines the expected JSON input (`problem` field).  
- `/solve` endpoint → takes the input, passes it to our agent, and returns the result.  


## 4. Running the server

Start the API with:

```bash
uvicorn app:app --reload
```

- `app:app` → first `app` is the filename (`app.py`), second `app` is the FastAPI instance inside it.  
- `--reload` → auto-restarts the server when you change code.  

Your API is now live at:  
[http://localhost:8000/solve](http://localhost:8000/solve)  


## 5. Testing the Maths Agent

Send a POST request:

```bash
curl -X POST http://localhost:8000/solve   -H "Content-Type: application/json"   -d '{"problem":"If a=12 and b=5 what is a*b + 3?"}'
```

### Expected response:

```json
{
  "question": {"problem": "If a=12 and b=5 what is a*b + 3?"},
  "answer": 63
}
```

The agent receives the question, uses the model to compute the answer, and sends it back in JSON.  


## 6. Why this works

- **Smol Agents** gives you the *brains* (reasoning + tool calling).  
- **FastAPI** gives you the *interface* (simple REST API).  
- **uv** keeps dependencies clean.  

This structure makes it easy to:  
- Add more tools (e.g., a calculator, web search).  
- Switch models (OpenAI, Transformers, vLLM).  
- Deploy anywhere (Docker, cloud, etc.).  


## Next Steps

- Add a **calculator tool** so the agent can handle harder math without relying only on the model.  
- Extend to other tasks (weather API, text summarizer, code runner).  
- Wrap it in a **UI of your choice** (Gradio, Streamlit, etc.).  


##  Key Takeaway

With less than **50 lines of code**, you built a working AI agent that runs inside a FastAPI server.  
That’s the power of **small, composable agents**.  


##  Source Code

🔗 [Full source code on GitHub](https://github.com/SKSudharsanan/Math-agent)  


For more short tutorials in **AI** and **Blockchain**, follow [@bearonchain](https://x.com/bearonchain)  

Comment what tutorial I should cover next!  
