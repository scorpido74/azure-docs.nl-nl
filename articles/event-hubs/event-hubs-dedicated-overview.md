---
title: Overzicht van gespecialiseerde Event hubs-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een overzicht van toegewezen Azure-Event Hubs, dat implementaties met één Tenant van Event hubs biedt.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 20b153c9093c96e7357a8e439b6655f1db80bd46
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516989"
---
# <a name="overview-of-event-hubs-dedicated"></a>Overzicht van Event Hubs Dedicated

*Event hubs-clusters* bieden implementaties met één Tenant voor klanten met de meest veeleisende streaming behoeften. Deze aanbieding met één Tenant heeft een gegarandeerde 99,99%-SLA en is alleen beschikbaar in onze specifieke prijs categorie. Een Event Hubs-cluster kan miljoenen gebeurtenissen per seconde opnemen met gegarandeerde capaciteit en latentie van minder dan een seconde. Naam ruimten en Event hubs die zijn gemaakt in het toegewezen cluster, bevatten alle functies van de Standard-aanbieding en meer, maar zonder ingangs limieten. Het bevat ook de populaire [Event hubs Capture](event-hubs-capture-overview.md) -functie zonder extra kosten, zodat u automatisch gegevens stromen naar Azure Storage of Azure data Lake kunt vastleggen. 

Clusters worden ingericht en gefactureerd op basis van **capaciteits eenheden (CUs)**, een vooraf toegewezen hoeveelheid CPU-en geheugen bronnen. U kunt voor elk cluster 1, 2, 4, 8, 12, 16 of 20 capaciteitseenheden kopen. Hoeveel u kunt opnemen en streamen per CU, is afhankelijk van verschillende factoren, zoals het aantal producenten en consumenten, de shape nettolading, het uitgangs bedrag (Zie de onderstaande benchmark resultaten voor meer informatie). 

> [!NOTE]
> Alle Event Hubs clusters Kafka standaard ingeschakeld en ondersteunen Kafka-eind punten die kunnen worden gebruikt door uw bestaande op Kafka gebaseerde toepassingen. Als Kafka in uw cluster is ingeschakeld, heeft dit geen invloed op uw gebruik van niet-Kafka. Er is geen optie of het is niet nodig om Kafka uit te scha kelen op een cluster.

## <a name="why-dedicated"></a>Wat is er specifiek?

Dedicated Event Hubs biedt drie aantrekkelijke voor delen voor klanten die behoefte hebben aan capaciteit op bedrijfs niveau:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>De capaciteit van één pacht garandeert voor betere prestaties

Een toegewezen cluster garandeert de capaciteit van de volledige schaal en kan Maxi maal gigabytes aan streaminggegevens met volledig duurzame opslag en een subseconde van het netwerk binnenhalen om alle bursts in het verkeer te kunnen verwerken. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inclusief en exclusieve toegang tot functies 
De speciale aanbieding bevat functies zoals vastleggen zonder extra kosten en exclusieve toegang tot aanstaande functies als Bring Your Own Key (BYOK). De service beheert ook taak verdeling, updates van het besturings systeem, beveiligings patches en partitioneren voor de klant, zodat u minder tijd kunt best Eden aan het onderhoud van de infra structuur en meer tijd hebt bij het bouwen van client-side-functies.  

#### <a name="cost-savings"></a>Kosten besparingen
Bij hoge ingangs volumes (>100 doorvoer eenheden), kost een cluster aanzienlijk minder per uur dan het kopen van een vergelijkbaar aantal doorvoer eenheden in de standaard aanbieding.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Quota en limieten Event Hubs Dedicated

De Event Hubs Dedicated-aanbieding wordt gefactureerd tegen een vaste maandelijkse prijs, met een minimum van 4 uur gebruik. De Dedicated-laag biedt alle functies van het Standard-abonnement, maar met schaalcapaciteit voor grote bedrijven en limieten voor klanten met veeleisende werkbelastingen. 

| Functie | Standard | Toegewezen |
| --- |:---:|:---:|
| Bandbreedte | 20 TUs (Maxi maal 40 TUs) | 20 CU's |
| Naamruimten |  1 | 50 per CU |
| Event Hubs |  10 per naam ruimte | 1000 per naamruimte |
| Ingangsgebeurtenissen | Betalen per miljoen gebeurtenissen | Inbegrepen |
| Berichtgrootte | 1.000.000 bytes | 1.000.000 bytes |
| Partities | 32 per Event hub | 1024 per Event hub |
| Consumentengroepen | 20 per Event hub | Geen limiet per CU, 1000 per event hub |
| Brokered verbindingen | 1.000 inbegrepen, 5.000 Maxi maal | 100 K inbegrepen en Max. |
| Bewaarperiode van berichten | 7 dagen, 84 GB inbegrepen per di | 90 dagen, 10 TB inbegrepen per CU |
| Vastleggen | Betalen per uur | Inbegrepen |

## <a name="how-to-onboard"></a>Onboarding

De eigen ervaring voor het [maken van een event hubs cluster](event-hubs-dedicated-cluster-create-portal.md) via de [Azure Portal](https://aka.ms/eventhubsclusterquickstart) is nu beschikbaar als preview-versie. Als u vragen hebt of hulp nodig hebt bij het voorbereiden op Event Hubs Dedicated, neemt u contact op met het [Event hubs team](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Veelgestelde vragen

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw micro soft-verkoop vertegenwoordiger of Microsoft Ondersteuning voor meer informatie over Event Hubs Dedicated. U kunt ook een cluster maken of meer informatie over Event Hubs prijs categorieën door de volgende koppelingen te bezoeken:

- [Een Event Hubs cluster maken via de Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event hubs dedicated prijzen](https://azure.microsoft.com/pricing/details/event-hubs/). U kunt ook contact opnemen met uw micro soft-verkoop medewerker of Microsoft Ondersteuning om meer informatie over Event Hubs Dedicated capaciteit te krijgen.
- De [Veelgestelde](event-hubs-faq.md) vragen over de Event hubs bevatten prijs informatie en antwoorden op veelgestelde vragen over Event hubs.
