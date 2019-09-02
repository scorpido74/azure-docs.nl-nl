---
title: Blobs toevoegen aan objecten in azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over het toevoegen van blobs aan objecten in azure Digital Apparaatdubbels.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 61c09435606612377781fb382d2d31144e96b07b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965950"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Blobs toevoegen aan objecten in azure Digital Apparaatdubbels

Blobs zijn niet-gestructureerde representaties van algemene bestands typen, zoals afbeeldingen en Logboeken. Blobs volgen wat voor soort gegevens ze vertegenwoordigen door gebruik te maken van een MIME-type (bijvoorbeeld: ' afbeelding/JPEG ') en meta gegevens (naam, beschrijving, type, enzovoort).

Azure Digital Apparaatdubbels ondersteunt het koppelen van blobs aan apparaten, ruimten en gebruikers. Blobs kunnen een profiel afbeelding vertegenwoordigen voor een gebruiker, een foto van een apparaat, een video, een kaart, een firmware-zip, JSON-gegevens, een logboek, enzovoort.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Overzicht van het uploaden van blobs

U kunt meerdelige aanvragen gebruiken om blobs te uploaden naar specifieke eind punten en hun respectieve functionaliteiten.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>De metagegevens van de blob

Naast het **inhouds type** en de **Content-dispositie**moeten door Azure Digital apparaatdubbels BLOB MEERDELIGE aanvragen de juiste JSON-hoofd tekst worden opgegeven. Welke JSON-hoofd tekst moet worden verzonden, is afhankelijk van het type HTTP-aanvraag bewerking dat wordt uitgevoerd.

De vier belangrijkste JSON-schema's zijn:

[![JSON-schema's](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

Meta gegevens van JSON-BLOB voldoen aan het volgende model:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Kenmerk | type | Description |
| --- | --- | --- |
| **parentId** | String | De bovenliggende entiteit waaraan de BLOB moet worden gekoppeld (spaties, apparaten of gebruikers) |
| **name** |String | Een mensen vriendelijke naam voor de BLOB |
| **type** | String | Het type Blob-kan *type* en *typeId* niet gebruiken  |
| **typeId** | Integer | De ID van het BLOB-type: *type* en *typeId* kunnen niet worden gebruikt |
| **subtype** | String | Het subtype van de blob: kan subtype en *subtypeId* niet gebruiken |
| **subtypeId** | Integer | De subtype-ID voor de blob: kan *subtype* en *subtypeId* niet gebruiken |
| **description** | String | Aangepaste beschrijving van de BLOB |
| **sharing** | String | Hiermee wordt aangegeven of de BLOB gedeeld kan worden`None`: `Tree`Enum `Global`[,,] |

BLOB-meta gegevens worden altijd opgegeven als het eerste segment met het **inhouds type** `application/json` of `.json` als een bestand. Bestands gegevens worden opgegeven in het tweede segment en kunnen van elk ondersteund MIME-type zijn.

In de Swagger-documentatie worden deze model schema's volledig gedetailleerd beschreven.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Meer informatie over het gebruik van de referentie documentatie door [te lezen hoe u Swagger kunt gebruiken](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Reactie gegevens van blobs

Individueel geretourneerde blobs voldoen aan het volgende JSON-schema:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Kenmerk | type | Description |
| --- | --- | --- |
| **id** | String | De unieke id voor de BLOB |
| **name** |String | Een mensen vriendelijke naam voor de BLOB |
| **parentId** | String | De bovenliggende entiteit waaraan de BLOB moet worden gekoppeld (spaties, apparaten of gebruikers) |
| **type** | String | Het type Blob-kan *type* en *typeId* niet gebruiken  |
| **typeId** | Integer | De ID van het BLOB-type: *type* en *typeId* kunnen niet worden gebruikt |
| **subtype** | String | Het subtype van de blob: kan subtype en *subtypeId* niet gebruiken |
| **subtypeId** | Integer | De subtype-ID voor de blob: kan *subtype* en *subtypeId* niet gebruiken |
| **sharing** | String | Hiermee wordt aangegeven of de BLOB gedeeld kan worden`None`: `Tree`Enum `Global`[,,] |
| **description** | String | Aangepaste beschrijving van de BLOB |
| **contentInfos** | Array | Hiermee geeft u ongestructureerde meta gegevens op, inclusief versie |
| **fullName** | String | De volledige naam van de BLOB |
| **spacePaths** | String | Het pad naar de schijf |

BLOB-meta gegevens worden altijd opgegeven als het eerste segment met het **inhouds type** `application/json` of `.json` als een bestand. Bestands gegevens worden opgegeven in het tweede segment en kunnen van elk ondersteund MIME-type zijn.

### <a name="blob-multipart-request-examples"></a>Voor beelden van BLOB multi part-aanvragen

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Als u een tekst bestand als een BLOB wilt uploaden en dit aan een ruimte wilt koppelen, maakt u een geverifieerde HTTP POST-aanvraag:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Met de volgende tekst:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Value | Vervangen door |
| --- | --- |
| USER_DEFINED_BOUNDARY | Een grens naam voor meerdelige inhoud |

De volgende code is een .NET-implementatie van dezelfde BLOB-upload, met behulp van de klasse [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Tot slot [](https://curl.haxx.se/) kunnen de gebruikers op dezelfde manier meerdelige formulier aanvragen maken:

[![Apparaat-blobs](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Value | Vervangen door |
| --- | --- |
| YOUR_TOKEN | Uw geldige OAuth 2,0-token |
| YOUR_SPACE_ID | De ID van de ruimte waaraan de BLOB moet worden gekoppeld |
| PATH_TO_FILE | Het pad naar het tekst bestand |

Een geslaagde POST retourneert de ID van de nieuwe BLOB (gemarkeerd in rood eerder).

## <a name="api-endpoints"></a>API-eind punten

In de volgende secties worden de belangrijkste BLOB-API-eind punten en de functionaliteit ervan beschreven.

### <a name="devices"></a>Apparaten

U kunt blobs koppelen aan apparaten. De volgende afbeelding toont de Swagger-referentie documentatie voor uw beheer-Api's. Hiermee worden Device-gerelateerde API-eind punten opgegeven voor het BLOB-verbruik en eventueel vereiste para meters voor het pad.

[![Apparaat-blobs](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Als u bijvoorbeeld een BLOB wilt bijwerken of maken en de BLOB aan een apparaat wilt koppelen, moet u een geverifieerde HTTP-PATCH aanvraag indienen voor het volgende:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_BLOB_ID* | De gewenste BLOB-ID |

Voltooide aanvragen retour neren een JSON-object zoals [eerder beschreven](#blobModel).

### <a name="spaces"></a>Spaties

U kunt ook blobs koppelen aan ruimten. De volgende afbeelding geeft een lijst van alle Space-API-eind punten die verantwoordelijk zijn voor het verwerken van blobs. Het bevat ook een lijst met alle para meters die moeten worden door gegeven aan deze eind punten.

[![Ruimte-blobs](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Als u bijvoorbeeld een BLOB wilt retour neren die aan een ruimte is gekoppeld, maakt u een geverifieerde HTTP GET-aanvraag naar:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_BLOB_ID* | De gewenste BLOB-ID |

Voltooide aanvragen retour neren een JSON-object zoals [eerder beschreven](#blobModel).

Met een PATCH-aanvraag voor hetzelfde eind punt worden meta gegevens beschrijvingen bijgewerkt en worden versies van de BLOB gemaakt. De HTTP-aanvraag wordt gedaan via de PATCH-methode, samen met alle benodigde meta gegevens en meerdelige formuliergegevens.

### <a name="users"></a>Gebruikers

U kunt blobs koppelen aan gebruikers modellen (bijvoorbeeld om een profiel foto te koppelen). De volgende afbeelding toont de relevante gebruikers-API-eind punten en alle vereiste para `id`meters voor het pad, zoals:

[![Gebruikers-blobs](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Als u bijvoorbeeld een BLOB wilt ophalen die aan een gebruiker is gekoppeld, maakt u een geverifieerde HTTP GET-aanvraag met de vereiste formulier gegevens voor:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_BLOB_ID* | De gewenste BLOB-ID |

Voltooide aanvragen retour neren een JSON-object zoals [eerder beschreven](#blobModel).

## <a name="common-errors"></a>Algemene fouten

Een veelvoorkomende fout houdt in dat u de juiste header-informatie niet opgeeft:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Om deze fout op te lossen, controleert u of de algemene aanvraag een juiste **Content-type-** header heeft:

* `multipart/mixed`
* `multipart/form-data`

Controleer ook of elk meerdelige segment een bijbehorend **inhouds type** heeft als dat nodig is.

## <a name="next-steps"></a>Volgende stappen

- Lees [use Azure Digital Apparaatdubbels Swagger](how-to-use-swagger.md)voor meer informatie over Swagger-referentie documentatie voor Azure Digital apparaatdubbels.

- Lees [hoe u postman kunt configureren](./how-to-configure-postman.md)om blobs te uploaden via postman.