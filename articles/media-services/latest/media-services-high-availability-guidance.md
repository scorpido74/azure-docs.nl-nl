---
title: Azure Media Services hoge Beschik baarheid
description: Meer informatie over hoe u een failover naar een secundaire Media Services account kunt doen als er sprake is van een storing in een regionaal Data Center.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202284"
---
# <a name="azure-media-services-high-availability-guidance"></a>Richt lijnen voor Azure Media Services met hoge Beschik baarheid

Azure Media Services encoding service is een regionaal batch verwerkings platform en is momenteel niet ontworpen voor hoge Beschik baarheid binnen één regio. De coderings service biedt momenteel geen directe failover van de service als er sprake is van een onderbreking van de regionale Data Center of het mislukken van een onderliggend onderdeel of afhankelijke services (zoals Storage, SQL, enz.)  In dit artikel wordt uitgelegd hoe u Media Services implementeert om een architectuur met hoge Beschik baarheid met failover te onderhouden en optimale Beschik baarheid voor uw toepassingen te garanderen. 

Door de richt lijnen en best practices te volgen die in het artikel worden beschreven, verlaagt u het risico op storingen in de code ring, vertragingen en minimale herstel tijd als er een storing optreedt in één regio.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Een cross-regionaal coderings systeem bouwen

* [Maak](create-account-cli-how-to.md) twee (of meer) Azure Media Services-accounts.
* Abonneer u op **JobStateChange** -berichten in elk account.

    * In Media Services V3 wordt dit gedaan via Azure Event Grid. Ga voor meer informatie naar:
    
       * [Event grid-voor beelden](../../event-grid/receive-events.md), 
       * [Azure Event grid schema's voor Media Services gebeurtenissen](media-services-event-schemas.md), 
       * [Registreer u voor gebeurtenissen via de Azure portal of cli](reacting-to-media-services-events.md) (u kunt dit ook doen met de SDK voor EventGrid Management)
       * [Micro soft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (waarmee Media Services-gebeurtenissen systeem eigen worden ondersteund). 
       
        U kunt ook Event Grid gebeurtenissen gebruiken via Azure Functions.
    * In Media Services v2 wordt dit gedaan via [NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md).
* Wanneer u [een taak maakt](transforms-jobs-concept.md):

    * Selecteer een wille keurig account in de lijst met momenteel gebruikte accounts (deze lijst bevat normaal gesp roken beide accounts, maar als er problemen worden gedetecteerd, mag het slechts één account bevatten). Als de lijst leeg is, moet u een waarschuwing genereren zodat een operator kan onderzoeken.
    * Algemene richt lijnen zijn een [gereserveerde media-eenheid](media-reserved-units-cli-how-to.md) per taak of [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) nodig (tenzij u [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) in V3 gebruikt).
    * Het aantal [gereserveerde media-eenheden](media-reserved-units-cli-how-to.md) (MRUs) voor het gekozen account ophalen. Als de huidige aantal **gereserveerde media-eenheden** niet al op de maximum waarde is, voegt u het aantal MRUs toe dat nodig is voor de taak en werkt u de service bij. Als de verzend snelheid van uw taak hoog is en u regel matig een query uitvoert op de MRUs om u het maximum te krijgen, gebruikt u een gedistribueerde cache voor de waarde met een redelijke time-out.
    * Behoud het aantal invlucht taken.
* Wanneer uw JobStateChange-handler een melding ontvangt dat de geplande status van een taak is bereikt, noteert u de tijd die de plannings status en de regio/het gebruikte account invult.    
* Als uw JobStateChange-handler een melding krijgt dat een taak de verwerkings status heeft bereikt, markeert u de record voor de taak als verwerking.
* Als uw JobStateChange-handler een melding krijgt dat een taak de status Gereedgemeld/fout/geannuleerd heeft bereikt, markeert u de record voor de taak als definitief en verlaagt u het aantal invlucht taken. Het aantal gereserveerde media-eenheden voor het gekozen account ophalen en het huidige MRU-nummer vergelijken met het aantal taken in de vlucht. Als uw invlucht aantal minder is dan het aantal MRU-items, verlaagt u het en werkt u de service bij.
* Een afzonderlijk proces hebben dat regel matig naar uw records van de taken zoekt. Als u taken in de geplande status hebt die binnen een redelijke hoeveelheid tijd voor een bepaalde regio nog niet in de verwerkings status zijn gevorderd, verwijdert u die regio uit de lijst met momenteel gebruikte accounts.

    * Afhankelijk van uw bedrijfs vereisten kunt u ervoor kiezen om deze taken direct te annuleren en deze opnieuw te verzenden naar het andere account. Of u kunt ze nog een paar tijd geven om over te gaan naar de volgende status.   
    * Na een bepaalde periode kunt u het account weer toevoegen aan de lijst die momenteel wordt gebruikt (met de veronderstelling dat de regio is hersteld).
    
Als u merkt dat het aantal MRU-items overbelasting omhoog en omlaag is, verplaatst u de regel voor het verlagen van de logica naar de periodieke taak. Laat voor de logica voor het verzenden van de voor bereiding voor de taak een aantal invluchten vergelijken met het huidige MRU-aantal om te zien of het MRUs moet worden bijgewerkt.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Video-on-demand streaming voor meerdere regio's bouwen 

* Video-on-demand streaming voor meerdere regio's omvat het dupliceren van [assets](assets-concept.md), [beleids regels voor inhouds sleutels](content-key-policy-concept.md) (indien gebruikt), [streaming-beleid](streaming-policy-concept.md)en [streaming-Locators](streaming-locators-concept.md). 
* U moet de beleids regels in beide regio's maken en deze up-to-date houden. 
* Wanneer u de streaming-Locators maakt, wilt u dezelfde Locator-id-waarde, ContentKey-id-waarde en ContentKey-waarde gebruiken.  
* Als u de inhoud codeert, wordt het aanbevolen om de inhoud in regio A te coderen en te publiceren, vervolgens de versleutelde inhoud naar regio B te kopiëren en deze te publiceren met dezelfde waarden als van regio A.
* U kunt Traffic Manager gebruiken voor de hostnamen voor de oorsprong en de key delivery-service (in Media Services configuratie dit lijkt op een aangepaste URL voor de sleutel Server).

## <a name="next-steps"></a>Volgende stappen

* [Een account maken](create-account-cli-how-to.md)
* [Code voorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-media-services) bekijken
