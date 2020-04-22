---
title: Lijst met Azure Media Player-functies
description: Een functieverwijzing voor Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727227"
---
# <a name="feature-list"></a>Lijst met functies #
Hier is de lijst met geteste functies en niet-ondersteunde functies:

|                                         | Getest | GEDEELTELIJK GETEST | Geteste | Unsupported | OPMERKINGEN                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Afspelen                                |        |                  |          |             |                                                                                                                      |
| Basisweergave op aanvraag                | X      |                  |          |             | Ondersteunt alleen streams vanuit Azure Media Services                                                                      |
| Basisweergave live                     | X      |                  |          |             | Ondersteunt alleen streams vanuit Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Ondersteunt Azure Media Services Key Delivery Service                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Ondersteunt Azure Media Services Key Delivery Service                                                                   |
| Widevine                                |        | X                |          |             | Ondersteunt Widevine PSSH-boxen die in manifest zijn beschreven                                                                    |
| FairPlay                                |        | X                |          |             | Ondersteunt Azure Media Services Key Delivery Service                                                                   |
| Techs                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash Fallback (FlashSS)                | X      |                  |          |             | Niet alle functies zijn beschikbaar op deze technologie.                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | Niet alle functies zijn beschikbaar op deze technologie.                                                                         |
| Native HLS-doorgeef (Html5)         |        | X                |          |             | Niet alle functies zijn beschikbaar op deze technologie als gevolg van platform beperkingen.                                            |
| Functies                                |        |                  |          |             |                                                                                                                      |
| API-ondersteuning                             | X      |                  |          |             | Lijst met bekende problemen bekijken                                                                                                |
| Basisgebruikersinterface                                | X      |                  |          |                                                                                                                                    |
| Initialisatie via JavaScript       | X      |                  |          |             |                                                                                                                      |
| Initialisatie via videotag        |        | X                |          |             |                                                                                                                      |
| Segmentadressering - Time Based         | X      |                  |          |             |                                                                                                                      |
| Segmentadressering - Indexgebaseerd        |        |                  |          | X           |                                                                                                                      |
| Segmentadressering - Byte Based         |        |                  |          | X           |                                                                                                                      |
| Rewriter van Azure Media Services-URL       |        | X                |          |             |                                                                                                                      |
| Toegankelijkheid - Bijschriften en ondertiteling  |        | X                |          |             |  WebVTT ondersteund voor on demand, live CEA 708 gedeeltelijk getest                                                       |
| Toegankelijkheid - Sneltoetsen                 | X      |                  |          |             |                                                                                                                      |
| Toegankelijkheid - Hoog contrast           |        | X                |          |             |                                                                                                                      |
| Toegankelijkheid - Tab Focus               |        | X                |          |             |                                                                                                                      |
| Foutberichten                         |        | X                |          |             | Foutberichten zijn inconsistent tussen technici                                                                         |
| Gebeurtenistriggering                        | X      |                  |          |             |                                                                                                                      |
| Diagnostiek                             |        | X                |          |             | Diagnostische informatie is alleen beschikbaar op de AzureHtml5JS-technologie en gedeeltelijk beschikbaar op de SilverlightSS-technologie. |
| Aanpasbare technische bestelling                 |        | X                |          |             |                                                                                                                      |
| Heuristiek - Basic                      | X      |                  |          |             |                                                                                                                      |
| Heuristiek - Maatwerk              |        |                  | X        |             | Maatwerk is alleen beschikbaar met de AzureHtml5JS-technologie.                                                          |
| Discontinuïteiten                         | X      |                  |          |             |                                                                                                                      |
| Bitrate selecteren                          | X      |                  |          |             | Deze API is alleen beschikbaar op de AzureHtml5JS- en FlashSS-technici.                                                    |
| Multi-audiostream                      |        | X                |          |             | Programmatic audioswitch wordt ondersteund op AzureHtml5JS- en FlashSS-technici en is beschikbaar via UI-selectie op AzureHtml5JS, FlashSS en native Html5 (in Safari).  De meeste platforms vereisen dezelfde codec privégegevens om audiostreams te schakelen (dezelfde codec, kanaal, samplingrate, enz.). |
| UI-lokalisatie                         |        | X                |          |             |                                                                                                                      |
| Afspelen met meerdere instance's                 |        |                  |          | X           | Dit scenario kan werken voor sommige techs, maar is momenteel niet ondersteund en niet getest. U dit ook aan het werk krijgen met iFrames |
| Ondersteuning voor advertenties                             |        | x                |          |             | AMP ondersteunt het inbrengen van pre-mid- en post-roll lineaire advertenties van VAST-compatibele advertentieservers voor VOD in de AzureHtml5JS-technologie |
| Analyse                               |        | X                |          |             | AMP biedt de mogelijkheid om naar analyses en diagnostische gebeurtenissen te luisteren om naar een Analytics-backend naar keuze te verzenden.  Alle evenementen en eigenschappen zijn niet beschikbaar in alle techs vanwege platformbeperkingen.                                                                            |
| Aangepaste skins                            |        |                  | X        |             | Zet besturingselementen in stelfout in AMP en gebruik uw eigen HTML en CSS.           |
| Zoek Bar Schrobben                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Alleen audio                              |        |                  |          | X           | Kan werken in sommige techs voor Adaptive Streaming, maar wordt momenteel niet ondersteund en werkt niet in AzureHtml5JS. Progressieve MP3-weergave kan werken met de HTML5-technologie als het platform dit ondersteunt.                                                                                                        |
| Alleen video                              |        |                  |          | X           | Kan werken in sommige techs voor Adaptive Streaming, maar wordt momenteel niet ondersteund en werkt niet in AzureHtml5JS.      |
| Presentatie voor meerdere perioden               |        |                  |          | X                                                                                                                                  |
| Meerdere camerahoeken                  |        |                  |          | X           |                                                                                                                      |
| Afspeelsnelheid                          |        | X                |          |             | De afspeelsnelheid wordt in de meeste scenario's ondersteund, behalve in de mobiele behuizing vanwege een gedeeltelijke bug in Chrome                 |

## <a name="next-steps"></a>Volgende stappen ##
- [Snel start azure mediaspeler](azure-media-player-quickstart.md)