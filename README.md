# Agents Development

A Python learning project for building and testing agentic AI workflows with LangChain.

This repository is structured as a crash-course workspace. It uses notebooks to explore model integrations, environment configuration, and a basic LangChain agent that can call a custom tool.

## What This Project Covers

- Python project setup with `uv`
- Environment variable loading with `python-dotenv`
- LangChain v1 basics
- Claude model usage through Anthropic
- Model integration experiments with multiple providers
- A simple tool-calling agent workflow

## Project Structure

```text
.
+-- langchain/
|   +-- 1-langchainintro.ipynb        # LangChain agent intro and tool calling
|   +-- 2-model-integration.ipynb     # Model integration experiments
+-- main.py                           # Minimal Python entry point
+-- pyproject.toml                    # Project metadata and dependencies
+-- requirements.txt                  # Dependency reference list
+-- uv.lock                           # Locked dependency versions
+-- .env.example                      # Example environment variables
+-- .gitignore                        # Local files excluded from tracking
+-- docs/
    +-- NOTES.md                      # Learning notes and command flow
```

## Requirements

- Python `3.13`
- `uv`
- VS Code, Jupyter, or another notebook runner
- API keys for the providers you want to test

## Environment Variables

Create a local `.env` file using `.env.example` as the template:

```env
ANTHROPIC_API_KEY=your_anthropic_key_here
GROQ_API_KEY=your_groq_key_here
GOOGLE_API_KEY=your_google_key_here
OPENAI_API_KEY=your_openai_key_here
OPENROUTER_API_KEY=your_openrouter_key_here
```

Only fill in the keys you need for the notebooks you are running.

## Notebooks

### `langchain/1-langchainintro.ipynb`

Introduces a basic LangChain agent. The notebook loads environment variables, defines a simple weather tool, creates an agent, invokes it with a user message, and reads the final response.

### `langchain/2-model-integration.ipynb`

Explores direct model initialization and invocation. This notebook is used to test provider integrations such as Claude, Gemini, Groq, OpenAI, and OpenRouter.

Useful working examples from this notebook:

```python
from langchain_anthropic import ChatAnthropic

model = ChatAnthropic(model="claude-haiku-4-5")
response = model.invoke("Hello What are the agents in agentic ai")
response.content
```

```python
from langchain.chat_models import init_chat_model

model = init_chat_model("google_genai:gemini-2.5-flash")
response = model.invoke("Hello What are the agents in agentic ai")
response.content
```

Notes:

- Use `ChatAnthropic`, not `Anthropic`, when importing from `langchain_anthropic`.
- If a Gemini model returns `404 NOT_FOUND`, switch to an available model such as `gemini-2.5-flash` or `gemini-2.5-pro`.

## Main Learning Flow

1. Set up the Python environment.
2. Add API keys to `.env`.
3. Select `.venv/bin/python` as the notebook kernel.
4. Run the model integration notebook.
5. Run the LangChain agent notebook.
6. Inspect the returned model and agent responses.

For step-by-step notes and commands, see [docs/NOTES.md](docs/NOTES.md).
