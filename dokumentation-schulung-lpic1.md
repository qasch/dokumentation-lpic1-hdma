# Dokumentation Schulung LPIC1 Heidelberg-Mannheim

## Dienstag 15.3.2022

### Variablen

- `var_name=eins`: weist der Variablen `var_name` den Wert `eins` zu
- `echo $var_name`: gibt den Inhalt von `var_name` (`eins`) aus (die Variable wird *substituiert*)
- Variablen sind immer nur in der aktuellen Shell gültig
- Variablen können mit dem Kommando `export` in Subshells vererbt werden
- Subshell: eine innerhalb der Shell geöffnete Shell bzw. eine Shell, deren Elternprozess eine andere Shell ist
- wird die Elternshell beendet, beendet dies automatisch auch die Subshell

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
  - Bsp.: Sowohl Ausgabe als auch Fehler in gleiche Datei leiten: `kommando >textdatei 2>&1` bzw. `kommando >& textdatei` 

### UNIX Philosophie
- Schreibe Computerprogramme so, dass sie nur eine Aufgabe erledigen und diese gut machen.
- Schreibe Programme so, dass sie zusammenarbeiten.
- Schreibe Programme so, dass sie Textströme verarbeiten, denn das ist eine universelle Schnittstelle.
- vereinfacht: Mache nur eine Sache und mache sie gut / KISS (Keep It Simple, Stupid) Prinzip

### Filterkommandos
Textströme können mit Filterkommandos bearbeitet werden, so dass die Information, die uns interessiert, herausgefiltert werden kann.

- `cut`: schneidet Spalten aus tabellarisch aufgebauten Dateien aus (`cut -d: -f1 /etc/passwd`: nur die Benutzernamen ausgeben)   
- `tail`: gibt die letzen (Standarmässig 10) Zeilen einer Datei aus (`tail -n5 /etc/passwd`: gibt die letzten 5 Zeilen der `passwd` aus)
- `grep`: sucht nach einem Suchbegriff innerhalb von Dateien/Textströmen und gibt die entsprechende Zeile aus (`grep bash /etc/passwd`: gibt alle Zeilen aus, in denen der String `bash` vorkommt)
- `tr`: übersetzt ein Zeichen in einem Textstrom (es können keine Dateien als Argument übergeben werden) in ein anderes/löscht dieses etc... (`tr a A < datei.txt`: wandelt jedes kleine `a` in ein grosses `A` um)
- `tee`: verzweigt den Textstrom, so dass sowohl eine Ausgabe erfolgt, als auch in eine Datei geschrieben werden kann (`ls /etc | tee ls-etc.txt`)



- `wc`: gibt die Anzahl der Zeilen, Wörter und Bytes einer Datei an (`wc -l /etc/passwd`: Anzahl Zeilen der Datei `/etc/passwd`)
