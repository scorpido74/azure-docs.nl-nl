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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901595"
---
In de volgende tabellen worden quota en limieten verstrekt die specifiek zijn voor [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Zie Prijzen van [gebeurtenishubs](https://azure.microsoft.com/pricing/details/event-hubs/)voor informatie over de prijzen van gebeurtenishubs.

De volgende limieten zijn gebruikelijk voor basis-, standaard- en dedicated lagen. 

| Limiet | Bereik | Opmerkingen | Waarde |
| --- | --- | --- | --- |
| Aantal naamruimten voor gebeurtenishubs per abonnement |Abonnement |- |100 |
| Aantal gebeurtenishubs per naamruimte |Naamruimte |Latere aanvragen voor het maken van een nieuwe gebeurtenishub worden afgewezen. |10 |
| Aantal partities per gebeurtenishub |Entiteit |- |32 |
| Maximale grootte van de naam van een gebeurtenishub |Entiteit |- |50 tekens |
| Aantal niet-tijdvakken ontvangers per consumentengroep |Entiteit |- |5 |
| Maximale doorvoereenheden |Naamruimte |Als u de limiet voor doorvoereenheid overschrijdt, worden uw gegevens beperkt en wordt een [serverdrukke uitzondering gegenereerd.](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) Als u een groter aantal doorvoereenheden voor een standaardlaag wilt aanvragen, dient u een [ondersteuningsverzoek](/azure/azure-portal/supportability/how-to-create-azure-support-request)in . [Extra doorvoereenheden](../articles/event-hubs/event-hubs-auto-inflate.md) zijn beschikbaar in blokken van 20 op een vastgelegde aankoopbasis. |20 |
| Aantal autorisatieregels per naamruimte |Naamruimte|Latere aanvragen voor het maken van autorisatieregels worden afgewezen.|12 |
| Aantal oproepen naar de methode GetRuntimeInformation | Entiteit | - | 50 per seconde | 
| Aantal regels voor virtueel netwerk (VNet) en IP Config | Entiteit | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic en Standard - quota en limieten
| Limiet | Bereik | Opmerkingen | Basic | Standard |
| --- | --- | --- | -- | --- |
| Maximale grootte van gebeurtenis Gebeurtenishubs|Entiteit | &nbsp; | 256 kB | 1 MB |
| Aantal consumentengroepen per gebeurtenishub |Entiteit | &nbsp; |1 |20 |
| Aantal AMQP-verbindingen per naamruimte |Naamruimte |Latere aanvragen voor aanvullende verbindingen worden afgewezen en een uitzondering wordt ontvangen door de belcode. |100 |5.000|
| Maximale bewaarperiode van gebeurtenisgegevens |Entiteit | &nbsp; |1 dag |1-7 dagen |
|Apache Kafka ingeschakeld naamruimte|Naamruimte |Naamruimte voor gebeurtenishubs streamt toepassingen met kafka-protocol |Nee | Ja |
|Capture |Entiteit | Indien ingeschakeld, microbatches op dezelfde stream |Nee |Ja |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated - quota en limieten
Het Evenement Hubs Dedicated-aanbod wordt gefactureerd tegen een vaste maandelijkse prijs, met een minimum van 4 uur gebruik. De Dedicated-laag biedt alle functies van het Standaard-plan, maar met capaciteit en limieten op bedrijfsschaal voor klanten met veeleisende workloads. 

| Functie | Limieten |
| --- | ---|
| Bandbreedte |  20 C's |
| Naamruimten | 50 per CU |
| Event Hubs |  1000 per naamruimte |
| Invallende gebeurtenissen | Inbegrepen |
| Berichtgrootte | 1 MB |
| Partities | 2000 per CU |
| Consumergroepen | Geen limiet per CU, 1000 per gebeurtenishub |
| Brokered connections | 100 K inbegrepen |
| Behoud van berichten | 90 dagen, 10 TB inbegrepen per CU |
| Capture | Inbegrepen |
