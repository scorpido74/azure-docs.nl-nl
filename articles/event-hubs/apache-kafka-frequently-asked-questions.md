---
title: Veelgestelde vragen - Azure Event Hubs voor Apache Kafka
description: In dit artikel ziet u hoe consumenten en producenten die verschillende protocollen gebruiken (AMQP, Apache Kafka en HTTPS) gebeurtenissen kunnen uitwisselen wanneer ze Azure Event Hubs gebruiken.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606741"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Veelgestelde vragen - Event Hubs voor Apache Kafka 
In dit artikel vindt u antwoorden op enkele van de veelgestelde vragen over het migreren naar gebeurtenishubs voor Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Run je Apache Kafka?

Nee.  We voeren Kafka API-bewerkingen uit op de infrastructuur van Event Hubs.  Omdat er een nauwe correlatie is tussen Apache Kafka en Event Hubs AMQP-functionaliteit (dat wil zeggen, produceren, ontvangen, beheren, enzovoort).), kunnen we de bekende betrouwbaarheid van Event Hubs naar de Kafka PaaS-ruimte brengen.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs consumentengroep vs. Kafka consumentengroep
Wat is het verschil tussen een Event Hub-consumentengroep en een Kafka-consumentengroep op Event Hubs? Kafka-consumentengroepen op Event Hubs onderscheiden zich volledig van standaard Event Hubs-consumentengroepen.

**Event Hubs consumentengroepen**

- Ze worden beheerd met het maken, ophalen, bijwerken en verwijderen van (CRUD)-bewerkingen via portal-, SDK- of Azure Resource Manager-sjablonen. Consumentengroepen voor gebeurtenishubs kunnen niet automatisch worden gemaakt.
- Het zijn kinderen entiteiten van een evenement hub. Dit betekent dat dezelfde naam van de consumentengroep kan worden hergebruikt tussen gebeurtenishubs in dezelfde naamruimte omdat het afzonderlijke entiteiten zijn.
- Ze worden niet gebruikt voor het opslaan van offsets. Georkestreerd AMQP-verbruik gebeurt met externe offsetopslag, bijvoorbeeld Azure Storage.

**Kafka consumentengroepen**

- Ze zijn automatisch gemaakt.  Kafka-groepen kunnen worden beheerd via de Kafka-consumentengroep API's.
- Ze kunnen compensaties opslaan in de Event Hubs-service.
- Ze worden gebruikt als sleutels in wat effectief een offset key-value store. Voor een uniek `group.id` `topic-partition`paar en , slaan we een offset op in Azure Storage (3x replicatie). Gebruikers van gebeurtenishubs maken geen extra opslagkosten door het opslaan van Kafka-compensaties. Compensaties zijn strafbaar via de ApI's van de Kafka-consumentengroep, maar de *offsetopslagaccounts* zijn niet direct zichtbaar of manipuleerbaar voor gebruikers van Event Hub.  
- Ze overspannen een naamruimte. Het gebruik van dezelfde Kafka-groepsnaam voor meerdere toepassingen op meerdere onderwerpen betekent dat alle toepassingen en hun Kafka-clients opnieuw in evenwicht worden gebracht wanneer slechts één toepassing opnieuw in evenwicht moet worden gebracht.  Kies uw groep namen verstandig.
- Ze onderscheiden zich volledig van Event Hubs-consumentengroepen. U **hoeft geen** '$Default' te gebruiken en u hoeft zich ook geen zorgen te maken dat Kafka-clients de AMQP-workloads verstoren.
- Ze zijn niet zichtbaar in de Azure-portal. Informatie over consumentengroepen is toegankelijk via Kafka API's.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over gebeurtenishubs en gebeurtenishubs voor Kafka:  

- [Apache Kafka-ontwikkelaarshandleiding voor Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka migratiegids voor Event Hubs](apache-kafka-migration-guide.md)
- [Handleiding voor het oplossen van problemen met Apache Kafka voor gebeurtenishubs](apache-kafka-troubleshooting-guide.md)
- [Aanbevolen configuraties](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

