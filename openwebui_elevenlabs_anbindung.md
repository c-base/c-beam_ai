# ElevenLabs TTS in Open WebUI anbinden

Diese Anleitung beschreibt, wie Sie ElevenLabs für hochwertige Text-zu-Sprache (TTS) in Open WebUI konfigurieren.

**Voraussetzungen:**

* Open WebUI manuell installiert und funktionstüchtig.
* Ein aktives ElevenLabs-Konto und ein gültiger API-Schlüssel.

**Schritte zur Anbindung:**

1.  **ElevenLabs API-Schlüssel besorgen:**
    * Melden Sie sich bei Ihrem ElevenLabs-Konto an: [https://elevenlabs.io/](https://elevenlabs.io/)
    * Gehen Sie zu Ihrem Profil oder den API-Einstellungen und kopieren Sie Ihren persönlichen API-Schlüssel (beginnt typischerweise mit `el_`).

2.  **`elevenlabs` Python-Paket installieren:**
    Open WebUI benötigt dieses Paket, um mit der ElevenLabs-API zu kommunizieren.
    ```bash
    cd ~/open-webui
    source venv/bin/activate
    pip install elevenlabs
    ```

3.  **`.env` Datei für ElevenLabs konfigurieren:**
    Öffnen Sie Ihre `.env`-Datei im Hauptverzeichnis von Open WebUI:
    ```bash
    nano .env
    ```
    **Kommentieren Sie die alten `AUDIO_TTS_` Einstellungen aus** (z.B. die von Edge TTS) und **fügen Sie die folgenden Zeilen hinzu oder passen Sie sie an:**

    ```
    # === ElevenLabs TTS Konfiguration ===
    AUDIO_TTS_ENGINE=elevenlabs                     # WICHTIG: Dies muss auf 'elevenlabs' gesetzt werden
    AUDIO_TTS_API_KEY="<DEIN_ELEVENLABS_API_KEY>"   # HIER DEINEN ECHTEN, KORREKTEN ELEVENLABS API-SCHLÜSSEL EINFÜGEN
    AUDIO_TTS_MODEL=eleven_multilingual_v2          # Ein gängiges, gutes ElevenLabs-Modell. Passen Sie es bei Bedarf an.
    AUDIO_TTS_VOICE=Nicole                          # Eine Beispielstimme. Wählen Sie eine verfügbare Stimme aus der ElevenLabs Voice Library.
    AUDIO_TTS_OPTIMIZE_STREAMING_LATENCY=1          # Optional: Kann die Geschwindigkeit verbessern (Werte: 1, 2, 3 oder 4)
    ```
    *Stellen Sie sicher, dass der API-Schlüssel **exakt** ist und innerhalb der Anführungszeichen steht.*
    *Wählen Sie eine verfügbare ElevenLabs-Stimme aus der "Voice Library" in Ihrem ElevenLabs-Konto.*

4.  **Speichern Sie die `.env`-Datei** (Ctrl+X, J, Enter).

5.  **Open WebUI Server neu starten:**
    Stoppen Sie den Uvicorn-Server in Ihrem Terminal (Ctrl+C) und starten Sie ihn dann erneut:
    ```bash
    uvicorn backend.open_webui.main:app --host 0.0.0.0 --port 8081 --reload
    ```

**Testen in Open WebUI:**

1.  Öffnen Sie Open WebUI in Ihrem Browser (`http://localhost:8081`).
2.  Navigieren Sie zu den **Einstellungen** und dann zum Bereich **Audio**.
3.  Überprüfen Sie, ob ElevenLabs als "Text-zu-Sprache-Engine" ausgewählt oder verfügbar ist und Ihre gewählte Stimme angezeigt wird.
4.  Aktivieren Sie die Sprachausgabe und testen Sie sie im Chat. Die Sprachausgabe sollte nun über ElevenLabs erfolgen.
