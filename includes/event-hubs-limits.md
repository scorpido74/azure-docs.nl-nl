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
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75901595"
---
De volgende tabellen bevatten quota's en limieten die specifiek zijn voor [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/). Zie [Event hubs prijzen](https://azure.microsoft.com/pricing/details/event-hubs/)voor meer informatie over Event hubs prijzen.

De volgende limieten zijn gebruikelijk voor de lagen basis, standaard en toegewezen. 

| Limiet | Bereik | Opmerkingen | Waarde |
| --- | --- | --- | --- |
| Aantal Event Hubs-naam ruimten per abonnement |Abonnement |- |100 |
| Aantal Event hubs per naam ruimte |Naamruimte |Volgende aanvragen voor het maken van een nieuwe Event Hub worden geweigerd. |10 |
| Aantal partities per Event Hub |Entiteit |- |32 |
| Maximale grootte van een Event Hub naam |Entiteit |- |50 tekens |
| Aantal niet-epoche receivers per consumenten groep |Entiteit |- |5 |
| Maximale doorvoer eenheden |Naamruimte |Door de limiet voor de doorvoer eenheid te overschrijden, worden uw gegevens beperkt en wordt er een [Server uitzondering](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)gegenereerd. Als u een groter aantal doorvoer eenheden wilt aanvragen voor een Standard-laag, moet u een [ondersteunings aanvraag](/azure/azure-portal/supportability/how-to-create-azure-support-request)indienen. [Extra doorvoer eenheden](../articles/event-hubs/event-hubs-auto-inflate.md) zijn beschikbaar in blokken van 20 op basis van een vastgelegde aankoop. |20 |
| Aantal autorisatie regels per naam ruimte |Naamruimte|Volgende aanvragen voor het maken van een autorisatie regel worden geweigerd.|12 |
| Aantal aanroepen naar de methode GetRuntimeInformation | Entiteit | - | 50 per seconde | 
| Aantal virtuele netwerken (VNet) en IP-configuratie regels | Entiteit | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic-en Standard-quota en-limieten
| Limiet | Bereik | Opmerkingen | Basic | Standard |
| --- | --- | --- | -- | --- |
| Maximale grootte van Event Hubs gebeurtenis|Entiteit | &nbsp; | 256 kB | 1 MB |
| Aantal consumenten groepen per Event Hub |Entiteit | &nbsp; |1 |20 |
| Aantal AMQP-verbindingen per naam ruimte |Naamruimte |Volgende aanvragen voor extra verbindingen worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |100 |5.000|
| Maximale Bewaar periode van gebeurtenis gegevens |Entiteit | &nbsp; |1 dag |1-7 dagen |
|Naam ruimte Apache Kafka ingeschakeld|Naamruimte |Event Hubs-naam ruimte streamt toepassingen met het Kafka-Protocol |Nee | Ja |
|Capture |Entiteit | Bij inschakeling worden micro batches op dezelfde stroom |Nee |Ja |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated quota's en limieten
De Event Hubs Dedicated aanbieding wordt gefactureerd tegen een vaste maandelijkse prijs, met een minimum van 4 uur gebruik. De toegewezen laag biedt alle functies van het Standard-abonnement, maar met de schaal capaciteit van bedrijven en limieten voor klanten met veeleisende workloads. 

| Functie | Limieten |
| --- | ---|
| Bandbreedte |  20 CUs |
| Naamruimten | 50 per CU |
| Event Hubs |  1000 per naam ruimte |
| Ingangs gebeurtenissen | Inbegrepen |
| Bericht grootte | 1 MB |
| Partities | 2000 per CU |
| Consumergroepen | Geen limiet per CU, 1000 per Event Hub |
| Brokered Connections | 100 K inbegrepen |
| Bewaar periode van het bericht | 90 dagen, 10 TB inbegrepen per CU |
| Capture | Inbegrepen |
