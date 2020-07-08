---
title: 'Veelgestelde vragen: Azure Event Hubs voor Apache Kafka'
description: In dit artikel wordt uitgelegd hoe consumenten en producenten die gebruikmaken van verschillende protocollen (AMQP, Apache Kafka en HTTPS), gebeurtenissen kunnen uitwisselen bij het gebruik van Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 066024f657e011aac1449106ec32689f145c5534
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314194"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Veelgestelde vragen-Event Hubs voor Apache Kafka 
In dit artikel vindt u antwoorden op enkele veelgestelde vragen over het migreren naar Event Hubs voor Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Voert u Apache Kafka uit?

Nee.  Kafka-API-bewerkingen worden uitgevoerd op basis van Event Hubs-infra structuur.  Omdat er sprake is van een strakke correlatie tussen Apache Kafka en Event Hubs AMQP-functionaliteit (dat wil zeggen, produceren, ontvangen, beheren, enzovoort), kunnen we de bekende betrouw baarheid van Event Hubs in de PaaS Space van Kafka brengen.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs consumenten groep versus consumenten groep Kafka
Wat is het verschil tussen een event hub-Consumer groep en een Kafka-Consumer groep op Event Hubs? Kafka consumenten groepen op Event Hubs zijn volledig onderscheiden van standaard Event Hubs consumenten groepen.

**Consumenten groepen Event Hubs**

- Ze worden beheerd met bewerkingen voor maken, ophalen, bijwerken en verwijderen via Portal-, SDK-of Azure Resource Manager sjablonen. Event Hubs consumenten groepen kunnen niet worden gemaakt.
- Ze zijn onderliggende entiteiten van een Event Hub. Dit betekent dat dezelfde naam van de Consumer groep opnieuw kan worden gebruikt tussen Event hubs in dezelfde naam ruimte, omdat het afzonderlijke entiteiten zijn.
- Ze worden niet gebruikt voor het opslaan van offsets. Het georganisatiede AMQP-verbruik wordt uitgevoerd met behulp van externe offset opslag, bijvoorbeeld Azure Storage.

**Kafka-consumenten groepen**

- Deze worden gemaakt.  Kafka groepen kunnen worden beheerd via de Api's van de Kafka-Consumer groep.
- Ze kunnen offsets in de Event Hubs-service opslaan.
- Ze worden gebruikt als sleutels in wat een offset-waarde voor sleutel waarden is. Voor een uniek paar `group.id` en `topic-partition` worden er in azure Storage een offset (3x-replicatie) opgeslagen. Event Hubs gebruikers geen extra opslag kosten in rekening brengen bij het opslaan van Kafka-offsets. Offsets worden manipulable via de Kafka-Api's van de gebruikers groep, maar de offset opslag *accounts* zijn niet direct zichtbaar of Manipulable voor Event hub-gebruikers.  
- Ze omvatten een naam ruimte. Als u dezelfde Kafka-groeps naam gebruikt voor meerdere toepassingen op meerdere onderwerpen, worden alle toepassingen en hun Kafka-clients opnieuw gebalanceerd wanneer slechts één toepassing opnieuw moet worden gebalanceerd.  Kies de namen van uw groepen.
- Ze zijn volledig onderscheiden van Event Hubs consumenten groepen. U hoeft ' $Default ' **niet** te gebruiken en u hoeft zich geen zorgen te maken over Kafka-clients die de AMQP-workloads belemmeren.
- Ze zijn niet zichtbaar in de Azure Portal. Informatie over de gebruikers groep is toegankelijk via Kafka-Api's.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Apache Kafka ontwikkelaars handleiding voor Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka migratie handleiding voor Event Hubs](apache-kafka-migration-guide.md)
- [Gids voor het oplossen van problemen met Apache Kafka voor Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Aanbevolen configuraties](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

