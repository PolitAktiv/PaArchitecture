# Realization


```
SELECT DISTINCT ?assetOfWebcontent ?titel ?url max(?version) ?DK ?Schlagwort ?autor ?webcontentInhalt WHERE {

  #Suche den Asset zu einem Webcontent, verlinke sie
  ?webcontent vocab:JournalArticle_resourcePrimKey ?webcontentAssetLink .
  ?assetOfWebcontent vocab:AssetEntry_classPK ?webcontentAssetLink .
  
  # relation: webcontent nach asset
  # ?webcontent vocab:AssetEntryOfJournalArticle ?assetOfWebcontent .

  #die Id des Assets
  ?assetOfWebcontent vocab:AssetEntry_entryId ?entryId .

  #Stelle Verbindung zwischen Asset und AssetTag (=Schlagwort) her
  ?entriesTagLink vocab:AssetEntries_AssetTags_entryId ?entryId .
  ?entriesTagLink vocab:AssetEntries_AssetTags_tagId ?tagId .

  # relation: asset nach assettag 
  # assetOfWebcontent vocab:AssetTagOfAssetEntry ?tag
  
  # die Id des Tags
  ?tag vocab:AssetTag_tagId ?tagId .


  #Extrahiere aus dem Webcontent: Schlagwort, Titel, Version, Inhalt, Autor
  #der Titel wird hierbei aus dem HTML-Gewusel, das ihn umgibt, herausgeschnitten
  ?tag vocab:AssetTag_name ?Schlagwort .
  ?webcontent vocab:JournalArticle_content ?webcontentInhalt .
  ?webcontent vocab:JournalArticle_userName ?autor .
  ?webcontent vocab:JournalArticle_version ?version .
  ?webcontent vocab:JournalArticle_urlTitle ?webcontentURLTitle .
  ?webcontent vocab:JournalArticle_title ?webcontentTitel.
  BIND (replace(strbefore(?webcontentTitel, "</Title>"), "<.*>", "") AS ?titel) .

  # Schränke Suche auf Diskussionskreis ein, z.B. Blaubeuren
  ?webcontent vocab:JournalArticle_groupId ?groupId .
  ?community vocab:Group__friendlyURL "/blaubeuren" .
  ?community vocab:Group__friendlyURL ?DK .
  ?community vocab:Group__groupId ?groupId .

  # Suche url der Page/Seite (in der Datenbank: Layout), auf der Asset liegt
  ?asset vocab:AssetEntry_layoutUuid ?uuid .
  ?layout vocab:Layout_uuid_ ?uuid .
  ?layout vocab:Layout_friendlyURL ?layoutURL .
  ?layout vocab:Layout_groupId ?groupId .
  
  # relation: asset und layout
  ?asset vocab:LayoutOfAssetEntry ?layout
  

  # Schränke Suche ein auf Pages/Seiten, welche in ihren TypeSettings das Portlet 101 (Nummer des Asset Publishers) einbinden
  ?layout vocab:Layout_typeSettings ?typeSettings .
  FILTER regex(?typeSettings, "101_INSTANCE_*") 

  # Suche die die Portlet-Id des Asset Publishers, welcher einen Content einbindet
  # bringe dazu die Typesettings in eine künstliche Struktur, um mit dem nächsten Aufruf den richtigen Substring errechnen zu können
  BIND (replace(replace(replace(?typeSettings, "\n", "#"), " ", "#"), ",", "#") AS ?searchString) .
  # berechne die Id aus dem Substring
  BIND (strbefore (strafter(?searchString, "101_INSTANCE_"), "#") AS ?assetPublisherInstanceId) .
  # erstelle Link (Beispiellink: https://intermediate.intra.politaktiv.org/web/blaubeuren/pinnwand/-/asset_publisher/3Xv2Pv6noxxQ/content/ernst-georg-bayer-or-pappelau-katastrophale-zustande-)
  BIND ((concat ("intermediate.intra.politaktiv.org/web", ?DK, ?layoutURL, "/-/asset_publisher/", ?assetPublisherInstanceId, "/content/", ?webcontentURLTitle)) AS ?url) .


} 
GROUP BY ?assetOfWebcontent ?titel ?url ?DK ?Schlagwort ?autor ?webcontentInhalt
ORDER BY DESC(?url)
```