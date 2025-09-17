# ChatHub

## Overview
**ChatHub** is a flexible, multi‑model chat interface built with **Gradio** and Python.  
It demonstrates how to integrate several large language models (LLMs) – OpenAI's GPT, Anthropic's Claude, and Google's Gemini – into a single interactive UI, allowing users to switch models on the fly. The repository includes a self‑contained Jupyter notebook (`Project AI Chat Interface.ipynb`) that walks through:

1. **Environment setup** – loading API keys from a `.env` file.
2. **Model initialization** – creating client objects for OpenAI, Anthropic, and Google Generative AI.
3. **Prompt engineering** – defining a reusable `system_message` that can be extended dynamically.
4. **Chat functions** – streaming response generators for each model.
5. **Unified interface** – a Gradio `ChatInterface` with a dropdown to select the active model.

All code is ready to run locally (or on a cloud VM) with minimal configuration.

---

## Repository Structure

```
📦 ChatHub
├─ 📄 Project AI Chat Interface.ipynb   # The main notebook containing all code
└─ 📄 README.md                         # This documentation
```

---

## Prerequisites

| Requirement | Details |
|-------------|---------|
| **Python** | >= 3.11 |
| **Packages** | `dotenv`, `requests`, `beautifulsoup4`, `gradio`, `openai`, `anthropic`, `google-generativeai` |
| **API Keys** | - `OPENAI_API_KEY` (OpenAI) <br> - `ANTHROPIC_API_KEY` (Anthropic) <br> - `GOOGLE_API_KEY` (Google Generative AI) |
| **Environment file** | Create a `.env` file in the notebook’s working directory with the keys above. Example: <br>```dotenv<br>OPENAI_API_KEY=sk-...<br>ANTHROPIC_API_KEY=sk-ant-...<br>GOOGLE_API_KEY=AIzaSy...<br>``` |

---

## How It Works

### 1. Environment Loading
```python
from dotenv import load_dotenv
import os

load_dotenv()
openai_api_key = os.getenv('OPENAI_API_KEY')
anthropic_api_key = os.getenv('ANTHROPIC_API_KEY')
google_api_key = os.getenv('GOOGLE_API_KEY')
```
The notebook prints the first few characters of each key to confirm they are loaded.

### 2. Model Clients
```python
from openai import OpenAI
import anthropic
import google.generativeai as genai

openai = OpenAI()
claude = anthropic.Anthropic()
genai.configure(api_key=google_api_key)
```

### 3. System Prompt
A reusable system prompt (`system_message`) defines the assistant’s persona (e.g., a helpful store associate).  
It can be extended later with additional instructions (e.g., handling specific product queries).

### 4. Streaming Chat Functions
Each model has a dedicated generator that yields partial responses for a smooth UI experience.

* **OpenAI GPT**
```python
def chat_gpt(message, history):
    # Build messages list with system prompt + conversation history
    # Call openai.chat.completions.create(..., stream=True)
    # Yield incremental text
```

* **Anthropic Claude**
```python
def chat_claude(message, history):
    # Build messages list
    # Use claude.messages.stream(...)
    # Yield incremental text
```

* **Google Gemini**
```python
def chat_gemini(message, history):
    # Build Gemini‑compatible message format
    # Call gemini.generate_content(..., stream=True)
    # Yield incremental text
```

### 5. Unified Dispatcher
```python
def chat(message, history, model):
    if model == 'GPT':
        response = chat_gpt(message, history)
    elif model == 'Claude':
        response = chat_claude(message, history)
    elif model == 'Gemini':
        response = chat_gemini(message, history)
    else:
        raise ValueError('Model Not Found')
    yield from response
```

### 6. Gradio Interface
```python
import gradio as gr

gr.ChatInterface(
    fn=chat,
    type='messages',
    additional_inputs=gr.Dropdown(
        choices=["GPT", "Claude", "Gemini"],
        label='Select Your Model'
    )
).launch(share=True)
```
The UI displays a chat window and a dropdown for model selection. The `share=True` flag creates a public link via Gradio’s sharing service.

---

## Running the Notebook

1. **Clone the repository** (or download the notebook).
2. **Install dependencies**  
   ```bash
   pip install -r requirements.txt   # Create this file if desired
   # Or install manually:
   pip install python-dotenv requests beautifulsoup4 gradio openai anthropic google-generativeai
   ```
3. **Create a `.env` file** with your API keys.
4. **Open the notebook** in JupyterLab / VS Code / Google Colab.
5. **Execute cells sequentially**.  
   The final cell will launch the Gradio interface and output a shareable URL.

---

## Extending the Project

- **Add more models**: Implement a new streaming function following the existing pattern and update the dispatcher.
- **Custom system prompts**: Modify `system_message` or load prompts from external files to support different domains (e.g., technical support, tutoring).
- **Persist conversation**: Store `history` in a database or file to resume sessions across restarts.
- **Deploy to Hugging Face Spaces**: Use `gradio deploy` as suggested in the notebook output to host a permanent public endpoint.

---

## License & Attribution

The notebook is provided **as‑is** for educational purposes.  
- OpenAI, Anthropic, and Google APIs are used under their respective terms of service.  
- Gradio is licensed under the Apache 2.0 License.  

Feel free to adapt, experiment, and share your own variations of ChatHub!
