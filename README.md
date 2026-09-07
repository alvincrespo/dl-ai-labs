# dl-ai-labs

Lab exercises exploring agentic design patterns, implemented as Jupyter notebooks.

Each module lives in its own top-level directory (e.g. `Module 2: Reflection Design Pattern/`) containing a notebook, a `utils.py` helper module, and any data/image artifacts it produces.

## Modules

- **Module 2: Reflection Design Pattern** — demonstrates a generate → execute → critique → regenerate loop: prompt a model to write matplotlib code for a chart, execute it, then feed the resulting image and code back to a model for critique and a refined second version.

## Setup

Python is managed via [pyenv](https://github.com/pyenv/pyenv); `.python-version` pins the `dl-ai-labs` virtualenv (Python 3.13.5).

```bash
pip install -r requirements.txt
```

Create a `.env` file with the API keys used by the notebooks:

```
OPENAI_API_KEY=...
ANTHROPIC_API_KEY=...
```

Run `test.ipynb` to sanity-check the environment — it imports the core libraries and prints their versions.

## Running

Open the notebooks with Jupyter (`jupyter notebook` / `nbclassic`) or in VS Code, which is configured (`.vscode/settings.json`) to use the pyenv environment manager.
