---
title: Veld toewijzingen voor automatische indexering met Indexeer functies-Azure Search
description: Configureer Azure Search Indexeer functie-veld toewijzingen aan het account voor verschillen in veld namen en gegevens representaties.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b64f6dcecb26e35689ad6f569ade6c7862f06f1a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648125"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Veld Toewijzingen en trans formaties met Azure Search Indexeer functies

Wanneer u Azure Search Indexeer functies gebruikt, merkt u soms dat de invoer gegevens niet echt overeenkomen met het schema van uw doel index. In dergelijke gevallen kunt u **veld Toewijzingen** gebruiken om de vorm van uw gegevens tijdens het indexerings proces te wijzigen.

Een aantal situaties waarin veld Toewijzingen handig zijn:

* De gegevens bron bevat een veld met `_id`de naam, maar Azure Search staat geen veld namen toe die beginnen met een onderstrepings teken. Met een veld toewijzing kunt u effectief de naam wijzigen van een veld.
* U wilt verschillende velden in de index vullen op basis van dezelfde gegevens bron gegevens. Het is bijvoorbeeld mogelijk dat u verschillende analyse functies op deze velden wilt Toep assen.
* U wilt een index veld vullen met gegevens uit meer dan één gegevens bron en de gegevens bronnen gebruiken verschillende veld namen.
* U moet uw gegevens met Base64 coderen of decoderen. Veld Toewijzingen ondersteunen verschillende **toewijzings functies**, waaronder functies voor Base64-code ring en-decodering.

> [!NOTE]
> De functie voor veld toewijzing van Azure Search Indexeer functies biedt een eenvoudige manier om gegevens velden toe te wijzen aan index velden, met een aantal opties voor gegevens conversie. Complexere gegevens kunnen vooraf worden verwerkt om deze te wijzigen in een formulier dat gemakkelijk kan worden geïndexeerd.
>
> Microsoft Azure Data Factory is een krachtige cloud-gebaseerde oplossing voor het importeren en transformeren van gegevens. U kunt ook code schrijven om bron gegevens te transformeren vóór het indexeren. Zie voor code voorbeelden [modellen relationele gegevens model leren](search-example-adventureworks-modeling.md) en facetten met [meerdere modellen](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Veld toewijzingen instellen

Een veld toewijzing bestaat uit drie delen:

1. A `sourceFieldName`, waarmee een veld in de gegevens bron wordt aangeduid. Deze eigenschap is vereist.
2. Een optioneel `targetFieldName`, waarmee een veld in uw zoek index wordt aangeduid. Als u dit weglaat, wordt dezelfde naam gebruikt als voor de gegevens bron.
3. Een optioneel `mappingFunction`, waarmee u uw gegevens kunt transformeren met behulp van een van de vooraf gedefinieerde functies. [Hieronder](#mappingFunctions)vindt u de volledige lijst met functies.

Veld toewijzingen worden toegevoegd aan de `fieldMappings` matrix van de definitie van de Indexeer functie.

## <a name="map-fields-using-the-rest-api"></a>Velden toewijzen met behulp van de REST API

U kunt veld toewijzingen toevoegen wanneer u een nieuwe Indexeer functie maakt met behulp van de API-aanvraag voor het maken van de [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) . U kunt de veld toewijzingen van een bestaande Indexeer functie beheren met de API-aanvraag [Update indexer](https://docs.microsoft.com/rest/api/searchservice/update-indexer) .

Hier volgt een voor beeld van het toewijzen van een bron veld aan een doel veld met een andere naam:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

In meerdere veld toewijzingen kan naar een bron veld worden verwezen. In het volgende voor beeld ziet u hoe u een veld kunt ' fork ', waarbij u hetzelfde bron veld kopieert naar twee verschillende index velden:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search maakt gebruik van niet-hoofdletter gevoelige vergelijking om de veld-en functie namen in veld toewijzingen op te lossen. Dit is handig (het is niet nodig om alle hoofd letters op te halen), maar het betekent wel dat uw gegevens bron of index geen velden kan bevatten die alleen per geval verschillen.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Velden toewijzen met behulp van de .NET SDK

U definieert veld toewijzingen in de .NET SDK met behulp van de klasse [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , die de `SourceFieldName` eigenschappen `TargetFieldName`en bevat en een `MappingFunction` optionele verwijzing.

U kunt veld Toewijzingen opgeven wanneer u de Indexeer functie bouwt of later door de `Indexer.FieldMappings` eigenschap rechtstreeks in te stellen.

In het C# volgende voor beeld worden de veld Toewijzingen ingesteld bij het maken van een Indexeer functie.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Functie voor veld toewijzing

Met een veld toewijzings functie wordt de inhoud van een veld getransformeerd voordat deze in de index wordt opgeslagen. De volgende toewijzings functies worden momenteel ondersteund:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>de functie base64Encode

Voert *URL-veilige base64-* code ring van de invoer teken reeks uit. Er wordt van uitgegaan dat de invoer UTF-8-code ring is.

#### <a name="example---document-key-lookup"></a>Voor beeld-zoeken naar document sleutels

Alleen URL-veilige tekens kunnen in een Azure Search document sleutel worden weer gegeven (omdat klanten het document moeten kunnen adresseren met de [lookup-API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Als het Bron veld voor uw sleutel URL-onveilige tekens bevat, kunt u de `base64Encode` functie gebruiken om deze te converteren naar de indexerings tijd.

Wanneer u de gecodeerde sleutel ophaalt tijdens de zoek tijd, kunt u `base64Decode` de functie gebruiken om de oorspronkelijke sleutel waarde op te halen en gebruiken om het bron document op te halen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Als u geen eigenschap para meters voor uw toewijzings functie opgeeft, wordt standaard de `{"useHttpServerUtilityUrlTokenEncode" : true}`waarde gebruikt.

Azure Search ondersteunt twee verschillende base64-code ringen. U moet dezelfde para meters gebruiken bij het coderen en decoderen van hetzelfde veld. Zie voor meer informatie base64-coderings [Opties](#base64details) om te bepalen welke para meters u moet gebruiken.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>de functie base64Decode

Voert base64-code ring uit van de invoer teken reeks. Er wordt van uitgegaan dat de invoer een *URL-veilige* base64-gecodeerde teken reeks is.

#### <a name="example---decode-blob-metadata-or-urls"></a>Voor beeld: BLOB-meta gegevens of Url's decoderen

Uw bron gegevens kunnen bestaan uit base64-gecodeerde teken reeksen, zoals teken reeksen voor BLOB-meta gegevens of Web-Url's, die u doorzoekbaar wilt maken als tekst zonder opmaak. U kunt de `base64Decode` functie gebruiken om de gecodeerde gegevens weer in te scha kelen in reguliere teken reeksen bij het invullen van uw zoek index.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Als u geen eigenschap para meters opneemt, wordt standaard `{"useHttpServerUtilityUrlTokenEncode" : true}`de waarde gebruikt.

Azure Search ondersteunt twee verschillende base64-code ringen. U moet dezelfde para meters gebruiken bij het coderen en decoderen van hetzelfde veld. Zie voor meer informatie base64-coderings [Opties](#base64details) om te bepalen welke para meters u moet gebruiken.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opties voor Base64-code ring

Azure Search ondersteunt twee verschillende base64-code ringen: **HTTPSERVERUTILITY URL-token**en **URL-veilige base64-code ring zonder opvulling**. Een teken reeks die met base64 is gecodeerd tijdens het indexeren, moet later worden gedecodeerd met dezelfde coderings opties, anders wordt het resultaat niet met de oorspronkelijke waarde overeenkomen.

Als de `useHttpServerUtilityUrlTokenEncode` or `useHttpServerUtilityUrlTokenDecode` -para meters voor respectievelijk coderen en `base64Encode` decoderen zijn ingesteld op `true`, wordt gereageerd `base64Decode` als [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) en gedraagt zich als [ HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Als u niet de volledige .NET Framework gebruikt (dat wil zeggen, u .net core of een ander Framework gebruikt) om de sleutel waarden voor het emuleren van Azure Search gedrag te maken, moet u `useHttpServerUtilityUrlTokenEncode` `false`en `useHttpServerUtilityUrlTokenDecode` instellen. Afhankelijk van de tape wisselaar die u gebruikt, kunnen de base64-coderings-en decodeer functies verschillen van die van Azure Search.

De volgende tabel vergelijkt verschillende base64-code ringen van `00>00?00`de teken reeks. Als u de vereiste aanvullende verwerking (indien van toepassing) voor uw base64-functies wilt bepalen, past u de functie `00>00?00` voor het coderen van de bibliotheek toe op `MDA-MDA_MDA`de teken reeks en vergelijkt u de uitvoer met de verwachte uitvoer.

| Encoding | Uitvoer van base64-code ring | Aanvullende verwerking na bibliotheek codering | Aanvullende verwerking voordat de tape wisselaar wordt gedecodeerd |
| --- | --- | --- | --- |
| Base64 met opvulling | `MDA+MDA/MDA=` | URL-veilige tekens gebruiken en opvulling verwijderen | Gebruik standaard base64-tekens en opvulling toevoegen |
| Base64 zonder opvulling | `MDA+MDA/MDA` | Gebruik URL-veilige tekens | Gebruik standaard base64-tekens |
| URL-veilig base64 met opvulling | `MDA-MDA_MDA=` | Opvulling verwijderen | Opvulling toevoegen |
| URL-veilig base64 zonder opvulling | `MDA-MDA_MDA` | Geen | Geen |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>de functie extractTokenAtPosition

Hiermee wordt een teken reeks veld gesplitst met het opgegeven scheidings teken en wordt het token op de opgegeven positie in de resulterende splitsing gepickt.

Deze functie maakt gebruik van de volgende para meters:

* `delimiter`: een teken reeks die als scheidings teken moet worden gebruikt bij het splitsen van de invoer teken reeks.
* `position`: een op nul gebaseerde positie van het token dat moet worden gekozen nadat de invoer reeks is gesplitst.

Als de `Jane Doe`invoer bijvoorbeeld, de `delimiter` is `" "`(spatie) `Jane`en de `position` 0, is het resultaat; als de `position` waarde 1 is, is `Doe`het resultaat. Als de positie verwijst naar een token dat niet bestaat, wordt er een fout geretourneerd.

#### <a name="example---extract-a-name"></a>Voor beeld: een naam ophalen

De gegevens bron bevat een `PersonName` veld en u wilt het indexeren als twee `LastName` afzonderlijke `FirstName` velden. U kunt deze functie gebruiken om de invoer te splitsen met behulp van de spatie als scheidings teken.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection function

Transformeert een teken reeks die is opgemaakt als een JSON-matrix met teken reeksen in een teken reeks matrix die `Collection(Edm.String)` kan worden gebruikt om een veld in de index te vullen.

Als de invoer teken reeks `["red", "white", "blue"]`bijvoorbeeld is, wordt het doel veld van het type `Collection(Edm.String)` gevuld met de drie waarden `red`, `white`en `blue`. Voor invoer waarden die niet kunnen worden geparseerd als JSON-teken reeks matrices, wordt een fout geretourneerd.

#### <a name="example---populate-collection-from-relational-data"></a>Voor beeld: verzameling van relationele gegevens invullen

Azure SQL database heeft geen ingebouwd gegevens type dat op natuurlijke wijze is gekoppeld aan `Collection(Edm.String)` velden in azure Search. Als u velden voor teken reeks verzameling wilt vullen, kunt u de bron gegevens vooraf verwerken als een JSON-teken reeks `jsonArrayToStringCollection` matrix en vervolgens de functie mapping gebruiken.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Zie [relationele gegevens model leren](search-example-adventureworks-modeling.md)voor een gedetailleerd voor beeld waarmee relationele gegevens worden omgezet in index verzamelings velden.

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>de functie urlEncode

Deze functie kan worden gebruikt om een teken reeks te coderen zodat deze ' URL safe ' is. Wanneer deze functie wordt gebruikt in combi natie met een teken reeks die tekens bevat die niet zijn toegestaan in een URL, worden deze ' onveilige ' tekens geconverteerd naar equivalente teken eenheden. Deze functie maakt gebruik van de UTF-8-indeling voor versleuteling.

#### <a name="example---document-key-lookup"></a>Voor beeld-zoeken naar document sleutels

`urlEncode`de functie kan worden gebruikt als alternatief voor de `base64Encode` functie als alleen onveilige URL-tekens moeten worden geconverteerd, terwijl andere tekens ongewijzigd blijven.

De invoer teken reeks is- `<hello>` en vervolgens wordt het doel veld van `(Edm.String)` het type ingevuld met de waarde`%3chello%3e`

Wanneer u de gecodeerde sleutel ophaalt tijdens de zoek tijd, kunt u `urlDecode` de functie gebruiken om de oorspronkelijke sleutel waarde op te halen en gebruiken om het bron document op te halen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>de functie urlDecode

 Met deze functie wordt een URL-gecodeerde teken reeks geconverteerd naar een gedecodeerde teken reeks met UTF-8-code ring.

 ### <a name="example---decode-blob-metadata"></a>Voor beeld: BLOB-meta gegevens decoderen

 Sommige Azure Storage-clients coderen BLOB-meta gegevens automatisch als deze niet-ASCII-tekens bevatten. Als u bijvoorbeeld wilt dat meta gegevens kunnen worden doorzocht (als tekst zonder opmaak), kunt u de `urlDecode` functie gebruiken om de gecodeerde gegevens weer in te stellen op reguliere teken reeksen wanneer u uw zoek index vult.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```