# Safe Git Push Workflow

Use this checklist before pushing this project to GitHub, especially after running notebooks.

The main risk in this repo is accidentally committing API keys. Keys can leak through:

- `.env`
- notebook cell output
- copied traceback output
- printed environment variables
- old commits that still contain secrets

## What Happened

GitHub rejected the push because it detected API keys in the commit history.

The exposed locations were notebook files and `.env`. The fix was:

- stop tracking `.env`
- keep `.env` in `.gitignore`
- clear notebook outputs
- amend the bad commit
- push the cleaned commit

## Daily Pre-Push Checklist

Run your notebooks normally, then complete these steps before pushing.

### 1. Clear Notebook Outputs

In VS Code:

```text
Open notebook -> ... menu -> Clear All Outputs -> Save
```

Do this for:

```text
langchain/1-langchainintro.ipynb
langchain/2-model-integration.ipynb
```

### 2. Confirm `.env` Is Ignored

Run:

```bash
git status --short
```

You should not see:

```text
.env
```

If `.env` appears as tracked, remove it from Git tracking while keeping the file locally:

```bash
git rm --cached .env
```

Make sure `.gitignore` contains:

```gitignore
.env
.venv/
```

### 3. Verify Notebook Outputs Are Empty

Run:

```bash
jq '[.cells[] | select(has("outputs") and (.outputs|length>0))] | length' langchain/1-langchainintro.ipynb
jq '[.cells[] | select(has("outputs") and (.outputs|length>0))] | length' langchain/2-model-integration.ipynb
```

Both commands should print:

```text
0
```

If either command prints a number greater than `0`, clear outputs again and save the notebook.

### 4. Stage Files

For normal commits:

```bash
git add .
```

Only use `git add .` after confirming `.env` is ignored.

For a safer targeted add:

```bash
git add .gitignore README.md docs/SAFE_GIT_PUSH_WORKFLOW.md .env.example langchain/1-langchainintro.ipynb langchain/2-model-integration.ipynb pyproject.toml requirements.txt uv.lock main.py
```

### 5. Commit

For a new commit:

```bash
git commit -m "Update LangChain notebooks and project documentation"
```

If fixing the previous blocked commit:

```bash
git commit --amend --no-edit
```

### 6. Push

For normal push:

```bash
git push
```

If you amended a commit that GitHub already knows about:

```bash
git push --force-with-lease
```

Use `--force-with-lease`, not plain `--force`, because it is safer when working with a remote branch.

## If GitHub Still Blocks The Push

Search the current commit for common secret patterns:

```bash
git grep -n "sk-" HEAD
git grep -n "gsk_" HEAD
git grep -n "ANTHROPIC" HEAD
git grep -n "OPENROUTER" HEAD
```

If a real key appears:

1. Remove it from the file.
2. Clear notebook outputs again.
3. Save the file.
4. Stage the fix.
5. Amend the commit.
6. Push again.

```bash
git add .
git commit --amend --no-edit
git push
```

## What `git rm --cached .env` Means

This command removes `.env` from Git tracking:

```bash
git rm --cached .env
```

It does not delete the file from your computer.

After running it:

- local `.env` still exists
- Git stops including `.env` in commits
- `.gitignore` prevents it from being added again

## What Key Rotation Means

Rotating a key means replacing an exposed API key with a new one.

The process is:

1. Go to the provider dashboard.
2. Revoke or delete the old key.
3. Create a new key.
4. Put the new key in local `.env`.
5. Never commit the new key.

Provider examples:

- Anthropic Console -> API Keys
- OpenRouter -> Settings -> API Keys
- Groq Console -> API Keys
- Google AI Studio -> API Keys
- OpenAI Platform -> API Keys

If you cannot rotate the key yourself, ask the account owner to do it. Removing a key from Git history stops future exposure, but a real key that was already exposed should still be treated as compromised.

## Recommended Habits

- Use `os.getenv("KEY_NAME")`, not `os.environ["KEY_NAME"]`, when testing keys in notebooks.
- Do not display the return value of API key cells.
- Do not commit notebooks with outputs.
- Keep `.env.example` committed and `.env` ignored.
- Review `git status --short` before every commit.
