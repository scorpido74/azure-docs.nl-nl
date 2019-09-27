---
title: Azure Media Services time-verschuivingen en Live-to-VOD (video on demand) gebruiken | Microsoft Docs
description: In dit artikel wordt uitgelegd wat live output is en hoe u een Cloud-DVR gebruikt.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: ffcd279830cb49b64ddbb58a888ad7d653918b1b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338843"
---
# <a name="using-time-shifting-and-live-to-vod-video-on-demand"></a>Time-verschuivingen en Live-to-VOD (video on demand) gebruiken

In Azure Media Services is een [Live uitvoer](https://docs.microsoft.com/rest/api/media/liveoutputs) object net als een digitale video recorder waarmee uw live stream wordt onderschept en opgenomen in een asset in uw Media Services-account. De opgenomen inhoud wordt opgeslagen in de container die is gedefinieerd door de bron van de [Asset](https://docs.microsoft.com/rest/api/media/assets) (de container bevindt zich in het Azure Storage-account dat aan uw account is gekoppeld). Met de live uitvoer kunt u ook enkele eigenschappen van de uitgaande Live Stream beheren, zoals hoeveel van de stroom wordt bewaard in de registratie van het archief (bijvoorbeeld de capaciteit van de Cloud-DVR) en of viewers de live stream kunnen bekijken. Het archief op schijf is een circulair Archief ' venster ' dat alleen de hoeveelheid inhoud bevat die is opgegeven in de eigenschap **archiveWindowLength** van de actieve uitvoer. Inhoud die buiten dit venster valt, wordt automatisch verwijderd uit de opslag container en kan niet worden hersteld. De waarde archiveWindowLength vertegenwoordigt een time-outwaarde van ISO-8601 (bijvoorbeeld PTHH: MM: SS), waarmee de capaciteit van het DVR wordt opgegeven en kan worden ingesteld op een minimum van 3 minuten tot een maximum van 25 uur.

De relatie tussen een live gebeurtenis en de live uitvoer is vergelijkbaar met de traditionele televisie-uitzending, waarbij een kanaal (live event) een constante stream van video vertegenwoordigt en een opname (live uitvoer) is gericht op een specifiek tijds segment (bijvoorbeeld 's avonds). Nieuws van 6:17.30 tot 7:13:00). Zodra u de stroom naar de live gebeurtenis hebt gestroomd, kunt u de streaming-gebeurtenis starten door een Asset, live output en streaming-Locator te maken. Live uitvoer archiveert de stream en maakt deze beschikbaar voor gebruikers via het [streaming-eind punt](https://docs.microsoft.com/rest/api/media/streamingendpoints). U kunt meerdere Live outputs (Maxi maal drie) maken voor een live-gebeurtenis met verschillende archief lengten en-instellingen. Zie de sectie [algemene stappen](live-streaming-overview.md#general-steps) voor informatie over de werk stroom voor live streamen.

## <a name="using-a-dvr-during-an-event"></a>Een DVR gebruiken tijdens een gebeurtenis 

In deze sectie wordt beschreven hoe u een DVR tijdens een gebeurtenis kunt gebruiken om te bepalen welke gedeelten van de stroom beschikbaar zijn voor terugspoelen.

De waarde archiveWindowLength bepaalt hoe ver terug in de tijd die een kijker van de huidige Live positie kan aangaan. De waarde archiveWindowLength bepaalt ook hoe lang de client manifesten kunnen groeien.

Stel dat u een voetbal wedstrijd streamt en een ArchiveWindowLength heeft van slechts 30 minuten. Een viewer die begint met het volgen van uw gebeurtenis 45 minuten nadat het spel is gestart, kan naar Maxi maal 15 minuten teruggaan. Uw Live outputs voor het spel worden voortgezet totdat de live-gebeurtenis is gestopt, maar de inhoud die buiten het archiveWindowLength valt, wordt voortdurend verwijderd uit de opslag en kan niet worden hersteld. In dit voor beeld zou de video tussen het begin van de gebeurtenis en de markering van 15 minuten zijn verwijderd uit uw DVR en uit de container in Blob Storage voor de Asset. Het archief kan niet worden hersteld en wordt verwijderd uit de container in Azure Blob Storage.

Een live-gebeurtenis ondersteunt Maxi maal drie gelijktijdig actieve live-uitvoer (u kunt Maxi maal 3 opnamen/archieven tegelijk maken vanuit één live stream). Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Stel dat u een 24x7 live lineaire feed moet uitzenden en "opnamen" van de verschillende Program ma's gedurende de hele dag wilt maken om klanten als inhoud op aanvraag te bieden voor het weer geven van inzoomen. Voor dit scenario maakt u eerst een primaire live-uitvoer, met een kort archief venster van 1 uur of minder. Dit is de primaire Live Stream die uw viewers zouden afstemmen. U maakt een streaming-Locator voor deze live uitvoer en publiceert deze naar uw toepassing of website als een live-feed. Terwijl de live-gebeurtenis wordt uitgevoerd, kunt u programmatisch een tweede gelijktijdige live-uitvoer maken aan het begin van een programma (of 5 minuten om een aantal ingangen te verkorten). Deze tweede live-uitvoer kan 5 minuten na afloop van het programma worden verwijderd. Met deze tweede Asset kunt u een nieuwe streaming-Locator maken om dit programma te publiceren als een Asset op aanvraag in de catalogus van uw toepassing. U kunt dit proces meerdere keren herhalen voor andere programma grenzen of hooglichten die u wilt delen als Video's op aanvraag, allemaal terwijl de live-feed van de eerste live uitvoer de lineaire feed blijft uitzenden. 

## <a name="creating-an-archive-for-on-demand-playback"></a>Een archief maken voor het afspelen op aanvraag

De Asset waarnaar de live uitvoer wordt gearchiveerd, wordt automatisch een Asset op aanvraag wanneer de live uitvoer wordt verwijderd. U moet alle Live outputs verwijderen voordat een live gebeurtenis kan worden gestopt. U kunt een optionele vlag [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) gebruiken om live-uitvoer automatisch te verwijderen bij het stoppen. 

Zelfs nadat u de gebeurtenis hebt gestopt en verwijderd, kunnen de gebruikers de gearchiveerde inhoud als een video op aanvraag streamen, mits u de Asset niet verwijdert. Een activum mag niet worden verwijderd als het wordt gebruikt door een gebeurtenis; de gebeurtenis moet eerst worden verwijderd.

Als u het activum van uw live uitvoer hebt gepubliceerd met behulp van een streaming-Locator, blijft de live-gebeurtenis (tot de lengte van het DVR-venster) zichtbaar totdat het verloop of de verwijdering van de streaming-Locator, afhankelijk van wat het eerste komt.

Zie voor meer informatie:

- [Overzicht van live streamen](live-streaming-overview.md)
- [Zelf studie over live streamen](stream-live-tutorial-with-api.md)

> [!NOTE]
> Wanneer u de live uitvoer verwijdert, worden de onderliggende activa en inhoud in de Asset niet verwijderd. 

## <a name="next-steps"></a>Volgende stappen

* [Uw Video's subfragmenten](subclip-video-rest-howto.md).
* [Definieer filters voor uw activa](filters-dynamic-manifest-rest-howto.md).
