# Analysis of the Liferay Database
## JournalArticles (webcontents), AssetEntries, AssetTags
* On the database level, a webcontent is called JournalArticle
* If there are multiple versions of the same webcontent, there exists an JournalArticle entry in the database for each version, each of them with their own ID.
* A JournalArticle is an AssetEntry
* Thus, multiple JournalArticles (different versions of the same webcontent) can reference the same AssetEntry
* to categorize webcontents, AssetTags (german: Schlagworte) can be used
* in the database, JournalArticles are not linked to associated AssetTags directly, however AssetEntries are

## URL
* the link to a given webcontent is composed as follows:
* server + /web + group-name + link-in-the-group + /-/ + /asset_publisher + id-of-asset-publisher + /content + title-of-content
* e.g. https://www.politaktiv.org/web/blaubeuren/pinnwand/-/asset_publisher/3Xv2Pv6noxxQ/content/ernst-georg-bayer-or-pappelau-katastrophale-zustande-
* in order to build the url, on needs to know the id of the publishing asset publisher portlet as well as the page that asset publisher is located on (amongst other things)
* in the database, the Layout holds those required information
