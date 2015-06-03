# Analyse
Nach der Besprechung am 22.5.15 zum Thema Leaflet ergeben sich folgende Änderungen in den Anforderungen an das Kartenportlet:
1. Es soll versucht werden, eine reichhaltige Basis von Leaflet als Grundlage für die Entwicklung zu nutzen. Dafür ist zunächst leaflet.draw vorgesehen. Darauf aufbauend sollten ergänzende Funktionen eingebaut werden. Das könnten die folgenden sein:
2. In jedem Fall muss dazu die Anbindung an die Datenbank in Liferay und die Handhabung der Berechtigungen selbst entwickelt werden.
3. Die Funktion „hinterlegte Grafik statt Karte“ wird nicht benötigt, wenn es gelingt, die Indoor-Funktion (leaflet.indoor) einzubauen, so dass damit Gebäudepläne bearbeitet werden können.
4. Eine Karte soll auf ein bestimmtes Zentrum und eine bestimmte Vergrößerung voreingestellt werden können: leaflet.viewcenter.
5. Die übereinander gelegten Layer sollen verwaltet werden, ein- oder ausgeschlatet werden können. Vorschlag: Leaflet Control Order Layers.
6. Die eingezeichneten Grafiken sollen beschriftet werden: leaflet.label?
Die Beschriftung kann/soll außerhalb der Karte angezeigt werden können: leaflet. ???.
7. Eine Funktion „Print“ soll hinzugenommen werden (leaflet.print?)
8. Die Funktion bookmark soll hinzugenommen werden. Damit soll eine Karte mit eingezeichneten Grafiken (rückwärts) referenzierbar werden: leafleet.bookmark. Eine Vorwärts-Referenzierung kann mit dem Text in der Beschriftung vorgenommen werden.