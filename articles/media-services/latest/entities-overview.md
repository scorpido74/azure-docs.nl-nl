---
title: Filters, ordening en paginering van Media Services entiteiten
titleSuffix: Azure Media Services
description: Meer informatie over het filteren, ordenen en pagineren van Azure Media Services v3-entiteiten.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 96f08f75d0921fdf88b71c8e8dd2398a6b85ec6d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89258466"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filters, ordening en paginering van Media Services entiteiten

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit onderwerp worden de OData-query opties en paginerings ondersteuning beschreven die beschikbaar zijn wanneer u Azure Media Services v3-entiteiten vermeldt.

## <a name="considerations"></a>Overwegingen

* Eigenschappen van entiteiten van het `Datetime` type zijn altijd in UTC-indeling.
* De lege ruimte in de query reeks moet een URL-code ring hebben voordat u een aanvraag verzendt.

## <a name="comparison-operators"></a>Vergelijkingsoperatoren

U kunt de volgende opera toren gebruiken om een veld te vergelijken met een constante waarde:

Gelijkheids operatoren:

- `eq`: Test of een veld *gelijk is aan* een constante waarde.
- `ne`: Test of een veld *niet gelijk is aan* een constante waarde.

Bereik operatoren:

- `gt`: Test of een veld *groter is dan* een constante waarde.
- `lt`: Test of een veld *kleiner is dan* een constante waarde.
- `ge`: Test of een veld *groter is dan of gelijk is aan* een constante waarde.
- `le`: Test of een veld *kleiner is dan of gelijk is aan* een constante waarde.

## <a name="filter"></a>Filter

Gebruik `$filter` om een OData-filter parameter op te geven om alleen de objecten te vinden waarin u bent geïnteresseerd.

In het volgende voor beeld wordt gefilterd op de `alternateId` waarde van een Asset:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

De volgende C#-voorbeeld filters voor de aangemaakte datum van het activum:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Sorteren op

Gebruiken `$orderby` om de geretourneerde objecten te sorteren op basis van de opgegeven para meter. Bijvoorbeeld:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Als u de resultaten wilt sorteren in oplopende of aflopende volg orde, voegt u een `asc` of `desc` aan de veld naam toe, gescheiden door een spatie. Bijvoorbeeld: `$orderby properties/created desc`.

## <a name="skip-token"></a>Token overs Laan

Als een query-antwoord veel items bevat, retourneert de service een `$skiptoken` ( `@odata.nextLink` )-waarde die u gebruikt om de volgende pagina met resultaten op te halen. Gebruik deze pagina om de gehele resultatenset te door lopen.

In Media Services v3 kunt u de pagina grootte niet configureren. De pagina grootte is afhankelijk van het type entiteit. Lees de afzonderlijke secties die volgen voor meer informatie.

Als entiteiten worden gemaakt of verwijderd terwijl u door de verzameling wisselt, worden de wijzigingen weer gegeven in de geretourneerde resultaten (als deze wijzigingen zich in het deel van de verzameling bevinden dat nog niet is gedownload).

> [!TIP]
> Gebruik altijd `nextLink` voor het opsommen van de verzameling en is niet afhankelijk van een bepaalde pagina grootte.
>
> De `nextLink` waarde wordt alleen weer gegeven als er meer dan één pagina met entiteiten is.

Bekijk het volgende voor beeld van waar `$skiptoken` wordt gebruikt. Zorg ervoor dat u *amstestaccount* vervangt door de naam van uw account en stel de *API-versie* waarde in op de nieuwste versie.

Als u een lijst met assets als volgt aanvraagt:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

U krijgt een antwoord dat vergelijkbaar is met het volgende:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

U kunt vervolgens de volgende pagina aanvragen door een GET-aanvraag te verzenden voor:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

In het volgende C#-voor beeld ziet u hoe u met alle streams locators in het account kunt inventariseren.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Query opties combi neren met logische Opera tors

Media Services v3 ondersteunt **or** -en **en** logische Opera tors. 

In het volgende voor beeld wordt de status van de taak gecontroleerd:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

U maakt dezelfde query in C# als de volgende: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opties voor filteren en ordenen van entiteiten

In de volgende tabel ziet u hoe u de opties voor filteren en ordenen kunt Toep assen op verschillende entiteiten:

|Entiteitsnaam|Naam van eigenschap|Filter|Bestellen|
|---|---|---|---|
|[Assets](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` en `desc`|
||Eigenschappen. alternateId |`eq`||
||Eigenschappen. assetId |`eq`||
||Eigenschappen. gemaakt| `eq`, `gt`, `lt`| `asc` en `desc`|
|[Beleid voor inhoudssleutels](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||Eigenschappen. gemaakt    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||Eigenschappen. Beschrijving    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Eigenschappen. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||Eigenschappen. policyId|`eq`, `ne`||
|[Taken](/rest/api/media/jobs)| name  | `eq`            | `asc` en `desc`|
||Eigenschappen. State        | `eq`, `ne`        |                         |
||Eigenschappen. gemaakt      | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|
||Eigenschappen. lastModified | `gt`, `ge`, `lt`, `le` | `asc` en `desc`| 
|[Streaming-locators](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||Eigenschappen. gemaakt    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` en `desc`|
||Eigenschappen. endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
|[Beleid voor streaming](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||Eigenschappen. gemaakt    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
|[Transformaties](/rest/api/media/transforms)| name | `eq`            | `asc` en `desc`|
|| Eigenschappen. gemaakt      | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|
|| Eigenschappen. lastModified | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|

## <a name="next-steps"></a>Volgende stappen

* [Lijst assets](/rest/api/media/assets/list)
* [Lijst met inhouds sleutel beleidsregels](/rest/api/media/contentkeypolicies/list)
* [Taken weer geven](/rest/api/media/jobs/list)
* [Streaming-beleids regels weer geven](/rest/api/media/streamingpolicies/list)
* [Streaming-Locators weer geven](/rest/api/media/streaminglocators/list)
* [Een bestand streamen](stream-files-dotnet-quickstart.md)
* [Quota en limieten](limits-quotas-constraints.md)
