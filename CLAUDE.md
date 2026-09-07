# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of DeepLearning.AI-style lab exercises exploring agentic design patterns (e.g. the "Reflection" pattern), implemented as Jupyter notebooks. Each module lives in its own top-level directory (e.g. `Module 2: Reflection Design Pattern/`) containing a notebook, a `utils.py` helper module, and any data/image artifacts it produces. There is no application entry point, build step, or test suite — the notebooks are the deliverable.

## Environment setup

- Python is managed via **pyenv**; `.python-version` pins the `dl-ai-labs` pyenv virtualenv (Python 3.13.5).
- Install dependencies: `pip install -r requirements.txt`
- Secrets live in `.env` (gitignored) and are loaded via `python-dotenv`. Required keys used by `utils.py`: `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`.
- Run notebooks with Jupyter (`jupyter notebook` / `nbclassic`) or via VS Code's notebook UI — `.vscode/settings.json` configures the pyenv environment manager.
- Sanity-check the environment with `test.ipynb`, which imports the core libraries (`anthropic`, `fastapi`, `pandas`, `matplotlib`) and prints their versions.

## Architecture notes

Each module's `utils.py` follows the same shape:
- Instantiates a module-level `openai_client` (`OpenAI`) and `anthropic_client` (`Anthropic`) at import time, reading keys from `.env`. Note: in `Module 2`'s `utils.py`, `anthropic_client` is re-assigned right after creation to point at a proxy base URL (`http://jupyter-api-proxy.internal.dlai/rev-proxy/anthropic`) — this is DeepLearning.AI's hosted lab environment proxy and will only resolve inside that platform.
- `get_response(model, prompt)` dispatches to Anthropic vs. OpenAI based on whether `"claude"`/`"anthropic"` appears in the model name string — there's no other provider-routing mechanism.
- Image-capable calls (`image_anthropic_call`, `image_openai_call`) take a pre-encoded `(media_type, b64)` pair from `encode_image_b64()` and post text+image content to each provider's respective API shape (Anthropic `messages.create` content blocks vs. OpenAI `responses.create` input items).
- `print_html()` is a notebook display helper (IPython `HTML`) used throughout instead of `print()`, for consistent styled output of text, DataFrames, and images.

The Reflection pattern notebook (`Module 2: Reflection Design Pattern/reflection.ipynb`) demonstrates a generate → execute → critique → regenerate loop:
1. Load data via `utils.load_and_prepare_data()`.
2. Prompt a model to emit matplotlib code wrapped in `<execute_python>...</execute_python>` tags.
3. Extract and `exec()` that code against the loaded `df` to produce a chart image.
4. Feed the resulting chart image + original code back to a (possibly different) model to critique and produce refined code.
5. `exec()` the refined code to produce a second chart version.

Models are mixed by design (e.g. `gpt-4o-mini` for generation, `o4-mini` for reflection) to compare provider/model behavior — don't assume a single model is used per notebook.

## Working with these files

- `utils.py` files are per-module and not shared/imported across module directories — treat each module as self-contained.
- Generated artifacts (`chart_v1.png`, `chart_v2.png`, `drink_sales_v*.png`, etc.) are committed outputs of running the notebook, not hand-authored assets.
- `exec()` is used deliberately in these notebooks to run LLM-generated code against a live DataFrame — this is expected lab behavior, not something to "fix."
