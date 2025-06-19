# Open WebUI Installation (ohne Docker) - Technische Anleitung

Diese Anleitung beschreibt die manuelle Installation von Open WebUI auf einem Linux-System (getestet mit Ubuntu unter WSL2).

**Voraussetzungen:**

* WSL2 (Windows Subsystem for Linux 2) installiert und konfiguriert.
* Eine Ubuntu-Distribution in WSL2.
* Git installiert: `sudo apt update && sudo apt install git`
* Python 3 und pip (innerhalb von WSL2): `sudo apt install python3 python3-pip python3-venv`
* Node.js und npm (innerhalb von WSL2): `sudo apt install nodejs npm`
* Ollama installiert und laufend (`ollama serve`).

**Installationsschritte:**

1.  **Open WebUI Repository klonen:**
    ```bash
    cd ~
    git clone [https://github.com/open-webui/open-webui.git](https://github.com/open-webui/open-webui.git)
    cd open-webui
    ```

2.  **Virtuelle Python-Umgebung erstellen und aktivieren:**
    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

3.  **Backend-Abhängigkeiten installieren:**
    ```bash
    cd backend
    pip install -r requirements.txt
    cd ..
    ```

4.  **Frontend-Abhängigkeiten installieren:**
    ```bash
    npm install
    ```

5.  **Frontend-Build erstellen (mit erhöhtem Speicher):**
    ```bash
    NODE_OPTIONS="--max-old-space-size=8192" npm run build
    ```
    (Passen Sie den Wert `8192` bei Speicherproblemen an den verfügbaren RAM an).

6.  **`main.py` Pfadkorrektur (manuell anpassen):**
    Öffnen Sie die Datei `backend/open_webui/main.py`:
    ```bash
    nano backend/open_webui/main.py
    ```
    Fügen Sie die folgenden Zeilen **am Anfang der Datei** (nach Kommentaren, vor den ersten `from ... import ...` Anweisungen) ein:
    ```python
    import sys
    import os
    sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '..')))
    ```
    Speichern und schließen Sie die Datei (Ctrl+X, J, Enter).

7.  **`webui.db` Datenbankdatei löschen (optional, bei Datenbankfehlern):**
    Falls es bei vorherigen Versuchen Datenbankfehler gab:
    ```bash
    rm -f backend/data/webui.db
    ```
    (Achtung: Dies löscht alle bestehenden Open WebUI-Daten wie Chats und Benutzer!)

8.  **Berechtigungen setzen:**
    ```bash
    sudo chown -R $(whoami):$(whoami) .
    sudo chmod -R u+rwX .
    ```

9.  **Umgebungsvariablen in `.env` konfigurieren:**
    Erstellen oder bearbeiten Sie die Datei `.env` im Hauptverzeichnis:
    ```bash
    nano .env
    ```
    Fügen Sie die folgenden Zeilen ein (oder passen Sie diese an):
    ```
    WEBUI_HOST=0.0.0.0
    WEBUI_PORT=8081
    OLLAMA_BASE_URL=http://localhost:11434
    ```
    Speichern und schließen Sie die Datei.

**Starten von Open WebUI:**

1.  **Ollama Server starten (falls nicht schon läuft):**
    In einem separaten WSL2-Terminal:
    ```bash
    ollama serve
    ```

2.  **Open WebUI Server starten:**
    In dem Terminal, in dem Ihre virtuelle Umgebung aktiviert ist (`(venv)` sichtbar):
    ```bash
    uvicorn backend.open_webui.main:app --host 0.0.0.0 --port 8081 --reload
    ```

**Zugriff:**

Öffnen Sie Ihren Webbrowser unter Windows und navigieren Sie zu: `http://localhost:8081`

---

### 2. edge_tts_container_installation.md

```markdown
# Edge TTS Container (travisvn/openai-edge-tts) Installation & Test

Diese Anleitung beschreibt die Installation und den Test des `travisvn/openai-edge-tts`-Docker-Containers für Text-zu-Sprache (TTS).

**Voraussetzungen:**

* Docker Desktop unter Windows installiert und laufend.

**Installations- und Testschritte:**

1.  **Docker-Image herunterladen:**
    Öffnen Sie **Windows PowerShell** (oder Windows Terminal) und führen Sie aus:
    ```powershell
    docker pull travisvn/openai-edge-tts:latest
    ```

2.  **Docker-Container starten:**
    Starten Sie den Container, indem Sie den Port `5050` mappen und einen API-Schlüssel übergeben. Diesen API-Schlüssel werden Sie später in Open WebUI verwenden.
    ```powershell
    docker run -d --name openai-edge-tts -p 5050:5050 -e API_KEY="sk-mein-geheimer-testschluessel" travisvn/openai-edge-tts:latest
    ```
    (Ersetzen Sie `"sk-mein-geheimer-testschluessel"` durch einen beliebigen, sicheren Dummy-Wert Ihrer Wahl).

3.  **Status des Containers überprüfen:**
    Stellen Sie sicher, dass der Container läuft:
    ```powershell
    docker ps
    ```
    Der Status sollte `Up ...` sein.

4.  **TTS-Dienst testen (PowerShell):**
    Testen Sie, ob der TTS-Dienst im Container reagiert und Audio generiert.
    ```powershell
    $headers = @{
        "Content-Type" = "application/json";
        "Authorization" = "Bearer sk-mein-geheimer-testschluessel" # Muss mit dem oben verwendeten API_KEY übereinstimmen
    }
    $body = '{"model": "tts-1", "input": "Hallo Welt, dies ist ein Test der Sprachausgabe.", "voice": "alloy"}' # "alloy" wird später angepasst
    Invoke-WebRequest -Uri http://localhost:5050/v1/audio/speech -Method POST -Headers $headers -Body $body -OutFile test_speech.mp3
    ```
    Wenn erfolgreich, wird eine `test_speech.mp3`-Datei in Ihrem aktuellen PowerShell-Verzeichnis erstellt, die Sie abspielen können.

**Wichtiger Hinweis zur Stimme ("alloy" nicht gefunden):**

Der `travisvn/openai-edge-tts`-Container emuliert zwar die OpenAI-API, verwendet aber die tatsächlichen Microsoft Edge TTS-Stimmen, die andere Namen haben. Wenn Sie im Browser den Fehler "Voice 'alloy' not found" erhalten, sehen Sie in den Fehlermeldungen des Open WebUI Backend-Terminals eine Liste der tatsächlich verfügbaren Stimmen (z.B. `am_alloy`, `af_nova`, `bf_emma` etc.). Sie müssen eine dieser Stimmen in Ihrer Open WebUI `.env`-Datei verwenden.

---

### 3. openwebui_ollama_models_anbinden.md

```markdown
# Ollama Modelle in Open WebUI anbinden

Diese Anleitung beschreibt, wie Sie weitere Sprachmodelle in Ollama herunterladen und in Open WebUI zur Verfügung stellen.

**Voraussetzungen:**

* Ollama Server läuft in WSL2 (`ollama serve`).
* Open WebUI läuft und ist über den Browser erreichbar.

**Schritte zum Hinzufügen von Modellen:**

1.  **Gewünschtes Modell auf Ollama Hub finden:**
    Besuchen Sie [https://ollama.ai/library](https://ollama.ai/library) in Ihrem Webbrowser, um verfügbare Modelle zu durchsuchen (z.B. `gemma:7b`, `mistral:7b`).

2.  **Modell mit Ollama herunterladen (Pull):**
    Öffnen Sie Ihr WSL2-Terminal und führen Sie den `ollama pull`-Befehl aus:
    ```bash
    ollama pull <modellname>:<tag>
    ```
    **Beispiele:**
    * `ollama pull gemma:7b`
    * `ollama pull mistral:7b`
    * `ollama pull llama2` (lädt den `latest`-Tag von Llama2)

    Warten Sie, bis der Download abgeschlossen ist.

3.  **Modell in Open WebUI verwenden:**
    Sobald das Modell erfolgreich heruntergeladen wurde, erkennt Open WebUI es automatisch.

    * Öffnen Sie Open WebUI in Ihrem Browser (`http://localhost:8081` oder den Port, den Sie konfiguriert haben).
    * Im Chat-Interface von Open WebUI finden Sie ein Dropdown-Menü zur Modellauswahl (oft oben in der Mitte oder in der Seitenleiste).
    * Das neu heruntergeladene Modell (`gemma:7b`, `mistral:7b` etc.) sollte nun in dieser Liste erscheinen. Wählen Sie es aus.
    * Sie können nun mit dem ausgewählten Modell chatten.

**Modelle entfernen:**

Um ein Modell zu entfernen und Speicherplatz freizugeben:
```bash
ollama rm <modellname>:<tag>
