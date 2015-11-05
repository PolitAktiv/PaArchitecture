# Architecture: Additional Mappings
The following custom mappings are included to the standard d2rq created mapping:

## JournalArticle_AssetEntry

```
map:JournalArticle_AssetEntry a d2rq:PropertyBridge;
   d2rq:belongsToClassMap map:JournalArticle;
   d2rq:property vocab:AssetEntryOfJournalArticle ;
   d2rq:refersToClassMap map:AssetEntry ;
   d2rq:join "JournalArticle.resourcePrimKey => AssetEntry.classPK";
   d2rq:join "JournalArticle.resourcePrimKey <= AssetEntry.classPK";
   .
```
It links the a JournalArticle with its corresponding AssetEntry and the other way around. This is a many-to-one relation. 

## AssetEntry_Layout
```
map:AssetEntry_Layout a d2rq:PropertyBridge;
   d2rq:belongsToClassMap map:AssetEntry;
   d2rq:property vocab:LayoutOfAssetEntry ;
   d2rq:refersToClassMap map:Layout ;
   d2rq:join "AssetEntry.layoutUuid => Layout.uuid_";
   d2rq:join "AssetEntry.layoutUuid <= Layout.uuid_";
   .
```
It links the an AssetEntry to the Layout (= the page) that the entry lies on in the portal. 

## AssetEntry_AssetTag

```
map:AssetEntry_AssetTag a d2rq:PropertyBridge;
   d2rq:belongsToClassMap map:AssetEntry;
   d2rq:property vocab:AssetTagOfAssetEntry ;
   d2rq:refersToClassMap map:AssetTag ;
   d2rq:join "AssetEntry.entryId <= AssetEntries_AssetTags.entryId";
   d2rq:join "AssetEntries_AssetTags.tagId => AssetTag.tagId";
   .
```
It links an AssetTag to AssetEntries. This is a many-to-many relation.

