---
title: Migreren naar Azure Event Hubs voor Apache Kafka
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
ms.openlocfilehash: 806a1f16327ad72a7f3527c813b355e1ba807dda
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606754"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migreren naar Azure Event Hubs voor Apache Kafka-ecosystemen
Azure Event Hubs legt een Apache Kafka-eindpunt bloot, waarmee u verbinding maken met gebeurtenishubs via het Kafka-protocol. Door minimale wijzigingen aan te brengen in uw bestaande Kafka-toepassing, u verbinding maken met Azure Event Hubs en profiteren van de voordelen van het Azure-ecosysteem. Event Hubs voor Kafka ondersteunen [Apache Kafka versie 1.0](https://kafka.apache.org/10/documentation.html) en hoger.

## <a name="pre-migration"></a>Voorafgaand aan de migratie 

### <a name="create-an-azure-account"></a>Een Azure-account maken
Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

### <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Volg stapsgewijze instructies in het artikel [Een gebeurtenishub maken](event-hubs-create.md) om een naamruimte voor gebeurtenishubs en een gebeurtenishub te maken. 

### <a name="connection-string"></a>Verbindingsreeks
Volg de stappen van de [verbindingstekenreeks Ophalen in het portalartikel.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) En noteer de verbindingstekenreeks voor later gebruik. 

### <a name="fully-qualified-domain-name-fqdn"></a>Volledig gekwalificeerde domeinnaam (FQDN)
Mogelijk hebt u ook de FQDN nodig die naar de naamruimte van uw Event Hub verwijst. De FQDN is als volgt te vinden in uw verbindingstekenreeks:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Als de naamruimte van uw gebeurtenishubs wordt geïmplementeerd in een niet-openbare \*cloud, \*kan uw \*domeinnaam verschillen (bijvoorbeeld .servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net of .servicebus.cloudapi.de).

## <a name="migration"></a>Migratie 

### <a name="update-your-kafka-client-configuration"></a>Uw Kafka-clientconfiguratie bijwerken

Als u verbinding wilt maken met een gebeurtenishub met Kafka, moet u de configuraties van de Kafka-client bijwerken. Als u problemen ondervindt bij het vinden `bootstrap.servers` van de uwe, probeert u te zoeken naar waar is ingesteld in uw toepassing.

Voeg de volgende configs waar zinvol in uw toepassing. Zorg ervoor dat `bootstrap.servers` `sasl.jaas.config` u de waarden en waarden bijwerkt om de client naar het eindpunt van uw gebeurtenishubs Kafka te leiden met de juiste verificatie. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Als `sasl.jaas.config` het geen ondersteunde configuratie in uw framework is, zoekt u de configuraties die worden gebruikt om de SASL-gebruikersnaam en -wachtwoord in te stellen en deze in plaats daarvan te gebruiken. Stel de `$ConnectionString` gebruikersnaam in op en het wachtwoord op de verbindingstekenreeks van Gebeurtenishubs.

## <a name="post-migration"></a>Na de migratie
Voer uw Kafka-toepassing uit die gebeurtenissen naar de gebeurtenishub verzendt. Controleer vervolgens of de gebeurtenishub de gebeurtenissen ontvangt via de Azure-portal. Ga op de **pagina Overzicht** van de naamruimte van uw gebeurtenishubs over naar de weergave **Berichten** in de sectie **Statistieken.** Vernieuw de pagina om de grafiek bij te werken. Het kan enkele seconden duren voordat het is aangetoond dat de berichten zijn ontvangen. 

[![Controleren of de gebeurtenishub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over gebeurtenishubs en gebeurtenishubs voor Kafka:  

- [Handleiding voor het oplossen van problemen met Apache Kafka voor gebeurtenishubs](apache-kafka-troubleshooting-guide.md)
- [Veelgestelde vragen - Event Hubs voor Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka-ontwikkelaarshandleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)
- [Aanbevolen configuraties](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
- 