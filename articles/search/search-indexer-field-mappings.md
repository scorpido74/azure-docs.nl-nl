---
title: Veldtoewijzingen in indexeerfuncties
titleSuffix: Azure Cognitive Search
description: Veldtoewijzingen in een indexer configureren om rekening te houden met verschillen in veldnamen en gegevensweergaven.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275149"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Veldtoewijzingen en -transformaties met Azure Cognitive Search-indexeerders

Bij het gebruik van Azure Cognitive Search-indexeerders komt u soms vast dat de invoergegevens niet helemaal overeenkomen met het schema van uw doelindex. In die gevallen u **veldtoewijzingen** gebruiken om uw gegevens opnieuw vorm te geven tijdens het indexeringsproces.

Enkele situaties waarin veldtoewijzingen nuttig zijn:

* Uw gegevensbron heeft `_id`een veld met de naam , maar Azure Cognitive Search staat geen veldnamen toe die beginnen met een underscore. Met een veldtoewijzing u de naam van een veld effectief wijzigen.
* U wilt meerdere velden in de index invullen op uit dezelfde gegevensbrongegevens. U bijvoorbeeld verschillende analyseapparaten op die velden toepassen.
* U wilt een indexveld vullen met gegevens uit meer dan één gegevensbron en de gegevensbronnen gebruiken elk verschillende veldnamen.
* U moet Base64 uw gegevens coderen of decoderen. Veldtoewijzingen ondersteunen verschillende **toewijzingsfuncties,** waaronder functies voor Base64-codering en -decodering.

> [!NOTE]
> De veldtoewijzingsfunctie van Azure Cognitive Search-indexeerders biedt een eenvoudige manier om gegevensvelden in kaart te brengen om velden te indexeren, met een paar opties voor gegevensconversie. Voor complexere gegevens is mogelijk een voorbewerking vereist om deze om te vormen tot een vorm die eenvoudig te indexeren is.
>
> Microsoft Azure Data Factory is een krachtige cloudgebaseerde oplossing voor het importeren en transformeren van gegevens. U ook code schrijven om brongegevens te transformeren voordat u indexeert. Zie [Relationele gegevens van modelleren](search-example-adventureworks-modeling.md) en [Facetten op meerdere niveaus modelleren voor](search-example-adventureworks-multilevel-faceting.md)codevoorbeelden.
>

## <a name="set-up-field-mappings"></a>Veldtoewijzingen instellen

Een veldmapping bestaat uit drie delen:

1. A `sourceFieldName`, die een veld in uw gegevensbron vertegenwoordigt. Deze accommodatie is vereist.
2. Een `targetFieldName`optionele , die een veld in uw zoekindex vertegenwoordigt. Als deze wordt weggelaten, wordt dezelfde naam als in de gegevensbron gebruikt.
3. Een `mappingFunction`optionele , die uw gegevens kan transformeren met behulp van een van de verschillende vooraf gedefinieerde functies. De volledige lijst met functies vindt [u hieronder](#mappingFunctions).

Veldtoewijzingen worden toegevoegd `fieldMappings` aan de array van de indexerdefinitie.

## <a name="map-fields-using-the-rest-api"></a>Velden toewijzen met de REST-API

U veldtoewijzingen toevoegen wanneer u een nieuwe indexer maakt met de [API-aanvraag voor indexeren maken.](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) U de veldtoewijzingen van een bestaande indexer beheren met behulp van de [API-aanvraag voor updateindexer.](https://docs.microsoft.com/rest/api/searchservice/update-indexer)

Zo u bijvoorbeeld een bronveld toewijzen aan een doelveld met een andere naam:

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

Een bronveld kan worden verwezen in meerdere veldtoewijzingen. In het volgende voorbeeld ziet u hoe u een veld 'vorkt', waarbij hetzelfde bronveld wordt gekopieerd naar twee verschillende indexvelden:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search gebruikt case-insensitive vergelijking om het veld en functienamen in veldtoewijzingen op te lossen. Dit is handig (u hoeft niet alle behuizing goed te krijgen), maar het betekent dat uw gegevensbron of -index geen velden kan hebben die alleen per geval verschillen.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Velden toewijzen met de .NET SDK

U definieert veldtoewijzingen in de .NET SDK met `SourceFieldName` de `TargetFieldName`klasse [FieldMapping,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) die de eigenschappen en een optionele `MappingFunction` verwijzing heeft.

U veldtoewijzingen opgeven bij het maken van de `Indexer.FieldMappings` indexer of later door de eigenschap rechtstreeks in te stellen.

In het volgende voorbeeld van C# worden de veldtoewijzingen ingesteld bij het maken van een indexeerder.

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

## <a name="field-mapping-functions"></a>Veldtoewijzingsfuncties

Een veldtoewijzingsfunctie transformeert de inhoud van een veld voordat het in de index wordt opgeslagen. De volgende toewijzingsfuncties worden momenteel ondersteund:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [geëxtraheerdTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode, functie

Hiermee voert *u URL-veilige Base64-codering* van de invoertekenreeks uit. Gaat ervan uit dat de invoer UTF-8 gecodeerd is.

#### <a name="example---document-key-lookup"></a>Voorbeeld - opzoeken van documentsleutel

Alleen URL-veilige tekens kunnen worden weergegeven in een Azure Cognitive Search-documentsleutel (omdat klanten het document moeten kunnen adresseren met de [Opzoek-API).](https://docs.microsoft.com/rest/api/searchservice/lookup-document) Als het bronveld voor uw sleutel URL-onveilige `base64Encode` tekens bevat, u de functie gebruiken om deze te converteren op indexeringstijd. Een documentsleutel (zowel voor als na conversie) mag echter niet langer zijn dan 1024 tekens.

Wanneer u de gecodeerde sleutel ophaalt tijdens het `base64Decode` zoeken, u de functie gebruiken om de oorspronkelijke sleutelwaarde te krijgen en deze gebruiken om het brondocument op te halen.

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

Als u geen eigenschap parameters opneemt voor uw toewijzingsfunctie, wordt de waarde standaard weergegeven. `{"useHttpServerUtilityUrlTokenEncode" : true}`

Azure Cognitive Search ondersteunt twee verschillende Base64-coderingen. U moet dezelfde parameters gebruiken bij het coderen en decoderen van hetzelfde veld. Zie voor meer informatie [de coderingsopties van Base64](#base64details) om te bepalen welke parameters u wilt gebruiken.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode, functie

Hiermee voert u Base64-decodering van de invoertekenreeks uit. De invoer wordt verondersteld een *URL-veilige* Base64-gecodeerde tekenreeks te zijn.

#### <a name="example---decode-blob-metadata-or-urls"></a>Voorbeeld - blobmetagegevens of URL's decoderen

Uw brongegevens kunnen base64-gecodeerde tekenreeksen bevatten, zoals tekenreeksen met blobmetagegevens of web-URL's, die u als platte tekst wilt doorzoekbaar maken. U `base64Decode` de functie gebruiken om de gecodeerde gegevens weer om te zetten in reguliere tekenreeksen wanneer u uw zoekindex bevolkt.

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

Als u geen eigenschap parameters opneemt, wordt `{"useHttpServerUtilityUrlTokenEncode" : true}`de waarde standaard weergegeven.

Azure Cognitive Search ondersteunt twee verschillende Base64-coderingen. U moet dezelfde parameters gebruiken bij het coderen en decoderen van hetzelfde veld. Zie voor meer informatie [de coderingsopties van Base64](#base64details) om te bepalen welke parameters u wilt gebruiken.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64-coderingsopties

Azure Cognitive Search ondersteunt URL-safe base64-codering en normale base64-codering. Een tekenreeks die is gebaseerd64 gecodeerd tijdens het indexeren moet later worden gedecodeerd met dezelfde coderingsopties, anders komt het resultaat niet overeen met het origineel.

Als `useHttpServerUtilityUrlTokenEncode` de `useHttpServerUtilityUrlTokenDecode` or-parameters voor respectievelijk codering en `true`decodering zijn ingesteld op `base64Encode` , gedraagt zich dan als [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) en `base64Decode` gedraagt zich als [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Als `base64Encode` wordt gebruikt om `useHttpServerUtilityUrlTokenEncode` belangrijke waarden te produceren, moet worden ingesteld op true. Alleen URL-safe base64-codering kan worden gebruikt voor belangrijke waarden. Zie [Naamgevingsregels &#40;Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) voor de volledige set beperkingen op tekens in belangrijke waarden.

De .NET-bibliotheken in Azure Cognitive Search gaan uit van het volledige .NET-framework, dat ingebouwde codering biedt. De `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` en opties maken gebruik van deze ingebouwde functionaity. Als u .NET Core of een ander framework `false` gebruikt, raden we u aan deze opties in te stellen en de coderings- en decoderingsfuncties van uw framework rechtstreeks in te stellen.

In de volgende tabel worden verschillende base64-coderingen van de tekenreeks vergeleken. `00>00?00` Als u de vereiste extra verwerking (indien van toepassing) voor uw base64-functies wilt bepalen, past u de functie bibliotheekcode op de tekenreeks `00>00?00` toe en vergelijkt u de uitvoer met de verwachte uitvoer `MDA-MDA_MDA`.

| Encoding | Base64-uitvoer coderen | Aanvullende verwerking na bibliotheekcodering | Aanvullende verwerking voordat bibliotheek decodeert |
| --- | --- | --- | --- |
| Base64 met vulling | `MDA+MDA/MDA=` | URL-veilige tekens gebruiken en opvulling verwijderen | Standaard base64-tekens gebruiken en opvulling toevoegen |
| Base64 zonder opvulling | `MDA+MDA/MDA` | URL-veilige tekens gebruiken | Standaardbasis64-tekens gebruiken |
| URL-veilige basis64 met opvulling | `MDA-MDA_MDA=` | Opvulling verwijderen | Opvulling toevoegen |
| URL-veilige basis64 zonder opvulling | `MDA-MDA_MDA` | Geen | Geen |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition, functie

Splitst een tekenreeksveld met de opgegeven scheidingsteken en kiest het token op de opgegeven positie in de resulterende splitsing.

Met deze functie worden de volgende parameters gebruikt:

* `delimiter`: een tekenreeks die u als scheidingsteken moet gebruiken bij het splitsen van de invoertekenreeks.
* `position`: een op een getal getal gebaseerde nulpositie van het token dat moet worden gekozen nadat de invoertekenreeks is gesplitst.

Als de invoer bijvoorbeeld `Jane Doe`is `delimiter` `" "`, is het `position` (spatie) `Jane`en de is 0, is het resultaat ; als `position` de is 1, `Doe`het resultaat is . Als de positie verwijst naar een token dat niet bestaat, wordt een fout geretourneerd.

#### <a name="example---extract-a-name"></a>Voorbeeld - een naam extraheren

Uw gegevensbron `PersonName` bevat een veld en u wilt `FirstName` `LastName` deze indexeren als twee afzonderlijke en velden. U deze functie gebruiken om de invoer te splitsen met het spatieteken als de scheidingsteken.

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection, functie

Hiermee wordt een tekenreeks opgemaakt die is opgemaakt als een JSON-array met tekenreeksen omgezet in een tekenreeksarray die kan worden gebruikt om een `Collection(Edm.String)` veld in de index te vullen.

Als de invoertekenreeks bijvoorbeeld `["red", "white", "blue"]`is, wordt het `Collection(Edm.String)` doelveld van het `red`type `white`gevuld `blue`met de drie waarden , en . Voor invoerwaarden die niet kunnen worden geparseerd als JSON-tekenreeksarrays, wordt een fout geretourneerd.

#### <a name="example---populate-collection-from-relational-data"></a>Voorbeeld - verzameling uit relationele gegevens invullen

Azure SQL Database heeft geen ingebouwd gegevenstype dat `Collection(Edm.String)` van nature wordt toegewezen aan velden in Azure Cognitive Search. Als u tekenreeksverzamelingsvelden wilt invullen, u uw brongegevens vooraf `jsonArrayToStringCollection` verwerken als een JSON-tekenreeksarray en vervolgens de toewijzingsfunctie gebruiken.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Zie [Relationele gegevens](search-example-adventureworks-modeling.md)modelleren voor een gedetailleerd voorbeeld dat relationele gegevens omzet in indexverzamelingsvelden.

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode, functie

Deze functie kan worden gebruikt om een tekenreeks te coderen, zodat deze "URL-veilig" is. Wanneer deze functie wordt gebruikt met een tekenreeks die tekens bevat die niet zijn toegestaan in een URL, worden deze tekens in een entiteit omgezet in equivalenten van tekens. Deze functie maakt gebruik van de UTF-8 coderingsindeling.

#### <a name="example---document-key-lookup"></a>Voorbeeld - opzoeken van documentsleutel

`urlEncode`functie kan worden gebruikt als `base64Encode` een alternatief voor de functie, als alleen URL onveilige tekens moeten worden geconverteerd, terwijl andere tekens as-is.

Zeg, de invoertekenreeks is `<hello>` - dan `(Edm.String)` wordt het doelveld van het type gevuld met de waarde`%3chello%3e`

Wanneer u de gecodeerde sleutel ophaalt tijdens het `urlDecode` zoeken, u de functie gebruiken om de oorspronkelijke sleutelwaarde te krijgen en deze gebruiken om het brondocument op te halen.

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

 ### <a name="urldecode-function"></a>urlDecode, functie

 Met deze functie wordt een door de URL gecodeerde tekenreeks omgezet in een gedecodeerde tekenreeks met utf-8-coderingsindeling.

 ### <a name="example---decode-blob-metadata"></a>Voorbeeld - blobmetagegevens decoderen

 Sommige Azure-opslagclients url-codekmetagegevens automatisch als deze niet-ASCII-tekens bevatten. Als u dergelijke metagegevens echter doorzoekbaar wilt maken `urlDecode` (als platte tekst), u de functie gebruiken om de gecodeerde gegevens weer om te zetten in reguliere tekenreeksen wanneer u uw zoekindex bevolkt.

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
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode, functie fixedLengthEncode
 
 Met deze functie wordt een tekenreeks van een lengte omgezet in een tekenreeks met een vaste lengte.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Voorbeeld - documentsleutels toewijzen die te lang zijn
 
Wanneer er klachten over een melding van een documentsleutel langer zijn dan 1024 tekens, kan deze functie worden toegepast om de lengte van de documentsleutel te verkorten.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
