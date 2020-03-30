---
title: Gebruik time-shifting en Live-uitgangen om on-demand videoweergave te maken
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u tijdverschuivende en live-uitvoer gebruiken om live streams op te nemen en on-demand weergave te maken.
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
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899798"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Gebruik time-shifting en Live-uitgangen om on-demand videoweergave te maken

In Azure Media Services is een object [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) als een digitale videorecorder die uw live stream opneemt en opneemt in een asset in uw Media Services-account. De opgenomen inhoud blijft bestaan in de container die is gedefinieerd door de [Asset-bron](https://docs.microsoft.com/rest/api/media/assets) (de container bevindt zich in het Azure Storage-account dat aan uw account is gekoppeld). Met de Live Output u ook bepaalde eigenschappen van de uitgaande livestream beheren, zoals hoeveel van de stream in de archiefopname wordt bewaard (bijvoorbeeld de capaciteit van de cloud-DVR) of wanneer kijkers de livestream kunnen gaan bekijken. Het archief op schijf is een cirkelvormig archief "venster" dat alleen de hoeveelheid inhoud bevat die is opgegeven in het **archiefWindowLength** eigenschap van de Live Output. Inhoud die buiten dit venster valt, wordt automatisch uit de opslagcontainer verwijderd en kan niet worden hersteld. De waarde archivewindowlengte vertegenwoordigt een ISO-8601-tijdsduur (bijvoorbeeld PTHH:MM:SS), die de capaciteit van de DVR aangeeft. De waarde kan worden ingesteld van minimaal drie minuten tot maximaal 25 uur.

De relatie tussen een Live Event en de Live Output is vergelijkbaar met de traditionele tv-uitzending, in die zin dat een kanaal (Live Event) een constante stroom van video vertegenwoordigt en een opname (Live Output) is scoped tot een specifiek tijdsegment (bijvoorbeeld avondnieuws van 18:30 tot 19:00 uur). Zodra je de stream naar het Live-evenement hebt gestreamd, kun je de streaming-gebeurtenis beginnen door een asset, Live Output en streaming locator te maken. Live Output archiveert de stream en maakt deze beschikbaar voor kijkers via het [Streaming Endpoint.](https://docs.microsoft.com/rest/api/media/streamingendpoints) U meerdere Live-uitgangen (maximaal drie) maken op een live-evenement met verschillende archieflengtes en -instellingen. Zie de [sectie Algemene stappen](live-streaming-overview.md#general-steps) voor informatie over de live streamingworkflow.

## <a name="using-a-dvr-during-an-event"></a>Een DVR gebruiken tijdens een evenement

In dit gedeelte wordt besproken hoe u een DVR gebruiken tijdens een evenement om te bepalen welke delen van de stream beschikbaar zijn voor 'terugspoelen'.

De `archiveWindowLength` waarde bepaalt hoe ver terug in de tijd een kijker kan gaan van de huidige live positie. De `archiveWindowLength` waarde bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Stel dat je een voetbalwedstrijd streamt `ArchiveWindowLength` en het heeft een van slechts 30 minuten. Een kijker die 45 minuten na het begin van de wedstrijd begint met het bekijken van je evenement, kan terugzoeken naar maximaal de 15 minuten. Je live-uitvoer voor het spel wordt voortgezet totdat het live-evenement is gestopt. Inhoud die buiten archiefvaltWindowLength wordt continu uit de opslag verwijderd en kan niet worden hersteld. In dit voorbeeld wordt de video tussen het begin van de gebeurtenis en het merk van 15 minuten verwijderd uit uw DVR en uit de container in blobopslag voor het actief. Het archief kan niet worden hersteld en wordt uit de container in Azure blob-opslag verwijderd.

Een Live Event ondersteunt maximaal drie gelijktijdig lopende Live Outputs (u maximaal 3 opnamen/archieven maken vanuit één livestream tegelijkertijd). Met deze ondersteuning u verschillende delen van een gebeurtenis publiceren en archiveren als dat nodig is. Stel dat u een 24x7 live lineaire feed moet uitzenden en gedurende de dag "opnames" van de verschillende programma's moet maken om klanten aan te bieden als on-demand content voor catch-up viewing. Voor dit scenario maakt u eerst een primaire live-uitvoer met een kort archiefvenster van 1 uur of minder, dit is de primaire livestream waarop uw kijkers zouden afstemmen. U maakt een Streaming Locator voor deze Live Output en publiceert deze naar uw app of website als de 'Live'-feed. Terwijl de Live Event wordt uitgevoerd, u programmatisch een tweede gelijktijdige Live Output maken aan het begin van een programma (of 5 minuten te vroeg om een aantal handvatten te geven om later bij te snijden). Deze tweede Live Output kan 5 minuten na het einde van het programma worden verwijderd. Met deze tweede asset u een nieuwe Streaming Locator maken om dit programma te publiceren als een on-demand asset in de catalogus van uw app. U dit proces meerdere keren herhalen voor andere programmagrenzen of hoogtepunten die u wilt delen als on-demand video's, terwijl de Live-feed van de eerste Live Output de lineaire feed blijft uitzenden.

## <a name="creating-an-archive-for-on-demand-playback"></a>Een archief maken voor on-demand afspelen

Het item waarop de Live-uitvoer wordt gearchiveerd, wordt automatisch een on-demand asset wanneer de Live-uitvoer wordt verwijderd. Je moet alle Live-uitgangen verwijderen voordat een live-evenement kan worden gestopt. U een optionele vlag [verwijderenUitvoergebruikengebruikenom](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) live uitvoer automatisch te verwijderen bij de stop.

Zelfs nadat u de gebeurtenis hebt gestopt en verwijderd, kunnen gebruikers uw gearchiveerde inhoud als video on-demand streamen, zolang u het item niet verwijdert. Een item mag niet worden verwijderd als het wordt gebruikt door een gebeurtenis; de gebeurtenis moet eerst worden verwijderd.

Als je de asset van je Live Output hebt gepubliceerd met behulp van een streaming locator, blijft het Live Event (tot de lengte van het DVR-venster) zichtbaar totdat de streaming locator afloopt of verwijderd is, wat het eerst komt.

Zie voor meer informatie:

- [Overzicht van live streaming](live-streaming-overview.md)
- [Zelfstudie voor live streaming](stream-live-tutorial-with-api.md)

> [!NOTE]
> Wanneer u de live-uitvoer verwijdert, verwijdert u de onderliggende waarde en inhoud in het item niet.

## <a name="next-steps"></a>Volgende stappen

* [Subclip je video's](subclip-video-rest-howto.md).
* [Definieer filters voor uw assets.](filters-dynamic-manifest-rest-howto.md)
