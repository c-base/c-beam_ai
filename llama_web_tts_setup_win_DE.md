# Schritt-für-Schritt-Anleitung: LLaMA mit Webinterface, TTS und Whisper auf Windows

## 1. Voraussetzungen

### Python & Anaconda installieren
- Stelle sicher, dass **Anaconda3** installiert ist ([Download](https://www.anaconda.com/products/distribution)).
- Prüfe die Installation:
  ```powershell
  conda --version
  python --version
  ```

### Conda-Umgebung einrichten
```powershell
conda create -n llama_env python=3.10
conda activate llama_env
```

---

## 2. Benötigte Pakete installieren

1. Installiere die erforderlichen Pakete:
   ```powershell
   pip install gradio llama-cpp-python pyttsx3 soundfile openai-whisper torch torchaudio
   ```

2. **Fehlerbehebung:** Falls Fehler bei der Installation von `llama-cpp-python` auftreten, installiere:
   ```powershell
   conda install -c conda-forge ffmpeg
   ```

3. Falls Whisper Fehler zeigt, stelle sicher, dass FFMPEG funktioniert:
   ```powershell
   ffmpeg -version
   ```

---

## 3. Modellvorbereitung

1. Speichere das LLaMA-Modell hier: `C:/Users/hardy/.ollama/models/llama2-7b-chat.safetensors`

2. Stelle sicher, dass Ollama installiert ist und das Modell läuft:
   ```powershell
   ollama run llama2-7b-chat
   ```

---

## 4. Webinterface erstellen

Erstelle die Datei `llama_webapp.py` mit folgendem Inhalt:

```python
import gradio as gr
import pyttsx3
import requests
import json

OLLAMA_MODEL = "llama2-7b-chat"
OLLAMA_PROMPT_TEMPLATE = "Du bist ein hilfreicher und freundlicher KI-Assistent. Bitte beantworte die folgende Frage klar und präzise: {prompt}"

def query_llama(prompt):
    formatted_prompt = OLLAMA_PROMPT_TEMPLATE.format(prompt=prompt)
    url = "http://localhost:11434/api/generate"
    headers = {"Content-Type": "application/json"}
    payload = {"model": OLLAMA_MODEL, "prompt": formatted_prompt, "stream": False}
    response = requests.post(url, headers=headers, data=json.dumps(payload))
    if response.status_code == 200:
        return response.json()["response"]
    else:
        return f"Fehler: {response.status_code}, {response.text}"

def text_to_speech(text):
    tts_engine = pyttsx3.init()
    tts_engine.save_to_file(text, "output.wav")
    tts_engine.runAndWait()
    return "output.wav"

def process_input(user_input):
    response_text = query_llama(user_input)
    audio_file = text_to_speech(response_text)
    return response_text, audio_file

with gr.Blocks() as demo:
    gr.Markdown("## Chat mit LLaMA 2 und Sprachwiedergabe")
    user_input = gr.Textbox(label="Gib deinen Text ein:")
    submit_button = gr.Button("Absenden")
    output_text = gr.Textbox(label="Antwort von LLaMA:")
    audio_output = gr.Audio(label="Sprachausgabe", autoplay=True)
    submit_button.click(process_input, inputs=[user_input], outputs=[output_text, audio_output])

demo.launch()
```

---

## 5. Webinterface starten

1. Führe das Skript aus:
   ```powershell
   python llama_webapp.py
   ```
2. Gehe zur angezeigten URL (z. B. `http://127.0.0.1:7860`).

---

## 6. Optional: Ollama automatisch starten

```python
import subprocess

def start_ollama():
    try:
        subprocess.Popen(["ollama", "run", OLLAMA_MODEL])
    except Exception as e:
        print(f"Fehler beim Starten von Ollama: {e}")

start_ollama()
```

---

## 7. Troubleshooting

Falls TTS nicht funktioniert:
```powershell
pip install pyttsx3 sounddevice
```

Falls Whisper-Fehler auftreten:
```powershell
conda install -c conda-forge ffmpeg
```

---

Mit dieser Anleitung kannst du ein funktionsfähiges Webinterface mit LLaMA, TTS und Whisper auf deinem Windows-System einrichten.
