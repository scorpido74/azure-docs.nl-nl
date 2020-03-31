---
title: Word een on-premises encoderpartner - Azure Media Services
description: In dit artikel wordt besproken hoe u uw on-premises live streaming encoders verifiëren.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f98d9942f8c30f06b0144503b056c1e8a393ae52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298628"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Hoe u uw on-premises live streaming encoder verifiëren

Als on-premises encoderpartner van Azure Media Services promoot Media Services uw product door uw encoder aan zakelijke klanten aan te bevelen. Om een on-premises encoderpartner te worden, moet u de compatibiliteit van uw on-premises encoder met Media Services verifiëren. Voer hiervoor de volgende verificaties uit.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Verificatie van live-evenementen doorgeven

1. Controleer in uw Media Services-account of het **streamingeindpunt** wordt uitgevoerd. 
2. Maak en start het **pass-through** Live Event. <br/> Zie [Live Event states and billing](live-event-states-billing.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.
3. Haal de inname URL's op en configureer uw on-premises encoder om de URL te gebruiken om een multibitrate live stream naar Media Services te sturen.
4. Download de preview-URL en gebruik deze om te controleren of de invoer van de encoder daadwerkelijk wordt ontvangen.
5. Een nieuw **object Asset** maken.
6. Maak een **live-uitvoer** en gebruik de assetnaam die u hebt gemaakt.
7. Maak een **streaminglocator** met de ingebouwde **typen streamingbeleid.**
8. Vermeld de paden in de **streaminglocator** om de URL's terug te krijgen die u wilt gebruiken.
9. Download de hostnaam voor het **streamingeindpunt** waarvan u wilt streamen.
10. Combineer de URL van stap 8 met de hostnaam in stap 9 om de volledige URL te krijgen.
11. Voer uw levende encoder voor ongeveer 10 minuten.
12. Stop de livegebeurtenis. 
13. Gebruik een speler zoals [Azure Media Player](https://aka.ms/azuremediaplayer) om het gearchiveerde item te bekijken om ervoor te zorgen dat afspelen geen zichtbare glitches heeft op alle kwaliteitsniveaus. Of bekijk en valideer via de preview-URL tijdens de live sessie.
14. Neem de asset-ID, de gepubliceerde streaming-URL voor het live-archief en de instellingen en versie op die worden gebruikt vanaf uw live-encoder.
15. De status Live Event opnieuw instellen nadat u elk voorbeeld hebt gemaakt.
16. Herhaal stap 5 tot en met 15 voor alle configuraties die worden ondersteund door uw encoder (met en zonder advertentiesignalering, bijschriften of verschillende coderingssnelheden).

## <a name="live-encoding-live-event-verification"></a>Verificatie live codering live-evenement

1. Controleer in uw Media Services-account of het **streamingeindpunt** wordt uitgevoerd. 
2. Maak en start het **live coderingslive-evenement.** <br/> Zie [Live Event states and billing](live-event-states-billing.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.
3. Haal de inname URL's en configureer uw encoder om een single-bitrate live stream naar Media Services te pushen.
4. Download de preview-URL en gebruik deze om te controleren of de invoer van de encoder daadwerkelijk wordt ontvangen.
5. Een nieuw **object Asset** maken.
6. Maak een **live-uitvoer** en gebruik de assetnaam die u hebt gemaakt.
7. Maak een **streaminglocator** met de ingebouwde **typen streamingbeleid.**
8. Vermeld de paden in de **streaminglocator** om de URL's terug te krijgen die u wilt gebruiken.
9. Download de hostnaam voor het **streamingeindpunt** waarvan u wilt streamen.
10. Combineer de URL van stap 8 met de hostnaam in stap 9 om de volledige URL te krijgen.
11. Voer uw levende encoder voor ongeveer 10 minuten.
12. Stop de livegebeurtenis.
13. Gebruik een speler zoals [Azure Media Player](https://aka.ms/azuremediaplayer) om het gearchiveerde item te bekijken om ervoor te zorgen dat afspelen geen zichtbare glitches heeft voor alle kwaliteitsniveaus. Of bekijk en valideer via de preview-URL tijdens de live sessie.
14. Neem de asset-ID, de gepubliceerde streaming-URL voor het live-archief en de instellingen en versie op die worden gebruikt vanaf uw live-encoder.
15. De status Live Event opnieuw instellen nadat u elk voorbeeld hebt gemaakt.
16. Herhaal stap 5 tot en met 15 voor alle configuraties die worden ondersteund door uw encoder (met en zonder advertentiesignalering, bijschriften of verschillende coderingssnelheden).

## <a name="longevity-verification"></a>Langverificatie

Volg dezelfde stappen als bij [verificatie van live-evenementen,](#pass-through-live-event-verification) behalve in stap 11. <br/>In plaats van 10 minuten, voer je live encoder een week of langer uit. Gebruik een speler zoals [Azure Media Player](https://aka.ms/azuremediaplayer) om de live streaming van tijd tot tijd (of een gearchiveerde asset) te bekijken om ervoor te zorgen dat afspelen geen zichtbare glitches heeft.

## <a name="email-your-recorded-settings"></a>Uw opgenomen instellingen e-mailen

Ten slotte u uw opgenomen instellingen en amshelp@microsoft.com live archiefparameters naar Azure Media Services e-mailen als een melding dat alle zelfverificatiecontroles zijn geslaagd. Vermeld ook uw contactgegevens voor eventuele follow-ups. U contact opnemen met het Azure Media Services-team met vragen over dit proces.

## <a name="see-also"></a>Zie ook

[Geteste on-premises encoders](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Volgende stappen

[Live streaming met Media Services v3](live-streaming-overview.md)
