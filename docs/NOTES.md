# Agentic AI Crash Course Notes

These notes explain the full project flow from the beginning. They are written so you can come back later and understand exactly what you did, why you did it, and how the notebooks fit together.

This file does not cover Git. It focuses only on project setup, Python environment setup, packages, API keys, and notebook work.

## 1. Install `uv`

You installed `uv` with:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

What this does:

- Downloads the official `uv` installer script.
- Runs the installer with `sh`.
- Installs `uv`, which is a fast Python package and environment manager.

Why you used it:

- `uv` can create virtual environments.
- `uv` can install packages.
- `uv` can manage `pyproject.toml`.
- `uv` can create and update `uv.lock`.

After installing, you can check it with:

```bash
uv --version
```

If the terminal cannot find `uv`, restart the terminal or reload your shell.

## 2. Initialize The Project

You initialized the project with:

```bash
uv init
```

What this creates:

```text
pyproject.toml
main.py
README.md
.python-version
```

What each file means:

- `pyproject.toml`: main Python project configuration file.
- `main.py`: starter Python file.
- `README.md`: project overview.
- `.python-version`: Python version used by the project.

The project name in `pyproject.toml` is:

```toml
name = "agentic-ai-crashcourse"
```

The project currently uses:

```toml
requires-python = ">=3.13"
```

## 3. Create The Virtual Environment

You created the virtual environment with:

```bash
uv venv
```

What this creates:

```text
.venv/
```

What `.venv/` means:

- It is a local Python environment for this project.
- Packages installed here belong only to this project.
- This avoids mixing project packages with your system Python packages.

## 4. Activate The Virtual Environment

You activated the environment with:

```bash
source .venv/bin/activate
```

After activation, your terminal showed something like:

```text
(agentic-ai-crashcourse)
```

That means the virtual environment is active.

You can confirm which Python is being used:

```bash
which python
```

Expected result should include:

```text
.venv/bin/python
```

## 5. Create `requirements.txt`

You created a `requirements.txt` file and wrote the main packages you wanted to install.

Current file:

```text
langchain
langchain-community
langchain-openai
langchain-groq
langchain-anthropic
python-dotenv
langchain-google-genai
```

Why each package is here:

- `langchain`: core LangChain framework.
- `langchain-community`: community integrations and utilities.
- `langchain-openai`: OpenAI model integration.
- `langchain-groq`: Groq model integration.
- `langchain-anthropic`: Claude/Anthropic model integration.
- `python-dotenv`: loads API keys from `.env`.
- `langchain-google-genai`: Google Gemini integration.

Important spelling note:

```text
langchain
```

not:

```text
lanchain
```

## 6. Install Requirements With `uv`

You installed all packages from `requirements.txt` with:

```bash
uv add -r requirements.txt
```

What this does:

- Reads every package name from `requirements.txt`.
- Installs those packages into `.venv`.
- Adds them into `pyproject.toml`.
- Updates `uv.lock`.

After this command, `pyproject.toml` contains dependencies like:

```toml
dependencies = [
    "langchain>=...",
    "langchain-anthropic>=...",
    "langchain-community>=...",
    "langchain-google-genai>=...",
    "langchain-groq>=...",
    "langchain-openai>=...",
    "python-dotenv>=...",
]
```

## 7. Add Jupyter Kernel Support

You added `ipykernel` with:

```bash
uv add ipykernel
```

Why this is needed:

- VS Code notebooks need a Python kernel.
- `ipykernel` allows `.ipynb` notebooks to run using your `.venv`.
- Without it, the notebook may use the wrong Python environment.

After installing it, select this kernel in VS Code:

```text
.venv/bin/python
```

This is important because your packages are installed inside `.venv`.

## 8. Create Environment Variables

You created a `.env` file to store API keys.

Example:

```env
ANTHROPIC_API_KEY=your_anthropic_key_here
GROQ_API_KEY=your_groq_key_here
GOOGLE_API_KEY=your_google_key_here
OPENAI_API_KEY=your_openai_key_here
OPENROUTER_API_KEY=your_openrouter_key_here
```

Why `.env` is used:

- API keys should not be written directly in Python code.
- The notebooks can read keys from `.env`.
- You can change keys without changing code.

Correct naming uses underscores:

```env
ANTHROPIC_API_KEY=...
```

Incorrect naming:

```env
ANTHROPIC-API-Key=...
```

Python and LangChain expect the standard underscore format.

## 9. Load `.env` In A Notebook

In the notebooks, you loaded environment variables like this:

```python
import os
from dotenv import load_dotenv

load_dotenv()

os.getenv("ANTHROPIC_API_KEY")
```

What each line does:

- `import os`: lets Python read environment variables.
- `from dotenv import load_dotenv`: imports the function that loads `.env`.
- `load_dotenv()`: reads `.env` and loads the variables.
- `os.getenv("ANTHROPIC_API_KEY")`: checks whether the key exists.

Use:

```python
os.getenv("KEY_NAME")
```

while learning because it returns `None` if the key is missing.

Be careful with:

```python
os.environ["KEY_NAME"]
```

because it throws an error if the key is missing.

## 10. Notebook 1: `1-langchainintro.ipynb`

You created:

```text
langchain/1-langchainintro.ipynb
```

Purpose:

- Learn LangChain basics.
- Load API keys.
- Create a tool.
- Create an agent.
- Invoke the agent.
- Read the final response.

### 10.1 Check LangChain Version

You can check the installed LangChain version:

```python
import langchain

langchain.__version__
```

This confirms that the notebook is using the environment where LangChain is installed.

### 10.2 Load API Keys

```python
import os
from dotenv import load_dotenv

load_dotenv()

os.getenv("GOOGLE_API_KEY")
os.getenv("GROQ_API_KEY")
os.getenv("ANTHROPIC_API_KEY")
```

This confirms the notebook can access provider keys.

### 10.3 Import Agent Tools

```python
from langchain.agents import create_agent
from langchain_core.tools import tool
```

What this means:

- `create_agent`: creates a LangChain agent.
- `tool`: turns a normal Python function into a tool the agent can call.

### 10.4 Create A Tool

```python
@tool
def get_weather(city: str) -> str:
    """Get the weather for a given city."""
    return f"The weather in {city} is sunny with a high of 25 C."
```

What this does:

- Defines a function named `get_weather`.
- Accepts a city name as input.
- Returns a mock weather answer.
- The `@tool` decorator makes it available to the agent.

This is not a real weather API. It is a practice tool so you can learn how tool calling works.

### 10.5 Create A Claude Agent

```python
agent = create_agent(
    model="claude-haiku-4-5",
    tools=[get_weather],
    system_prompt="You are a helpful assistant that provides latest information about the world.",
)
```

What this does:

- Uses Claude as the model.
- Gives the agent access to the `get_weather` tool.
- Sets the agent behavior using `system_prompt`.

The model:

```python
"claude-haiku-4-5"
```

is a Claude model through Anthropic.

### 10.6 Invoke The Agent

```python
response = agent.invoke({
    "messages": [{"role": "user", "content": "What is the weather in New York?"}]
})
```

What this does:

- Sends a user message to the agent.
- The agent decides whether to call the tool.
- The result is stored in the variable `response`.

The message must use:

```python
"role"
```

not:

```python
" role"
```

The extra space causes an error.

### 10.7 Read The Final Answer

```python
response["messages"][-1].content
```

What this means:

- `response["messages"]`: list of all messages in the run.
- `[-1]`: gets the last message.
- `.content`: reads the text content.

If you get:

```text
NameError: name 'response' is not defined
```

it means you did not run the cell that creates `response`.

## 11. Notebook 2: `2-model-integration.ipynb`

You created:

```text
langchain/2-model-integration.ipynb
```

Purpose:

- Test models directly before using agents.
- Learn how `init_chat_model` works.
- Try provider integrations.

### 11.1 Load Environment Variables

```python
import os
from dotenv import load_dotenv

load_dotenv()

os.getenv("GOOGLE_API_KEY")
os.getenv("GROQ_API_KEY")
os.getenv("ANTHROPIC_API_KEY")
```

### 11.2 Initialize A Claude Model

```python
from langchain.chat_models import init_chat_model

model = init_chat_model("claude-haiku-4-5")
model
```

What this does:

- Imports LangChain's model initializer.
- Creates a chat model object.
- Uses your `ANTHROPIC_API_KEY` from `.env`.

You can also initialize Claude directly through the Anthropic integration:

```python
from langchain_anthropic import ChatAnthropic

model = ChatAnthropic(model="claude-haiku-4-5")
response = model.invoke("Hello What are the agents in agentic ai")
response.content
```

Important:

- Use `ChatAnthropic`, not `Anthropic`.
- `langchain_anthropic` does not export a class named `Anthropic`.

### 11.3 Invoke The Model

```python
response = model.invoke("Hello, how are you?")
response.content
```

What this does:

- Sends a direct prompt to the model.
- Stores the response.
- Prints only the response content.

This is simpler than an agent because there are no tools.

### 11.4 Initialize A Gemini Model

```python
from langchain.chat_models import init_chat_model

model = init_chat_model("google_genai:gemini-2.5-flash")
response = model.invoke("Hello What are the agents in agentic ai")
response.content
```

What this does:

- Uses Google's Gemini integration through LangChain.
- Reads `GOOGLE_API_KEY` from `.env`.
- Uses an available Gemini model name.

If `gemini-2.0-pro` returns `404 NOT_FOUND`, replace it with an available model such as:

```python
"google_genai:gemini-2.5-flash"
```

or:

```python
"google_genai:gemini-2.5-pro"
```

## 12. Difference Between Model And Agent

### Model

A model is the direct LLM call.

Example:

```python
response = model.invoke("Hello")
```

Use this when:

- you only need a direct answer
- you are testing provider setup
- you do not need tools

### Agent

An agent can reason, call tools, and produce a final answer.

Example:

```python
response = agent.invoke({
    "messages": [{"role": "user", "content": "What is the weather in New York?"}]
})
```

Use this when:

- you want the model to use functions/tools
- you want workflow-style behavior
- you want to build agentic AI applications

## 13. Common Errors You Faced

### Missing Package Error

Error:

```text
Initializing ChatAnthropic requires the langchain-anthropic package
```

Cause:

- `langchain-anthropic` was not installed in the active environment.
- Or the notebook was using the wrong kernel.

Fix:

```bash
uv add langchain-anthropic
```

or:

```bash
uv sync
```

Then select:

```text
.venv/bin/python
```

as the notebook kernel.

### Wrong Anthropic Import

Error:

```text
ImportError: cannot import name 'Anthropic' from 'langchain_anthropic'
```

Cause:

- The package exports `ChatAnthropic`, not `Anthropic`.

Wrong:

```python
from langchain_anthropic import Anthropic

model = Anthropic(model="claude-haiku-4-5")
```

Correct:

```python
from langchain_anthropic import ChatAnthropic

model = ChatAnthropic(model="claude-haiku-4-5")
```

### Wrong Environment Variable Name

Error:

```text
OPENROUTER_API_KEY must be set
```

Cause:

- The key name used hyphens instead of underscores.

Wrong:

```env
OPENROUTER-API-Key=...
```

Correct:

```env
OPENROUTER_API_KEY=...
```

### Wrong Message Key

Error:

```text
Message dict must contain 'role' and 'content' keys
```

Cause:

```python
{" role": "user"}
```

has a space before `role`.

Fix:

```python
{"role": "user"}
```

### Old Or Unavailable Model Name

Error:

```text
NotFoundError: model: claude-2
```

Cause:

- The model name was not available.

Fix:

```python
model="claude-haiku-4-5"
```

Gemini example:

```text
404 NOT_FOUND: models/gemini-2.0-pro is not found
```

Fix:

```python
model = init_chat_model("google_genai:gemini-2.5-flash")
```

### `response` Not Defined

Error:

```text
NameError: name 'response' is not defined
```

Cause:

- You ran `response["messages"][-1].content` before creating `response`.

Fix:

```python
response = agent.invoke({
    "messages": [{"role": "user", "content": "What is the weather in New York?"}]
})
```

Then:

```python
response["messages"][-1].content
```

## 14. Full Command Flow You Used

This is the clean sequence from start to notebook work:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv init
uv venv
source .venv/bin/activate
```

Then you created `requirements.txt` and added:

```text
langchain
langchain-community
langchain-openai
langchain-groq
langchain-anthropic
python-dotenv
langchain-google-genai
```

Then you installed those packages:

```bash
uv add -r requirements.txt
```

Then you added notebook kernel support:

```bash
uv add ipykernel
```

Then you created:

```text
langchain/1-langchainintro.ipynb
langchain/2-model-integration.ipynb
```

Then you selected this kernel in VS Code:

```text
.venv/bin/python
```

Then you loaded `.env`, initialized models, created an agent, invoked it, and inspected the response.

## 15. Final Mental Model

Think of the project in this order:

```text
uv installation
-> project initialization
-> virtual environment
-> dependency installation
-> API key setup
-> notebook kernel selection
-> direct model test
-> agent with tool test
```

That is the full learning path you followed.
