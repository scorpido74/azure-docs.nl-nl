---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8700bbfe697a6b5fb81380831950d704fcb1f5ff
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90012843"
---
De volgende tabellen bevatten quota en limieten die specifiek zijn voor [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Zie [Prijzen voor Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) voor prijsinformatie over Event Hubs.

De volgende limieten gelden voor de basis- en standaardlaag. 

| Limiet | Bereik | Notities | Waarde |
| --- | --- | --- | --- |
| Aantal Event Hubs-naamruimten per abonnement |Abonnement |- |100 |
| Aantal event hubs per naamruimte |Naamruimte |Volgende aanvragen voor het maken van een nieuwe event hub worden geweigerd. |10 |
| Aantal partities per event hub |Entiteit |- |32 |
| Maximumlengte van een event hub-naam |Entiteit |- | 256 tekens |
| Maximumlengte van de naam van een consumentengroep |Entiteit |- | 256 tekens |
| Aantal niet-epoche ontvangers per consumentengroep |Entiteit |- |5 |
| Maximum aantal doorvoereenheden |Naamruimte |Wanneer de limiet voor het aantal doorvoereenheden wordt overschreden, worden uw gegevens beperkt en wordt een [server bezet-uitzondering](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) gegenereerd. U kunt een groter aantal doorvoereenheden voor een Standard-laag aanvragen door een [ondersteuningsaanvraag](/azure/azure-portal/supportability/how-to-create-azure-support-request) in te dienen. [Extra doorvoereenheden](../articles/event-hubs/event-hubs-auto-inflate.md) zijn beschikbaar in blokken van 20 op basis van een vaste aankoop. |20 |
| Aantal autorisatieregels per naamruimte |Naamruimte|Volgende aanvragen voor het maken van een autorisatieregel worden geweigerd.|12 |
| Aantal aanroepen van de methode GetRuntimeInformation | Entiteit | - | 50 per seconde | 
| Aantal virtuele netwerken (VNet) en IP-configuratieregels | Entiteit | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic en Standard: quota en limieten
| Limiet | Bereik | Notities | Basic | Standard |
| --- | --- | --- | -- | --- |
| Maximumgrootte van Event Hubs-gebeurtenis|Entiteit | &nbsp; | 256 kB | 1 MB |
| Aantal consumentengroepen per event hub |Entiteit | &nbsp; |1 |20 |
| Aantal AMQP-verbindingen per naamruimte |Naamruimte |Volgende aanvragen voor extra verbindingen worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. |100 |5.000|
| Maximale bewaartermijn van gebeurtenisgegevens |Entiteit | &nbsp; |1 dag |1-7 dagen |
|Naamruimte met Apache Kafka ingeschakeld|Naamruimte |Event Hubs-naamruimte streamt toepassingen met behulp van het Kafka-protocol. Zie [Azure Event Hubs gebruiken vanuit Apache Kafka-toepassingen](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md) voor meer informatie. |Nee | Ja |
|Vastleggen |Entiteit | Indien ingeschakeld, microbatches in dezelfde stroom. Zie [Capture events through Azure Event Hubs in Azure Blob Storage or Azure Data Lake Storage](../articles/event-hubs/event-hubs-capture-overview.md) (gebeurtenissen opnemen via Azure Event Hubs in Azure Blob Storage of Azure Data Lake Storage) voor meer informatie. |Nee |Ja |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated: quota en limieten
De Event Hubs Dedicated-aanbieding wordt gefactureerd tegen een vaste maandelijkse prijs, met een minimum van 4 uur gebruik. De Dedicated-laag biedt alle functies van het Standard-abonnement, maar met schaalcapaciteit voor grote bedrijven en limieten voor klanten met veeleisende werkbelastingen. 

| Functie | Limieten |
| --- | ---|
| Bandbreedte |  20 CU's |
| Naamruimten | 50 per CU |
| Event Hubs |  1000 per naamruimte |
| Ingangsgebeurtenissen | Inbegrepen |
| Berichtgrootte | 1 MB |
| Partities | 2000 per CU |
| Consumentengroepen | Geen limiet per CU, 1000 per event hub |
| Brokered verbindingen | 100 K inbegrepen |
| Bewaarperiode van berichten | 90 dagen, 10 TB inbegrepen per CU |
| Vastleggen | Inbegrepen |
