---
title: Een persoonsmodel aanpassen met videoindexer-API
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een persoonsmodel met de API voor videoindexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127892"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Een persoonsmodel aanpassen met de API voor videoindexer

Video Indexer ondersteunt gezichtsherkenning en herkenning door beroemdheden voor video-inhoud. De functie voor herkenning van beroemdheden omvat ongeveer een miljoen gezichten op basis van veelgevraagde gegevensbronnen zoals IMDB, Wikipedia en top LinkedIn-beïnvloeders. Gezichten die niet worden herkend door de functie voor herkenning van beroemdheden worden gedetecteerd, maar links naamloos. Nadat je je video hebt geüpload naar Video Indexer en resultaten hebt teruggekregen, kun je teruggaan en de gezichten een naam geven die niet is herkend. Zodra u een gezicht met een naam labelt, worden het gezicht en de naam toegevoegd aan het persoonsmodel van uw account. Video Indexer herkent dit gezicht dan in je toekomstige video's en eerdere video's.

U de API voor video-indexergebruiken om gezichten te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U ook de website Video Indexer gebruiken, zoals beschreven in [het model Persoon aanpassen met behulp van de website Video Indexer.](customize-person-model-with-api.md)

## <a name="managing-multiple-person-models"></a>Meerdere persoonsmodellen beheren

Video Indexer ondersteunt meerdere persoonsmodellen per account. Deze functie is momenteel alleen beschikbaar via de API's voor video-indexer.

Als uw account zich richt op verschillende use-case scenario's, u meerdere persoonsmodellen per account maken. Als uw inhoud bijvoorbeeld gerelateerd is aan sport, u voor elke sport een apart persoonsmodel maken (voetbal, basketbal, voetbal, enzovoort).

Zodra een model is gemaakt, u het gebruiken door de model-ID van een specifiek persoonsmodel op te geven bij het uploaden/indexeren of opnieuw indexeren van een video. Het trainen van een nieuw gezicht voor een video werkt het specifieke aangepaste model bij waarmee de video is gekoppeld.

Elk account heeft een limiet van 50 person modellen. Als u de ondersteuning voor het model met meerdere personen niet nodig hebt, wijst u geen persoonsmodel-id toe aan uw video bij het uploaden/indexeren of opnieuw indexeren. In dit geval gebruikt Video Indexer het standaard aangepaste persoonsmodel in uw account.

## <a name="create-a-new-person-model"></a>Een nieuw persoonsmodel maken

Als u een nieuw persoonsmodel wilt maken in het opgegeven account, gebruikt u de API [voor een persoonsmodel maken.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)

Het antwoord bevat de naam en gegenereerde model-ID van het persoonsmodel dat u zojuist hebt gemaakt naar aanleiding van de indeling van het onderstaande voorbeeld.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Vervolgens gebruikt u de **id-waarde** voor de parameter **personModelId** bij het uploaden van een video om een video [te indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) of [opnieuw te indexeren.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)

## <a name="delete-a-person-model"></a>Een persoonsmodel verwijderen

Als u een aangepast persoonsmodel uit het opgegeven account wilt verwijderen, gebruikt u de API [voor een persoonsmodel verwijderen.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)

Zodra het persoonsmodel is verwijderd, blijft de index van uw huidige video's die het verwijderde model gebruikten ongewijzigd totdat u ze opnieuw indexeert. Bij het opnieuw indexeren worden de gezichten die in het verwijderde model zijn genoemd, niet herkend door Video Indexer in uw huidige video's die met dat model zijn geïndexeerd, maar de gezichten worden nog steeds gedetecteerd. Uw huidige video's die zijn geïndexeerd met het verwijderde model, gebruiken nu het standaard persoonsmodel van uw account. Als gezichten uit het verwijderde model ook worden genoemd in het standaardmodel van uw account, blijven deze gezichten worden herkend in de video's.

Er is geen geretourneerde inhoud wanneer het persoonsmodel is verwijderd.

## <a name="get-all-person-models"></a>Alle Person-modellen ophalen

Als u alle person-modellen in het opgegeven account wilt krijgen, gebruikt u de API [voor een persoonsmodel.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)

Het antwoord bevat een lijst met alle persoonsmodellen in uw account (inclusief het standaardpersoonsmodel in het opgegeven account) en elk van hun namen en id's volgens de indeling van het onderstaande voorbeeld.

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

U kiezen welk model u voor een `id` video wilt gebruiken `personModelId` met behulp van de waarde van het persoonsmodel voor de parameter bij [het uploaden van een video om](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) een video te indexeren of [opnieuw te indexeren.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)

## <a name="update-a-face"></a>Een gezicht bijwerken

Met deze opdracht u een gezicht in uw video bijwerken met een naam met behulp van de id van de video en id van het gezicht. Met deze actie wordt vervolgens het persoonsmodel bijgewerkt waarmee de video is gekoppeld bij het uploaden/indexeren of opnieuw indexeren. Als er geen Persoonsmodel is toegewezen, wordt het standaardpersoonsmodel van het account bijgewerkt.

Het systeem herkent vervolgens de gebeurtenissen van hetzelfde gezicht in uw andere huidige video's die hetzelfde persoonsmodel delen. Het herkennen van het gezicht in uw andere huidige video's kan enige tijd duren voordat het van kracht wordt, omdat dit een batchproces is.

U een gezicht bijwerken dat Video Indexer herkende als een beroemdheid met een nieuwe naam. De nieuwe naam die je geeft zal voorrang hebben op de ingebouwde celebrity erkenning.

Als u het gezicht wilt bijwerken, gebruikt u de [update van een videoface-API.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)

Namen zijn uniek voor persoonsmodellen, dus als u twee `name` verschillende gezichten in hetzelfde persoonsmodel dezelfde parameterwaarde geeft, bekijkt Video Indexer de gezichten als dezelfde persoon en convergeert deze zodra u uw video opnieuw indexeert.

## <a name="next-steps"></a>Volgende stappen

[Persoonsmodel aanpassen met de website Video-indexer](customize-person-model-with-website.md)
