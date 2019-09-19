---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c163e3cce862640d43f8696dca4eeef29f2ae12a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "68912344"
---
De volgende tabel geeft een lijst van quota's en limieten die specifiek zijn voor [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/). Zie [Event hubs prijzen](https://azure.microsoft.com/pricing/details/event-hubs/)voor meer informatie over Event hubs prijzen.

| Limiet | Scope | Opmerkingen | Value |
| --- | --- | --- | --- |
| Aantal Event Hubs-naam ruimten per abonnement |Subscription |- |100 |
| Aantal Event hubs per naam ruimte |Naamruimte |Volgende aanvragen voor het maken van een nieuwe Event Hub worden geweigerd. |10 |
| Aantal partities per Event Hub |Entiteit |- |32 |
| Maximale grootte van Event Hubs gebeurtenis|Entiteit |- |1 MB |
| Maximale grootte van een Event Hub naam |Entiteit |- |50 tekens |
| Aantal niet-epoche receivers per consumenten groep |Entiteit |- |5 |
| Maximum aantal Throughput Units |Naamruimte |Door de limiet voor de doorvoer eenheid te overschrijden, worden uw gegevens beperkt en wordt er een [Server uitzondering](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)gegenereerd. Als u een groter aantal doorvoer eenheden wilt aanvragen voor een Standard-laag, moet u een [ondersteunings aanvraag](/azure/azure-supportability/how-to-create-azure-support-request)indienen. [Extra doorvoer eenheden](../articles/event-hubs/event-hubs-auto-inflate.md) zijn beschikbaar in blokken van 20 op basis van een vastgelegde aankoop. |20 |
| Aantal autorisatie regels per naam ruimte |Naamruimte|Volgende aanvragen voor het maken van een autorisatie regel worden geweigerd.|12 |
| Aantal aanroepen naar de methode GetRuntimeInformation | Entiteit | - | 50 per seconde | 
| Aantal virtuele netwerken (VNet) en IP-configuratie regels | Entiteit | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic-en Standard-quota en-limieten
| Limiet | Scope | Opmerkingen | Basic | Standard |
| --- | --- | --- | -- | --- |
| Aantal consumenten groepen per Event Hub |Entiteit | - |1 |20 |
| Aantal AMQP-verbindingen per naam ruimte |Naamruimte |Volgende aanvragen voor extra verbindingen worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |100 |5,000|
| Maximale Bewaar periode van gebeurtenis gegevens |Entiteit | - |1 dag |1-7 dagen |
|Naam ruimte Apache Kafka ingeschakeld|Naamruimte |Event Hubs-naam ruimte streamt toepassingen met het Kafka-Protocol |Nee | Ja |
|Capture |Entiteit | Bij inschakeling worden micro batches op dezelfde stroom |Nee |Ja |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated quota's en limieten
De Event Hubs Dedicated aanbieding wordt gefactureerd tegen een vaste maandelijkse prijs, met een minimum van 4 uur gebruik. De toegewezen laag biedt alle functies van het Standard-abonnement, maar met de schaal capaciteit van bedrijven en limieten voor klanten met veeleisende workloads. 

| Functie | Limieten |
| --- | ---|
| Bandbreedte |  20 CUs |
| Naamruimten | 50 per CU |
| Event Hubs |  1000 per naam ruimte |
| Ingangsgebeurtenissen | Inbegrepen |
| Berichtgrootte | 1\.000.000 bytes |
| Partities | 2000 per CU |
| Consumergroepen | Geen limiet per CU, 1000 per Event Hub |
| Brokered Connections | 100 K inbegrepen |
| Bewaartermijn voor berichten | Maxi maal 7 dagen (90 dag bewaren binnenkort beschikbaar), 10 TB inbegrepen per CU |
| Capture | Inbegrepen |
