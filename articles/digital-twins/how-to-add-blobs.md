---
title: Blobs toevoegen aan objecten - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over het toevoegen van blobs aan gebruikers, apparaten en spaties in Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75929669"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Blobs toevoegen aan objecten in Azure Digital Twins

Blobs zijn ongestructureerde weergaven van veelvoorkomende bestandstypen, zoals afbeeldingen en logboeken. Blobs houden bij wat voor soort gegevens ze vertegenwoordigen met behulp van een MIME-type (bijvoorbeeld: 'image/jpeg') en metagegevens (naam, beschrijving, type, enzovoort).

Azure Digital Twins ondersteunt het koppelen van blobs aan apparaten, spaties en gebruikers. Blobs kunnen een profielfoto voor een gebruiker, een apparaatfoto, een video, een kaart, een firmware-zip, JSON-gegevens, een logboek, enz.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Overzicht van blobs uploaden

U meerdelige aanvragen gebruiken om blobs te uploaden naar specifieke eindpunten en hun respectieve functionaliteiten.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>De metagegevens van de blob

Naast **Content-Type** en **Content-Disposition**moeten Azure Digital Twins blob multidelige aanvragen de juiste JSON-body opgeven. Welke JSON-instantie moet indienen, is afhankelijk van het soort HTTP-aanvraagbewerking dat wordt uitgevoerd.

De vier belangrijkste JSON schema's zijn:

[![JSON-schema's](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

Json blob metadata voldoet aan het volgende model:

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

| Kenmerk | Type | Beschrijving |
| --- | --- | --- |
| **parentId** | Tekenreeks | De bovenliggende entiteit waaraan de blob moet worden gekoppeld (spaties, apparaten of gebruikers) |
| **Naam** |Tekenreeks | Een mensvriendelijke naam voor de blob |
| **Type** | Tekenreeks | Het type blob - kan *geen type* en *typeId* gebruiken  |
| **typeId** | Geheel getal | De blobtype-id - kan *geen type* en *typeId* gebruiken |
| **Subtype** | Tekenreeks | Het blob-subtype - kan *subtype* en *subtypeId* niet gebruiken |
| **subtypeId** | Geheel getal | De subtype-id voor de blob - kan *subtype* en *subtypeId* niet gebruiken |
| **Beschrijving** | Tekenreeks | Aangepaste beschrijving van de blob |
| **Delen** | Tekenreeks | Of de blob gedeeld kan`None`worden `Tree` `Global`- enum [ , , ] |

Blob-metagegevens worden altijd geleverd als de eerste `.json` brok met **Content-Type** `application/json` of als bestand. Bestandsgegevens worden geleverd in de tweede brok en kunnen van elk ondersteund MIME-type zijn.

De Swagger documentatie beschrijft deze modelschema's in detail.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Meer informatie over het gebruik van de referentiedocumentatie door [Swagger te gebruiken.](./how-to-use-swagger.md)

### <a name="blobs-response-data"></a>Klodders-antwoordgegevens

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

| Kenmerk | Type | Beschrijving |
| --- | --- | --- |
| **id** | Tekenreeks | De unieke id voor de blob |
| **Naam** |Tekenreeks | Een mensvriendelijke naam voor de blob |
| **parentId** | Tekenreeks | De bovenliggende entiteit waaraan de blob moet worden gekoppeld (spaties, apparaten of gebruikers) |
| **Type** | Tekenreeks | Het type blob - kan *geen type* en *typeId* gebruiken  |
| **typeId** | Geheel getal | De blobtype-id - kan *geen type* en *typeId* gebruiken |
| **Subtype** | Tekenreeks | Het blob-subtype - kan *subtype* en *subtypeId* niet gebruiken |
| **subtypeId** | Geheel getal | De subtype-id voor de blob - kan *subtype* en *subtypeId* niet gebruiken |
| **Delen** | Tekenreeks | Of de blob gedeeld kan`None`worden `Tree` `Global`- enum [ , , ] |
| **Beschrijving** | Tekenreeks | Aangepaste beschrijving van de blob |
| **contentInfos** | Matrix | Hiermee geeft u ongestructureerde metagegevensinclusief versie op |
| **Fullname** | Tekenreeks | De volledige naam van de blob |
| **ruimtePaden** | Tekenreeks | Het ruimtepad |

Blob-metagegevens worden altijd geleverd als de eerste `.json` brok met **Content-Type** `application/json` of als bestand. Bestandsgegevens worden geleverd in de tweede brok en kunnen van elk ondersteund MIME-type zijn.

### <a name="blob-multipart-request-examples"></a>Voorbeelden van blob-aanvragen voor meerdere delen

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Als u een tekstbestand als blob wilt uploaden en koppelen aan een spatie, dient u een geverifieerd HTTP POST-verzoek in om:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Met de volgende instantie:

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

| Waarde | Vervangen door |
| --- | --- |
| USER_DEFINED_BOUNDARY | Een meerdelige naam van de inhoud |

De volgende code is een .NET-implementatie van dezelfde blob-upload, met behulp van de klasse [MultipartFormDataContent:](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent)

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

Ten slotte kunnen [cURL-gebruikers](https://curl.haxx.se/) meerderedelenformulieraanvragen op dezelfde manier indienen:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Waarde | Vervangen door |
| --- | --- |
| YOUR_TOKEN | Uw geldige OAuth 2.0-token |
| YOUR_SPACE_ID | De id van de ruimte om de blob aan te koppelen |
| PATH_TO_FILE | Het pad naar uw tekstbestand |

[![cURL-voorbeeld](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

Een geslaagde post retourneert de id van de nieuwe blob.

## <a name="api-endpoints"></a>API-eindpunten

In de volgende secties worden de belangrijkste blob-gerelateerde API-eindpunten en hun functionaliteiten beschreven.

### <a name="devices"></a>Apparaten

U blobs aan apparaten koppelen. De volgende afbeelding toont de swagger-referentiedocumentatie voor uw beheer-API's. Het specificeert apparaatgerelateerde API-eindpunten voor blobverbruik en alle vereiste padparameters die erin moeten worden doorgegeven.

[![Apparaatblobs](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Als u bijvoorbeeld een blob wilt bijwerken of maken en de blob aan een apparaat wilt koppelen, dient u een geverifieerd HTTP PATCH-verzoek in om:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_BLOB_ID* | De gewenste blob-id |

Succesvolle aanvragen retourneren een JSON-object zoals [eerder beschreven](#blobs-response-data).

### <a name="spaces"></a>Ruimten

U ook blobs aan spaties koppelen. In de volgende afbeelding worden alle ruimte-API-eindpunten weergegeven die verantwoordelijk zijn voor het verwerken van blobs. Het bevat ook alle padparameters die naar die eindpunten moeten worden doorgegeven.

[![Ruimteblobs](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Als u bijvoorbeeld een blob wilt retourneren die aan een spatie is gekoppeld, moet u een geverifieerd HTTP GET-verzoek indienen bij:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_BLOB_ID* | De gewenste blob-id |

Succesvolle aanvragen retourneren een JSON-object zoals [eerder beschreven](#blobs-response-data).

Een PATCH-verzoek om hetzelfde eindpunt bij te werken, werkt metagegevensbeschrijvingen bij en maakt versies van de blob. De HTTP-aanvraag wordt gedaan via de PATCH-methode, samen met de benodigde meta- en meerdelige formuliergegevens.

### <a name="users"></a>Gebruikers

U blobs koppelen aan gebruikersmodellen (bijvoorbeeld om een profielfoto te koppelen). De volgende afbeelding toont relevante API-eindpunten van `id`gebruikers en de vereiste padparameters, zoals:

[![Gebruikersblobs](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Als u bijvoorbeeld een blob wilt ophalen die aan een gebruiker is gekoppeld, dient u een geverifieerd HTTP GET-verzoek in met alle vereiste formuliergegevens om:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_BLOB_ID* | De gewenste blob-id |

Succesvolle aanvragen retourneren een JSON-object zoals [eerder beschreven](#blobs-response-data).

## <a name="common-errors"></a>Algemene fouten

* Een veelvoorkomende fout houdt in dat u niet de juiste koptekstgegevens verstrekt:

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  Als u deze fout wilt oplossen, controleert u of de algemene aanvraag een geschikte **koptekst voor inhoudstype** heeft:

     * `multipart/mixed`
     * `multipart/form-data`

  Controleer ook of elke *meerdelige chunk* een geschikte bijbehorende **Inhoudstype**heeft.

* Een tweede veelvoorkomende fout ontstaat wanneer meerdere blobs aan dezelfde bron worden toegewezen in uw [grafiek met ruimtelijke intelligentie:](concepts-objectmodel-spatialgraph.md)

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > Het **kenmerk van** het bericht is afhankelijk van de resource. 

  Slechts één blob (van elke soort) mag worden gekoppeld aan elke resource in uw ruimtelijke grafiek. 

  Als u deze fout wilt oplossen, werkt u de bestaande blob bij met behulp van de juiste API HTTP PATCH-bewerking. Hierdoor worden de bestaande blobgegevens vervangen door de gewenste gegevens.

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure Digital Twins Swagger](how-to-use-swagger.md)gebruiken voor meer informatie over swagger-referentiedocumentatie voor Azure Digital Twins.

- Als u blobs wilt uploaden via Postman, leest u [Postman configureren.](./how-to-configure-postman.md)