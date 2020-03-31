---
title: Video-indexerwidgets insluiten in uw apps
titleSuffix: Azure Media Services
description: Meer informatie over het insluiten van video-indexerwidgets in uw apps.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: e475c1bc1878c6b5a0efbbe41f2a3a0fe86bcff2
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389372"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Video-indexerwidgets insluiten in uw apps

In dit artikel ziet u hoe u video-indexerwidgets in uw apps insluiten. Video Indexer ondersteunt het insluiten van drie soorten widgets in uw apps: *Cognitive Insights*, *Player*en *Editor*.

Vanaf versie 2 bevat de URL van de widgetbasis de regio van het opgegeven account. Een account in de regio West-VS genereert bijvoorbeeld: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Typen widget

### <a name="cognitive-insights-widget"></a>Widget Inzichten

De widget Inzichten bevat alle visuele inzichten die tijdens het indexeringsproces zijn opgehaald uit uw video. De widget Cognitieve inzichten ondersteunt de volgende optionele URL-parameters:

|Name|Definitie|Beschrijving|
|---|---|---|
|`widgets` | Tekenreeksen gescheiden door komma's | Hiermee u de inzichten beheren die u wilt renderen.<br/>Voorbeeld: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` hiermee worden alleen personen en zoekwoorden ui-inzichten weergegeven.<br/>Beschikbare opties: personen, geanimeerde tekens, trefwoorden, labels, gevoelens, emoties, onderwerpen, hoofdframes, transcript, ocr, luidsprekers, scènes en namedEntiteiten.|
|`controls`|Tekenreeksen gescheiden door komma's|Hiermee u de besturingselementen beheren die u wilt renderen.<br/>Voorbeeld: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` renders alleen zoekoptie en download knop.<br/>Beschikbare opties: zoeken, downloaden, presets, taal.|
|`language`|Een korte taalcode (taalnaam)|Hiermee bepaalt u de taal van inzichten.<br/>Voorbeeld: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>Of`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Een korte taalcode | Hiermee bepaalt u de taal van de gebruikersinterface. De standaardwaarde is `en`. <br/>Bijvoorbeeld: `locale=de`.|
|`tab` | Het standaard geselecteerde tabblad | Hiermee bepaalt u het tabblad **Insights** dat standaard wordt weergegeven. <br/>Voorbeeld: `tab=timeline` hiermee worden de inzichten weergegeven waarop het tabblad **Tijdlijn** is geselecteerd.|

### <a name="player-widget"></a>Widget Speler

Je de widget Player gebruiken om video te streamen met behulp van adaptieve bitsnelheid. De widget Player ondersteunt de volgende optionele URL-parameters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`t` | Seconden vanaf het begin | Hiermee begint de speler te spelen vanaf het opgegeven tijdspunt.<br/> Bijvoorbeeld: `t=60`. |
|`captions` | Een taalcode | Haalt het bijschrift in de opgegeven taal op tijdens het laden van de widget om beschikbaar te zijn in het menu **Bijschriften.**<br/> Bijvoorbeeld: `captions=en-US`. |
|`showCaptions` | Een Booleaanse waarde | Zorgt ervoor dat speler wordt geladen met de ondertiteling al ingeschakeld.<br/> Bijvoorbeeld: `showCaptions=true`. |
|`type`| | Hiermee activeert u een skin voor audiospelers (het videogedeelte wordt verwijderd).<br/> Bijvoorbeeld: `type=audio`. |
|`autoplay` | Een Booleaanse waarde | Geeft aan of de speler moet beginnen met het afspelen van de video wanneer deze is geladen. De standaardwaarde is `true`.<br/> Bijvoorbeeld: `autoplay=false`. |
|`language`/`locale` | Een taalcode | Hiermee bepaalt u de taal van de speler. De standaardwaarde is `en-US`.<br/>Bijvoorbeeld: `language=de-DE`.|

### <a name="editor-widget"></a>Object Editor

U de widget Editor gebruiken om nieuwe projecten te maken en de inzichten van een video te beheren. De widget Editor ondersteunt de volgende optionele URL-parameters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`accessToken`<sup>*</sup> | Tekenreeks | Biedt toegang tot video's die alleen in het account zitten dat wordt gebruikt om de widget in te sluiten.<br> De widget Editor `accessToken` vereist de parameter. |
|`language` | Een taalcode | Hiermee bepaalt u de taal van de speler. De standaardwaarde is `en-US`.<br/>Bijvoorbeeld: `language=de-DE`. |
|`locale` | Een korte taalcode | Hiermee bepaalt u de taal van de inzichten. De standaardwaarde is `en`.<br/>Bijvoorbeeld: `language=de`. |

<sup>*</sup>De eigenaar `accessToken` moet met de nodige voorzichtigheid zorgen.

## <a name="embedding-public-content"></a>Openbare inhoud insluiten

1. Meld u aan bij de website [van Video Indexer.](https://www.videoindexer.ai/)
2. Selecteer de video waarmee u wilt werken.
3. Selecteer de knop **Insluiten** die onder de video wordt weergegeven.

    ![Knop Insluiten in video-indexer](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Nadat u de knop **Insluiten** hebt geselecteerd, u de widget selecteren die u in uw app wilt insluiten.
4. Selecteer het gewenste type widget **(Cognitive Insights,** **Player**of **Editor).**
 
5. Kopieer de insluitcode en voeg deze toe aan uw app.

    ![Code insluiten voor app—Video-indexer](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Als u problemen hebt met het `location` delen van uw video-URL's, voegt u de parameter toe aan de koppeling. De parameter moet worden ingesteld op de [Azure-gebieden waarin VideoIndexer bestaat](regions.md). Bijvoorbeeld: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Persoonlijke inhoud insluiten

Als u een privévideo wilt insluiten, `src` moet u een toegangstoken doorgeven in het kenmerk van het iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Gebruik een van de volgende methoden om de inhoud van de widget Cognitive Insights te krijgen:

- De [Get Insights Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API.<br/>
- Het [token voor videotoegang krijgen](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Voeg deze toe als queryparameter aan de URL. Geef deze URL `src` op als de waarde voor het iframe, zoals eerder wordt weergegeven.

Als u bewerkingsinzichten wilt bieden in uw ingesloten widget, moet u een toegangstoken doorgeven dat bewerkingsmachtigingen bevat. Gebruik [Insights-widget opdoen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) of `&allowEdit=true` [Videotoegangstoken met](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) .

## <a name="widgets-interaction"></a>Interactie van widgets

De widget Cognitive Insights kan communiceren met een video in uw app. In deze sectie wordt beschreven hoe u deze interactie bereikt.

![Video-indexer voor cognitieve inzichten](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Communicatie van verschillende bronnen

Als u video-indexerwidgets wilt laten communiceren met andere componenten, gaat u als gevolg van de service Video Indexer:

- Gebruikt de HTML5-methode `postMessage`voor cross-origincommunicatie .
- Het bericht wordt gevalideerd op basis van de bron VideoIndexer.ai.

Als u uw eigen spelerscode implementeert en integreert met Cognitive Insights-widgets, is het uw verantwoordelijkheid om de oorsprong van het bericht dat afkomstig is van VideoIndexer.ai te valideren.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Widgets insluiten in uw app of blog (aanbevolen)

In deze sectie ziet u hoe u interactie bereiken tussen twee Video Indexer-widgets, zodat wanneer een gebruiker de inzichtscontrole op uw app selecteert, de speler naar het relevante moment springt.

1. Kopieer de invoegcode van de widget Speler.
2. Kopieer de invoegcode van de widget Inzichten.
3. Voeg het [Mediator-bestand](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) voor het afhandelen van de communicatie tussen de twee widgets toe:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Wanneer een gebruiker nu de inzichtscontrole op uw app selecteert, springt de speler naar het relevante moment.

Zie voor meer informatie de [video-indexer - Sluit beide widgets demo in.](https://codepen.io/videoindexer/pen/NzJeOb)

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>De widget Inzichten insluiten en Azure Media Player gebruiken om de inhoud af te spelen

In deze sectie ziet u hoe u interactie bereiken tussen een widget Cognitive Insights en een azure mediaplayer-instantie met behulp van de [AMP-plug-in](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Voeg een Video Indexer-plug-in toe voor de AMP-speler:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instantiate Azure Media Player met de Video Indexer plug-in.

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

3. Kopieer de invoegcode van de widget Inzichten.

U nu communiceren met Azure Media Player.

Zie de demo [Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO)voor meer informatie.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Sluit de widget Cognitieve inzichten van video-indexer in en gebruik een andere videospeler

Als u een andere videospeler dan Azure Media Player gebruikt, moet u de videospeler handmatig manipuleren om de communicatie te bereiken.

1. Voeg uw videospeler in.

    Een standaard HTML5-speler bijvoorbeeld:

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

Zie de demo [Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd)voor meer informatie.

## <a name="adding-subtitles"></a>Ondertiteling toevoegen

Als u videoindexer-inzichten insluit met uw eigen `GetVttUrl` Azure Media [Player,](https://aka.ms/azuremediaplayer)u de methode gebruiken om ondertiteling (ondertitels) te krijgen. U ook een JavaScript-methode aanroepen via `getSubtitlesUrl` de AMP-invoegtoepassing Video-indexer (zoals eerder wordt weergegeven).

## <a name="customizing-embeddable-widgets"></a>Insluitbare widgets aanpassen

### <a name="cognitive-insights-widget"></a>Widget Inzichten

U de gewenste inzichten kiezen. Geef hiervoor op als waarde voor de volgende URL-parameter die is toegevoegd aan de insluitcode die `&widgets=<list of wanted widgets>`u krijgt (uit de API of van de web-app): .

De mogelijke waarden `people` `animatedCharacters` zijn: `sentiments`, `emotions` `topics`, `keyframes` `transcript`, `ocr` `keywords` `labels` `speakers`, , , , , , , `scenes`, en `namedEntities`.

Als u bijvoorbeeld een widget wilt insluiten die alleen personen en trefwoordeninzichten bevat, ziet de IFrame-insluit-URL er als volgt uit:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

De titel van het iframe-venster `&title=<YourTitle>` kan ook worden aangepast door de iframe-URL te verstrekken. (Hiermee wordt de <title> HTML-waarde aangepast).
   
Als u bijvoorbeeld uw iframevenster de titel 'MyInsights' wilt geven, ziet de URL er als volgt uit:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

U ziet dat deze optie alleen relevant is voor gevallen waarbij u de inzichten in een nieuw venster moet openen.

### <a name="player-widget"></a>Widget Speler

Bij het insluiten van een Video Indexer-speler kunt u de grootte van de speler kiezen door de grootte van de iframe op te geven.

Bijvoorbeeld:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Standaard heeft Video Indexer-speler automatisch ondertiteling gegenereerd die zijn gebaseerd op het transcript van de video. Het transcript wordt uit de video gehaald met de brontaal die is geselecteerd toen de video werd geüpload.

Als u wilt insluiten met een andere taal, u &captions=< Language Code > toevoegen aan de URL van de insluitende speler. Als u wilt dat de bijschriften standaard worden weergegeven, u &showCaptions=true doorgeven.

De insluit-URL ziet er dan als volgt uit:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Automatisch afspelen

Standaard begint de speler de video af te spelen. u ervoor kiezen `&autoplay=false` om dit niet te doen door door te geven aan de vorige insluit-URL.

## <a name="code-samples"></a>Codevoorbeelden

Zie de [codemonsters](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) repo die monsters bevat voor Video Indexer API en Widgets:

| Bestand/map                       | Beschrijving                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Video-indexervideo laden in een aangepaste Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Sluit VI Insights in met een aangepaste Azure Media Player.                             |
| `control-vi-embedded-player`      | Sluit VI Player in en bedien hem van buitenaf.                                    |
| `custom-index-location`           | Vi Insights insluiten vanaf een aangepaste externe locatie (kan klant een blob zijn).     |
| `embed-both-insights`             | Basisgebruik van VI Insights zowel speler als inzichten.                            |
| `embed-insights-with-AMP`         | Sluit de VI Insights-widget in met een aangepaste Azure Media Player.                      |
| `customize-the-widgets`           | Vi-widgets insluiten met aangepaste opties.                                     |
| `embed-both-widgets`              | Integreer VI Player en Insights en communiceer tussen hen.                      |
| `url-generator`                   | Hiermee genereert u aangepaste URL's voor het insluiten van aangepaste insluiten op basis van door de gebruiker opgegeven opties.             |
| `html5-player`                    | Sluit VI Insights in met een standaard HTML5-videospeler.                           |

## <a name="next-steps"></a>Volgende stappen

Zie Inzichten van [video-indexeren weergeven en bewerken](video-indexer-view-edit.md)voor informatie over het bekijken en bewerken van inzichten in video-indexeren.

Bekijk ook [Video-indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
