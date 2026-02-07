
<br />

<h1 align="center">Workflow Use - Deterministic, Self Healing Workflows</h1>

<p align="center">
  <strong>RPA 2.0: Record once, reuse forever</strong><br/>
  <em>Build deterministic, self-healing browser automation workflows</em>
</p>

<p align="center">
  <a href="#features">Features</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#installation">Installation</a> •
  <a href="#usage">Usage</a> •
  <a href="#vision--roadmap">Roadmap</a>
</p>

---

> ⚠️ **Early Development**: This project is actively being developed. While functional, features may change and the API is not yet stable. Use at your own risk in production environments.

---

## About

Workflow Use enables you to record browser interactions once and transform them into reusable, deterministic workflows. Instead of constantly re-prompting AI to perform the same tasks, define it once and execute it reliably, with automatic self-healing capabilities when things change.

## 🎯 Features

- 🔁 **Record Once, Reuse Forever**: Record browser interactions once and replay them indefinitely
- ⏳ **Show, Don't Prompt**: Eliminate repetitive AI prompts for the same tasks
- ⚙️ **Structured & Executable**: Convert recordings into deterministic, fast, and reliable workflows that automatically extract variables
- 🪄 **Intelligent Interaction Filtering**: Automatically removes noise to create meaningful, reusable workflows
- 🔒 **Enterprise-Ready**: Self-healing capabilities and workflow diffs built in
- ☁️ **Cloud Browser Support**: Run workflows in Browser-Use Cloud with semantic abstraction

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/nommnanony/workflow-use
cd workflow-use

# Setup Python environment
cd workflows
uv sync
source .venv/bin/activate  # or .venv\Scripts\activate on Windows
playwright install chromium

# Configure environment
cp .env.example .env
# Add your OPENAI_API_KEY to the .env file
```

### Generate a Workflow from Natural Language

```bash
python cli.py generate-workflow "Find GitHub stars for browser-use repo"
```

### List & Run Workflows

```bash
# List all workflows
python cli.py list-workflows

# Run a stored workflow
python cli.py run-stored-workflow <workflow-id>
```

### Record Your Own Workflow

```bash
python cli.py create-workflow
```

---

## ✨ NEW: Generation Mode

Automatically generate workflows from natural language! Describe your task, we run browser-use once, then create a reusable semantic workflow stored in a database.

### Quick Commands

```bash
# Generate workflow from task description
python cli.py generate-workflow "Find GitHub stars for browser-use repo"

# List all workflows
python cli.py list-workflows

# Filter by generation mode
python cli.py list-workflows --generation-mode browser_use

# Run stored workflow
python cli.py run-stored-workflow <workflow-id> --prompt "Find stars for playwright repo"

# View workflow details
python cli.py workflow-info <workflow-id>

# Delete workflow
python cli.py delete-workflow <workflow-id>
```

### How It Works

1. **Describe**: Give a task in natural language
2. **Execute**: Browser-use completes the task once
3. **Generate**: Execution history → semantic workflow with parameters
4. **Store**: Save to database with metadata
5. **Reuse**: Run the workflow with different inputs, no AI needed

### Advanced Options

```bash
# Custom models for generation
python cli.py generate-workflow "Your task" \
  --agent-model "gpt-4.1-mini" \
  --extraction-model "gpt-4.1-mini" \
  --workflow-model "gpt-4o"

# Use Browser-Use Cloud browser
python cli.py generate-workflow "Your task" --use-cloud

# Save to custom location
python cli.py generate-workflow "Your task" --output-file ./my-workflow.json

# Skip database storage
python cli.py generate-workflow "Your task" --no-save-to-storage
```

### Storage

Workflows stored at `workflows/storage/`:
- `metadata.json` - Searchable index of all workflows
- `workflows/<id>.workflow.json` - Individual workflow files

### Programmatic Usage

```python
from workflow_use.healing.service import HealingService
from workflow_use.storage.service import WorkflowStorageService
from browser_use.llm import ChatOpenAI

healing_service = HealingService(llm=ChatOpenAI(model='gpt-4.1'))
storage_service = WorkflowStorageService()

# Generate workflow
workflow = await healing_service.generate_workflow_from_prompt(
    prompt="Fill contact form on example.com",
    agent_llm=ChatOpenAI(model='gpt-4.1-mini'),
    extraction_llm=ChatOpenAI(model='gpt-4.1-mini'),
    use_cloud=True  # Optional: use Browser-Use Cloud
)

# Save to storage
metadata = storage_service.save_workflow(
    workflow=workflow,
    generation_mode='browser_use',
    original_task="Fill contact form on example.com"
)

# Retrieve and execute
loaded_workflow = storage_service.get_workflow(metadata.id)
```

## 📋 Detailed Setup & Usage

### Build the Extension

```bash
cd extension && npm install && npm run build
```

### Setup Workflow Environment

```bash
cd workflows
uv sync
source .venv/bin/activate # for macOS/Linux
# .venv\Scripts\activate on Windows
playwright install chromium
cp .env.example .env # add your OPENAI_API_KEY
```

### Run Workflows

```bash
# Run as tool (with AI assistance)
python cli.py run-as-tool examples/example.workflow.json --prompt "fill the form with example data"

# Run with predefined variables
python cli.py run-workflow examples/example.workflow.json

# Record your own workflow
python cli.py create-workflow

# See all commands
python cli.py --help
```

### Python Integration

```python
from workflow_use import Workflow
import asyncio

# Load and run workflow
workflow = Workflow.load_from_file("example.workflow.json")
result = asyncio.run(workflow.run_as_tool("I want to search for 'workflow use'"))
```

### Cloud Browser Support

Run workflows in [Browser-Use Cloud](https://cloud.browser-use.com) with semantic abstraction:

```python
from workflow_use import Workflow

workflow = Workflow.load_from_file("workflow.json", llm, use_cloud=True)
result = await workflow.run_with_no_ai()  # No LLM calls, uses semantic mapping
```

## 🎨 Workflow GUI

The Workflow UI provides a visual interface for managing, viewing, and executing workflows.

### Launch with CLI (Recommended)

```bash
cd workflows
python cli.py launch-gui
```

This will:
- Start the backend server (FastAPI)
- Start the frontend development server
- Open http://localhost:5173 automatically
- Capture logs to `./tmp/logs`

Press Ctrl+C to stop both servers.

### Start Servers Separately

**Backend:**
```bash
cd workflows
uvicorn backend.api:app --reload
```

**Frontend:**
```bash
cd ui
npm install
npm run dev
```

Then visit http://localhost:5173 in your browser.

### GUI Features

- 📊 Visualize workflows as interactive graphs
- ▶️ Execute workflows with custom input parameters
- 📝 Monitor workflow execution logs in real-time
- ✏️ Edit workflow metadata and details

---

## 🌟 Vision & Roadmap

**Core Mission**: Show the computer what it needs to do once, and it will do it over and over again without any human intervention.

### Workflow Improvements

- [ ] Streamlined API for using `.json` files in Python
- [ ] Better LLM fallback mechanisms  
- [ ] Automatic self-healing when workflows fail
- [ ] Enhanced LLM step support
- [ ] Output chaining across workflow steps
- [ ] Expose workflows as MCP tools
- [ ] Auto-generate workflows from website behavior

### Developer Experience

- [ ] Enhanced CLI interface
- [ ] Improved browser extension
- [ ] Visual step editor

### Agent Integration

- [ ] Allow Browser Use to leverage workflows as MCP tools
- [ ] Use workflows as intelligent website caching layer

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## 💬 Support & Community

For questions, issues, or discussions:
- Open an [Issue](https://github.com/nommnanony/workflow-use/issues)
- Start a [Discussion](https://github.com/nommnanony/workflow-use/discussions)

---

Built with ❤️ for automating repetitive browser tasks
