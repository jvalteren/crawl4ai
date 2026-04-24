# SPEC: OpenRouter LLM Provider Support

## 1. Objective
Enable users to use OpenRouter as an LLM provider by adding support for its API key and provider prefix in the configuration system. This allows for seamless integration of any model hosted on OpenRouter (e.g., `openrouter/google/gemma-4-26b-a4b-it`).

## 2. Proposed Changes

### 2.1 Environment Variable Support
Add `OPENROUTER_API_KEY` to the supported environment variables.

**Update to `.llm.env` (Documentation/Template):**
```dotenv
# ...existing code...
#ANTHROPIC_API_KEY=your_anthropic_key_here
#GROQ_API_KEY=your_groq_key_here
#OPENROUTER_API_KEY=your_openrouter_api_key_here
# ...existing code...
```

### 2.2 Configuration Logic Updates

#### A. Update `crawl4ai/config.py`
1.  **Update `PROVIDER_MODELS_PREFIXES`**: Add `"openrouter": os.getenv("OPENROUTER_API_KEY")`. This ensures that any provider string starting with `openrouter/` will automatically attempt to use the `OPENROUTER_API_KEY`.
2.  **Update `PROVIDER_MODELS` (Optional but recommended)**: Add common OpenRouter models if desired, though the prefix support is the primary driver for flexibility.

#### B. Update `crawl4ai/async_configs.py`
*   No code changes are strictly required in `LLMConfig.__init__` because it dynamically iterates over `PROVIDER_MODELS_PREFIXES.keys()`. Once the prefix is added to `config.py`, the logic will automatically pick it up.

## 3. Implementation Plan

1.  **Step 1: Update `crawl4ai/config.py`**
    *   Add `openrouter` to `PROVIDER_MODELS_PREFIXES`.
2.  **Step 2: Update `.llm.env`**
    *   Add a commented-out example for `OPENROUTER_API_KEY`.
3.  **Step 3: Verification**
    *   Verify that `LLMConfig(provider="openrouter/google/gemma-4-26b-a4b-it")` correctly resolves the `api_token` from `os.getenv("OPENROUTER_API_KEY")`.

## 4. Example Usage

**Via `.llm.env`:**
```dotenv
OPENROUTER_API_KEY=sk-or-v1-your-key
LLM_PROVIDER=openrouter/google/gemma-4-26b-a4b-it
```

**Via Code:**
```python
from crawl4ai import LLMConfig
import os

# If OPENROUTER_API_KEY is in env
llm_config = LLMConfig(provider="openrouter/google/gemma-4-26b-a4b-it")
print(llm_config.api_token) # Should show the key from env
```
