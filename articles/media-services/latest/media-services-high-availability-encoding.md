---
title: Azure Media Services-code ring met hoge Beschik baarheid
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
ms.openlocfilehash: f5b02376111a3deba33cd5688330018bd7c370d8
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899215"
---
# <a name="media-services-high-availability-encoding"></a>Media Services-code ring met hoge Beschik baarheid 

Azure Media Services encoding service is een regionaal batch verwerkings platform en is momenteel niet ontworpen voor hoge Beschik baarheid binnen één regio. De coderings service biedt momenteel geen directe failover van de service als er sprake is van een onderbreking van de regionale Data Center of het mislukken van een onderliggend onderdeel of afhankelijke services (zoals Storage, SQL). In dit artikel wordt uitgelegd hoe u Media Services implementeert om een architectuur met hoge Beschik baarheid met failover te onderhouden en optimale Beschik baarheid voor uw toepassingen te garanderen.

Door de richt lijnen en best practices te volgen die in het artikel worden beschreven, verlaagt u het risico op storingen in de code ring, vertragingen en minimale herstel tijd als er een storing optreedt in één regio.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Een cross-regionaal coderings systeem bouwen

* [Maak](create-account-cli-how-to.md) twee (of meer) Azure Media Services-accounts.

    De twee accounts moeten zich in verschillende regio's behoeven. Zie [regio's waarin de Azure Media Services-service is geïmplementeerd](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)voor meer informatie.
* Upload uw media naar de regio van waaruit u de taak wilt indienen. Zie [een taak invoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md) of [een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md)voor meer informatie over het starten van code ring.

    Als u de [taak](transforms-jobs-concept.md) vervolgens opnieuw naar een andere regio wilt verzenden, kunt u JobInputHttp gebruiken of [kopiëren-BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) gebruiken om de gegevens van de bron-Asset container te kopiëren naar een Asset-container in de alternatieve regio.
* Abonneer u op JobStateChange-berichten in elk account via Azure Event Grid. Ga voor meer informatie naar:

    * [Audio Analytics](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) -voor beeld waarin wordt uitgelegd hoe u een taak bewaakt met Azure Event grid, inclusief het toevoegen van een terugval als de Azure Event grid berichten om een of andere reden worden vertraagd.
    * [Azure Event Grid schema's voor Media Services gebeurtenissen](media-services-event-schemas.md)
    * [Registreer u voor gebeurtenissen via de Azure portal of cli](reacting-to-media-services-events.md) (u kunt dit ook doen met de SDK voor EventGrid Management)
    * [Micro soft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (waarmee Media Services-gebeurtenissen systeem eigen worden ondersteund).

    U kunt ook Event Grid gebeurtenissen gebruiken via Azure Functions.
*    Wanneer u een [taak](transforms-jobs-concept.md)maakt:
    
    * Selecteer een wille keurig account in de lijst met momenteel gebruikte accounts (deze lijst bevat normaal gesp roken beide accounts, maar als er problemen worden gedetecteerd, mag het slechts één account bevatten). Als de lijst leeg is, moet u een waarschuwing genereren zodat een operator kan onderzoeken.
    * Algemene richt lijnen hebt u één [gereserveerde media-eenheid](media-reserved-units-cli-how-to.md) per [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) nodig (tenzij u [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) gebruikt waarbij 3 gereserveerde media-eenheden per JobOutput worden aanbevolen).
    * Het aantal gereserveerde media-eenheden (MRUs) voor het gekozen account ophalen. Als de huidige aantal **gereserveerde media-eenheden** niet al op de maximum waarde is, voegt u het aantal MRUs toe dat nodig is voor de taak en werkt u de service bij. Als de verzend snelheid van uw taak hoog is en u regel matig een query uitvoert op de MRUs om u het maximum te krijgen, gebruikt u een gedistribueerde cache voor de waarde met een redelijke time-out.
    * Behoud het aantal invlucht taken.

* Wanneer uw JobStateChange-handler een melding ontvangt dat de geplande status van een taak is bereikt, noteert u de tijd die de plannings status en de regio/het gebruikte account invult.
* Als uw JobStateChange-handler een melding krijgt dat een taak de verwerkings status heeft bereikt, markeert u de record voor de taak als verwerking.
* Als uw JobStateChange-handler een melding krijgt dat een taak de status Gereedgemeld/fout/geannuleerd heeft bereikt, markeert u de record voor de taak als definitief en verlaagt u het aantal invlucht taken. Het aantal gereserveerde media-eenheden voor het gekozen account ophalen en het huidige MRU-nummer vergelijken met het aantal taken in de vlucht. Als uw invlucht aantal minder is dan het aantal MRU-items, verlaagt u het en werkt u de service bij.
* Een afzonderlijk proces hebben dat regel matig naar uw records van de taken zoekt
    
    * Als u taken in de geplande status hebt die binnen een redelijke hoeveelheid tijd voor een bepaalde regio nog niet in de verwerkings status zijn gevorderd, verwijdert u die regio uit de lijst met momenteel gebruikte accounts.  Afhankelijk van uw bedrijfs vereisten kunt u ervoor kiezen om deze taken direct te annuleren en ze opnieuw naar de andere regio te verzenden. Of u kunt ze nog een paar tijd geven om over te gaan naar de volgende status.
    * Afhankelijk van het aantal gereserveerde media-eenheden dat is geconfigureerd voor het account en het verzend percentage, kunnen er ook taken in de wachtrij staat dat het systeem nog niet is opgehaald voor verwerking.  Als de lijst met taken in de status in de wachtrij groter wordt dan een acceptabele limiet in een regio, kunnen die taken worden geannuleerd en verzonden naar de andere regio.  Dit kan echter een symptoom zijn van onvoldoende gereserveerde media-eenheden die zijn geconfigureerd voor het account voor de huidige belasting.  U kunt zo nodig een grotere quota voor gereserveerde media-eenheden aanvragen via ondersteuning voor Azure.
    * Als een regio uit de lijst met accounts is verwijderd, controleert u deze voor herstel voordat u deze weer aan de lijst toevoegt.  De regionale status kan worden bewaakt met behulp van de bestaande taken in de regio (als deze niet zijn geannuleerd en opnieuw worden ingediend) door het account na een bepaalde periode opnieuw aan de lijst toe te voegen en door Opera tors die Azure-communicatie controleren over storingen die van invloed kunnen zijn op Azure Media Services.
    
Als u merkt dat het aantal MRU-items overbelasting omhoog en omlaag is, verplaatst u de regel voor het verlagen van de logica naar de periodieke taak. Laat voor de logica voor het verzenden van de voor bereiding voor de taak een aantal invluchten vergelijken met het huidige MRU-aantal om te zien of het MRUs moet worden bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

* [Video-on-demand streaming voor meerdere regio's bouwen](media-services-high-availability-streaming.md)
* [Code voorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-media-services) bekijken
