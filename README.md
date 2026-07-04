# Using LangGraph with a local LLM via LM Studio

---

## Attribution: 

https://github.com/guozheng/hello-langgraph

Thanks, guozheng!

---

This projects shows how to build an AI workflow with LM Studio, Qwen and LangGraph to better understand how LangGraph works. All with 165 lines of code in `main.py`. Totally amazing!

I had `qwen/qwen3.5-9b` already up and running in LM Studio, so I didn't use guozheng's specified `qwen3-4b-2507` llm, although it is a non-thinking model and may be faster. I'm still learning which model types are most suitable for which tasks and wondering if a multi-modal model is necessary for this experiment.

## Setup Flow

### Install LM Studio

Download from [https://lmstudio.ai](https://lmstudio.ai) and install.

### Install LLM

Open LM Studio and click the search for model button and select an appropriate model. I used Qwen3.5 9B for my machine. Pick a model based on your VRAM (e.g., Qwen3.5 9B is a solid choice). Make sure the format is `GGUF`. Download the model. Depending on your network speed this may take a while.

### Start the Local Server

Go to the Developer panel, load your model and turn on your server. I had to flick the status switch from `Stopped` to `Running`; this may change in the future.
Your local server runs on http://localhost:1234 by default.

### Install uv

Command-line time - open one up.

Instructions are [here](https://docs.astral.sh/uv/getting-started/installation/).

I used `brew install uv`.

### Create a project

`uv init langgraph-hello-world`

and `cd` into the newly created directory

I copied guozheng's `main.py` and LICENSE file. I made a few changes but most of the `main.py` remains true to the original. 

### Install dependencies

`uv tool install langgraph-cli`

Then we update the shell and start a new terminal session.

`uv tool update-shell`

### To run

`uv run main.py`

and this is what you are presented with:

🚀 Hello from LangGraph!
Choose an option:
1. Interactive conversation
2. Demo workflow
3. Exit

Enter your choice (1-3):


Here is the output from the demo workflow where the **User** prompts are canned.:

Enter your choice (1-3): 2
🔄 Running LangGraph Workflow Demo
----------------------------------------
User: Hello! Can you introduce yourself?

Bot:

Hello! 👋 I'm **Qwen3.5**, the latest large language model developed by Tongyi Lab. I'm here to assist you with tasks like:
- 🧠 **Answering questions** (from trivia to complex topics)
- ✍️ **Writing & editing** (emails, stories, code, etc.)
- 🔍 **Analyzing documents** or images (I can read charts, diagrams, and text!)
- 💻 **Coding help** (debugging, explaining snippets, or even building projects)
- 🌐 **Multilingual support** (fluently in over 100 languages)

Just let me know what you need! 😊 What would you like to tackle today?

User: What is LangGraph?
Bot:

**LangGraph** is a powerful framework within the **LangChain ecosystem** designed for building **stateful, multi-actor applications** with large language models (LLMs). It focuses on creating **complex workflows** that go beyond simple linear chains, enabling structured control over how AI agents or tasks interact, iterate, and collaborate. Here's a breakdown of what makes it unique:

---

### 🔑 **Core Concepts**
1. **Graph-Based Workflows**
   - Defines workflows as **directed graphs**, where each **node** represents an operation (e.g., LLM call, function, tool use) and **edges** define control flow (sequential, conditional, or parallel).
   - Supports **loops**, branching logic, and dynamic routing based on state.

2. **State Management**
   - Maintains a shared **state object** across nodes, allowing data to persist and evolve as the workflow progresses.
   - Critical for multi-step tasks (e.g., "research → summarize → draft → review → refine").

3. **Multi-Agent Coordination**
   - Enables multiple agents or modules to collaborate (e.g., one agent researches, another critiques, a third finalizes).
   - Each agent can access shared state and trigger actions based on conditions.

---

### 🧠 **Why Use LangGraph?**
- **Complex Workflows**: Ideal for tasks requiring iteration (e.g., refining code, multi-turn dialogue, or human-in-the-loop approvals).
- **Control Flow**: Unlike linear chains, you can define custom paths (e.g., "if confidence < 90%, re-query the model").
- **Scalability**: Modular design makes it easier to debug, extend, or parallelize parts of a workflow.
- **Human Interaction**: Supports integrating human feedback into loops (e.g., "user approves draft → proceed to final output").

---

### 🛠️ **Example Use Case**
Imagine building a **research assistant** that:
1. Searches for relevant papers (Node 1).
2. Summarizes findings (Node 2).
3. Checks summaries for accuracy (Node 3).
4. If inaccurate, loops back to refine the search (Edge from Node 3 → Node 1).

LangGraph handles the **state** (e.g., storing intermediate results) and **control flow** (e.g., looping conditionally) seamlessly.

---

### ⚖️ **LangGraph vs. LangChain Chains**
- **LangChain Chains**: Linear, stateless steps (e.g., "prompt → model → output").
- **LangGraph**: Graph-based, stateful, and supports loops/branching for complex logic.

Think of it as upgrading from a conveyor belt (chains) to a flexible assembly line with decision points (graphs).

---

### 📦 **Key Tools**
- `StateGraph`: Defines the workflow structure.
- `MessageGraph`: For chat-based interactions.
- `RunnableGraph`: To execute workflows asynchronously.

---

LangGraph is especially useful when you need **memory**, **iteration**, or **collaborative agents** in your AI application. Want to dive deeper into a specific use case or code example? 😊

User: Can you explain state management in workflows?
Bot:

**State management** is the backbone of any workflow system that handles multi-step tasks, loops, or branching logic. In the context of **LangGraph**, it refers to how a workflow remembers what has happened so far and passes data between steps, enabling intelligent behavior like iteration, persistence, and coordination across agents.

---

### 🧠 What is State?
**State** is the evolving data that persists throughout a workflow. It captures:
- **Intermediate results** (e.g., search queries, summaries)
- **Control signals** (e.g., confidence scores, decision flags)
- **Context** (e.g., conversation history, user preferences)
- **Metadata** (e.g., step numbers, timestamps, error logs)

---

### 🔄 Why is State Important?

Without state, each workflow step would be isolated and unable to build on previous results. With state, workflows can:
1. **Remember past actions** → Enables iteration and refinement.
2. **Make decisions based on history** → Allows conditional branching.
3. **Coordinate multiple actors** → Agents share information for collaboration.
4. **Support long-running tasks** → State survives interruptions or checkpoints.

---

### 🧩 Example: A Research Workflow with State

| Step        | Input (from state)          | Output (to state)       | Action                        |
|-------------|-----------------------------|--------------------------|-------------------------------|
| 1. Search   | `query = "AI trends"`       | `papers`                 | Fetch relevant documents      |
| 2. Summarize| `papers`                    | `summary`                | Generate summaries            |
| 3. Review   | `summary`                   | `confidence_score`       | Evaluate quality              |
| 4. Final    | `if confidence < 80%`       | `final_report`           | Loop back or finalize         |

**State evolution:**
- Initial: `{ query: "AI trends" }`
- After Step 1: `{ papers: [...] }`
- After Step 2: `{ summary: "...", papers: [...] }`
- After Step 3: `{ summary: "...", confidence: 75% }`
- Decision: **Loop back** to Step 1 with updated query

---

### ⚙️ How State Works in LangGraph

In LangGraph, state is managed by a **State object** that persists through nodes and edges. You can define it using Pydantic models for type safety:

```python
from typing import List, Optional
from pydantic import BaseModel

class Document(BaseModel):
    title: str
    summary: str

class ResearchState(BaseModel):
    query: str
    papers: List[Document] = []
    summary: Optional[str] = None
    confidence: float = 100.0
    step: int = 0
```

- **Nodes read/write to state** → Each operation modifies or queries the shared state.
- **Edges define flow** → Control logic determines what happens next based on state values.
- **State persists across steps** → Even after interruptions, the last known state is restored.

---

### 🧭 State vs. Stateless Workflows

| Feature           | Stateless Workflow       | Stateful Workflow (LangGraph)      |
|-------------------|--------------------------|------------------------------------|
| Memory            | ❌ No                    | ✅ Yes                              |
| Loops             | ❌ Not supported         | ✅ Yes                              |
| Branching         | ❌ Linear only           | ✅ Conditional logic based on state |
| Multi-agent       | ❌ Limited               | ✅ Shared state for coordination    |
| Debugging         | Hard to track progress   | Easy via state snapshots            |

---

### 🛠️ Common Use Cases

- **Chatbots:** Store conversation history so the model remembers context.
- **Research Assistants:** Keep research results and refine based on feedback.
- **Code Generation:** Track changes across multiple edits and iterations.
- **Human-in-the-loop:** Allow humans to approve or reject steps while preserving progress.

---

### ⚠️ Best Practices

1. **Keep state relevant** → Don't store unnecessary data that bloats memory.
2. **Version your state** → Track changes for debugging and recovery.
3. **Validate types** → Use Pydantic models to ensure consistency.
4. **Handle updates carefully** → Avoid unintended side effects when modifying state.

---

### 🧪 Quick Example: ChatBot with Memory

```python
from langgraph.graph import StateGraph, END

class ChatState(BaseModel):
    messages: List[dict] = []
    summary: Optional[str] = None

# Define workflow
builder = StateGraph(ChatState)
builder.add_node("chat", chat_node)
builder.add_edge("start", "chat")
builder.add_edge("chat", END)

workflow = builder.compile()
```

Here, each message is appended to the `messages` list in state, so future responses can reference earlier context.

---

In short, **state management** gives workflows memory and logic. It's what transforms isolated steps into intelligent, adaptable systems—especially important when building agents or complex AI applications with LangGraph.

Would you like a deeper dive into a specific part of state design (e.g., persistence, versioning, or debugging)? 😊

