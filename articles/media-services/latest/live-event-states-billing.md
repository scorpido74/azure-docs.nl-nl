---
title: LiveEvent-statussen en facturering in Azure Media Services | Microsoft Documenten
description: In dit onderwerp vindt u een overzicht van de statussen en facturering van Azure Media Services LiveEvent.
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
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543072"
---
# <a name="live-event-states-and-billing"></a>Statussen en facturering van live-evenementen

In Azure Media Services begint een Live-gebeurtenis met facturering zodra de status wordt overgezet naar **Actief**. U wordt gefactureerd, zelfs als er geen video stroomt door de dienst. Als je wilt voorkomen dat het live-evenement wordt facturering, moet je het live-evenement stoppen. Live Transcription wordt op dezelfde manier gefactureerd als het Live Event.

Wanneer **LiveEventEncodingType** op uw [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) is ingesteld op Standard of Premium1080p, schakelt Media Services automatisch een Live-gebeurtenis uit die nog steeds in de status **Actief** is 12 uur nadat de invoerfeed is verloren en er geen Live **Output**wordt uitgevoerd. U wordt echter nog steeds gefactureerd voor de tijd dat het live-evenement in de **status Actief** was.

> [!NOTE]
> Pass-through Live Events worden niet automatisch uitgeschakeld en moeten expliciet worden gestopt via de API om overmatige facturering te voorkomen. 

## <a name="states"></a>Staten

Het live-evenement kan zich in een van de volgende staten bevindt.

|Status|Beschrijving|
|---|---|
|**Gestopt**| Dit is de beginstatus van het Live Event na het maken (tenzij automatisch starten is ingesteld op true.) Er vindt geen facturering plaats in deze status. In deze status kunnen de eigenschappen van live-evenement worden bijgewerkt, maar is streamen niet toegestaan.|
|**Beginnen**| Het Live Event wordt gestart en er worden middelen toegewezen. Er vindt geen facturering plaats in deze status. Updates of streaming zijn niet toegestaan tijdens deze status. Als er een fout optreedt, keert de livegebeurtenis terug naar de status Gestopt.|
|**Wordt uitgevoerd**| De bronnen voor live-gebeurtenissen zijn toegewezen, de inname en de preview-URL's zijn gegenereerd en kunnen livestreams ontvangen. Op dit moment is facturering actief. U moet expliciet Stop aanroepen in de resource van de livegebeurtenis om verdere facturering stop te zetten.|
|**Stoppen**| Het live-evenement wordt gestopt en resources worden gede-provisioned. Er vindt geen facturering plaats in deze tijdelijke status. Updates of streaming zijn niet toegestaan tijdens deze status.|
|**Verwijderen**| Het live-evenement wordt verwijderd. Er vindt geen facturering plaats in deze tijdelijke status. Updates of streaming zijn niet toegestaan tijdens deze status.|

U ervoor kiezen om Live Transcripties in te schakelen wanneer u het Live-evenement maakt. Als u dit doet, wordt u gefactureerd voor Live Transcripties wanneer het Live-evenement zich in de **status Actief** bevindt. Houd er rekening mee dat er kosten in rekening worden gebracht, zelfs als er geen audio door het Live-evenement stroomt.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van live streaming](live-streaming-overview.md)
- [Zelfstudie voor live streaming](stream-live-tutorial-with-api.md)
