## Comprehensive Step-by-Step Tutorial: BeeAI, watsonx Orchestrate, & ICA for Agentic Applications

This end-to-end tutorial takes you from zero to hero by building **Hello World** demos and lab exercises for three leading AI-agent frameworks:

1. **BeeAI Framework & Platform**
2. **IBM watsonx Orchestrate ADK**
3. **ICA for Agentic Applications**

Each section includes:

* Prerequisites
* Environment setup
* Code walk-through (with full Python scripts)
* Hello World lab
* Advanced extension ideas

---

### Table of Contents

1. [BeeAI Tutorial](#1-beeai-tutorial)
2. [watsonx Orchestrate ADK Tutorial](#2-watsonx-orchestrate-adk-tutorial)
3. [ICA for Agentic Applications Tutorial](#3-ica-for-agentic-applications-tutorial)

---

## 1. BeeAI Tutorial

### 1.1 Prerequisites

* **Python** ≥ 3.9
* **Poetry** (or pip)
* **Git**
* Optional: IBM Cloud Pak for Data access

### 1.2 Setup

1. **Clone** the BeeAI repo:

   ```bash
   git clone https://github.com/your-org/beeai-workshop.git
   cd beeai-workshop
   ```
2. **Install** dependencies:

   ```bash
   poetry install  # or pip install -r requirements.txt
   ```
3. **Configure** credentials:

   ```bash
   cp .env.template .env
   # Edit .env: set CP4D_URL, CP4D_APIKEY, LLM_API_KEY
   ```

### 1.3 Hello World Agent

Create a simple agent that echoes input text.

1. **Create file** `agents/echo_agent.py`:

   ```python
   from beeai_framework import BaseAgent, AgentConfig, run_agent

   class EchoAgent(BaseAgent):
       def run(self, data: dict) -> dict:
           # Simply return the same message
           return {"echo": data.get("message", "")}

   if __name__ == "__main__":
       # Load config from YAML or inline
       config = AgentConfig(
           id="echo-agent",
           model="gpt-4",
           input_schema={"message": str},
           output_schema={"echo": str},
       )
       run_agent(EchoAgent, config)
   ```

2. **Run** it:

   ```bash
   python agents/echo_agent.py --input '{"message":"Hello BeeAI!"}'
   ```

**Expected Output**:

```json
{"echo":"Hello BeeAI!"}
```

### 1.4 Laboratory: Extending the Echo Agent

* **Task**: modify `EchoAgent` to reverse the string and uppercase it.
* **Solution**:

  ```python
  class TransformAgent(BaseAgent):
      def run(self, data: dict) -> dict:
          msg = data.get("message", "")
          transformed = msg[::-1].upper()
          return {"transformed": transformed}
  ```

### 1.5 Orchestration Hello World

1. **Create** `agents/workflow_hello.py`:

   ```python
   from beeai_framework import WorkflowAgent, run_workflow

   class HelloWorkflowAgent(WorkflowAgent):
       agents = ["echo-agent"]

   if __name__ == "__main__":
       run_workflow(HelloWorkflowAgent, input={"message":"Hi BeeAI Orchestration!"})
   ```
2. **Run**:

   ```bash
   python agents/workflow_hello.py
   ```

### 1.6 Deployment Outline

* Build Docker image: `docker build -t beeai/echo-agent .`
* Push to registry, register on CP4D AI Catalog
* Create deployment in IBM Cloud Pak for Data UI

---

## 2. watsonx Orchestrate ADK Tutorial

### 2.1 Prerequisites

* **Python** ≥ 3.9
* **Docker**
* watsonx Orchestrate entitlement & API key

### 2.2 Setup

```bash
pip install ibm-watsonx-orchestrate-adk
orchestrate env init  # follow prompts for API key & region
```

### 2.3 Hello World Agent

1. **Scaffold**:

   ```bash
   orchestrate agents create hw-agent --description "Hello World"
   ```
2. **Implement** `agents/hw-agent/agent.py`:

   ```python
   from orchestrate import Agent, io

   class HelloWorldAgent(Agent):
       def run(self, payload: io.Input) -> io.Output:
           text = payload.params.get("text", "")
           return io.Output(result=f"Hello, {text} from watsonx Orchestrate!")
   ```
3. **Test Locally**:

   ```bash
   orchestrate agents test hw-agent --params '{"text":"Agent"}'
   ```

**Expected**:

```
Result: Hello, Agent from watsonx Orchestrate!
```

### 2.4 Laboratory: Adding a Tool

* **Objective**: create a REST tool that appends a suffix.
* **Commands**:

  ```bash
  orchestrate tools add suffixer --template rest
  orchestrate connections create suffix-conn --type apikey --params '{"url":"https://api.suffixer.com","apikey":"XYZ"}'
  ```
* **Use** in agent YAML:

  ```yaml
  tools:
    - name: suffixer
      connection: suffix-conn
  ```

### 2.5 Deploy to Cloud

```bash
orchestrate import hw-agent
orchestrate deploy hw-agent --stage prod
```

---

## 3. ICA for Agentic Applications Tutorial

### 3.1 Prerequisites

* **Node.js** ≥ 14 + npm
* **Python** ≥ 3.9
* Cloud account (Azure/AWS/GCP/IBM)

### 3.2 Setup & Clone

```bash
git clone https://repo/ica/agentic-apps-library/account-payable.git
cd account-payable
npm install
pip install -r requirements.txt
cp .env.template .env  # configure Data Product endpoints
```

### 3.3 Hello World App: Account Payable Lite

1. **Backend**: create `agents/hello_ap_agent.py`:

   ```python
   from ica_core import BaseAgent

   class HelloAPAgent(BaseAgent):
       def run(self, context: dict) -> dict:
           return {"message": "Hello from Account Payable Agentic App!"}
   ```
2. **Workflow**: in `core/workflows/hello_ap.yaml`:

   ```yaml
   steps:
     - agent: hello-ap-agent
   ```
3. **UI**: add button in `ui/src/App.jsx`:

   ```jsx
   <button onClick={async ()=>{
     const res = await fetch('/api/run/hello-ap-agent', {method:'POST'})
     alert((await res.json()).message)
   }}>Say Hello</button>
   ```
4. **Run**:

   ```bash
   npm start    # UI on localhost:3000
   python core/server.py  # backend on 8000
   ```
5. **Test**: click button → alert with greeting.

### 3.4 Laboratory: Data Product Integration

* **Task**: connect to an Orders database and count records.
* **Demo Code** in `agents/order_count_agent.py`:

  ```python
  from ica_core import BaseAgent, DataProductClient

  class OrderCountAgent(BaseAgent):
      def run(self, context):
          db = DataProductClient('OrdersDB')
          count = db.query('SELECT COUNT(*) FROM orders')[0]
          return {"order_count": count}
  ```

### 3.5 Governance Quickstart

* **Observability**: configure `governance/observability.yaml`
* **Policies**: set `max-response-length` in `governance/policies.yaml`

### 3.6 Next Steps & Contribution

* Use scaffolding scripts (`npm run scaffold`)
* Follow branching and PR guidelines in `development/CONTRIBUTING.md`
* Reach out to:

  * Francesco Brenna (Offering Leader)
  * Janorious Rabeela (CTO)
  * Moustapha Karaki (Microsoft)
  * Chayan Ray (AWS)
  * Monisankar Das (IBM)
  * Pradeep Raj (GCP/NVIDIA)
  * Muthu Mangai Muthiah (Product)
  * Daniel Ehmann (Agentic Apps)
  * Malina Negru (Product)

---

You now have fully working **Hello World** demos and labs for each platform. Extend these examples by adding complex logic, integrating more data sources, and refining governance. Happy building!
