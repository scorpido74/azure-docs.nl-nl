---
title: Overzicht van speciale gebeurtenishubs - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u een overzicht van speciale Azure-gebeurtenishubs, die implementaties van gebeurtenishubs met één tenant aanbieden.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516743"
---
# <a name="overview-of-event-hubs-dedicated"></a>Overzicht van evenementhubs gewijd

*Event Hubs-clusters* bieden implementaties met één tenant voor klanten met de meest veeleisende streamingbehoeften. Dit single-tenant aanbod heeft een gegarandeerde SLA van 99,99% en is alleen beschikbaar op onze dedicated pricing tier. Een cluster van Gebeurtenishubs kan miljoenen gebeurtenissen per seconde binnendringen met gegarandeerde capaciteit en latentie onder één seconde. Naamruimten en gebeurtenishubs die binnen het dedicated cluster zijn gemaakt, bevatten alle functies van het standaardaanbod en meer, maar zonder invallen. Het bevat ook de populaire [functie voor het vastleggen van gebeurtenishubs](event-hubs-capture-overview.md) zonder extra kosten, zodat u automatisch gegevensstromen batchen en logboeken naar Azure Storage of Azure Data Lake. 

Clusters worden ingericht en gefactureerd door **Capaciteitseenheden (CPU's),** een vooraf toegewezen hoeveelheid CPU- en geheugenbronnen. U voor elk cluster 1, 2, 4, 8, 12, 16 of 20 CPU's kopen. Hoeveel u per CU innemen en streamen, hangt af van verschillende factoren, zoals het aantal producenten en consumenten, payload-vorm, uitgangssnelheid (zie benchmarkresultaten hieronder voor meer details). 

> [!NOTE]
> Alle clusterclusters van gebeurtenishubs zijn standaard ingeschakeld voor Kafka en ondersteunen Kafka-eindpunten die kunnen worden gebruikt door uw bestaande op Kafka gebaseerde toepassingen. Het hebben van Kafka ingeschakeld op uw cluster heeft geen invloed op uw niet-Kafka-gebruiksaanvragen; er is geen optie of noodzaak om Kafka uit te schakelen op een cluster.

## <a name="why-dedicated"></a>Waarom Dedicated?

Dedicated Event Hubs biedt drie overtuigende voordelen voor klanten die capaciteit op ondernemingsniveau nodig hebben:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-tenancy garandeert capaciteit voor betere prestaties

Een dedicated cluster garandeert capaciteit op volledige schaal en kan tot gigabyte aan streaminggegevens binnendringen met volledig duurzame opslag en latentie van subseconden om elke uitbarsting in het verkeer mogelijk te maken. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inclusieve en exclusieve toegang tot functies 
Het speciale aanbod bevat functies zoals Capture zonder extra kosten, evenals exclusieve toegang tot aankomende functies zoals Bring Your Own Key (BYOK). De service beheert ook taakverdeling, OS-updates, beveiligingspatches en partitionering voor de klant, zodat u minder tijd besteden aan onderhoud van de infrastructuur en meer tijd aan het bouwen van client-side functies.  

#### <a name="cost-savings"></a>Kostenbesparingen
Bij hoge instroomvolumes (>100 TV's) kost een cluster aanzienlijk minder per uur dan de aankoop van een vergelijkbare hoeveelheid doorvoereenheden in het standaardaanbod.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Speciale quota en limieten voor gebeurtenishubs

Het Evenement Hubs Dedicated-aanbod wordt gefactureerd tegen een vaste maandelijkse prijs, met een minimum van 4 uur gebruik. De Dedicated-laag biedt alle functies van het Standaard-plan, maar met capaciteit en limieten op bedrijfsschaal voor klanten met veeleisende workloads. 

| Functie | Standard | Toegewezen |
| --- |:---:|:---:|
| Bandbreedte | 20 amerikaanse sus's (tot 40 amerikaanse) | 20 C's |
| Naamruimten |  1 | 50 per CU |
| Event Hubs |  10 per naamruimte | 1000 per naamruimte |
| Invallende gebeurtenissen | Betaal per miljoen evenementen | Inbegrepen |
| Berichtgrootte | 1 miljoen bytes | 1 miljoen bytes |
| Partities | 32 per gebeurtenishub | 1024 per gebeurtenishub |
| Consumergroepen | 20 per gebeurtenishub | Geen limiet per CU, 1000 per gebeurtenishub |
| Brokered connections | 1.000 inbegrepen, 5.000 max. | 100 K inbegrepen en max. |
| Behoud van berichten | 7 dagen, 84 GB inbegrepen per TU | 90 dagen, 10 TB inbegrepen per CU |
| Capture | Betalen per uur | Inbegrepen |

## <a name="how-to-onboard"></a>Hoe aan boord

De self-serve-ervaring voor [het maken van een cluster van gebeurtenishubs](event-hubs-dedicated-cluster-create-portal.md) via de Azure [Portal](https://aka.ms/eventhubsclusterquickstart) bevindt zich nu in Preview. Als je vragen hebt of hulp nodig hebt bij het instappen naar Event Hubs Dedicated, neem dan contact op met het [Event Hubs-team.](mailto:askeventhubs@microsoft.com)

## <a name="faqs"></a>Veelgestelde vragen

#### <a name="what-can-i-achieve-with-a-cluster"></a>Wat kan ik bereiken met een cluster?

Voor een cluster van Event Hubs is hoeveel u innemen en streamen afhankelijk van verschillende factoren, zoals uw producenten, consumenten, de snelheid waarmee u inneemt en verwerkt, en nog veel meer. 

Onderstaande tabel toont de benchmarkresultaten die we tijdens onze tests hebben behaald:

| Payload-vorm | Ontvangers | Binnenvallende bandbreedte| Binnenkomende berichten | Bandbreedte uit de uitgang | Berichten uit de uitgang | Totaal amerikaanse tus | Amerikaanse sus's per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batches van 100x1KB | 2 | 400 MB/s | 400k berichten per seconde | 800 MB/s | 800k berichten per seconde | 400 tUs | 100 tus | 
| Batches van 10x10KB | 2 | 666 MB/s | 66,6k berichten per seconde | 1,33 GB/s | 133k berichten per seconde | 666 tus's | 166 tUs |
| Batches van 6x32KB | 1 | 1,05 GB/s | 34k berichten / sec | 1,05 GB/s | 34k berichten per seconde | 1000 tus | 250 amerikaanse sus's |

Bij de tests werden de volgende criteria gebruikt:

- Er is gebruik gemaakt van een cluster met gebeurtenishubs met vier capaciteitseenheden (CPU's). 
- De gebeurtenishub die voor inname wordt gebruikt had 200 partities. 
- De gegevens die werd ingenomen werd ontvangen door twee ontvanger toepassingen ontvangen van alle partities.

#### <a name="can-i-scale-updown-my-cluster"></a>Kan ik mijn cluster opschalen/omlaag?

Na het maken worden clusters gefactureerd voor minimaal 4 uur gebruik. In de preview-versie van de zelfbedieningservaring u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#create/Microsoft.Support) indienen bij het Team Gebeurtenishubs onder *Technische > Quota >-aanvraag om dedicated cluster op te schalen of op te schalen* om uw cluster omhoog of omlaag te schalen. Het kan tot 7 dagen duren voordat de aanvraag is voltooid om uw cluster te schalen. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Hoe werkt Geo-DR met mijn cluster?

U een naamruimte koppelen onder een cluster met dedicated-tier met een andere naamruimte onder een cluster met speciale lagen. We moedigen het koppelen van een dedicated-tier naamruimte met een naamruimte in ons standaardaanbod niet aan, omdat de doorvoerlimiet onverenigbaar zal zijn, wat tot fouten zal leiden. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kan ik mijn standaardnaamruimten migreren om deel uit te maken van een dedicated-tier cluster?
We ondersteunen momenteel geen geautomatiseerd migratieproces voor het migreren van uw gebeurtenishubsgegevens van een standaardnaamruimte naar een dedicated-status. 

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw Microsoft-vertegenwoordiger of Microsoft Support voor meer informatie over Dedicated Event Hubs. U ook een cluster maken of meer informatie krijgen over prijsniveaus van gebeurtenishubs door naar de volgende koppelingen te gaan:

- [Een cluster van gebeurtenishubs maken via de Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Speciale prijzen voor gebeurtenishubs](https://azure.microsoft.com/pricing/details/event-hubs/). U ook contact opnemen met uw Microsoft-vertegenwoordiger of Microsoft Support voor meer informatie over de toegewezen capaciteit van gebeurtenishubs.
- De [veelgestelde vragen over gebeurtenishubs](event-hubs-faq.md) bevat prijsinformatie en beantwoordt een aantal veelgestelde vragen over eventhubs.
