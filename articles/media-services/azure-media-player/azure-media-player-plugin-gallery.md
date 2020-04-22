---
title: Azure Media Player-galerie voor plug-ins
description: Dit artikel bevat een lijst met plug-ins die beschikbaar zijn voor Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 206200f3ba0757cd738439b58c8b94874cf5a938
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726478"
---
# <a name="azure-media-player-plugin-gallery"></a>Azure Media Player-galerie voor plug-ins #

## <a name="plugins"></a>Invoegtoepassingen ##

| Naam van plug-in                         | Demo-URL                    | Broncode                | Beschrijving    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Extra functies                 | | | |
| **Nieuw!** AMP360Video                | [Demo](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Met de plug-in kun je 360-video's visualiseren in Amp op je bureaublad of in VR-compatibele apparaten. De volledige documentatie is [hier](https://doc\.babylonjs\.com/extensions/amp360video)beschikbaar: |
|  Sprite-tip                         | [Demo](http://www.skymedia.tv/asset/sprite)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Amp-plug-in Azure Media Player (AMP) voor het renderen van tijdlijnweergave van een sprite van een videominiatuurafbeelding die wordt gegenereerd uit Azure Media Services (AMS) Media Encoder Standard (MES). |
| Diagnostische overlay                 | [Demo](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Deze plug-in wordt weergegeven: alle belangrijke parameters, statistieken van video, alle gebeurtenissen in de levenscyclus van videoafspelen en DRM-beveiligingsgegevens, zoals key ID, URL's voor licentieverwerving, indien beveiligd.                                                                                                                                                                      |
| Rekenmachine framesnelheid en tijdcode | [Demo](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Deze plugin berekent de framesnelheid `tfhd` / `trun` van video op basis van de MP4-vakken van het eerste MPEG-DASH-videofragment, ontleedt de tijdschaalwaarde van het MPEG-DASH-clientmanifest en biedt ook een manier om de tijdcode voor een bepaalde absolute tijd van de speler te genereren (en biedt de speler absolute tijd gegeven door de tijdcode) |
| <strike>Afspeelsnelheid</strike>                      | [Demo](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Deze plugin stelt kijkers in staat om te bepalen welke snelheid van de video. *Let op, deze functionaliteit is automatisch beschikbaar in versie AMP v2.0.0+ maar standaard uitgeschakeld.* Voor meer informatie over hoe het in te schakelen, bekijk onze monsters [hier](https://github.com/Azure-Samples/azure-media-player-samples) |
| Hover TijdTip                      | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Hiermee geeft u een tijdstip weer over de voortgangsbalk op muiszweven voor nauwkeurig zoeken in de tijd. *Opmerking: Deze plugin is al geïntegreerd in AMP,* maar als je geïnteresseerd bent in het zien hoe het is geprogrammeerd voel je vrij om een kijkje te nemen.                                                                                                                       |
| Titeloverlay                       | [Demo](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/")                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Overlays configureerbare videotitel boven scherm |
| Tijdlijnmarkeringen                    | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Deze plugin neemt in een scala van tijden en overlays kleine markers over de voortgangsbalk op die momenten. |
| Analyse                           | | | |
| Application Insights                | [Blogbericht](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Plugin die je spelersstatistieken bijhoudt en deze naar Power BI poort voor een intuïtieve grafische weergave van de spelerervaring van je kijkers. |
| Google Analytics                    | N.v.t.                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Google Analytics-plug-in voor Azure Media Player |
| Diagnostiek                         | | | |
| Diagnostische uitvoer                  | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Deze plugin maakt een reeks diagnoses van uw speler, om deze in actie te zien ga naar de demokoppeling en opent u uw JavaScript-console. |
| Toegankelijkheid                      | | | |
| Inzoomen                             | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Deze plug-in geeft een inzoomschaal met drag-able weer op het spelersscherm, zodat kijkers kunnen inzoomen op uw inhoud |
| Live bijschriften                       | [Azure-blogbericht](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[subply-bericht](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | N.v.t. | *Zie bericht voor meer info.* End-to-end workflow ontworpen voor live captioning built plugin voor Azure Media Player, klik op de meest linkse lin om naar de site van SubPly te gaan en meer te weten te komen over de oplossing |
| Sneltoetsen                            | <strike>[Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | De hot keys plugin stelt kijkers in staat om verschillende aspecten van de speler te bedienen met generieke plugin combinaties zoals F voor fullscreen, M voor mute en pijltoetsen voor progress bar control. *Opmerking: Deze plugin is al geïntegreerd in AMP, maar voel je vrij om het te gebruiken als een bron* |
| Sociaal                              | | | |
| Delen                               | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Deze plug-in voegt een deelknop toe aan de bedieningsbalk van de speler, zodat je kijkers de video die ze bekijken met hun vrienden kunnen delen via Facebook, Twitter of Linkedin. |

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)