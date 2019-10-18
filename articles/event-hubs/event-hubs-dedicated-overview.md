---
title: Overzicht van gespecialiseerde Event hubs-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een overzicht van toegewezen Azure-Event Hubs, dat implementaties met één Tenant van Event hubs biedt.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516743"
---
# <a name="overview-of-event-hubs-dedicated"></a>Overzicht van Event Hubs Dedicated

*Event hubs-clusters* bieden implementaties met één Tenant voor klanten met de meest veeleisende streaming behoeften. Dit aanbod met één tenant heeft een gegarandeerde SLA van 99,99 procent en is alleen beschikbaar in onze speciale prijscategorie. Een Event Hubs cluster kan miljoenen gebeurtenissen per seconde met gegarandeerde capaciteit en een subseconde van de seconden binnenkomen. Naam ruimten en Event hubs die zijn gemaakt in het toegewezen cluster, bevatten alle functies van de Standard-aanbieding en meer, maar zonder ingangs limieten. Het bevat ook de populaire [Event hubs Capture](event-hubs-capture-overview.md) -functie zonder extra kosten, zodat u automatisch gegevens stromen naar Azure Storage of Azure data Lake kunt vastleggen. 

Clusters worden ingericht en gefactureerd op basis van **capaciteits eenheden (CUs)** , een vooraf toegewezen hoeveelheid CPU-en geheugen bronnen. U kunt 1, 2, 4, 8, 12, 16 of 20 voor elk cluster aanschaffen. Hoeveel u kunt opnemen en streamen per CU, is afhankelijk van verschillende factoren, zoals het aantal producenten en consumenten, de shape nettolading, het uitgangs bedrag (Zie de onderstaande benchmark resultaten voor meer informatie). 

> [!NOTE]
> Alle Event Hubs clusters Kafka standaard ingeschakeld en ondersteunen Kafka-eind punten die kunnen worden gebruikt door uw bestaande op Kafka gebaseerde toepassingen. Als Kafka in uw cluster is ingeschakeld, heeft dit geen invloed op uw gebruik van niet-Kafka. Er is geen optie of het is niet nodig om Kafka uit te scha kelen op een cluster.

## <a name="why-dedicated"></a>Wat is er specifiek?

Dedicated Event Hubs biedt drie aantrekkelijke voor delen voor klanten die behoefte hebben aan capaciteit op bedrijfs niveau:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>De capaciteit van één pacht garandeert voor betere prestaties

Een toegewezen cluster garandeert voldoende capaciteit op de volledige schaal en kan Maxi maal gigabytes aan streaminggegevens met volledig duurzame opslag en een subseconde met een latentie van elke burst in verkeer. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inclusief en exclusieve toegang tot functies 
De speciale aanbieding bevat functies zoals vastleggen zonder extra kosten en exclusieve toegang tot aanstaande functies als Bring Your Own Key (BYOK). De service beheert ook taak verdeling, updates van het besturings systeem, beveiligings patches en partitioneren voor de klant, zodat u minder tijd kunt best Eden aan het onderhoud van de infra structuur en meer tijd hebt bij het bouwen van client-side-functies.  

#### <a name="cost-savings"></a>Kosten besparingen
Bij hoge ingangs volumes (> 100 TUs), kost het cluster aanzienlijk minder per uur dan het kopen van een vergelijkbaar aantal doorvoer eenheden in de standaard aanbieding.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Quota en limieten Event Hubs Dedicated

De Event Hubs Dedicated aanbieding wordt gefactureerd tegen een vaste maandelijkse prijs, met een minimum van 4 uur gebruik. De toegewezen laag biedt alle functies van het Standard-abonnement, maar met de schaal capaciteit van bedrijven en limieten voor klanten met veeleisende workloads. 

| Functie | Standard | Toegewezen |
| --- |:---:|:---:|
| Bandbreedte | 20 TUs (Maxi maal 40 TUs) | 20 CUs |
| Naamruimten |  1 | 50 per CU |
| Event Hubs |  10 per naam ruimte | 1000 per naam ruimte |
| Ingangsgebeurtenissen | Betalen per miljoen gebeurtenissen | Inbegrepen |
| Berichtgrootte | 1\.000.000 bytes | 1\.000.000 bytes |
| Partities | 32 per Event hub | 1024 per Event hub |
| Consumentengroepen | 20 per Event hub | Geen limiet per CU, 1000 per Event Hub |
| Brokered Connections | 1\.000 inbegrepen, 5.000 Maxi maal | 100 K inbegrepen en Max. |
| Bewaartermijn voor berichten | 7 dagen, 84 GB inbegrepen per di | 90 dagen, 10 TB inbegrepen per CU |
| Capture | Betalen per uur | Inbegrepen |

## <a name="how-to-onboard"></a>Onboarding

De eigen ervaring voor het [maken van een event hubs cluster](event-hubs-dedicated-cluster-create-portal.md) via [Azure Portal](https://aka.ms/eventhubsclusterquickstart) is nu beschikbaar als preview-versie. Als u vragen hebt of hulp nodig hebt bij het voorbereiden van Event Hubs Dedicated, neemt u contact op met het [Event hubs team](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Veelgestelde vragen

#### <a name="what-can-i-achieve-with-a-cluster"></a>Wat kan ik met een cluster krijgen?

Hoeveel u voor een Event Hubs cluster kunt opnemen en streamen, is afhankelijk van verschillende factoren, zoals uw producenten, consumenten, de snelheid waarmee u opnameert en verwerkt, en nog veel meer. 

De volgende tabel toont de Bench Mark-resultaten die we hebben behaald tijdens onze tests:

| De shape Payload | Ontvangers | Ingangs bandbreedte| Berichten binnenkomend | Uitgangs band breedte | Uitstaande berichten | Totaal aantal TUs | TUs per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batches van 100x1KB | 2 | 400 MB/sec. | 400k berichten per seconde | 800 MB/sec. | 800k berichten per seconde | 400 TUs | 100 TUs | 
| Batches van 10x10KB | 2 | 666 MB/sec. | 66.6 k-berichten/sec | 1,33 GB/sec. | 133k berichten per seconde | 666 TUs | 166 TUs |
| Batches van 6x32KB | 1 | 1,05 GB/sec. | 34k berichten per seconde | 1,05 GB/sec. | 34k berichten per seconde | 1000 TUs | 250 TUs |

Bij het testen zijn de volgende criteria gebruikt:

- Er is een toegewezen Event Hubs cluster met vier capaciteits eenheden (CUs) gebruikt. 
- De Event Hub die wordt gebruikt voor opname, had 200 partities. 
- De gegevens die zijn opgenomen, zijn ontvangen door twee receiver-toepassingen die van alle partities ontvangen.

#### <a name="can-i-scale-updown-my-cluster"></a>Kan ik mijn cluster omhoog/omlaag schalen?

Na het maken van clusters worden er mini maal vier uur verbruik in rekening gebracht. In de preview-versie van de zelf-onderhouds ervaring kunt u een [ondersteunings aanvraag](https://ms.portal.azure.com/#create/Microsoft.Support) indienen bij het event hubs team onder *technisch > quotum > aanvraag om een toegewezen cluster* omhoog of omlaag te schalen om het cluster omhoog of omlaag te schalen. Het kan tot 7 dagen duren voordat de aanvraag is voltooid om uw cluster omlaag te schalen. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Hoe werkt geo-DR met mijn cluster?

U kunt geografisch koppelen aan een naam ruimte onder een cluster met een specifieke laag met een andere naam ruimte onder een cluster met een specifieke laag. Het is niet raadzaam om een naam ruimte met een toegewezen laag te koppelen aan een naam ruimte in onze standaard aanbieding, omdat de doorvoer limiet incompatibel is. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kan ik mijn standaard naam ruimten migreren naar een cluster met een toegewezen laag?
Momenteel ondersteunen we geen geautomatiseerd migratie proces voor het migreren van uw event hubs-gegevens uit een standaard naam ruimte naar een speciaal voor beeld. 

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw micro soft-verkoop vertegenwoordiger of Microsoft Ondersteuning voor meer informatie over Event Hubs Dedicated. U kunt ook een cluster maken of meer informatie over Event Hubs prijs categorieën door de volgende koppelingen te bezoeken:

- [Een Event Hubs-cluster maken via de Azure-Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event hubs dedicated prijzen](https://azure.microsoft.com/pricing/details/event-hubs/). U kunt ook contact opnemen met uw micro soft-verkoop medewerker of Microsoft Ondersteuning om meer informatie over Event Hubs Dedicated capaciteit te krijgen.
- De [Veelgestelde](event-hubs-faq.md) vragen over de Event hubs bevatten prijs informatie en antwoorden op veelgestelde vragen over Event hubs.
