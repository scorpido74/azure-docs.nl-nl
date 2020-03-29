---
title: Codering met hoge beschikbaarheid van Azure Media Services
description: Meer informatie over het mislukken naar een secundair Media Services-account als er een regionale datacenterstoring of -storing optreedt.
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
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934191"
---
# <a name="media-services-high-availability-encoding"></a>Codering met hoge beschikbaarheid van Media Services 

Azure Media Services-coderingsservice is een regionaal batchverwerkingsplatform en momenteel niet ontworpen voor hoge beschikbaarheid binnen één regio. De coderingsservice biedt momenteel geen directe failover van de service als er een regionale datacenterstoring is of een fout van onderliggende component of afhankelijke services (zoals opslag, SQL). In dit artikel wordt uitgelegd hoe u Media Services implementeert om een architectuur met hoge beschikbaarheid met failover te behouden en een optimale beschikbaarheid voor uw toepassingen te garanderen.

Door de richtlijnen en aanbevolen procedures te volgen die in het artikel worden beschreven, verlaagt u het risico op coderingsfouten, vertragingen en minimaliseert u de hersteltijd als er een storing optreedt in één regio.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Hoe maak je een cross-regional codering systeem te bouwen

* [Maak](create-account-cli-how-to.md) twee (of meer) Azure Media Services-accounts.

    De twee rekeningen moeten in verschillende regio's zijn. Zie [regio's waarin de Azure Media Services-service is geïmplementeerd](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)voor meer informatie.
* Upload je media naar dezelfde regio waar je de vacature wilt indienen. Zie [Een taakinvoer maken van een HTTPS-URL](job-input-from-http-how-to.md) of [Een taakinvoer maken uit een lokaal bestand](job-input-from-local-file-how-to.md)voor meer informatie over het starten van codering.

    Als u de [taak](transforms-jobs-concept.md) vervolgens opnieuw naar een andere regio moet verzenden, u JobInputHttp gebruiken of [Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) gebruiken om de gegevens van de bronassetcontainer naar een assetcontainer in het alternatieve gebied te kopiëren.
* Abonneer u op JobStateChange-berichten in elk account via Azure Event Grid. Zie voor meer informatie:

    * [Audio Analytics-voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) dat laat zien hoe u een taak met Azure Event Grid controleren, inclusief het toevoegen van een terugval in het geval de Azure Event Grid-berichten om de een of andere reden worden vertraagd.
    * [Azure Event Grid-schema's voor gebeurtenissen in Media Services](media-services-event-schemas.md)
    * [Registreer voor gebeurtenissen via de Azure portal of de CLI](reacting-to-media-services-events.md) (u dit ook doen met de EventGrid Management SDK)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (dat gebeurtenissen in Media Services native ondersteunt).

    U ook gebeurtenisrastergebeurtenissen gebruiken via Azure-functies.
* Wanneer u een [taak maakt:](transforms-jobs-concept.md)

    * Selecteer willekeurig een account in de lijst met momenteel gebruikte accounts (deze lijst bevat normaal gesproken beide accounts, maar als er problemen worden gedetecteerd, kan dit slechts één account bevatten). Als de lijst leeg is, moet u een waarschuwing instellen zodat een operator dit kan onderzoeken.
    * Algemene richtlijnen is dat u één [mediagereserveerde eenheid](media-reserved-units-cli-how-to.md) per [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) nodig hebt (tenzij u [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) gebruikt waarbij 3 mediagereserveerde eenheden per JobOutput worden aanbevolen).
    * Ontvang het aantal gereserveerde mediaeenheden (MRU's) voor het gekozen account. Als het aantal huidige **mediagereserveerde eenheden** nog niet op de maximale waarde staat, voegt u het aantal MRU's toe dat de taak nodig heeft en werkt u de service bij. Als uw percentage vacatures hoog is en u vaak de MRU's opvraagt om te ontdekken dat u maximaal bent, gebruikt u een gedistribueerde cache voor de waarde met een redelijke time-out.
    * Houd het aantal inflight jobs bij.

* Wanneer uw JobStateChange-handler een melding ontvangt dat een taak de geplande status heeft bereikt, registreert u de tijd waarin de planningsstatus wordt weergegeven en de regio/account die wordt gebruikt.
* Wanneer uw JobStateChange-handler een melding ontvangt dat een taak de verwerkingsstatus heeft bereikt, markeert u de record voor de taak als verwerking.
* Wanneer uw JobStateChange-handler een melding ontvangt dat een taak de status Voltooid/Fout/Geannuleerd heeft bereikt, markeert u de record voor de taak als definitief en verschep het aantal inflight-taak. Haal het aantal gereserveerde media-eenheden voor het gekozen account op en vergelijk het huidige MRU-nummer met het aantal vacatures aan boord. Als uw aantal aan boord lager is dan de MRU-telling, verminder deze dan en werk u de service bij.
* Een apart proces hebben dat periodiek uw gegevens van de taken bekijkt
    
    * Als u taken in de geplande status hebt die niet binnen een redelijke tijd voor een bepaalde regio naar de verwerkingsstatus zijn gevorderd, verwijdert u dat gebied uit uw lijst met momenteel gebruikte accounts.  Afhankelijk van uw bedrijfsvereisten u besluiten deze taken meteen te annuleren en opnieuw in te dienen bij de andere regio. Of je ze wat meer tijd geven om naar de volgende staat te gaan.
    * Afhankelijk van het aantal media gereserveerde eenheden geconfigureerd op het account en de indiening tarief, kunnen er ook taken in de wachtrij staat het systeem nog niet is opgepikt voor verwerking.  Als de lijst met taken in de wachtrijstatus in een regio boven een aanvaardbare limiet komt, kunnen deze taken worden geannuleerd en naar de andere regio worden verzonden.  Dit kan echter een symptoom zijn dat er niet genoeg mediagereserveerde eenheden zijn geconfigureerd voor de account voor de huidige belasting.  U indien nodig een hoger mediagereserveerd eenheidsquotum aanvragen via Azure Support.
    * Als een regio uit de accountlijst is verwijderd, controleert u het voor herstel voordat u het weer aan de lijst toevoegt.  De regionale status kan worden gecontroleerd via de bestaande taken in de regio (als ze niet zijn geannuleerd en opnieuw zijn ingediend), door het account na een bepaalde periode weer aan de lijst toe te voegen en door operators die Azure-communicatie controleren over storingen die van invloed kunnen zijn Azure Media Services.
    
Als u merkt dat de MRU-telling veel op en neer gaat, verplaatst u de decrementlogica naar de periodieke taak. Laat de logica voor het indienen van de pre-job vergelijken met het aantal instroom en het huidige AANTAL MRU's om te zien of de MRU's moeten worden bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

* [Video-on-demand cross-region streaming bouwen](media-services-high-availability-streaming.md)
* Codevoorbeelden [code samples](https://docs.microsoft.com/samples/browse/?products=azure-media-services) bekijken
