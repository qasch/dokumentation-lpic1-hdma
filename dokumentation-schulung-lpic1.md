# Dokumentation Schulung LPIC1 Heidelberg-Mannheim

## Dienstag 15.3.2022

### Textströme, Umleitungen und Redirects

- Standardkanäle:
  - Standardeingabekanal `stdin` - Kanal 0
  - Standardausgabekanal `stdout` - Kanal 1
  - Standardfehlerkanal `stderr` - Kanal 2
- hiermit kann der Textfluss von Textströmen gesteuert werden
- Kanäle können umgeleitet werden, entweder in Dateien oder andere Kommandos  
  - `kommando 1>datei`: Ausgabe von `kommando` wird in Datei umgeleitet, Inhalt der Datei wird ersetzt
  - `kommando > datei`: gleich wie oben, `1` kann weggelassen werden
  - `kommando >> datei`: gleich wie oben, Inhalt wird an Datei angehängt
  - `kommando < datei`: Inhalt von datei wird an die Standardeingabe von Kommando gesendet/umgeleitet
  - `kommando1 | kommando2`: die Ausgabe (Kanal 1) von `kommando1` wird an die Eingabe (Kanal 0) von `kommando2` geleitet 

