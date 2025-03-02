Protokoll – Kickoff-Meeting c-beam ai reaktivierung

Datum: 1. März 2025, 18:00 Uhr
Ort: c-base
Protokollführer: cynk

1. anwesende & verantwortlichkeiten
teilnehmende:

    cynk, kristall, sasquatch, riskieee, saces

verantwortlichkeiten & aufgabenbereiche:

*     llm-implementierung → saces
*     rag / finetuning → kristall & riskieee
*     tts / stt & matrix-anbindung → sasquatch
*     hardware-auswahl & installation → kristall & sasquatch
*         alternative serverstandorte prüfen (lüftung, stromkosten!)

2. zielsetzung & mvp-definition (single-user-phase)

ziel: c-beam lokal auf linux/docker zum laufen bringen, mit tts/stt ansprechbar machen

mvp-kernfunktionen:

*     basiswissen abrufbar (absturz & rekonstruktion der raumstation, geschichte der c-base)
*     infos zu aktuellen c-base-events & kalenderintegration
*     sprachschnittstelle (chatbot mit grundlegender sprachsteuerung)

3. infrastruktur & hardware

serverstandort:

*     physisch in der c-base
*     testlauf auf privater hardware, bevor auf eine stabile lösung umgestellt wird

hardware-anforderungen & nutzung:

*     erstbetrieb: private hardware
*     zukünftige nutzung: forschung und parallele modelle ermöglichen (modelltraining)

bestehende systeme nutzen:

*     24/7-server für erste tests
*     lokal starten, dann schrittweise absichern & nach außen öffnen
*     training data: nur öffentliche daten, keine persönlichen informationen
*     sicherheit: separation zwischen public & private data

4. software & schnittstellen

technologie-stack:

*     betriebssystem: linux
*     containerisierung: docker
*     ai-betrieb: lokales llm, ggf. api-zugriff später evaluieren

schnittstellen:

*     web-interface (browser-zugriff oder eigene c-beam app)
*     sprachsteuerung (mikrofon + tts/stt)

5. testing & sicherheit

teststrategie:

*     testserver & prototyp-umgebung
*     zentrale fragen:
*         welche daten können von außen zugeführt werden?
*         wie verhindern wir unerwünschten zugriff?

sicherheitsfragen (zunächst nachrangig)

*     erste mvp-phase ohne security-layers
*     release-phasen später mit ci/cd absicherung

6. zeitplan & meilensteine

ziel: august 2025 – mvp zur 30-jahr-feier der c-base live präsentieren
monat	|meilenstein

märz	|kickoff & konzept finalisieren
april	|erste tests mit hardware & software
mai	    |schnittstellen & basisfunktionen (sprachsteuerung, event-integration)
juni	|mvp alpha-test (rag / c-base history & events)
juli	|bugfixing & feintuning
august	|mvp-live-präsentation mit spendenaufruf für hardware

regelmäßige meetings:

*     jeden 3. montag im monat (ai-enthusiasts-runde)
*     nächstes meeting: 17. märz, 18:00 uhr, main hall

7. dokumentation & kommunikation

wo dokumentieren wir?

*     github, wiki, etherpad

kommunikation & austausch:

*     matrix-kanal: ki / ai / ml

8. langfristige vision & erweiterungen (nach mvp)

was passiert nach dem mvp?

*     circle-vorstellung in der crew
*     entwicklung eines multi-user-systems
*     benchmark-tests: wie viele nutzer kann das system gleichzeitig verwalten?

zukünftige funktionen & erweiterungen:

*     ki-gestützte c-base-assistenz (datenanalyse, sprachinteraktion)
*     mehr schnittstellen für die c-base-infrastruktur (z. b. lichtsteuerung per sprachkommando)
*     persönlichkeitsanpassung: c-beam könnte stimmungsabhängig reagieren (z. b. humorvoll, sarkastisch, neutral) – aber auch deaktivierbar
*     lokal trainiertes modell: eigene datenhaltung, kein cloud-training
*     integration in code.c-base.org
*     visuelle erweiterung: jarvis-ähnlicher ki-assistent mit multimodalem input (bild & text)

dataflow & rag-system-architektur:

*     wie können members content beisteuern?
*     kuratierte basisdaten vs. neue datenquellen
*     multi-bot-system mit spezialisierter silotrennung

9. aufgaben & next steps

to-do für nächstes meeting (17. märz 2025):
aufgabe	                                            |verantwortlich

hardware- & raumfrage klären (anfrage an pisush)	|kristall
erster test auf brotkiste & alternativer hardware	|saces & sasquatch

nächstes meeting:

17. märz 2025
18:00 uhr
main hall, c-base
