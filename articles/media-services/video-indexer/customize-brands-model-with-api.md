---
title: Azure Video Indexer gebruiken om het merk model aan te passen
titleSuffix: Azure Media Services
description: In dit artikel wordt gedemonstreerd hoe u Azure Video Indexer kunt gebruiken om het merk model aan te passen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 81ba4cc7be5f9361d21aaea2ba78d0fd6f0f8c95
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289914"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Een Brands model aanpassen met de Video Indexer-API

Video Indexer ondersteunt merk detectie van spraak-en visuele tekst tijdens het indexeren en opnieuw indexeren van video-en audio-inhoud. De merk detectie functie identificeert vermeldingen van producten, services en bedrijven die worden voorgesteld door de merken database van Bing. Als micro soft bijvoorbeeld wordt vermeld in een video-of audio-inhoud of als het in visuele tekst in een video wordt weer gegeven, detecteert Video Indexer deze als een merk in de inhoud. Met een aangepast merk model kunt u bepaalde merken uitsluiten van detectie en merken opnemen die deel uitmaken van uw model en die zich mogelijk niet in de data base van Bing Brands bevinden.

Zie [overzicht](customize-brands-model-overview.md)voor een gedetailleerd overzicht.

U kunt de Video Indexer-Api's gebruiken om aangepaste Brands modellen te maken, te gebruiken en te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U kunt ook de Video Indexer website gebruiken, zoals beschreven in [het model Brands aanpassen met behulp van de video indexer-website](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Een merk maken

Met de [Create a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) API maakt u een nieuw aangepast merk en voegt u dit toe aan het aangepaste merk model voor het opgegeven account. 

> [!NOTE]
> Als de instelling is **ingeschakeld** (in de hoofd tekst) op True, wordt het merk in de lijst met *insluitingen* voor video indexer gedetecteerd. Als **de instelling is ingesteld op** False, wordt het merk in de *uitsluitings* lijst geplaatst, zodat video indexer dit niet detecteert.

Enkele andere para meters die u in de hoofd tekst kunt instellen:

* De **referenceUrl** -waarde kan bestaan uit een referentie websites voor het merk, zoals een koppeling naar de Wikipedia-pagina.
* De waarde **Tags** is een lijst met tags voor het merk. Dit wordt weer gegeven in het *categorie* veld van het merk op de website van video indexer. Het merk "Azure" kan bijvoorbeeld worden gelabeld of gecategoriseerd als "Cloud".

### <a name="response"></a>Antwoord

Het antwoord bevat informatie over het merk dat u zojuist hebt gemaakt, in de indeling van het onderstaande voor beeld.

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

Met de brand-API [verwijderen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) wordt een merk van het aangepaste merk model verwijderd voor het opgegeven account. Het account is opgegeven in de **accountId** -para meter. Zodra het merk is aangeroepen, bevallen de lijsten voor het *opnemen* of *uitsluiten* van merken niet meer.

### <a name="response"></a>Antwoord

Er is geen inhoud geretourneerd wanneer het merk wordt verwijderd.

## <a name="get-a-specific-brand"></a>Een specifiek merk ophalen

Met de [een merk](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) -API ophalen kunt u zoeken naar de details van een merk in het aangepaste merk model voor het opgegeven account met behulp van de merk-id.

### <a name="response"></a>Antwoord

Het antwoord bevat informatie over het merk dat u hebt gezocht (met behulp van merk-ID) volgens de indeling van het voor beeld hieronder.

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
> **als deze instelling is** ingesteld op **True** , geeft dit aan dat het merk *in de lijst met* insluitingen voor video indexer moet worden **gedetecteerd en is ingesteld op** False, betekent dit dat het merk zich in de *uitsluitings* lijst bevindt, zodat dit niet door video indexer wordt gedetecteerd.

## <a name="update-a-specific-brand"></a>Een specifiek merk bijwerken

Met de [Update een gloed](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) -API kunt u zoeken naar de details van een merk in het aangepaste merk model voor het opgegeven account met behulp van de merk-id.

### <a name="response"></a>Antwoord

Het antwoord bevat de bijgewerkte informatie over het merk dat u hebt bijgewerkt volgens de indeling van het onderstaande voor beeld.

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

## <a name="get-all-of-the-brands"></a>Alle merken ophalen

De [all Brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) -API ophalen retourneert alle Brands in het aangepaste Brands model voor het opgegeven account, ongeacht of het merk is bedoeld om te worden *opgenomen* in de lijst met merken of *uitsluiten* .

### <a name="response"></a>Antwoord

Het antwoord bevat een lijst met alle Brands in uw account en alle details op basis van de indeling van het onderstaande voor beeld.

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
> Het merk in de lijst *bevat* een voor *beeld* van video indexer om te detecteren en het merk met de naam *example2* bevindt zich in de *uitsluitings* lijst, waardoor het niet door video indexer wordt gedetecteerd.

## <a name="get-brands-model-settings"></a>Instellingen voor Brands model ophalen

Met de API-instellingen voor het [ophalen van Brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) worden de model instellingen voor Brands in het opgegeven account geretourneerd. De instellingen van het merk model geven aan of detectie van de Bing Brands-data base is ingeschakeld. Als Bing-merken niet zijn ingeschakeld, detecteert Video Indexer alleen Brands van het aangepaste merk model van het opgegeven account.

### <a name="response"></a>Antwoord

In het antwoord wordt weer gegeven of Bing Brands is ingeschakeld volgens de indeling van het onderstaande voor beeld.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** wordt ingesteld op True, betekent dat Bing-merken zijn ingeschakeld. Als *useBuiltin* False is, zijn Bing-merken uitgeschakeld. De **status** waarde kan worden genegeerd, omdat deze is afgeschaft.

## <a name="update-brands-model-settings"></a>Model instellingen voor update Brands bijwerken

Met de [Update-Brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) worden de instellingen van het merk model bijgewerkt in het opgegeven account. De instellingen van het merk model geven aan of detectie van de Bing Brands-data base is ingeschakeld. Als Bing-merken niet zijn ingeschakeld, detecteert Video Indexer alleen Brands van het aangepaste merk model van het opgegeven account.

De vlag **useBuiltIn** ingesteld op True geeft aan dat Bing-merken zijn ingeschakeld. Als *useBuiltin* False is, zijn Bing-merken uitgeschakeld.

### <a name="response"></a>Antwoord

Er is geen inhoud geretourneerd wanneer de instelling voor het model Brands is bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

[Het merk model aanpassen met behulp van website](customize-brands-model-with-website.md)
