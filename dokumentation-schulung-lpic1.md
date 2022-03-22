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

## Mittwoch, 16.03.2022

### Kommandosubstitution
- `$(kommando)`: `kommando` wird ausgeführt (in einer Subshell) und durch sein Ergebnis ersetzt: 
  - Bsp.: Unterschied von `var=date` gegenüber `var=$(date)`:  
  ``
  var=date
  echo $var
  > date   # String/Zeichenkette date

  var=$(date)
  echo $var
  > Thu 17 Mar 2022 08:48:09 AM CET  # aktuelles Datum
  ``
- ``kommdo``: ältere Syntax, sonst gleich wie oben
- Konkretes AnwendungsbeispieL: Erstellung Backups mit aktuellem Datum im Dateinamen:
``
tar -cvzf backup-home-tux_$(date +%Y%m%d%M%H).tar.gz /home/tux 
# Erzeugte Datei:
backup-home-tux_202203161103.tar.gz
``
- Nested Command Substitution:
`echo -e "Hallo, ich bin $(cut -d: -f5 /etc/passwd | grep -i $(whoami) | cut -d, -f1). \n \nHeute ist der $(date +'%F, %R Uhr'.)"`

### echo
- `echo -e`: so kann `echo` gewisse Steuerungszeichen interpretieren, um z.B. einen Zeilenumbruch zu erzeugen, einen horizontalen oder vertikalen Tabulator, ein Backspace etc.
- diese Steuerungszeichen / Sequenzen beginnen mit einem `\` (Backslash)
- `echo -e '\n'`: echo gibt eine (zusätzliche) Leerzeile aus (echo an sich führt bereits einen Zeilenumbruch am Ende der Ausgabe aus, so erhalten wir also zwei Leerzeilen) 
- `\n` muss in diesem Fall _escaped_/_maskiert_/_gequotet_ werden, damit nicht die BASH, sondern das Kommando an sich (`echo`) den Backslash als Sonderzeichen interpretieren kann
- der Backslash muss sozusagen vor der Shell "versteckt" werden
- das Escapen kann sowohl durch Einfassen in einfache oder doppelete Anführungszeichen (`'` oder `"`) erfolgen, oder durch die Voranstellung eines Backslashs (`\`) (gilt für jedes Sonderzeichen wie z.B. `\`, `*`, `?` etc.): 
``
echo -e '\n'
echo -e "\n"
echo -e \\n
``

## Donnerstag, 17.03.2022

### Prozesse
Ein Programm resultiert immer in mindestens einem Prozess. Prozesse laufen jeweils in einem von anderen unabhängigen "Resourcenraum", haben eine eigene PID, kennen nur die PID des Prozesses, von dem sie gestartet wurden. Prozesse können mit dem Kommando `kill` über sog. Signale beeinflusst werden.

Auf der Shell kann immer nur ein einzelner Prozess im Vordergrund ausgeführt werden. Prozesse können mit der Tastenkomnination `STRG+Z` angehalten und in den Hintergrund geschickt werden.
- `ps -aux`: Anzeige aller laufende Prozessez
- `ps -ef`: auch Anzeige aller laufenden Prozesse
- `ps --forest`: Prozesshirarchie (Baumstruktur) anzeigen
- `jobs`: Anzeigen der Hintergrundprozesse
- `jobs %<jobnummer>`: bestimmten Job ansprechen
- `fg`: letzten/aktuellen/default Job in den Vordergrund holen
- `fg %<jobnummer>`: Job mit Jobnummer <jobnummer> in den Vordergrund holen
- `bg`: Hintergrundprozess fortsetzen
- `kill`: sendet Siganle an Prozesse 
- `kill -s <signal> <PID>`: sendet <siganl> an Prozess mit der PID <PID>
- `kill -<signal> <PID>`: sendet <siganl> an Prozess mit der PID <PID>
- `pkill`: analog zu oben, `pkill` erwartet aber den Namen bzw. einen Teil des Namesns eines Prozesses anstatt der PID
- `killall`: wie oben, erwartet aber den exakten Prozessnamen
- `pgrep`: PID laufender Prozesse ermitteln, ähnlich wie `ps -ef | grep`

### Liste der Status
- S, > I ...


## Freitag, 18.03.2022

- `nohup`: aufgerufener Prozess wird von der aufrufenden Shell gelöst, so dass dieser Prozess auch weiterläuft, wenn die aufrufenden Shell beendet wird
- `nohup ping 1.1.1.1 > ping.out`: Ausgabe von `ping` in Datei `ping.out` umleiten
- `tail -f`: fortlaufende Beobachtung einer Datei (neue Einträge werden automatisch angezeigt)
- `top`: Anzeige laufender Prozesse, ähnlich zum Taskmanager unter Windows, Prozesse können auch interaktiv beeinflusst werden
- `htop`: komfortablere Varinate von `top`
- `screen, tmux`: *Terminalmulitpexer*, mehrere Shells können in einem Terminal gestartet werden, Fenster können auf bestimmte Arten angeordnet werden etc.

## Montag, 21.03.2022

### Bootvorgang
- Rechner anschalten
- BIOS/UEFI wird gestartet (befindet sich auf CMOS)
- POST (Power On Self Test): grundlegender Hardwaretest (welche ist vorhanden?)
- im BIOS/UEFI eingestelltes Startmedium (Festplatte, USB, Netzwerk) bzw. die Konfiguration wird eingelesen 
- Medium wird gestartet
- Bootloader wird gestartet
  - bei MBR partitionierten Platten (BIOS): 1. Teil des Bootloaders befindet sich in den ersten 440 Byte (restlichen 64 Byte sind die Partitionstabelle, Rest *Magic Number*)
  - bei GPT partitionierten Platten (UEFI): auf der EFI-Partition (in der Regel z.B. 100 MB gross, FAT32 formatiert) vorhandene EFI-Applikation wird gestartet, diese startet dann den eignetlichen Bootloader
  - GRUB2 kann sowohl MBR als auch GPT 
  - UEFI liest den MBR nicht aus, benötig also eine GPT basierte Formatierung
  - ein Feature von UEFI ist *Secure Boot*, wenn aktiviert, können nur *signierte*/vertrauenwürdige EFI-Applikationen gestartet werden (*TPM*), hierfür muss eine entsprechende Signatur/Schlüssel erworben/bereitgestellt werden
  - sieht auch 
    - https://www.windowspro.de/wolfgang-sommergut/faq-zu-uefi-bios-guid-partition-table-gpt-master-boot-record
    - https://www.thomas-krenn.com/de/wiki/UEFI_Secure_Boot
- Bootloader startet den Kernel
- durch den Bootloader können dem Kernel bestimmte Startoptionen übergeben werden 
  - entweder nur für diesen Bootvorgang durch Drücken der Taste `e` im GRUB Menu
  - oder über die Datei `/boot/grub/grub.cfg`
  - diese wird aber nicht direkt editiert, sondern ein *Template* unter `/etc/default/grub`
  - anschliessend muss das Kommando `grub-mkconfig -o /boot/grub/grub.cfg` ausgeführt werden
  - das Kommando `update-grub` ist ein Wrapperskript für obiges Kommando, kann alternativ genutzt werden
- Kernel initialisiert Hardware, lädt Treiber etc. (-> *Kernelspace*)  
- anschliessend startet der Kernel einen einzigen Prozess (mit der PID 1)
- dies ist der Prozess `init` bzw. auf `systemd` basierten Systemen manchmal auch `systemd` genannt
- dieser Prozess startet alle anderen für den Betrieb notwendigen Prozesse

### SysV-Init
- altes Init-System, Vorgänger von Systemd
- Prozesse werden sequentiell, also nacheinander gestartet
- Konfiguration dieser Vorgänge über Shell-Skripte
- diese liegen unter `/etc/init.d`

#### Runlevel
- Betriebszustände in denen sich das System befinden kann
- werden beim Bootvorgang durchlaufen
- es gibt die Runlevel 1 bis 6
- Runlevel werden in der Datei `/etc/inittab` definiert
  - Runlevel 0: Computer ausschalten
  - Runlevel 1: Single User Mode / Rescue Mode (vergleichbar mit dem Abgesicherten Modus)
  - Runlevel 2: Multi User Mode
  - Runlevel 3: zusätzlich Netzerk
  - Runlevel 4: nicht genutzt
  - Runlevel 5: zusätzlich grafische Oberfläche
  - Runlevel 6: Reboot
- Definition ist nicht starr, kann angepasst werden
- hier wird aussedem der Default Runlevel gesetzt und das Verhalten der Tastenkombination `STRG+ALT+ENTF` eingestellt
- in den Verzeichnissen `/etc/rc0.d` bis `/etc/rc6.d` liegen Symlinks auf Skripte, die Dienste starten bzw. stoppen
- Skripte/Dienste mit einem `S` am Anfang werden beim Betreten des Runlevel gestartet, solche mit einem `K` entsprechend gestoppt, Reihenfolge über Zahlen hinter `S` oder `K`


