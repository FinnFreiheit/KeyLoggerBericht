## Gliederung

* Begriffsdefinition
* Vergleich unterschiedlicher Key-Logger
  * Drahtgebundene 
  * Drahtlose 
  * Firmenware Keylogger  
* Schutz vor Keylogger 
* Gafahrenbewertung 
* eigenen Key-Logger implementieren (Python)


# Begriffsdefinition 

# Drahtgebundene Hardware-Keylogger

Drahtgebunde Hardware-Keylogger sind Geräte, die zwischen der Tastatur und dem Rechner platziert werden. Der Hardware-Keylogger agiert dann als *"Man-in-the Middle"*. Das bedeutet, sämtliche Daten, die von der Tastatur an den Rechner versendet werden, durchqueren den Keylogger und können dadurch von dem Keylogger gespeichert oder über ein Netzwerk versendet werden. 
Auf der Website *keelog.com* können solche Hardware-Keylogger für 32 Euro erworben werden (Stand 2022). Die Seite wirbt mit einem 16GB großen Speicher, Kompatibilität mit  Windows, Linux und MacOS ohne zusätzliche Treiber und das Der Keylogger vom Rechner nicht detektiert werden kann.

# Drahtlose Keylogger

Viele Tastaturen benötigen keine physische Verbindung mit dem Rechner um Daten zu übertragen. Stattdessen findet die Datenübertragung über Funkwellen wie Bluetooth statt. Die drahtlose Übertragung kann somit nicht durch ein Hardware-Keylogger inspiziert werden. 
Bei der Tastatur handelt es sich jedoch um ein *Broadcast-Medium*, das bedeutet, die Tastatur versendet die Daten mithilfe von Elektromagnetischen Wellen in den gesamten Raum. Diese Wellen können von jedem Gerät in Reichweite der Tastatur gelesen werden. Damit die Daten der Tastatur somit nicht von dritten gelesen werden können ist es essentiell, das diese verschlüsselt werden. Marc Newlin aus dem Bastille Research Team hat bei Untersuchungen jedoch festgestellt, das besonders bei Preiswerten drahtlosen Tastaturen keine Verschlüsselung der Daten erfolgt [Newlin]. Durch die unverschlüsselte Übertragung entsteht neben dem Lesen von Daten eine weiter Sicherheitslücke. Ist das Übertragungsprotokoll zwischen Tastatur und Rechner bekannt, so kann der Angreifer auch Daten an den Rechner senden, der Angreifer kann somit Eingaben auf dem Rechner des Angegriffenen durchführen ohne einen direkten physischen Zugang zu dem Rechner zu besitzen. 
Einige Tastaturen verfügen über ein USB-Dongle, der an den Rechner angeschlossen wird und somit die Übertragung ermöglicht. Viele dieser Dongles übertragen in periodischen Zeitabständen Datenpakete. Ein Angreifer ist durch diese Datenpakete in der Lage ein Gebiet nach möglichen Schwachstellen zu Scannen. Die Verwendung von Verstärkern ermöglicht auch ein Scannen außerhalb von Bürogebäuden. 


# Firmware-Keylogger

Bei Firmware-keyloggern wird die Software der Tastatur um eine schadhafte Komponente erweitert. Diese Komponente könnte dann die Tastatureinschläge auch an den Angreifer weiterleiten. Auf der IT-Sicherheitskonferenz im Jahr 2009 hat K.Chen das Konzept anhand einer Apple-Tastatur erläutert [Chen]. In der Praxis sind jedoch keine größeren Angriffe durch Firmware-Keylogger publik geworden [Hessel].

## Bewertung 

- **Skizzieren Sie ein Beispiel** anhand dessen Sie die folgenden Fragen beantworten und erklären Sie kurz warum Sie speziell dieses gewählt haben:
- **Zeigen Sie die Sicherheitsaspekte** Ihres gewählten Beispiels auf und diskutieren Sie diese:
- Wählen Sie ein Szenario zu Fehlfunktion, Hack, Ausfall und beschreiben Sie die **Auswirkungen für die Betroffenen**:
- Beschreiben Sie nun Mechanismen um dem **entgegen zu wirken**, dies zu verhindern, zu erkennen, einzudämmen, darauf zu reagieren:


# Quellen 

[Newlin] Keysniffer technical details, Marc Newlin, Bastille Research Team, https://keysniffer.net/technical-details/


[Hessel] Hardware-Keylogger: Die Tastatur in der Hand des Feindes, Stefan Hessel,  Dipl.-Jur. Stephanie
Vogelgesang, Frederik Möllers, MSc., 2016, 
https://link.springer.com/content/pdf/10.1007/s11623-016-0692-9.pdf

[Chen] Reversing and exploiting an Apple firmware update, K. Chen, Georgia Institute of Technology, 2009, https://www.blackhat.com/presentations/bh-usa-09/CHEN/BHUSA09-Chen-RevAppleFirm-PAPER.pdf