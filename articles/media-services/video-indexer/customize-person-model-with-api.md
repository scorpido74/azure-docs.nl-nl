---
title: De Video Indexer-API gebruiken om een persoons model aan te passen-Azure
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u een persoons model kunt aanpassen met de Video Indexer-API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 44f97e3d9af9daac8d62ae42be76bd73dedbd453
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838266"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Een persoonlijk model aanpassen met de Video Indexer-API

Video Indexer ondersteunt gezichts detectie en beroemdheden herkenning voor video-inhoud. De functie voor beroemdheden-herkenning gaat over ongeveer 1.000.000 gezichten op basis van een gevraagde gegevens bron, zoals IMDB, Wikipedia en belangrijkste LinkedIn-invloeds. Gezichten die niet worden herkend door de beroemdheden-herkennings functie, worden gedetecteerd. de namen blijven echter ongewijzigd. Nadat u uw video naar Video Indexer hebt geüpload en de resultaten terug hebt opgehaald, kunt u teruggaan en de gezichten die niet zijn herkend, een naam geven. Zodra u een gezicht met een naam hebt gelabeld, worden het gezicht en de naam toegevoegd aan het persoonlijke model van uw account. Video Indexer zal dit gezicht vervolgens herkennen in uw toekomstige Video's en eerdere Video's.

U kunt de Video Indexer-API gebruiken om gezichten te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U kunt ook de Video Indexer website gebruiken, zoals beschreven in [persoonlijk model aanpassen met behulp van de video indexer-website](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Meerdere persoons modellen beheren 

Video Indexer ondersteunt meerdere persoons modellen per account. Deze functie is momenteel alleen beschikbaar via de Video Indexer-Api's.

Als uw account verantwoordelijk is voor verschillende gebruiks scenario's, wilt u mogelijk meerdere persoons modellen per account maken. Als uw inhoud bijvoorbeeld is gerelateerd aan sport, kunt u vervolgens een afzonderlijk persoons model maken voor elke sport (voetbal, basketbal, voetbal enz.). 

Zodra een model is gemaakt, kunt u het gebruiken door de model-ID van een specifiek persoons model op te geven bij het uploaden/indexeren of indexeren van een video. Als u een nieuw gezicht voor een video bijwerkt, wordt het specifieke aangepaste model waarmee de video is gekoppeld, bijgewerkt.

Voor elk account geldt een limiet van 50 persoons modellen. Als u de ondersteuning voor meerdere persoons modellen niet nodig hebt, moet u geen persoons model-ID aan uw video toewijzen tijdens het uploaden/indexeren of opnieuw indexeren. In dit geval gebruikt Video Indexer het standaard aangepaste persoons model in uw account.

## <a name="create-a-new-person-model"></a>Een nieuw persoons model maken

Een nieuw persoons model maken in het opgegeven account. 

### <a name="request-url"></a>Aanvraag-URL

Dit is een POST-aanvraag.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Hieronder ziet u de aanvraag in krul.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Aanvraag parameters 

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|naam|tekenreeks|Ja|De naam voor het persoons model|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Er is geen verdere aanvraag tekst vereist voor deze aanroep.

### <a name="response"></a>Antwoord

Het antwoord bevat de naam en de gegenereerde model-ID van het persoons model dat u zojuist hebt gemaakt, in de indeling van het onderstaande voor beeld.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Gebruik vervolgens de **id-** waarde voor de para meter **personModelId** wanneer u [een video uploadt om een video te indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) of opnieuw te [indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Een persoons model verwijderen

Een aangepast persoons model verwijderen uit het opgegeven account. 

Zodra het persoons model is verwijderd, blijft de index van uw huidige Video's die gebruikmaken van het verwijderde model ongewijzigd totdat u ze opnieuw indexeert. Bij het opnieuw indexeren worden de gezichten die in het verwijderde model werden genoemd, niet herkend door Video Indexer in uw huidige Video's die zijn geïndexeerd met het model. deze gezichten worden echter wel gedetecteerd. Uw huidige Video's die zijn geïndexeerd met het verwijderde model, gebruiken nu het standaard persoons model van uw account. Als gezichten van het verwijderde model ook worden genoemd in het standaard model van uw account, worden deze gezichten nog steeds herkend in de Video's.

### <a name="request-url"></a>Aanvraag-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Hieronder ziet u de aanvraag in krul.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|id|tekenreeks|Ja|De id van het persoons model (gegenereerd wanneer het persoons model wordt gemaakt)|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Er is geen verdere aanvraag tekst vereist voor deze aanroep.

### <a name="response"></a>Antwoord

Er is geen inhoud geretourneerd wanneer het persoons model is verwijderd.

## <a name="get-all-person-models"></a>Alle persoons modellen ophalen

Alle persoons modellen ophalen in het opgegeven account. 

### <a name="request-call"></a>Oproep aanvragen

Dit is een GET-aanvraag.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Hieronder ziet u de aanvraag in krul.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Er is geen verdere aanvraag tekst vereist voor deze aanroep.

### <a name="response"></a>Antwoord

Het antwoord bevat een lijst met alle persoons modellen in uw account (met inbegrip van het standaard persoons model in het opgegeven account) en elk van de namen en id's volgens de indeling van het onderstaande voor beeld.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

U kunt kiezen welk model u wilt gebruiken voor een video door de **id-** waarde van het persoons model voor de para meter **personModelId** te gebruiken wanneer [u een video uploadt om een video te indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) of opnieuw te [indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Een gezicht bijwerken

Met deze opdracht kunt u een gezicht in uw video bijwerken met een naam met behulp van de ID van de video en de ID van het gezicht. Vervolgens wordt het persoons model bijgewerkt waarmee de video is gekoppeld bij het uploaden/indexeren of opnieuw indexeren. Als er geen persoons model is toegewezen, wordt het standaard persoons model van de account bijgewerkt. 

Als dit het geval is, herkent het de instanties van hetzelfde gezicht in uw andere huidige Video's die hetzelfde persoons model delen. De herkenning van het gezicht in uw andere huidige Video's kan enige tijd in beslag nemen omdat dit een batch proces is.

U kunt een gezicht bijwerken dat Video Indexer herkend als een beroemdheden met een nieuwe naam. De nieuwe naam die u geeft, heeft voor rang op de ingebouwde beroemdheden-herkenning.

### <a name="request-call"></a>Oproep aanvragen

Dit is een POST-aanvraag.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Hieronder ziet u de aanvraag in krul.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|videoId|tekenreeks|Ja|De id voor de video waarin het gezicht dat u wilt bijwerken wordt weer gegeven. Deze wordt gemaakt wanneer de video wordt geüpload en geïndexeerd.|
|faceId|geheel getal|Ja|De id voor het gezicht dat wordt bijgewerkt. U kunt de faceId ophalen uit de video-index|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|
|naam|tekenreeks|Ja|Nieuwe naam voor het bijwerken van het gezicht met.|

Namen zijn uniek voor persoons modellen, dus als u twee verschillende gezichten in dezelfde persoon een model met dezelfde **naam** parameter waarde geeft video indexer worden de gezichten als dezelfde persoon weer gegeven en worden deze geconvergeerd zodra u de video opnieuw indexeert. 

### <a name="request-body"></a>Aanvraagbody

Er is geen verdere aanvraag tekst vereist voor deze aanroep.

### <a name="response"></a>Antwoord

Er is geen inhoud geretourneerd wanneer het gezicht is bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

[Persoonlijk model aanpassen met behulp van de Video Indexer-website](customize-person-model-with-website.md)
