---
title: Problemen met Azure Event Hubs voor Apache Kafka oplossen
description: In dit artikel ziet u hoe u problemen met Azure Event Hubs voor Apache Kafka oplossen
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
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606728"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Handleiding voor het oplossen van problemen met Apache Kafka voor gebeurtenishubs
In dit artikel vindt u tips voor het oplossen van problemen die u tegenkomen bij het gebruik van gebeurtenishubs voor Apache Kafka. 

## <a name="server-busy-exception"></a>Uitzondering serverbezet
U ontvangt mogelijk een uitzondering voor Serverbezet vanwege Kafka-beperking. Met AMQP-clients retourneert Event Hubs onmiddellijk een **serverdrukke** uitzondering bij servicebeperking. Het is gelijk aan een "probeer het later opnieuw" bericht. In Kafka worden berichten vertraagd voordat ze worden voltooid. De vertragingslengte wordt geretourneerd `throttle_time_ms` in milliseconden zoals in de antwoord op producten/ophalen. In de meeste gevallen worden deze vertraagde aanvragen niet geregistreerd als ServerBusy-uitzonderingen op dashboards van Gebeurtenishubs. In plaats daarvan `throttle_time_ms` moet de waarde van de respons worden gebruikt als een indicator dat de doorvoer het ingerichte quotum heeft overschreden.

Als het verkeer buitensporig is, heeft de service het volgende gedrag:

- Als de vertraging van het aanvragen van aanvragen groter is dan de time-out van de aanvraag, retourneert gebeurtenishubs **de foutcode beleidsschending.**
- Als de vertraging van het ophalenvan aanvragen de time-out van het verzoek overschrijdt, worden de aanvragen als beperkt vastgelegd en reageert deze met lege set records en geen foutcode.

[Speciale clusters](event-hubs-dedicated-overview.md) hebben geen beperkingsmechanismen. U bent vrij om al uw clusterbronnen te gebruiken.

## <a name="no-records-received"></a>Geen records ontvangen
U zien dat consumenten geen records krijgen en voortdurend opnieuw in evenwicht worden brengen. In dit scenario krijgen consumenten geen records en voortdurend opnieuw in evenwicht. Er is geen uitzondering of fout wanneer het gebeurt, maar de Kafka logs zal laten zien dat de consumenten vast zitten proberen om de groep weer aan te sluiten en partities toewijzen. Er zijn een paar mogelijke oorzaken:

- Zorg ervoor `request.timeout.ms` dat je ten minste de aanbevolen waarde van `session.timeout.ms` 60000 en je is ten minste de aanbevolen waarde van 30000. Het hebben van deze instellingen te laag kan leiden tot time-outs van de consument, die vervolgens leiden tot herbalances (die vervolgens leiden tot meer time-outs, die leiden tot meer herbalancering, enzovoort) 
- Als uw configuratie overeenkomt met de aanbevolen waarden en u nog steeds constant opnieuw in evenwicht bent, u gerust een probleem openen (zorg ervoor dat u uw hele configuratie in het probleem opneemt, zodat we kunnen helpen bij het opsporen)!

## <a name="compressionmessage-format-version-issue"></a>Versieprobleem compressie/berichtindeling
Kafka ondersteunt compressie en gebeurtenishubs voor Kafka momenteel niet. Fouten die een versie met berichtindeling `The message format version on the broker does not support the request.`vermelden (bijvoorbeeld) worden veroorzaakt wanneer een klant gecomprimeerde Kafka-berichten naar onze makelaars probeert te verzenden.

Als gecomprimeerde gegevens nodig zijn, is het comprimeren van uw gegevens voordat u deze naar de makelaars verzendt en decomprimeert na ontvangst een geldige tijdelijke oplossing. De berichttekst is slechts een bytearray voor de service, zodat compressie/decompressie aan de clientzijde geen problemen veroorzaakt.

## <a name="unknownserverexception"></a>UnknownServerUitzondering
Mogelijk ontvangt u een UnknownServerException van Kafka-clientbibliotheken die vergelijkbaar zijn met het volgende voorbeeld: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Open een ticket met Microsoft-ondersteuning.  Logboekregistratie op foutopsporingsniveau en uitzonderingstijdstempels in UTC zijn handig bij het opsporen van het probleem. 

## <a name="other-issues"></a>Overige problemen
Controleer de volgende items als u problemen ziet wanneer u Kafka gebruikt op gebeurtenishubs.

- **Firewall blokkeert verkeer** - Zorg ervoor dat poort **9093** niet wordt geblokkeerd door uw firewall.
- **TopicAuthorizationException** - De meest voorkomende oorzaken van deze uitzondering zijn:
    - Een typfout in de verbindingstekenreeks in uw configuratiebestand, of
    - Probeer Gebeurtenishubs voor Kafka te gebruiken op een basiscategorie. Event Hubs voor Kafka [wordt alleen ondersteund voor standaard- en speciale naamruimten.](https://azure.microsoft.com/pricing/details/event-hubs/)
- **Mismatch kafka-versie** - Event Hubs voor Kafka Ecosystems ondersteunt Kafka-versies 1.0 en hoger. Sommige toepassingen met Kafka-versie 0.10 en later kunnen af en toe werken vanwege de achterwaartse compatibiliteit van het Kafka-protocol, maar we raden ten zeerste aan om oude API-versies te gebruiken. Kafka-versies 0.9 en eerder ondersteunen niet de vereiste SASL-protocollen en kunnen geen verbinding maken met Event Hubs.
- **Vreemde coderingen op AMQP-headers bij het consumeren met Kafka** - bij het verzenden van gebeurtenissen naar een gebeurtenishub via AMQP worden alle AMQP-payloadheaders geserialiseerd in AMQP-codering. Kafka-consumenten deserialiseren de headers van AMQP niet. Als u kopwaarden wilt lezen, decodeert u handmatig de AMQP-headers. U ook voorkomen dat u AMQP-headers gebruikt als u weet dat u via het Kafka-protocol zult consumeren. Zie voor meer informatie [dit GitHub-probleem](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **SASL-verificatie** - Het kan moeilijker zijn om uw framework te laten samenwerken met het SASL-verificatieprotocol dat vereist is door Event Hubs. Kijk of u problemen oplossen met de bronnen van uw framework voor SASL-verificatie. 

## <a name="limits"></a>Limieten
Apache Kafka vs. Event Hubs Kafka. Voor het grootste deel hebben de gebeurtenishubs voor Kafka-ecosystemen dezelfde standaardwaarden, eigenschappen, foutcodes en algemeen gedrag als Apache Kafka. De gevallen waarin deze twee expliciet verschillen (of waar gebeurtenishubs een limiet opleggen die Kafka niet oplegt), worden hieronder weergegeven:

- De maximale `group.id` lengte van de eigenschap is 256 tekens
- De maximale `offset.metadata.max.bytes` grootte van 1024 bytes
- Offset commits worden beperkt bij 4 calls/second per partitie met een maximale interne loggrootte van 1 MB


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over gebeurtenishubs en gebeurtenishubs voor Kafka:  

- [Apache Kafka-ontwikkelaarshandleiding voor Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka migratiegids voor Event Hubs](apache-kafka-migration-guide.md)
- [Veelgestelde vragen - Event Hubs voor Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Aanbevolen configuraties](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
