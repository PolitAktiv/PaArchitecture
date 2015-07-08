# Architektur
(Zwischenstand/Sammlung)

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



## Zusammengefasst (das, was später der Inhalt dieses Files sein soll)

### Oberflächenkonzept
* wir benutzen die vorhandene Aufteilung des Containers für UI-Komponenten von Leaflet
  * je ein Container in allen 4 Ecken
  * Floating innerhalb dieser Container

### Codeintegrationskonzept
* Plugins werden heruntergeladen und ins Liferay-Portlet-Projekt importiert
* liferay-portlet.xml bindet jeweils das Javascript und das CSS eines Plugins ein
* die Liferay jsps nutzen nutzen die Plugin-Funktionalität

### Abhängigkeitenkonzept
* Namenskollisionen sind wahrscheinlich (!) bereits durch die Namenskonvention der Pluginentwickler vermieden
* Plugins selbst haben bisher keine Abhängigkeiten außer zu Leaflet
* Leafletversion passt zu Plugins
* Versionsproblematiken einzelner Plugins werden durch das Herunterladen und lokale Einbinden vermieden

