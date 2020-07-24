---
title: Azure Media Player functie lijst
description: Een functie verwijzing voor Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 41e090b9e1d4c091bd3972afd296c5751e6b8c58
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082712"
---
# <a name="feature-list"></a>Functielijst #
Hier volgt een lijst met geteste functies en niet-ondersteunde functies:

| Functie | ONDERZOEKEN | GEDEELTELIJK GETEST | Niet getest | NIET-ondersteunde | OPMERKINGEN |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| Afspelen                                |        |                  |          |             |                                                                                                                      |
| Eenvoudig afspelen op aanvraag                | X      |                  |          |             | Ondersteunt alleen stromen van Azure Media Services                                                                      |
| Eenvoudig afspelen van Live                     | X      |                  |          |             | Ondersteunt alleen stromen van Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Ondersteunt Azure Media Services key delivery service                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Ondersteunt Azure Media Services key delivery service                                                                   |
| Widevine                                |        | X                |          |             | Ondersteunt Widevine PSSH-vakken die worden beschreven in het manifest                                                                    |
| FairPlay                                |        | X                |          |             | Ondersteunt Azure Media Services key delivery service                                                                   |
| Techers                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Uitvallende Flash (knipperend)                | X      |                  |          |             | Niet alle functies zijn beschikbaar op deze technische mede werker.                                                                         |
| Silverlight fallback Silverlight      | X      |                  |          |             | Niet alle functies zijn beschikbaar op deze technische mede werker.                                                                         |
| Systeem eigen HLS Pass-Through (Html5)         |        | X                |          |             | Niet alle functies zijn beschikbaar voor deze technische technologie vanwege platform beperkingen.                                            |
| Functies                                |        |                  |          |             |                                                                                                                      |
| API-ondersteuning                             | X      |                  |          |             | Zie de lijst met bekende problemen                                                                                                |
| Basis GEBRUIKERSINTERFACE                                | X      |                  |          |                                                                                                                                    |
| Initialisatie via Java script       | X      |                  |          |             |                                                                                                                      |
| Initialisatie via video code        |        | X                |          |             |                                                                                                                      |
| Segment adresseren-tijd op basis         | X      |                  |          |             |                                                                                                                      |
| Segment adressering-op index gebaseerd        |        |                  |          | X           |                                                                                                                      |
| Segment adresseren-op basis van een byte         |        |                  |          | X           |                                                                                                                      |
| Azure Media Services URL ReWriter       |        | X                |          |             |                                                                                                                      |
| Toegankelijkheid-bijschriften en ondertitels  |        | X                |          |             |  WebVTT ondersteund voor on-demand, Live CEA 708 gedeeltelijk getest                                                       |
| Toegankelijkheid-sneltoetsen                 | X      |                  |          |             |                                                                                                                      |
| Toegankelijkheid-hoog contrast           |        | X                |          |             |                                                                                                                      |
| Toegankelijkheid: tabblad focus               |        | X                |          |             |                                                                                                                      |
| Fout berichten                         |        | X                |          |             | Fout berichten zijn inconsistent voor techs                                                                         |
| Gebeurtenis activering                        | X      |                  |          |             |                                                                                                                      |
| Diagnostiek                             |        | X                |          |             | Diagnostische gegevens zijn alleen beschikbaar voor de AzureHtml5JS tech en zijn deels beschikbaar op Silverlight. |
| Aanpas bare technische volg orde                 |        | X                |          |             |                                                                                                                      |
| Heuristiek-basis                      | X      |                  |          |             |                                                                                                                      |
| Heuristiek-aanpassing              |        |                  | X        |             | Aanpassing is alleen beschikbaar in de AzureHtml5JS-techniek.                                                          |
| Beëindigingen                         | X      |                  |          |             |                                                                                                                      |
| Bitrate selecteren                          | X      |                  |          |             | Deze API is alleen beschikbaar in de AzureHtml5JS-en flits techniek.                                                    |
| Multi-audio-stream                      |        | X                |          |             | De programmatische audio-switch wordt ondersteund op AzureHtml5JS en in de vorm van technologische hulp, en is beschikbaar via de gebruikers interface selectie op AzureHtml5JS, in de vorm van een beflitsing en systeem eigen Html5 (in Safari).  Voor de meeste platforms zijn dezelfde gegevens van de codec vereist voor het wisselen van audio stromen (dezelfde codec, kanaal, sampling frequentie, enzovoort). |
| Lokalisatie van gebruikers interface                         |        | X                |          |             |                                                                                                                      |
| Meerdere instanties afspelen                 |        |                  |          | X           | Dit scenario kan voor sommige technici worden gebruikt, maar wordt momenteel niet ondersteund en is niet getest. U kunt dit ook doen met behulp van iframes |
| ADS-ondersteuning                             |        | x                |          |             | AMP biedt ondersteuning voor het invoegen van vooraf-mid-en post-roll lineaire advertenties van voor VOD in de AzureHtml5JS Tech |
| Analyse                               |        | X                |          |             | AMP biedt de mogelijkheid om te Luis teren naar analyses en diagnostische gebeurtenissen om te verzenden naar een analyse back-end van uw keuze.  Alle gebeurtenissen en eigenschappen zijn niet beschikbaar in technici vanwege beperkingen van het platform.                                                                            |
| Aangepaste skins                            |        |                  | X        |             | Stel besturings elementen in op ONWAAR in AMP en gebruik uw eigen HTML en CSS.           |
| Zoek balk reinigen                      |        |                  |          | X           |                                                                                                                      |
| Slagen-Play                              |        |                  |          | X           |                                                                                                                      |
| Alleen audio                              |        |                  |          | X           | Kan in sommige techs werken voor adaptieve streaming, maar wordt momenteel niet ondersteund en werkt niet in AzureHtml5JS. Progressief MP3-afspelen kan samen werken met de HTML5-tech als dit door het platform wordt ondersteund.                                                                                                        |
| Alleen video                              |        |                  |          | X           | Kan in sommige techs werken voor adaptieve streaming, maar wordt momenteel niet ondersteund en werkt niet in AzureHtml5JS.      |
| Presentaties met meerdere Peri Oden               |        |                  |          | X                                                                                                                                  |
| Meerdere camera hoeken                  |        |                  |          | X           |                                                                                                                      |
| Afspeelsnelheid                          |        | X                |          |             | De afspeel snelheid wordt in de meeste scenario's ondersteund, met uitzonde ring van de mobiele situatie als gevolg van een gedeeltelijke bug in Chrome                 |

## <a name="next-steps"></a>Volgende stappen ##
- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)