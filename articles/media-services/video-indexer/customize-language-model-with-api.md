---
title: Een taalmodel aanpassen met de API voor videoindexer
titlesuffix: Azure Media Services
description: Meer informatie over het aanpassen van een taalmodel met de Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127982"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Een taalmodel aanpassen met de API voor videoindexer

Met Video Indexer u aangepaste taalmodellen maken om spraakherkenning aan te passen door aanpassingstekst te uploaden, namelijk tekst uit het domein waarvan u de woordenschat wilt waaraan de engine zich wilt aanpassen. Zodra u uw model traint, worden nieuwe woorden in de aanpassingstekst herkend.

Zie [Een taalmodel aanpassen met videoindexer](customize-language-model-overview.md)voor een gedetailleerd overzicht en aanbevolen procedures voor aangepaste taalmodellen.

U de API's voor video-indexeren gebruiken om aangepaste taalmodellen in uw account te maken en te bewerken, zoals beschreven in dit onderwerp. U ook de website gebruiken, zoals beschreven in [Het taalmodel aanpassen met behulp van de website Video Indexer.](customize-language-model-with-api.md)

## <a name="create-a-language-model"></a>Een taalmodel maken

Met de API [voor een taalmodel maken](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) wordt een nieuw aangepast taalmodel gemaakt in het opgegeven account. U in dit gesprek bestanden uploaden voor het taalmodel. U hier ook het taalmodel maken en later bestanden voor het model uploaden door het taalmodel bij te werken.

> [!NOTE]
> U moet het model nog steeds trainen met de ingeschakelde bestanden voor het model om de inhoud van de bestanden te leren. Aanwijzingen voor het trainen van een taal zijn in de volgende sectie.

Als u bestanden wilt uploaden die aan het taalmodel moeten worden toegevoegd, moet u bestanden uploaden in de behuizing met FormData, naast het verstrekken van waarden voor de bovenstaande vereiste parameters. Er zijn twee manieren om deze taak uit te voeren:

* Sleutel zal de bestandsnaam en waarde zal de txt-bestand.
* De sleutel is de bestandsnaam en de waarde is een URL naar txt-bestand.

### <a name="response"></a>Antwoord

Het antwoord biedt metagegevens op het nieuw gemaakte taalmodel, samen met metagegevens op elk van de bestanden van het model volgens de indeling van dit voorbeeld JSON-uitvoer:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Een taalmodel trainen

De [trein een taalmodel](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API traint een aangepast taalmodel in het opgegeven account met de inhoud in de bestanden die zijn geüpload naar en ingeschakeld in het taalmodel.

> [!NOTE]
> U moet eerst het taalmodel maken en de bestanden uploaden. U bestanden uploaden bij het maken van het taalmodel of door het taalmodel bij te werken.

### <a name="response"></a>Antwoord

Het antwoord biedt metagegevens op het nieuw getrainde taalmodel, samen met metagegevens op elk van de bestanden van het model volgens de indeling van dit voorbeeld JSON-uitvoer:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

De `id` geretourneerde is een unieke ID die `languageModelId` wordt gebruikt om onderscheid te maken tussen taalmodellen, terwijl wordt gebruikt voor het [uploaden van een video te indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) en [opnieuw indexeren van een video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API's (ook bekend als `linguisticModelId` in Video Indexer upload / reindex API's).

## <a name="delete-a-language-model"></a>Een taalmodel verwijderen

Met de API voor het verwijderen van [een taalmodel](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) wordt een aangepast taalmodel uit het opgegeven account verwijderd. Elke video die het verwijderde taalmodel heeft gebruikt, houdt dezelfde index bij totdat u de video opnieuw indexeert. Als u de video opnieuw indexeert, u een nieuw taalmodel aan de video toewijzen. Anders gebruikt Video Indexer het standaardmodel om de video opnieuw te indexeren.

### <a name="response"></a>Antwoord

Er is geen geretourneerde inhoud wanneer het taalmodel is verwijderd.

## <a name="update-a-language-model"></a>Een taalmodel bijwerken

De [update van een Taalmodel-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) werkt een aangepast taalpersoonsmodel bij in het opgegeven account.

> [!NOTE]
> U moet het taalmodel al hebben gemaakt. U deze oproep gebruiken om alle bestanden onder het model in of uit te schakelen, de naam van het taalmodel bij te werken en bestanden te uploaden die aan het taalmodel moeten worden toegevoegd.

Als u bestanden wilt uploaden die aan het taalmodel moeten worden toegevoegd, moet u bestanden uploaden in de behuizing met FormData, naast het verstrekken van waarden voor de bovenstaande vereiste parameters. Er zijn twee manieren om deze taak uit te voeren:

* Sleutel zal de bestandsnaam en waarde zal de txt-bestand.
* De sleutel is de bestandsnaam en de waarde is een URL naar txt-bestand.

### <a name="response"></a>Antwoord

Het antwoord biedt metagegevens op het nieuw getrainde taalmodel, samen met metagegevens op elk van de bestanden van het model volgens de indeling van dit voorbeeld JSON-uitvoer:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Gebruik `id` de bestanden die in het antwoord zijn geretourneerd om de inhoud van het bestand te downloaden.

## <a name="update-a-file-from-a-language-model"></a>Een bestand bijwerken vanuit een taalmodel

Met [de update van een](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) bestand `enable` u de naam en de status van een bestand bijwerken in een aangepast taalmodel in het opgegeven account.

### <a name="response"></a>Antwoord

Het antwoord bevat metagegevens in het bestand dat u hebt bijgewerkt naar aanleiding van de indeling van de onderstaande JSON-uitvoer.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Gebruik `id` de van het bestand geretourneerd in het antwoord om de inhoud van het bestand te downloaden.

## <a name="get-a-specific-language-model"></a>Een specifiek taalmodel

Met [de](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) BRON-API wordt informatie geretourneerd over het opgegeven taalmodel in het opgegeven account, zoals taal en de bestanden die zich in het taalmodel bevinden.

### <a name="response"></a>Antwoord

Het antwoord biedt metagegevens op het opgegeven taalmodel, samen met metagegevens op elk van de bestanden van het model volgens de indeling van dit voorbeeld JSON-uitvoer:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Gebruik `id` de van het bestand geretourneerd in het antwoord om de inhoud van het bestand te downloaden.

## <a name="get-all-the-language-models"></a>Download alle taalmodellen

Als [u alle API's krijgt,](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) worden alle aangepaste taalmodellen in het opgegeven account in een lijst geretourneerd.

### <a name="response"></a>Antwoord

Het antwoord bevat een lijst met alle taalmodellen in uw account en elk van hun metagegevens en bestanden volgens de indeling van dit voorbeeld JSON-uitvoer:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Een bestand verwijderen uit een taalmodel

De [delete](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) API verwijdert het opgegeven bestand uit het opgegeven taalmodel in het opgegeven account.

### <a name="response"></a>Antwoord

Er is geen geretourneerde inhoud wanneer het bestand wordt verwijderd uit het taalmodel.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Metagegevens van een bestand ophalen uit een taalmodel

[Metagegevens ophalen van een bestands-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) retourneert de inhoud van en metagegevens op het opgegeven bestand van het gekozen taalmodel in uw account.

### <a name="response"></a>Antwoord

Het antwoord biedt de inhoud en metagegevens van het bestand in JSON-indeling, vergelijkbaar met dit voorbeeld:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> De inhoud van dit voorbeeldbestand zijn de woorden "hallo" en wereld" in twee afzonderlijke regels.

## <a name="download-a-file-from-a-language-model"></a>Een bestand downloaden uit een taalmodel

Als [u een bestands-API downloadt,](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) wordt een tekstbestand gedownload dat de inhoud van het opgegeven bestand bevat van het opgegeven taalmodel in het opgegeven account. Dit tekstbestand moet overeenkomen met de inhoud van het oorspronkelijk geüploade tekstbestand.

### <a name="response"></a>Antwoord

Het antwoord is het downloaden van een tekstbestand met de inhoud van het bestand in de JSON-indeling.

## <a name="next-steps"></a>Volgende stappen

[Taalmodel aanpassen met behulp van website](customize-language-model-with-website.md)
