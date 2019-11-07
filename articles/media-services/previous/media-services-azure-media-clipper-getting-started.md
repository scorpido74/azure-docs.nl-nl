---
title: Aan de slag met Azure media Clipper | Microsoft Docs
description: Aan de slag met Azure media Clipper, een hulp programma voor het maken van video clips uit AMS-assets
services: media-services
keywords: clip; subclip; code ring; media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 45ecc81967d6a95f817b10bce7f8396d9379bc94
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685073"
---
# <a name="create-clips-with-azure-media-clipper"></a>Clips maken met Azure media Clipper
In deze sectie wordt beschreven hoe u aan de slag gaat met Azure media Clipper. In de volgende secties vindt u de specifieke informatie over het configureren van Azure media Clipper.

- Voeg eerst de volgende koppelingen voor Azure Media Player en Azure media Clipper toe aan de kop van uw document. We raden u aan om een versie van de Clipper en Azure Media Player in de Url's expliciet op te geven. Gebruik niet de meest recente versie van deze resources in productie, aangezien ze op aanvraag kunnen worden gewijzigd.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Voeg vervolgens de volgende klassen toe aan het div-element waar u de Clipper wilt instantiëren.

```javascript
<div id="root" class="azure-subclipper" />
```

Als u het donkere thema wilt inschakelen, voegt u de klasse Dark-Skin toe:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Vervolgens maakt u een exemplaar van de Clipper met de volgende API-aanroep:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

De para meters voor de aanroep van de initialisatie methode zijn:
- `selector` {REQUIRed, String}: CSS-selector van het overeenkomende HTML-element waarin de widget moet worden gerenderd.
- `restVersion` {REQUIRed, String}: de Azure Media Services REST API versie op doel. De REST-versie definieert de indeling van de uitvoer die door de widget wordt gegenereerd. Momenteel wordt slechts 2,0 ondersteund.
- `submitSubclipCallback` {REQUIRed, Promise} de call back functie die is aangeroepen wanneer op de knop verzenden van de widget wordt geklikt. De call back functie verwacht de uitvoer die door de widget wordt gegenereerd (een render-taak configuratie of een filter definitie). Zie retour aanroep van subclip verzenden voor meer informatie.
- `logLevel` {optioneel, {' info ', ' warn ', ' fout '}}: het logboek registratie niveau dat moet worden weer gegeven in de console van de browser. Standaard waarde: fout
- `minimumMarkerGap` {optioneel, int}: de minimale grootte van een subclip (in seconden). Opmerking: de waarde moet groter zijn dan of gelijk zijn aan 6, wat ook de standaard instelling is.
- `singleBitrateMp4Profile` {optioneel, JSON-object} het MP4-profiel met enkele bitsnelheid dat moet worden gebruikt voor de gegenereerde taak configuratie door de widget. Als u dit niet opgeeft, wordt het [standaard MP4-profiel met één bitsnelheid](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p)gebruikt.
- `multiBitrateMp4Profile` {optioneel, JSON-object} het MP4-profiel met meerdere bitsnelheden dat moet worden gebruikt voor het genereren van de taak configuratie die door de widget wordt gegenereerd. Als u dit niet opgeeft, wordt het [standaard-multi-bitrate MP4-profiel](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p)gebruikt.
- met `keymap` {optioneel, JSON-object} kunt u de sneltoetsen van de widget aanpassen. Zie [aanpas bare](media-services-azure-media-clipper-keyboard-shortcuts.md)sneltoetsen voor meer informatie.
- `assetsPanelLoaderCallback` {optioneel, Promise} de call back functie die is aangeroepen om (asynchroon) een nieuwe pagina met assets te laden in het deel venster activa telkens wanneer de gebruiker naar beneden schuift naar de onderkant van het deel venster. Zie voor meer informatie het deel venster loader terugbellen.
- `height` {optioneel, Number} de totale hoogte van de widget (minimum hoogte is 600 PX zonder activa deel venster en 850 px met het deel venster assets).
- `subclippingMode` (optioneel, {' all ', ' render ', ' filter '}): de subknip modus (en) zijn toegestaan. De standaard waarde is alle.
- `filterAssetsTypes` (optioneel, BOOL): met filterAssetsTypes kunt u de vervolg keuzelijst filters weer geven/verbergen in het deel venster assets. De standaard waarde is True.
- `speedLevels` (optioneel, matrix): met speedLevels kunt u verschillende snelheids niveaus voor de video speler instellen, Zie [Azure Media Player documentatie](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) voor meer informatie.
- `resetOnJobDone` (optioneel, BOOL): met resetOnJobDone kan Clipper de subclipper opnieuw instellen op een begin status wanneer een taak wordt verzonden.
- `autoplayVideo` (optioneel, BOOL): met autoplayVideo kan Clipper de video in de laad functie AutoPlay. De standaard waarde is True.
- `language` {optioneel, String}: taal stelt de taal van de widget in. Als dat niet is opgegeven, probeert de widget de berichten te lokaliseren op basis van de taal van de browser. Als er geen taal wordt gedetecteerd in de browser, wordt de widget standaard ingesteld op Engels. Zie de sectie [lokalisatie configureren](media-services-azure-media-clipper-localization.md) voor meer informatie.
- `languages` {optioneel, JSON}: de para meter talen vervangt de standaard woordenlijst van talen met een aangepaste woorden lijst die door de gebruiker is gedefinieerd. Zie de sectie [lokalisatie configureren](media-services-azure-media-clipper-localization.md) voor meer informatie.
- `extraLanguages` (optioneel, JSON): de para meter extraLanguages voegt nieuwe talen aan de standaard woordenlijst toe. Zie de sectie [lokalisatie configureren](media-services-azure-media-clipper-localization.md) voor meer informatie.

## <a name="typescript-definition"></a>Type script definitie
[Hier](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts)vindt u een [type script](https://www.typescriptlang.org/) definitie bestand voor de Clipper.

## <a name="azure-media-clipper-api"></a>Azure media Clipper-API
In deze sectie wordt het API-gedeelte van de Clipper gedocumenteerd.

- `ready(handler)`: biedt een manier om Java script uit te voeren zodra de Clipper volledig is geladen en klaar is om te worden gebruikt.
- `load(assets)`: er wordt een lijst met assets geladen in de tijdlijn van de widget (moet niet worden gebruikt in combi natie met assetsPanelLoaderCallback). Raadpleeg dit [artikel](media-services-azure-media-clipper-load-assets.md) voor meer informatie over het laden van assets in de Clipper.
- `setLogLevel(level)`: Hiermee stelt u het registratie niveau in dat moet worden weer gegeven in de console van de browser. Mogelijke waarden zijn: `info`, `warn``error`.
- `setHeight(height)`: stelt de totale hoogte van de widget in pixels in (minimale hoogte is 600 PX zonder assets deel venster en 850 px met het deel venster assets).
- `version`: Hiermee wordt de versie van de widget opgehaald.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende stappen voor het configureren van Azure media Clipper:
- [Assets in azure media Clipper laden](media-services-azure-media-clipper-load-assets.md)
- [Aangepaste sneltoetsen configureren](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Knip taken verzenden vanuit de Clipper](media-services-azure-media-clipper-submit-job.md)
- [Lokalisatie configureren](media-services-azure-media-clipper-localization.md)
