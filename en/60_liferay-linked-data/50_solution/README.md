# Solution

## Query

 
```
SELECT DISTINCT ?assetOfWebcontent ?titel ?url max(?version) ?DK ?Schlagwort ?autor ?webcontentInhalt WHERE { 

  #Get the asset, a webcontent belongs to
  ?webcontent vocab:AssetEntryOfJournalArticle ?assetOfWebcontent .

  #Get the asset tags that belong to an asset
  ?assetOfWebcontent vocab:AssetTagOfAssetEntry ?tag .
  
  #Extrahiere from the webcontent: asset tag name, title, version, content, author
  ?tag vocab:AssetTag_name ?Schlagwort .
  ?webcontent vocab:JournalArticle_content ?webcontentInhalt .
  ?webcontent vocab:JournalArticle_userName ?autor .
  ?webcontent vocab:JournalArticle_version ?version .
  ?webcontent vocab:JournalArticle_urlTitle ?webcontentURLTitle .
  ?webcontent vocab:JournalArticle_title ?webcontentTitel.
  #remove HTML-tags from title
  BIND (replace(strbefore(?webcontentTitel, "</Title>"), "<.*>", "") AS ?titel) .

  # limit to one community (Diskussionskreis) (e.g. Blaubeuren)
  ?webcontent vocab:JournalArticle_groupId ?groupId .
  ?community vocab:Group__friendlyURL "/blaubeuren" .
  ?community vocab:Group__friendlyURL ?DK .
  ?community vocab:Group__groupId ?groupId .

  # URL
  # get layout (=page) that is linked to the asset
  ?asset vocab:LayoutOfAssetEntry ?layout .
  ?layout vocab:Layout_friendlyURL ?layoutURL .
  ?layout vocab:Layout_groupId ?groupId .
  
  # limit to pages that mention portlet 101 (portlet number of the asset publisher) in their type settings
  ?layout vocab:Layout_typeSettings ?typeSettings .
  FILTER regex(?typeSettings, "101_INSTANCE_*") 

  # search portlet-id of that specific asset publisher that include the webcontent
  # therefore, create an artificial string ...
  BIND (replace(replace(replace(?typeSettings, "\n", "#"), " ", "#"), ",", "#") AS ?searchString) .
  # ... that can be used for further string operations (that extract the portlet id)
  BIND (strbefore (strafter(?searchString, "101_INSTANCE_"), "#") AS ?assetPublisherInstanceId) .
  # create Link (e.g. https://politaktiv.org/web/blaubeuren/pinnwand/-/asset_publisher/3Xv2Pv6noxxQ/content/ernst-georg-bayer-or-pappelau-katastrophale-zustande-)
  BIND ((concat ("intermediate.intra.politaktiv.org/web", ?DK, ?layoutURL, "/-/asset_publisher/", ?assetPublisherInstanceId, "/content/", ?webcontentURLTitle)) AS ?url) .

} 
GROUP BY ?assetOfWebcontent ?titel ?url ?DK ?Schlagwort ?autor ?webcontentInhalt
ORDER BY DESC(?url)
 
```

## Necessary query adaptations
### Community
The query above works for a specific community "/blaubeuren". This may be changed, before running the query. Use the friendly URL of the community. For example "/inklusion-schwaebisch-gmuend"  in https://politaktiv.org/web/inklusion-schwaebisch-gmuend.
### Server
In the last BIND-statement, the server on which the query is opperating, is hard-coded. This must be changed in order to work. E.g.:
replace "intermediate.intra.politaktiv.org/web" with "politaktiv.org/web".

## Query result
The following facts are important:
* Since a \# starts a comment, the query above can be copied, pasted and run as it is. 
* for every asset, there are several webcontents. Every webcontent entry resembles one version of the webcontent
* in its current form, the query only shows assets, which have at least one asset tag 




















