---
title: Azure Video Indexer gebruiken om het merk model aan te passen
titleSuffix: Azure Media Services
description: In dit artikel wordt uitgelegd hoe u Azure Video Indexer kunt gebruiken om het merk model aan te passen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 4289c592644d7570ff0dd9ce6aed0cd77f51f25e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838339"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Een Brands model aanpassen met de Video Indexer-API

Video Indexer ondersteunt merk detectie van spraak-en visuele tekst tijdens het indexeren en opnieuw indexeren van video-en audio-inhoud. De merk detectie functie identificeert vermeldingen van producten, services en bedrijven die worden voorgesteld door de merken database van Bing. Als micro soft bijvoorbeeld wordt vermeld in een video-of audio-inhoud of als het in visuele tekst in een video wordt weer gegeven, detecteert Video Indexer deze als een merk in de inhoud. Met een aangepast merk model kunt u bepaalde merken uitsluiten van detectie en merken opnemen die deel uitmaken van uw model en die zich mogelijk niet in de data base van Bing Brands bevinden.

Zie [overzicht](customize-brands-model-overview.md)voor een gedetailleerd overzicht.

U kunt de Video Indexer-Api's gebruiken om aangepaste Brands modellen te maken, te gebruiken en te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U kunt ook de Video Indexer website gebruiken, zoals beschreven in [het model Brands aanpassen met behulp van de video indexer-website](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Een merk maken

Hiermee maakt u een nieuw aangepast merk en voegt u dit toe aan het aangepaste merk model voor het opgegeven account.

### <a name="request-url"></a>Aanvraag-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Naast deze para meters moet u een JSON-object van de aanvraag tekst opgeven die informatie biedt over het nieuwe merk, volgend de indeling van het voor beeld hieronder.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Als **deze optie is ingesteld op** True, wordt het merk in de lijst met *insluitingen* voor video indexer gedetecteerd. Als **de instelling is ingesteld op** False, wordt het merk in de *uitsluitings* lijst geplaatst, zodat video indexer dit niet detecteert.

De **referenceUrl** -waarde kan bestaan uit een referentie websites voor het merk, zoals een koppeling naar de Wikipedia-pagina.

De waarde **Tags** is een lijst met tags voor het merk. Dit wordt weer gegeven in het *categorie* veld van het merk op de website van video indexer. Het merk "Azure" kan bijvoorbeeld worden gelabeld of gecategoriseerd als "Cloud".

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

Hiermee verwijdert u een merk van het aangepaste Brands model voor het opgegeven account. Het account is opgegeven in de **accountId** -para meter. Zodra het merk is aangeroepen, bevallen de lijsten voor het *opnemen* of *uitsluiten* van merken niet meer.

### <a name="request-url"></a>Aanvraag-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|id|geheel getal|Ja|De merk-id (gegenereerd bij het maken van het merk)|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Er is geen verdere aanvraag tekst vereist voor deze aanroep.

### <a name="response"></a>Antwoord

Er is geen inhoud geretourneerd wanneer het merk wordt verwijderd.

## <a name="get-a-specific-brand"></a>Een specifiek merk ophalen

Zo kunt u zoeken naar de details van een merk in het aangepaste merk model voor het opgegeven account met behulp van de merk-id.

### <a name="request-url"></a>Aanvraag-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|id|geheel getal|Ja|De merk-ID (gegenereerd bij het maken van het merk)|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Er is geen verdere aanvraag tekst vereist voor deze aanroep.

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

Zo kunt u zoeken naar de details van een merk in het aangepaste merk model voor het opgegeven account met behulp van de merk-ID.

### <a name="request-url"></a>Aanvraag-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|id|geheel getal|Ja|De merk-ID (gegenereerd bij het maken van het merk)|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Naast deze para meters moet u een JSON-object van de aanvraag tekst opgeven met de bijgewerkte informatie over het merk dat u wilt bijwerken, zoals hieronder wordt beschreven in het onderstaande voor beeld.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> In dit voor beeld wordt het merk dat in de hoofd tekst van de aanvraag is gemaakt in het gedeelte **een merk maken** , hier bijgewerkt met een nieuwe tag en een nieuwe beschrijving. De **ingeschakelde** waarde is ook gewijzigd in ONWAAR om deze in de *uitsluitings* lijst te plaatsen.

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

Dit retourneert alle Brands in het aangepaste Brands model voor het opgegeven account, ongeacht of het merk is bedoeld om te worden *opgenomen* in de lijst met merken of *uitsluiten* .

### <a name="request-url"></a>Aanvraag-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Er is geen verdere aanvraag tekst vereist voor deze aanroep.

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

Hiermee worden de model instellingen voor Brands in het opgegeven account geretourneerd. De instellingen van het merk model geven aan of detectie van de Bing Brands-data base is ingeschakeld. Als Bing-merken niet zijn ingeschakeld, detecteert Video Indexer alleen Brands van het aangepaste merk model van het opgegeven account.

### <a name="request-url"></a>Aanvraag-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Er is geen verdere aanvraag tekst vereist voor deze aanroep.

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

Hiermee worden de instellingen van het merk model bijgewerkt in het opgegeven account. De instellingen van het merk model geven aan of detectie van de Bing Brands-data base is ingeschakeld. Als Bing-merken niet zijn ingeschakeld, detecteert Video Indexer alleen Brands van het aangepaste merk model van het opgegeven account.

### <a name="request-url"></a>Aanvraag-URL:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Bekijk de vereiste para meters en test uit met behulp van de video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Aanvraag parameters

|**Naam**|**Type**|**Vereist**|**Beschrijving**|
|---|---|---|---|
|location|tekenreeks|Ja|De Azure-regio waarnaar de aanroep moet worden gerouteerd. Zie [Azure-regio's en-video indexer](regions.md)voor meer informatie.|
|accountId|tekenreeks|Ja|GUID (Globally Unique Identifier) voor het account|
|accessToken|tekenreeks|Ja|Toegangs token (moet een [toegangs token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)van het bereik account zijn) om te kunnen worden geverifieerd bij de aanroep. Toegangs tokens verlopen binnen 1 uur.|

### <a name="request-body"></a>Aanvraagbody

Naast deze para meters moet u een JSON-object van de aanvraag tekst opgeven die informatie biedt over het nieuwe merk, volgend de indeling van het voor beeld hieronder.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** wordt ingesteld op True, betekent dat Bing-merken zijn ingeschakeld. Als *useBuiltin* False is, zijn Bing-merken uitgeschakeld.

### <a name="response"></a>Antwoord

Er is geen inhoud geretourneerd wanneer de instelling voor het model Brands is bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

[Het merk model aanpassen met behulp van website](customize-brands-model-with-website.md)
