# Architektur
(Zwischenstand/Sammlung)


## Oberflächenkonzept
### Menüpunkte/UI-Komponenten
* wir benutzen die vorhandene Aufteilung des Containers für UI-Komponenten von Leaflet
  * je ein Container in allen 4 Ecken
  * Floating innerhalb dieser Container
* auf Codeebene
  * Plugins mit UI-Komponenten bieten per Konvention eine von der Klasse 'Control' geerbte Klasse.
  * ein Objekt der [Plugin]-Control Klasse wird dann initialisiert und einfach der Karte hinzugefügt
  * eine der 4 Ecken wird hier spezifiziert
### Nützliche Plugins
* https://github.com/aratcliffe/Leaflet.contextmenu Kontextmenü
  * bei Rechtsklick Kontextmenü öffnen
  * gute Möglichkeit, um Interaktion unterzubringen
* https://github.com/calvinmetcalf/leaflet-ajax Ajax-Unterstützung
  * falls wir Ajax-Funktionalität benötigen um aus DB zu laden
  * Liferay hat Ajax bereits
  * sicher noch Forschung nötig
* https://github.com/aratcliffe/Leaflet.twofingerzoom Auf Tablet und Smartphone mit zwei Fingern zoomen
* https://github.com/turbo87/leaflet-sidebar/ Side-Bar
  * eventuell als Marker-Popup-Ersatz
  * noch mächtigere Version: http://turbo87.github.io/sidebar-v2/examples/gmaps.html#settings
* https://github.com/tinuzz/leaflet-messagebox 
  * für die Einblendung von Anweisungen oder Hilfetexten
* https://github.com/gregallensworth/L.Control.Credits
  * für Werbung 

## Codeintegrationskonzept
* kein tatsächlicher Plugin-Mechanismus zur Einbindung von Plugins
* es wird einfach der entsprechende Plugin-Code geladen
### Möglichkeit 1: alle Plugins einzeln einbinden
* Vorteile
  * einfacher wartbar
  * keine zusätzlichen Tools nötig
* Nachteile
  * Browser muss mehr Files laden -> Geschwindigkeitseinbußen
* Vorgehen 
  * Plugins werden heruntergeladen und ins Liferay-Portlet-Projekt importiert
  * liferay-portlet.xml bindet jeweils das Javascript und das CSS eines Plugins ein
  * die Liferay jsps nutzen nutzen die Plugin-Funktionalität
### Möglichkeit 2: ein großes Javascript-File
* Vorteile
  * Geschwindigkeitsgewinn
* Nachteile
  * schwieriger zu warten / öfter Rebuild nötig
  * zusätzliche Tools benötigt
### Nützliche Tools für Javascript-Code-Integration
* Module Loaders (RequireJS/Browserify): Binden eine Art Require/Include-Funktionalität ein
* Code-Kompressoren (uglify-js/merge-js): Minimieren Größen von Javascript-Files (Variablennamen verkürzen, Whitespaces entfernen, etc.)
* Namenskollisions-Schutz-Tools (jake) (genutzt Leaflet): Prüft Code auf Namenskollisionen
### Auslieferung
* Code sollte von uns kommen
  * keine Versionsprobleme, da fix
  * keine Zugriffsprobleme: Quellen sind z.T. http (Nachladen von http-Responses auf https-Seite wird von Browsern gestoppt)
* es wird empfohlen, allen Code auf einmal auszuliefern (Möglichkeit 2, s.o.)
### eigener Leaflet-Build
* Idee: 
  * eigene Leaflet-Version bauen mit der stock-Funktionalität, die wir benötigen
  * Plugins in diese Leaflet-Version mergen
  * ausliefern
* Problematiken
  * Plugins liegen auf github
    * also nicht in einem zentralen Repository
    * haben manchmal nicht einmal ein release
    * Code wird unterschiedlich angeboten (github-release, git-clone, Internetreferenz)
  * Rebuild nach jeder kleinen Änderung nötig
     

## Abhängigkeitenkonzept
### Code
* Namenskollisionen sind wahrscheinlich (!) bereits durch die Namenskonvention der Pluginentwickler vermieden
* Plugins selbst haben bisher keine Abhängigkeiten außer zu Leaflet
* Leafletversion muss zu Plugins passen
* Versionsproblematiken einzelner Plugins werden durch das Herunterladen und lokale Einbinden vermieden
### Logik
* Logische Überschneidungen
  * z.B. verschiedene Plugins arbeiten mit Overlays
  * Requirements für Interaktion werden nötig sein
  * Konzept für Interaktion wird nötig sein
* Event-Trigger
  * Kontrolbefehle (Rechtsklick, Linksklick, Pfeiltasten, etc.) sind unter Umständen bereits belegt
  * Lösungsansätze
    * andere Tastenbelegung wählen (einfach)
    * Funktionalität auslagern, z.B. in Kontext-Menü (falls möglich) 


# Anmerkungen
## Allgemeines 
* Auf der Leaflet-Plugins Seite existiert ein Tab für third party integrations. Wenn die Liferay-Integration geklappt hat, können wir dort auf unser Portlet verlinken und Fame abstauben
## Weitere interessante Plugins
* https://github.com/yohanboniface/Leaflet.RevealOSM 
  * Klick auf Karte liefert zusätzliche Informationen zu angeklicktem Ort
* https://github.com/mlevans/leaflet-hash 
  * direktes Springen auf Karte via URL
  * da wir es in einem Portlet benutzen, wird das nicht so einfach zu integrieren sein
* https://github.com/astromatic/Leaflet.TileLayer.IIP
  * Bild statt Karte



# Notizen

## Oberflächenkonzept
### Aufbau
* Die Leafpad-Oberfläche hat auf html-Ebene grob folgende Container aufgeteilt:
  * .leaflet-map-pane
    * .leaflet-tile-pane
    * .leaflet-objects-pane
  * .leaflet-control-container
    * .leaflet-top .leaflet-left
    * .leaflet-top .leaflet-right
    * .leaflet-bottom .leaflet-left
    * .leaflet-bottom .leaflet-right
* im leaflet-tile-pane liegen die geladen Karten-Layer
* im leaflet-objects-pane liegen diverse Objekte wie z.B. Marker oder Overlays
* im leaflet-control-container liegen die Kontrollelemente für den User
  * für jede der vier Ecken existiert so ein Container
  * innerhalb des Containers können die Kontrollelemente schön gefloated werden
  * bzw. tun sie das bereits defaultmäßig

### Erkenntnisse
* wir werden um ajax nicht umher kommen
* die wir werden wohl keine UI-Anordnungs und -Integrationsproblematiken haben, Leaflet löst das bereits gut, da ...
* ... man einer UI-Komponente einen der vier Ecken im Fenster zuweisen kann

### Konzept
* Anordnung der Kontrollelemente erstmal default lassen
* Floating passiert von alleine
* Anordnung kann einfach per CSS angepasst werden

## Codeintegrationskonzept
### Die Problematik
* Wir wollen eine Menge Plugins nutzen
  * diese kommen mit eigenen Abhängigkeiten (Plugins, Versionen)
  * die Plugins selbst werden unter Umständen noch weiterentwickelt
* Javascript-Bibliotheken können theoretisch zu Kollisionen führen

### Pluginintegration bisher
* Die Einbindung eines Plugins erfolgt in folgenden Schritten
  * Download des Plugins von Github
    * wenn möglich neuestes Release
    * wenn kein Release veröffentlicht, dann momentenen Master-Zustand;
  * Einbindung des heruntergeladenen Plugins ins Leaflet-Portlet-Projekt in den Ordner docroot/plugins
    * mit Version oder Datum im Namen
  * CSS und JS des heruntergeladenen Plugins ins Projekt einbinden. Dazu in liferay-portlet.xml einfügen:
	* <header-portlet-css>[/pfad/zum/plugin/css]</header-portlet-css>
	* <footer-portlet-javascript>[/pfad/zum/plugin/javascript]</footer-portlet-javascript>
  * das Plugin in view.jsp nach Anleitung benutzen
* Leaflet binden wir vom Leaflet-Server aus ein (Leaflet selbst ist wahrscheinlich relativ groß), ebenfalls in liferay-portlet.xml
* Wir laden uns zur Sicherheit die momentan benutze Leaflet-Version herunter und speichern sie außerhalb des Projekts (falls sie einmal benötigt wird) 
* Wir laden die Plugins alle runter und binden sie bei uns ins Projekt ein ( d.h. wir brauchen in jedem Fall css, js und evtl. Bilder o.Ä.) weil...
  * sie klein sind
  * wir dann einen definierten Stand haben (Code, Abhängigkeiten, etc.)

### Pluginintegration alternative
* eigenen Leaflet-Build (npm ?)

### Javascript-Codeintegration
* ziemlich sicher keine Namenskonflikte, da sich die Pluginentwickler (der bisher ausprobierten Plugins) an Namespace-Klassen-Konventionen halten
* im Zweifelsfall müssen wir wrappen:
  * http://stackoverflow.com/questions/881515/how-do-i-declare-a-namespace-in-javascript
  * http://stackoverflow.com/questions/9635973/can-i-sandbox-3rd-party-javascript-to-protect-the-global-namespace
  * http://www.kenneth-truyers.net/2013/04/27/javascript-namespaces-and-modules/ 
* nach der Einbindung (s.o.) können die neuen Funktionalitäten einfach genutzt werden, d.h. meistens
  * für UI-Komponenten
    * (Definieren von einem plugin-spezifischen Optionen-Objekt (optional, überbügelt default-Einstellungen))
    * Erstellung einer neuen UI-Einheit (mit diesen Optionen)
    * Hinzufügen der UI-Komponente zur Karte
  * für Layer
    * Layer erstellen
    * (Layer mit Optionen versehen)
    * Layer zur Karte hinzufügen


### CSS-Codeintegration
* da sich unterschiedliche Plugins eventuell unterschiedliche (CSS-)Klassennamen teilen, kommt es zu Überschneidungen im CSS
* d.h. das zuletzt geladene CSS hat bei Kollisionen Priorität
* d.h. es ist keine gute Idee, Reihenfolge der CSS-Code-Einbindungen zu verändern
* in den meisten Fällen wird es sich bei diesen Kollisionen um Farben, o.Ä. handeln
* es bietet sich an, den Plugin-CSS-Code zwar jeweils einzubinden, die Korrekturen dann allerdings auf Portlet-Ebene zentral zu verwalten


