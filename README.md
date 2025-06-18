## Tutorial: Building AI Agentic Applications with BeeAI, watsonx Orchestrate, and ICA

This comprehensive tutorial guides you step-by-step through three powerful frameworks for developing, deploying, and governing AI-driven agents and Agentic Applications:

* **BeeAI Framework & Platform**
* **IBM watsonx Orchestrate Agent Development Kit (ADK)**
* **ICA for Agentic Applications**

Each section includes prerequisites, installation, core concepts, hands-on labs, and best practices.

---

### Table of Contents

1. [BeeAI Tutorial](#beeai-tutorial)

   * 1.1 Prerequisites
   * 1.2 Installation & Setup
   * 1.3 Defining Your First Agent
   * 1.4 Orchestrating Multiple Agents
   * 1.5 Deployment on IBM Cloud Pak for Data
   * 1.6 Troubleshooting & Next Steps

2. [watsonx Orchestrate ADK Tutorial](#watsonx-orchestrate-adk-tutorial)

   * 2.1 Prerequisites
   * 2.2 Installation & CLI Configuration
   * 2.3 Creating and Testing a Simple Agent
   * 2.4 Defining Tools and Connections
   * 2.5 Local Development vs. Cloud Deployment
   * 2.6 CI/CD and Integration with Channels

3. [ICA for Agentic Applications Tutorial](#ica-for-agentic-applications-tutorial)

   * 3.1 Overview & Architecture
   * 3.2 Prerequisites
   * 3.3 Getting Started with Agentic Apps Library
   * 3.4 Building a New Agentic App
   * 3.5 Orchestration & Data Product Integration
   * 3.6 Governance, Observability & Security
   * 3.7 Contribution Guidelines & Community

---

## 1. BeeAI Tutorial

### 1.1 Prerequisites

* Python 3.9+ environment
* Poetry or pip for dependency management
* Access to an IBM Cloud Pak for Data instance (optional for local run)
* Git client

### 1.2 Installation & Setup

1. **Clone the BeeAI workshop repository**:

   ```bash
   git clone https://github.com/your-org/beeai-workshop.git
   cd beeai-workshop
   ```
2. **Install dependencies** via Poetry:

   ```bash
   poetry install
   ```

   Or with pip:

   ```bash
   pip install -r requirements.txt
   ```
3. **Configure environment** by copying and editing `.env.template`:

   ```bash
   cp .env.template .env
   # Fill in CP4D credentials and LLM provider keys
   ```

### 1.3 Defining Your First Agent

1. **Locate the Framework module**:

   ```python
   from beeai_framework import BaseAgent, AgentConfig
   ```
2. **Create a new agent class** (e.g., a triage agent):

   ```python
   class TicketTriageAgent(BaseAgent):
       def run(self, data: dict) -> dict:
           # classify urgency
           return {"urgency": "high"}
   ```
3. **Configure the agent** in YAML or JSON under `agents/triage_agent.yaml`:

   ```yaml
   id: ticket-triage
   model: gpt-4
   input_schema: TriageInput
   output_schema: TriageOutput
   ```
4. **Execute locally**:

   ```bash
   python -m beeai run-agent --config agents/triage_agent.yaml
   ```

### 1.4 Orchestrating Multiple Agents

1. **Define a Workflow Agent** that lists sub-agents and transitions:

   ```python
   from beeai_framework import WorkflowAgent

   class TicketWorkflowAgent(WorkflowAgent):
       agents = ["ticket-triage", "ticket-response"]
   ```
2. **Provide orchestration logic** in `agents/workflow_agent.py` and config in `agents/workflow_agent.yaml`.
3. **Run end-to-end**:

   ```bash
   python -m beeai run-workflow --config agents/workflow_agent.yaml
   ```

### 1.5 Deployment on IBM Cloud Pak for Data

1. \*\*Use \*\*\`\` to provision a deployment space:

   ```python
   from acp_sdk import DeploymentManager
   dm = DeploymentManager()
   dm.create_space("beeai-agents")
   ```
2. **Push agent images** via Docker and register under CP4D’s AI Catalog.
3. **Create deployments** and bind secrets for LLM provider keys.

### 1.6 Troubleshooting & Next Steps

* Check logs via `cp4d-ui` or `kubectl logs`.
* Extend agents by implementing custom schemas and validators.
* Explore advanced orchestration features: retries, branching, parallelism.

---

## 2. watsonx Orchestrate ADK Tutorial

### 2.1 Prerequisites

* Python 3.9+
* Docker (for local developer edition)
* IBM Cloud account with watsonx Orchestrate entitlement
* API Key or IAM service credentials

### 2.2 Installation & CLI Configuration

1. **Install the ADK**:

   ```bash
   pip install ibm-watsonx-orchestrate-adk
   ```
2. **Initialize the CLI**:

   ```bash
   orchestrate env init
   # provide API key and region
   ```

### 2.3 Creating and Testing a Simple Agent

1. **Generate an agent scaffold**:

   ```bash
   orchestrate agents create my-first-agent --description "Hello world agent"
   ```
2. **Implement **\`\`** logic** in `agents/my-first-agent/agent.py`:

   ```python
   class MyFirstAgent(Agent):
       def run(self, request):
           return {"message": "Hello, world!"}
   ```
3. **Test locally**:

   ```bash
   orchestrate agents test my-first-agent
   ```

### 2.4 Defining Tools and Connections

1. **Add a tool**:

   ```bash
   orchestrate tools add email_sender --template rest
   ```
2. **Configure connection** for API keys:

   ```bash
   orchestrate connections create email-conn --type apikey --params '{"url":"https://api.email.com","apikey":"..."}'
   ```
3. **Reference tool in agent YAML**:

   ```yaml
   tools:
     - name: email_sender
       connection: email-conn
   ```

### 2.5 Local Development vs. Cloud Deployment

* **Local developer edition**: uses Docker containers to run orchestrate services on localhost.
* **Cloud**: use `orchestrate import` and `orchestrate deploy` to push definitions to IBM Cloud.

### 2.6 CI/CD and Integration with Channels

* Use the REST APIs under `/v2/agents` to automate deployment in pipelines.
* Connect agents to Slack or Teams via channel integrations defined in YAML.

---

## 3. ICA for Agentic Applications Tutorial

### 3.1 Overview & Architecture

ICA for Agentic Applications is IBM Consulting’s modular platform to accelerate Agentic App delivery. It comprises:

* **Agentic Apps Library**: Pre-built templates for business processes (Source-to-Pay, Customer Service, Regulatory Approval)
* **ICA Core**: Multi-agent orchestration, UI integration, Data Product connectivity
* **Governance Layer**: Observability, security, compliance controls

### 3.2 Prerequisites

* Node.js 14+ and npm (for UI assets)
* Python 3.9+ (for backend orchestration)
* Access to client’s cloud (Azure, AWS, GCP, IBM Cloud)
* IBM Consulting Advantage (ICA) credentials

### 3.3 Getting Started with the Agentic Apps Library

1. **Browse available templates** in the `agentic-apps-library/` directory.
2. **Clone a template** for Account Payable:

   ```bash
   git clone https://repo/ica/agentic-apps-library/account-payable.git
   cd account-payable
   ```
3. **Install dependencies**:

   ```bash
   npm install      # UI
   pip install -r requirements.txt  # backend
   ```
4. **Configure** `.env` with Data Product endpoints and credentials.

### 3.4 Building a New Agentic App

1. **Scaffold a new app** using the boilerplate generator:

   ```bash
   node scripts/scaffold --name my-app --type service-ordering
   ```
2. **Define agents** under `agents/` following templates:

   * Foundational agents: text extraction, classification
   * Functional agents: process-specific logic
3. **Customize UI** by editing `ui/src/components/ChatInterface.jsx` to hook into new intents.

### 3.5 Orchestration & Data Product Integration

1. **Define workflow** in `core/workflows/order_workflow.yaml`:

   ```yaml
   steps:
     - agent: classify_order
     - agent: validate_data
     - agent: send_confirmation
   ```
2. **Register Data Products** in `core/dataproducts.yaml`:

   ```yaml
   products:
     - name: OrdersDB
       type: relational
       connection: orders-conn
   ```
3. **Deploy orchestration** via CLI or Terraform modules included in `infra/`.

### 3.6 Governance, Observability & Security

1. **Enable observability** by configuring logs and metrics collection in `governance/observability.yaml`.
2. **Set guardrails** in `governance/policies.yaml`:

   ```yaml
   policies:
     - name: max-response-length
       rule: response.length < 1000
   ```
3. **Integrate IAM** and FinOps controls via existing `ica-core` modules.

### 3.7 Contribution Guidelines & Community

* **Branching model**: follow `feature/`, `bugfix/`, `release/` conventions
* **Templates**: use page and agent scaffolding tools in `development/`
* **Review process**: open PR against `main`, assign AIIS Offering Leader and CTO
* **Community contacts**:

  * Francesco Brenna (Offering Leader)
  * Janorious Rabeela (CTO)
  * Moustapha Karaki (Microsoft)
  * Chayan Ray (AWS)
  * Monisankar Das (IBM)
  * Pradeep Raj (NVIDIA/GCP)
  * Muthu Mangai Muthiah (AI Product)
  * Daniel Ehmann (Agentic Apps)
  * Malina Negru (Product)

---

Congratulations! You now have a complete tutorial for BeeAI, watsonx Orchestrate ADK, and ICA for Agentic Applications. Follow each section’s labs and guides to rapidly prototype, build, and deploy your own AI-driven Agentic Apps.
