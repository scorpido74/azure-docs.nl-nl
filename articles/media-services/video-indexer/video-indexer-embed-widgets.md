---
title: Video Indexer widgets insluiten in uw toepassingen
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u Azure Media Services Video Indexer widgets insluit in uw toepassing.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: b9fb15fc9f3dc51a0df40a4ccb738a97d4558dff
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545888"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Video Indexer widgets insluiten in uw toepassingen

In dit artikel wordt uitgelegd hoe u Video Indexer widgets kunt insluiten in uw toepassingen. Video Indexer ondersteunt het insluiten van drie typen objecten in uw toepassing: *cognitieve inzichten*, *speler*en *Redacteur*. 

Vanaf versie 2 bevat de basis-URL van de widget de regio van het opgegeven account. Een account in de regio vs-West genereert bijvoorbeeld het volgende: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Typen widget

### <a name="cognitive-insights-widget"></a>Widget Inzichten

Een cognitieve Insights-widget bevat alle visuele inzichten die zijn geëxtraheerd uit het indexerings proces van uw video. De cognitieve Insights-widget ondersteunt de volgende optionele URL-para meters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`widgets`|Tekenreeksen gescheiden door komma's|Hiermee kunt u de inzichten bepalen die u wilt weer geven. <br/> Voor beeld: met `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` worden alleen de gebruikers interface-inzichten van gebruikers en Brands weer gegeven.<br/>Beschikbare opties: mensen, trefwoorden, aantekeningen, merken, gevoelens, transcript, zoeken.<br/>Houd er rekening mee dat de para meter `widgets` URL niet wordt ondersteund in versie 2.<br/>|
|`locale`|Een korte taal code|Hiermee bepaalt u de inzichten taal. De standaardwaarde is `en`. <br/> Voorbeeld: `locale=de`.|
|`tab`|Het standaard geselecteerde tabblad|Hiermee bepaalt u het tabblad **inzichten** dat standaard wordt weer gegeven. <br/> Voor beeld: `tab=timeline` de inzichten weer geven op het tabblad **tijd lijn** geselecteerd.|

### <a name="player-widget"></a>Widget Speler

U kunt de Player-widget gebruiken om video te streamen met behulp van adaptieve bitsnelheid. De Player-widget ondersteunt de volgende optionele URL-para meters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`t`|Seconden vanaf het begin|Hiermee wordt het afspelen van de speler vanaf het opgegeven tijd punt gestart.<br/> Voorbeeld: `t=60`.|
|`captions`|Een taal code|Haalt het bijschrift in de opgegeven taal op tijdens het laden van de widget zodat deze beschikbaar is in het menu **bijschriften** .<br/> Voorbeeld: `captions=en-US`.|
|`showCaptions`|Een Booleaanse waarde|Zorgt ervoor dat speler wordt geladen met de ondertiteling al ingeschakeld.<br/> Voorbeeld: `showCaptions=true`.|
|`type`||Hiermee wordt een audio speler-weer gave geactiveerd (het video onderdeel wordt verwijderd).<br/> Voorbeeld: `type=audio`.|
|`autoplay`|Een Booleaanse waarde|Hiermee wordt aangegeven of de speler de video moet starten wanneer deze wordt geladen. De standaardwaarde is `true`.<br/> Voorbeeld: `autoplay=false`.|
|`language`|Een taal code|Hiermee bepaalt u de taal van de speler. De standaardwaarde is `en-US`.<br/>Voorbeeld: `language=de-DE`.|

### <a name="editor-widget"></a>Editor-widget

U kunt de editor-widget gebruiken om nieuwe projecten te maken en de inzichten van een video te beheren. De editor-widget ondersteunt de volgende optionele URL-para meters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`accessToken`<sup>*</sup>|Tekenreeks|Biedt toegang tot Video's die alleen voor komen in het account dat wordt gebruikt om de widget in te sluiten.<br> Voor de editor-widget is de para meter `accessToken` vereist.|
|`language`|Een taal code|Hiermee bepaalt u de taal van de speler. De standaardwaarde is `en-US`.<br/>Voorbeeld: `language=de-DE`.|
|`locale`|Een korte taal code|Hiermee bepaalt u de inzichten taal. De standaardwaarde is `en`.<br/>Voorbeeld: `language=de`.|

<sup>*</sup> De eigenaar moet er `accessToken` voorzichtig van kunnen zijn.

## <a name="embedding-public-content"></a>Openbare inhoud insluiten

1. Meld u aan bij de [video indexer](https://www.videoindexer.ai/) -website.
2. Selecteer de video waarmee u wilt werken.
3. Selecteer de knop **insluiten** die wordt weer gegeven onder de video.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Nadat u de knop **insluiten** hebt geselecteerd, kunt u de widget selecteren die u wilt insluiten in uw toepassing. 
4. Selecteer het gewenste type widget (**cognitieve inzichten**, **speler**of **Redacteur**).
 
5. Kopieer de insluit code en voeg deze toe aan uw toepassing. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Als u problemen hebt met het delen van uw video-Url's, voegt u de para meter `location` toe aan de koppeling. De para meter moet worden ingesteld op de [Azure-regio's waarin video indexer bestaat](regions.md). Bijvoorbeeld: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Persoonlijke inhoud insluiten

Als u een privé video wilt insluiten, moet u een toegangs token in het **src** -kenmerk van het iframe door geven:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Als u de inhoud van het cognitieve Insights-object wilt ophalen, gebruikt u een van de volgende opties:<br/>
- De API voor het [verkrijgen van inzichten-widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- Het [token voor video toegang ophalen](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?). Voeg het als een query parameter toe aan de URL. Geef deze URL op als de **src** -waarde voor de iframe, zoals eerder is weer gegeven.

Als u de mogelijkheden voor het bewerken van inzichten in uw Inge sloten object wilt bieden, moet u een toegangs token door geven dat bewerkings machtigingen bevat. Gebruik de widget voor het [verkrijgen van inzichten](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) of [Haal token voor video toegang](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) op met `&allowEdit=true`. 

## <a name="widgets-interaction"></a>Interactie van widgets

De widget cognitieve Insights kan communiceren met een video in uw toepassing. In deze sectie wordt beschreven hoe u deze interactie bereikt.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Communicatie van verschillende bronnen

Om Video Indexer widgets te laten communiceren met andere onderdelen, de Video Indexer-service:

- Maakt gebruik van de cross-Origin Communication HTML5-methode **postMessage**. 
- Het bericht wordt gevalideerd op basis van de bron VideoIndexer.ai. 

Als u uw eigen Player-code implementeert en integreert met cognitieve Insights-widgets, is het uw verantwoordelijkheid om de oorsprong van het bericht te valideren dat afkomstig is van VideoIndexer.ai.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Widgets insluiten in uw toepassing of blog (aanbevolen) 

In deze sectie wordt uitgelegd hoe u de interactie tussen twee Video Indexer widgets kunt beheersen, zodat wanneer een gebruiker het Insight-besturings element selecteert in uw toepassing, de speler naar het relevante moment springt.

1. De insluit code van de Player-widget kopiëren.
2. Kopieer de code van de cognitieve Insights-invoeg toepassing.
3. Voeg het bewerkings [bestand](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) toe om de communicatie tussen de twee widgets te verwerken:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Wanneer een gebruiker nu het besturings element voor inzicht selecteert in uw toepassing, springt de speler naar het gewenste moment.

Zie voor meer informatie de [video indexer-beide widgets-demo insluiten](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>De widget Inzichten insluiten en Azure Media Player gebruiken om de inhoud af te spelen

In deze sectie wordt uitgelegd hoe u de interactie tussen een cognitieve Insights-widget en een Azure Media Player-exemplaar kunt verkrijgen met behulp van de [amp-invoeg toepassing](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Voeg een Video Indexer-invoeg toepassing toe voor de AMP-speler:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Exemplaar Azure Media Player met de Video Indexer-invoeg toepassing.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Kopieer de code van de cognitieve Insights-invoeg toepassing.

U moet nu kunnen communiceren met Azure Media Player.

Zie de [demo over Azure Media Player en VI](https://codepen.io/videoindexer/pen/rYONrO)voor meer informatie.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>De Video Indexer cognitieve Insights-widget insluiten en een andere video speler gebruiken

Als u een andere video speler gebruikt dan Azure Media Player, moet u de video speler hand matig bewerken om de communicatie te verzorgen. 

1. Voeg uw videospeler in.

    Bijvoorbeeld een standaard HTML5-speler:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Sluit de widget Inzichten in.
3. Implementeer communicatie voor de speler door te luisteren naar de gebeurtenis 'message'. Bijvoorbeeld:

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Zie de [demo over Azure Media Player en VI](https://codepen.io/videoindexer/pen/YEyPLd)voor meer informatie.

## <a name="adding-subtitles"></a>Ondertiteling toevoegen

Als u Video Indexer Insights insluit met uw eigen [Azure Media Player](https://aka.ms/azuremediaplayer), kunt u de methode **GetVttUrl** gebruiken om ondertiteling (ondertiteling) te verkrijgen. U kunt ook een Java script-methode aanroepen vanuit de Video Indexer AMP-invoeg toepassing **getSubtitlesUrl** (zoals eerder weer gegeven). 

## <a name="customizing-embeddable-widgets"></a>Insluitbare widgets aanpassen

### <a name="cognitive-insights-widget"></a>Widget Inzichten

U kunt kiezen welke typen inzichten u wilt. Als u dit wilt doen, geeft u ze op als een waarde voor de volgende URL-para meter die wordt toegevoegd aan de Inge sloten code die u ontvangt (van de API of van de webtoepassing): `&widgets=<list of wanted widgets>`.

De mogelijke waarden zijn: **personen**, **tref woorden**, **gevoel**, **Transcripten**en **zoeken**.

Als u bijvoorbeeld een widget wilt insluiten die alleen personen en zoek inzichten bevat, ziet de iframe insluiten URL er als volgt uit:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

De titel van het iframe-venster kan ook worden aangepast door `&title=<YourTitle>` aan de iframe-URL op te geven. (De HTML-\<Titel > waarde wordt aangepast).
    
Als u bijvoorbeeld het iframe-venster de titel ' MyInsights ' wilt geven, ziet de URL er als volgt uit:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

U ziet dat deze optie alleen relevant is voor gevallen waarbij u de inzichten in een nieuw venster moet openen.

### <a name="player-widget"></a>Widget Speler

Bij het insluiten van een Video Indexer-speler kunt u de grootte van de speler kiezen door de grootte van de iframe op te geven.

Bijvoorbeeld:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Video Indexer Player heeft standaard ondertiteling gegenereerd die is gebaseerd op de transcriptie van de video. De transcriptie wordt geëxtraheerd uit de video met de bron taal die is geselecteerd toen de video werd geüpload.

Als u wilt insluiten met een andere taal, kunt u `&captions=< Language | "all" | "false" >` toevoegen aan de Inge sloten URL van de speler. Als u bijschriften in alle beschik bare bijschriften van talen wilt, gebruikt u de waarde `all`. Als u wilt dat de bijschriften standaard worden weer gegeven, kunt u `&showCaptions=true`door geven.

De Inge sloten URL ziet er dan als volgt uit: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Als u bijschriften wilt uitschakelen, kunt u de waarde van de `captions` parameter als `false`door geven.

#### <a name="autoplay"></a>Zorgen
Standaard wordt de video afgespeeld met de speler. u kunt ervoor kiezen om `&autoplay=false` niet door te geven aan de voor gaande Inge sloten URL.

## <a name="next-steps"></a>Volgende stappen

Zie [video indexer Insights weer geven en bewerken](video-indexer-view-edit.md)voor meer informatie over het weer geven en bewerken van video indexer inzichten.

Bekijk ook de [video indexer-CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
