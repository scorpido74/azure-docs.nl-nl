---
title: Filteren, bestellen en paging van Media Services entiteiten
titleSuffix: Azure Media Services
description: Meer informatie over het filteren, bestellen en paging van V3-entiteiten van Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7e4f1141a9d4bd58451782e8412063a22565556d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584536"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filteren, bestellen en paging van Media Services entiteiten

In dit onderwerp worden de OData-queryopties en pagination-ondersteuning besproken die beschikbaar zijn wanneer u Azure Media Services v3-entiteiten aanbiedt.

## <a name="considerations"></a>Overwegingen

* Eigenschappen van entiteiten van `Datetime` het type zijn altijd in UTC-indeling.
* Witruimte in de querytekenreeks moet worden gecodeerd door de URL voordat u een verzoek verzendt.

## <a name="comparison-operators"></a>Vergelijkingsoperators

U de volgende operatoren gebruiken om een veld te vergelijken met een constante waarde:

Exploitanten van gelijkheid:

- `eq`: Test of een veld gelijk is *aan* een constante waarde.
- `ne`: Test of een veld *niet gelijk* is aan een constante waarde.

Exploitanten van het bereik:

- `gt`: Test of een veld groter is *dan* een constante waarde.
- `lt`: Test of een veld *minder dan* een constante waarde heeft.
- `ge`: Test of een veld *groter is dan of gelijk is aan* een constante waarde.
- `le`: Test of een veld *kleiner is dan of gelijk is aan* een constante waarde.

## <a name="filter"></a>Filteren

Gebruik `$filter` om een OData-filterparameter te leveren om alleen de objecten te vinden waarin u geïnteresseerd bent.

In het volgende REST-voorbeeld wordt de `alternateId` waarde van een actief gefiltert:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

De volgende C#-voorbeeldfilters op de gemaakte datum van het item:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Bestellen op

De `$orderby` geretourneerde objecten sorteren op de opgegeven parameter. Bijvoorbeeld:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Als u de resultaten wilt sorteren in `asc` `desc` oplopende of aflopende volgorde, sluit u een van beide of aan de veldnaam toe, gescheiden door een spatie. Bijvoorbeeld: `$orderby properties/created desc`.

## <a name="skip-token"></a>Token overslaan

Als een queryantwoord veel items bevat, retourneert de service een `$skiptoken` (`@odata.nextLink`) waarde die u gebruikt om de volgende pagina met resultaten te krijgen. Gebruik het om de hele resultaatset te bekijken.

In Media Services v3 u het paginaformaat niet configureren. De paginagrootte is afhankelijk van het type entiteit. Lees de afzonderlijke secties die volgen voor meer informatie.

Als entiteiten worden gemaakt of verwijderd terwijl u door de verzameling bladert, worden de wijzigingen weergegeven in de geretourneerde resultaten (als deze wijzigingen deel uitmaken van het deel van de verzameling dat niet is gedownload).

> [!TIP]
> Gebruik `nextLink` altijd om de verzameling op te sommen en niet afhankelijk te zijn van een bepaald paginaformaat.
>
> De `nextLink` waarde is alleen aanwezig als er meer dan één pagina met entiteiten is.

Neem het volgende `$skiptoken` voorbeeld van waar wordt gebruikt. Zorg ervoor dat u *amstestaccount* vervangt door uw accountnaam en stel de waarde van de *api-versie* in op de nieuwste versie.

Als u een lijst met activa als deze aanvraagt:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Je krijgt een antwoord terug dat vergelijkbaar is met deze:

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

U zou dan de volgende pagina aanvragen door een aanvraag te sturen voor:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

In het volgende C#-voorbeeld ziet u hoe u alle streaminglocators in het account opsommen.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Logische operatoren gebruiken om queryopties te combineren

Media Services v3 ondersteunt **OR-** **en EN-logische** operatoren. 

In het volgende restvoorbeeld wordt de status van de taak gecontroleerd:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

U construeert dezelfde query in C# als volgt: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opties voor filteren en bestellen van entiteiten

In de volgende tabel ziet u hoe u de filter- en bestelopties toepassen op verschillende entiteiten:

|Entiteitsnaam|Naam van eigenschap|Filteren|Bestellen|
|---|---|---|---|
|[Activa](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` en `desc`|
||eigenschappen.alternateId |`eq`||
||properties.assetId |`eq`||
||eigenschappen.gemaakt| `eq`, `gt`, `lt`| `asc` en `desc`|
|[Beleid voor inhoudssleutel](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||eigenschappen.gemaakt    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||eigenschappen.beschrijving    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||eigenschappen.lastGewijzigd|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||properties.policyId|`eq`, `ne`||
|[Taken](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` en `desc`|
||eigenschappen.status        | `eq`, `ne`        |                         |
||eigenschappen.gemaakt      | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|
||eigenschappen.lastGewijzigd | `gt`, `ge`, `lt`, `le` | `asc` en `desc`| 
|[Streaming locators](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||eigenschappen.gemaakt    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` en `desc`|
||eigenschappen.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
|[Streamingbeleid](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||eigenschappen.gemaakt    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
|[Transformaties](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` en `desc`|
|| eigenschappen.gemaakt      | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|
|| eigenschappen.lastGewijzigd | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|

## <a name="next-steps"></a>Volgende stappen

* [Lijstactiva](https://docs.microsoft.com/rest/api/media/assets/list)
* [Inhoudssleutelbeleid weergeven](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Taken weergeven](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Streamingbeleid aanbieden](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Lijst Streaming Locators](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Een bestand streamen](stream-files-dotnet-quickstart.md)
* [Quota en limieten](limits-quotas-constraints.md)
