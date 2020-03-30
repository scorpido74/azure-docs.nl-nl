---
title: Een Brands-model aanpassen met Video Indexer API
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een Brands-model met de Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127996"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Een Brands-model aanpassen met de API voor videoindexer

Video Indexer ondersteunt merkdetectie van spraak en visuele tekst tijdens het indexeren en opnieuw indexeren van video- en audio-inhoud. De merkdetectiefunctie identificeert vermeldingen van producten, services en bedrijven die worden voorgesteld door de merkendatabase van Bing. Als Microsoft bijvoorbeeld wordt vermeld in video- of audio-inhoud of als deze wordt weergegeven in visuele tekst in een video, detecteert Video Indexer het als een merk in de inhoud. Met een aangepast brands-model u uitsluiten dat bepaalde merken worden gedetecteerd en merken opnemen die deel moeten uitmaken van uw model dat mogelijk niet in de merkendatabase van Bing staat.

Zie [Overzicht](customize-brands-model-overview.md)voor een gedetailleerd overzicht .

U de API's voor video-indexeren gebruiken om aangepaste merkenmodellen te maken, te gebruiken en te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U ook de video-indexerwebsite gebruiken, zoals beschreven in [het model Merken aanpassen met behulp van de website Video Indexer.](customize-brands-model-with-api.md)

## <a name="create-a-brand"></a>Een merk maken

Het [maken van een merk](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) API creëert een nieuw aangepast merk en voegt het toe aan het aangepaste Merken-model voor het opgegeven account.

> [!NOTE]
> Als `enabled` u (in het lichaam) op true zet, wordt het merk in de lijst *Opnemen* voor Video-indexer ingesteld om te detecteren. Als `enabled` u false instelt, wordt het merk in de lijst *Uitsluiten* weergegeven, zodat Video Indexer het niet detecteert.

Enkele andere parameters die u in het lichaam instellen:

* De `referenceUrl` waarde kan elke referentiewebsites voor het merk zijn, zoals een link naar de Wikipedia-pagina.
* De `tags` waarde is een lijst met tags voor het merk. Deze tag wordt weergegeven in het *veld Categorie* van het merk op de website van Video Indexer. Het merk "Azure" kan bijvoorbeeld worden getagd of gecategoriseerd als "Cloud".

### <a name="response"></a>Antwoord

Het antwoord geeft informatie over het merk dat u zojuist hebt gemaakt naar aanleiding van de indeling van het onderstaande voorbeeld.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Een merk verwijderen

Met [de delete a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) API wordt een merk verwijderd uit het aangepaste merkenmodel voor het opgegeven account. Het account is `accountId` opgegeven in de parameter. Zodra het merk succesvol wordt genoemd, staat het niet langer in de lijsten met merken *opnemen* of *uitsluiten.*

### <a name="response"></a>Antwoord

Er is geen geretourneerde inhoud wanneer het merk is verwijderd.

## <a name="get-a-specific-brand"></a>Krijg een specifiek merk

Met de [GET a Brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) API u zoeken naar de details van een merk in het aangepaste merkenmodel voor het opgegeven account met behulp van de merk-ID.

### <a name="response"></a>Antwoord

Het antwoord geeft informatie over het merk dat u hebt gezocht (met merk-ID) volgens de indeling van het onderstaande voorbeeld.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled`wordt ingesteld `true` om te betekenen dat het merk in de lijst `enabled` *Opnemen* voor Video-indexer om te detecteren, en vals betekent dat het merk in de lijst *Uitsluiten,* dus Video Indexer zal het niet detecteren.

## <a name="update-a-specific-brand"></a>Een specifiek merk bijwerken

Met [de update van een merk-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) u zoeken naar de details van een merk in het aangepaste merkenmodel voor het opgegeven account met behulp van de merk-ID.

### <a name="response"></a>Antwoord

Het antwoord bevat de bijgewerkte informatie over het merk dat u hebt bijgewerkt naar aanleiding van de indeling van het onderstaande voorbeeld.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Krijg alle merken

De [get all brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) API retourneert alle merken in het aangepaste Merken-model voor het opgegeven account, ongeacht of het merk in de lijst met merken *opnemen* of *uitsluiten* moet staan.

### <a name="response"></a>Antwoord

Het antwoord bevat een lijst van alle merken in uw account en elk van hun gegevens volgens de indeling van het onderstaande voorbeeld.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Het merk met de naam *Voorbeeld* staat in de lijst *Opnemen* voor Video-indexer om te detecteren en het merk met de naam *Example2* staat in de lijst *Uitsluiten,* dus Video Indexer detecteert het niet.

## <a name="get-brands-model-settings"></a>De modelinstellingen van Merken weergeven

De API [voor merkinstellingen voor nl](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) retourneert de modelinstellingen van Merken in het opgegeven account. De modelinstellingen van Brands geven aan of detectie van de bing-merkendatabase is ingeschakeld of niet. Als Bing-merken niet zijn ingeschakeld, detecteert Video Indexer alleen merken op het aangepaste merkenmodel van het opgegeven account.

### <a name="response"></a>Antwoord

Het antwoord laat zien of Bing-merken zijn ingeschakeld volgens de indeling van het onderstaande voorbeeld.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`true wordt ingesteld, geeft aan dat Bing-merken zijn ingeschakeld. Als `useBuiltin` dit niet klopt, zijn Bing-merken uitgeschakeld. De `state` waarde kan worden genegeerd omdat deze is afgeschaft.

## <a name="update-brands-model-settings"></a>Modelinstellingen voor merken bijwerken

De [API van updatemerken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) werkt de modelinstellingen van Brands bij in het opgegeven account. De modelinstellingen van Brands geven aan of detectie van de bing-merkendatabase is ingeschakeld of niet. Als Bing-merken niet zijn ingeschakeld, detecteert Video Indexer alleen merken op het aangepaste merkenmodel van het opgegeven account.

De `useBuiltIn` vlag ingesteld op true betekent dat Bing merken zijn ingeschakeld. Als `useBuiltin` dit niet klopt, zijn Bing-merken uitgeschakeld.

### <a name="response"></a>Antwoord

Er is geen geretourneerde inhoud wanneer de modelinstelling Brands is bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

[Merkenmodel aanpassen met website](customize-brands-model-with-website.md)
