---
title: Een MPEG-DASH Adaptive Streaming Video insluiten in een HTML5-toepassing met DASH.js | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u een MPEG-DASH Adaptive Streaming Video insluit in een HTML5-toepassing met DASH.js.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6c1df14ba5a9f233f42750d4e6dea68a7d6ddc0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564853"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Een adaptieve MPEG-DASH-videostream insluiten in een HTML5-toepassing met DASH.js  

## <a name="overview"></a>Overzicht
MPEG-DASH is een ISO-standaard voor het adaptieve streamen van video-inhoud, wat aanzienlijke voordelen biedt voor ontwikkelaars die hoogwaardige, adaptieve videostreaming willen leveren. Met MPEG-DASH past de videostream zich automatisch aan een lagere definitie aan wanneer het netwerk overbelast raakt. Dit vermindert de kans dat de kijker een "onderbroken" video ziet terwijl de speler de volgende seconden downloadt om af te spelen (dat wil zeggen, het vermindert de kans op buffering). Als netwerk congestie vermindert, zal de videospeler op zijn beurt terug te keren naar een hogere kwaliteit stream. Dit vermogen om de vereiste bandbreedte aan te passen resulteert ook in een snellere starttijd voor video. Dat betekent dat de eerste paar seconden kunnen worden afgespeeld in een snel te downloaden segment van lagere kwaliteit en vervolgens naar een hogere kwaliteit kunnen gaan zodra voldoende inhoud is gebufferd.

Dash.js is een open-source MPEG-DASH videospeler geschreven in JavaScript. Het doel is om een robuuste, cross-platform speler die vrij kan worden hergebruikt in toepassingen die video afspelen vereisen. Het biedt MPEG-DASH afspelen in elke browser die de W3C Media Source Extensions (MSE) ondersteunt, vandaag dat is Chrome, Microsoft Edge, en IE11 (andere browsers hebben aangegeven hun intentie om MSE te ondersteunen). Voor meer informatie over DASH.js, js zie de GitHub dash.js repository.

## <a name="creating-a-browser-based-streaming-video-player"></a>Een browsergebaseerde streaming videospeler maken
Als u een eenvoudige webpagina wilt maken die een videospeler met de verwachte besturingselementen zoals afspelen, pauzeren, terugspoelen enz.

1. Een HTML-pagina maken
2. De videotag toevoegen
3. Voeg de dash.js-speler toe
4. Initialiseren van de speler
5. Enkele CSS-stijl toevoegen
6. Bekijk de resultaten in een browser die MSE implementeert

Het initialiseren van de speler kan worden voltooid in slechts een handvol regels JavaScript-code. Met behulp van dash.js is het echt zo eenvoudig om MPEG-DASH-video in te sluiten in uw browsergebaseerde toepassingen.

## <a name="creating-the-html-page"></a>De HTML-pagina maken
De eerste stap is het maken van **video** een standaard HTML-pagina met het video-element, sla dit bestand op als basicPlayer.html, zoals het volgende voorbeeld illustreert:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>De DASH.js-speler toevoegen
Als u de dash.js-referentieimplementatie aan de toepassing wilt toevoegen, moet u het dash.all.js-bestand uit de nieuwste versie van dash.js-project halen. Dit moet worden opgeslagen in de JavaScript-map van uw toepassing. Dit bestand is een gemaksbestand dat alle benodigde dash.js-code samenbrengt in één bestand. Als je een kijkje neemt in de dash.js repository, vind je de individuele bestanden, testcode en nog veel meer, maar als je alleen dash.js wilt gebruiken, dan is het dash.all.js-bestand wat je nodig hebt.

Als u de dash.js-speler aan uw toepassingen wilt toevoegen, voegt u een scripttag toe aan het hoofdgedeelte van basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Maak vervolgens een functie om de speler te initialiseren wanneer de pagina wordt geladen. Voeg het volgende script toe na de regel waarin u dash.all.js laadt:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Met deze functie wordt eerst een DashContext gemaakt. Dit wordt gebruikt om de toepassing voor een specifieke runtime-omgeving te configureren. Vanuit een technisch oogpunt definieert het de klassen die het framework voor afhankelijkheidsinjectie moet gebruiken bij het samenstellen van de toepassing. In de meeste gevallen gebruikt u Dash.di.DashContext.

Vervolgens u de primaire klasse van het dash.js-framework, MediaPlayer, instantiëren. Deze klasse bevat de kernmethoden die nodig zijn, zoals afspelen en pauzeren, beheert de relatie met het video-element en beheert ook de interpretatie van het MPD-bestand (Media Presentation Description), waarin de video wordt beschreven die moet worden afgespeeld.

De functie opstarten() van de Klasse MediaPlayer wordt aangeroepen om ervoor te zorgen dat de speler klaar is om video af te spelen. De functie zorgt er onder andere voor dat alle benodigde klassen (zoals gedefinieerd door de context) zijn geladen. Zodra de speler klaar is, u het video-element eraan koppelen met behulp van de functie attachView(). De opstartfunctie stelt de MediaPlayer in staat om de videostream in het element te injecteren en ook het afspelen te regelen als dat nodig is.

Geef de URL van het MPD-bestand door aan de MediaPlayer, zodat het op de hoogte is van de video die naar verwachting wordt afgespeeld. De zojuist gemaakte functie setupVideo() moet worden uitgevoerd zodra de pagina volledig is geladen. Doe dit door de onload gebeurtenis van het lichaamselement te gebruiken. Wijzig `<body>` uw element in:

```html
    <body onload="setupVideo()">
```

Stel ten slotte de grootte van het video-element in met CSS. In een adaptieve streamingomgeving is dit vooral belangrijk omdat de grootte van de video die wordt afgespeeld kan veranderen naarmate het afspelen zich aanpast aan veranderende netwerkomstandigheden. In deze eenvoudige demo gewoon dwingen de video-element te worden 80% van de beschikbare browser venster door het toevoegen van de volgende CSS aan het hoofd gedeelte van de pagina:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Een video afspelen
Als u een video wilt afspelen, wijst u uw browser op het bestand basicPlayback.html en klikt u op afspelen op de weergegeven videospeler.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook

[GitHub dash.js repository](https://github.com/Dash-Industry-Forum/dash.js) 

