Linux version llama web tts setup

```markdown
# LLaMA 2 Chat Interface with TTS Installation Guide (Linux)

## 1. Prerequisites
### Install Python & Anaconda
- Install **Anaconda3** ([Download](https://www.anaconda.com/products/distribution))
- Verify installation:
  ```bash
  conda --version
  python --version
  ```

### Install espeak for TTS
```bash
sudo apt-get install espeak
```

---

## 2. Setup Conda Environment
```bash
conda create -n llama_env python=3.10
conda activate llama_env
```

---

## 3. Install Required Packages
1. Install core dependencies:
   ```bash
   pip install gradio llama-cpp-python soundfile openai-whisper torch torchaudio
   ```
2. **Troubleshooting:** If `llama-cpp-python` installation fails:
   ```bash
   conda install -c conda-forge ffmpeg
   ```
3. Verify FFMPEG installation:
   ```bash
   ffmpeg -version
   ```

---

## 4. Model Preparation
1. Save LLaMA model to:
   `/home/${USER}/.ollama/models/llama2-7b-chat.safetensors`
2. Install & run Ollama ([Installation Guide](https://ollama.ai/download)):
   ```bash
   curl -fsSL https://ollama.ai/install.sh | sh
   ollama run llama2-7b-chat
   ```

---

## 5. Create Web Interface
Create `llama_webapp.py` with:

```python
import gradio as gr
import subprocess
import requests
import json
import os

OLLAMA_MODEL = "llama2-7b-chat"
OLLAMA_PROMPT_TEMPLATE = "You are a helpful AI assistant. Please answer the following question clearly and concisely: {prompt}"

def query_llama(prompt):
    formatted_prompt = OLLAMA_PROMPT_TEMPLATE.format(prompt=prompt)
    url = "http://localhost:11434/api/generate"
    headers = {"Content-Type": "application/json"}
    payload = {"model": OLLAMA_MODEL, "prompt": formatted_prompt, "stream": False}
    
    try:
        response = requests.post(url, headers=headers, data=json.dumps(payload))
        return response.json()["response"] if response.status_code == 200 else f"Error: {response.text}"
    except Exception as e:
        return f"Connection error: {str(e)}"

def text_to_speech(text):
    output_file = os.path.join(os.path.dirname(__file__), "output.wav")
    subprocess.run(["espeak", "-w", output_file, text])
    return output_file

def process_input(user_input):
    response_text = query_llama(user_input)
    audio_file = text_to_speech(response_text)
    return response_text, audio_file

with gr.Blocks() as demo:
    gr.Markdown("## LLaMA 2 Chat Interface with Speech Synthesis")
    with gr.Row():
        user_input = gr.Textbox(label="Input Text")
        submit_btn = gr.Button("Submit")
    with gr.Column():
        output_text = gr.Textbox(label="LLaMA Response")
        audio_output = gr.Audio(label="Speech Output", autoplay=True)

    submit_btn.click(process_input, inputs=user_input, outputs=[output_text, audio_output])

if __name__ == "__main__":
    demo.launch()
```

---

## 6. Start the Interface
```bash
python llama_webapp.py
```
Access the interface at `http://127.0.0.1:7860`

---

## 7. Optional: Auto-start Ollama
Add to your Python script:
```python
import subprocess

def start_ollama():
    try:
        subprocess.Popen(["ollama", "run", OLLAMA_MODEL])
    except Exception as e:
        print(f"Ollama start error: {e}")

start_ollama()
```

---

## 8. Troubleshooting
**TTS Issues:**
```bash
sudo apt-get install espeak libespeak1
```

**Whisper Errors:**
```bash
conda install -c conda-forge ffmpeg
```

**Port Conflicts:**
```bash
python llama_webapp.py --server_port 7861
```
