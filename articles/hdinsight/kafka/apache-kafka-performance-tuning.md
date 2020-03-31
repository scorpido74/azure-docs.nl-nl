---
title: Prestaties van Apache Kafka HDInsight-clusters optimaliseren
description: Biedt een overzicht van technieken voor het optimaliseren van Apache Kafka-workloads op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494927"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Prestaties van Apache Kafka HDInsight-clusters optimaliseren

Dit artikel geeft enkele suggesties voor het optimaliseren van de prestaties van uw Apache Kafka-workloads in HDInsight. De focus ligt op het aanpassen van de configuratie van producenten en makelaars. Er zijn verschillende manieren om de prestaties te meten, en de optimalisaties die u toepast, zijn afhankelijk van uw bedrijfsbehoeften.

## <a name="architecture-overview"></a>Overzicht van de architectuur

Kafka-onderwerpen worden gebruikt om records te ordenen. Records worden geproduceerd door producenten en worden gebruikt door consumenten. Producenten sturen records naar Kafka makelaars, die vervolgens de gegevens op te slaan. Elk werkrolknooppunt in uw HDInsight-cluster is een Kafka-broker.

Onderwerpen verdelen records over brokers. Wanneer records worden verbruikt, kunt u maximaal één consumer per partitie gebruiken voor parallelle verwerking van de gegevens.

Replicatie wordt gebruikt om partities tussen knooppunten te dupliceren. Dit beschermt tegen node (broker) storingen. Een enkele partitie tussen de groep replica's wordt aangewezen als de partitieleider. Producentverkeer wordt doorgestuurd naar de leider van elk knooppunt, met behulp van de status die wordt beheerd door ZooKeeper.

## <a name="identify-your-scenario"></a>Uw scenario identificeren

Apache Kafka prestaties heeft twee belangrijke aspecten - doorvoer en latentie. Doorvoer is de maximale snelheid waarmee gegevens kunnen worden verwerkt. Hogere doorvoer is meestal beter. Latentie is de tijd die nodig is om gegevens op te slaan of op te halen. Lagere latentie is meestal beter. Het vinden van de juiste balans tussen doorvoer, latentie en de kosten van de infrastructuur van de toepassing kan een uitdaging zijn. Uw prestatievereisten komen waarschijnlijk overeen met een van de volgende drie veelvoorkomende situaties, op basis van de vraag of u een hoge doorvoer, lage latentie of beide nodig hebt:

* Hoge doorvoer, lage latentie. Dit scenario vereist zowel hoge doorvoer als lage latentie (~ 100 milliseconden). Een voorbeeld van dit type toepassing is servicebeschikbaarheidsbewaking.
* Hoge doorvoer, hoge latentie. Dit scenario vereist een hoge doorvoer (~ 1,5 GBps), maar kan een hogere latentie verdragen (< 250 ms). Een voorbeeld van dit type toepassing is telemetrie gegevens opname voor near real-time processen zoals beveiliging en inbraakdetectie toepassingen.
* Lage doorvoer, lage latentie. Dit scenario vereist een lage latentie (< 10 ms) voor real-time verwerking, maar kan een lagere doorvoer verdragen. Een voorbeeld van dit type toepassing is online spelling- en grammaticacontroles.

## <a name="producer-configurations"></a>Producentenconfiguraties

In de volgende secties worden enkele van de belangrijkste configuratie-eigenschappen belicht om de prestaties van uw Kafka-producenten te optimaliseren. Zie [Apache Kafka-documentatie over producentenconfiguraties voor](https://kafka.apache.org/documentation/#producerconfigs)een gedetailleerde uitleg van alle configuratie-eigenschappen.

### <a name="batch-size"></a>Batchgrootte

Apache Kafka-producenten assembleren groepen berichten (batches genoemd) die worden verzonden als een eenheid die in één opslagpartitie moet worden opgeslagen. Batchgrootte: het aantal bytes dat aanwezig moet zijn voordat die groep wordt verzonden. Het `batch.size` verhogen van de parameter kan de doorvoer verhogen, omdat het de verwerkingsoverhead van netwerk- en IO-aanvragen vermindert. Onder lichte belasting kan de grotere batchgrootte de gebruikslaten van Kafka verhogen, omdat de producent wacht tot een batch klaar is. Onder zware belasting wordt aanbevolen om de batchgrootte te vergroten om de doorvoer en latentie te verbeteren.

### <a name="producer-required-acknowledgments"></a>Producent vereiste erkenningen

De vereiste `acks` configuratie van de producent bepaalt het aantal bevestigingen dat de partitieleider nodig heeft voordat een schrijfaanvraag als voltooid wordt beschouwd. Deze instelling is van invloed op `0`de `1`betrouwbaarheid `-1`van gegevens en er zijn waarden voor nodig van , of . De waarde `-1` van betekent dat een bevestiging moet worden ontvangen van alle replica's voordat het schrijven is voltooid. Instelling `acks = -1` biedt sterkere garanties tegen gegevensverlies, maar resulteert ook in een hogere latentie en een lagere doorvoer. Als de toepassingsvereisten een hogere `acks = 0` `acks = 1`doorvoer vereisen, probeert u deze in te stellen of . Houd er rekening mee dat het niet erkennen van alle replica's de betrouwbaarheid van gegevens kan verminderen.

### <a name="compression"></a>Compressie

Een Kafka-producent kan worden geconfigureerd om berichten te comprimeren voordat ze naar makelaars worden verzonden. De `compression.type` instelling geeft de compressiecode c aan die moet worden gebruikt. Ondersteunde compressiecodecs zijn 'gzip', 'pittig' en 'lz4'. Compressie is gunstig en moet worden overwogen als er een beperking op de schijfcapaciteit.

Onder de twee veelgebruikte `gzip` compressiecodecs, en `snappy`, `gzip` heeft een hogere compressieverhouding, wat resulteert in een lager schijfgebruik ten koste van een hogere CPU-belasting. De `snappy` codec biedt minder compressie met minder CPU overhead. U beslissen welke codec te gebruiken op basis van makelaar schijf of producent CPU beperkingen. `gzip`kan gegevens comprimeren met een `snappy`snelheid die vijf keer hoger is dan .

Als u gegevenscompressie gebruikt, wordt het aantal records dat op een schijf kan worden opgeslagen, verhoogd. Het kan ook verhogen CPU overhead in gevallen waarin er een mismatch tussen de compressie formaten worden gebruikt door de producent en de makelaar. als de gegevens moeten worden gecomprimeerd voordat ze worden verzonden en vervolgens gedecomprimeerd voordat de verwerking.

## <a name="broker-settings"></a>Broker-instellingen

In de volgende secties worden enkele van de belangrijkste instellingen belicht om de prestaties van uw Kafka-makelaars te optimaliseren. Zie [Apache Kafka-documentatie over productieconfiguraties voor](https://kafka.apache.org/documentation/#producerconfigs)een gedetailleerde uitleg van alle makelaarsinstellingen.

### <a name="number-of-disks"></a>Aantal schijven

Opslagschijven hebben beperkte IOPS (Input/Output Operations per Seconde) en lees/schrijf bytes per seconde. Bij het maken van nieuwe partities slaat Kafka elke nieuwe partitie op de schijf op met de minste bestaande partities om ze over de beschikbare schijven te balanceren. Ondanks de opslagstrategie kan Kafka bij de verwerking van honderden partitiereplica's op elke schijf gemakkelijk de beschikbare schijfdoorvoer verzadigen. De afweging hier is tussen doorvoer en kosten. Als uw toepassing een grotere doorvoer vereist, maakt u een cluster met meer beheerde schijven per broker. HDInsight biedt momenteel geen ondersteuning voor het toevoegen van beheerde schijven aan een lopend cluster. Zie Opslag en schaalbaarheid configureren voor Apache [Kafka op HDInsight voor](apache-kafka-scalability.md)meer informatie over het configureren van het aantal beheerde schijven. Begrijp de kostenimplicaties van het vergroten van de opslagruimte voor de knooppunten in uw cluster.

### <a name="number-of-topics-and-partitions"></a>Aantal onderwerpen en partities

Kafka producenten schrijven naar onderwerpen. Kafka consumenten lezen van onderwerpen. Een onderwerp wordt gekoppeld aan een logboek, dat een gegevensstructuur op schijf is. Kafka voegt records toe van een producent(en) tot het einde van een onderwerplogboek. Een onderwerplogboek bestaat uit veel partities die over meerdere bestanden zijn verspreid. Deze bestanden zijn op hun beurt verspreid over meerdere Kafka-clusterknooppunten. Consumenten lezen van Kafka onderwerpen op hun cadans en kunnen hun positie (offset) te halen in het onderwerp log.

Elke Kafka-partitie is een logboekbestand op het systeem en producententhreads kunnen tegelijkertijd naar meerdere logboeken schrijven. Op dezelfde manier, omdat elke consument thread leest berichten uit een partitie, consumeren van meerdere partities wordt behandeld in parallel ook.

Het verhogen van de partitiedichtheid (het aantal partities per broker) voegt een overhead toe met betrekking tot metadata-bewerkingen en per partitieaanvraag/reactie tussen de partitieleider en zijn volgers. Zelfs bij afwezigheid van gegevens die doorstromen, halen partitiereplica's nog steeds gegevens op van leiders, wat resulteert in extra verwerking voor het verzenden en ontvangen van aanvragen via het netwerk.

Voor Apache Kafka clusters 1.1 en hoger in HDInsight raden we u aan om maximaal 1000 partities per broker te hebben, inclusief replica's. Het verhogen van het aantal partities per broker vermindert de doorvoer en kan ook leiden tot onbeschikbaarheid van onderwerpen. Voor meer informatie over Kafka partitie ondersteuning, zie [de officiële Apache Kafka blog post over de toename van het aantal ondersteunde partities in versie 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Zie [Apache Kafka: onderwerpen wijzigen voor](https://kafka.apache.org/documentation/#basic_ops_modify_topic)meer informatie over het wijzigen van onderwerpen.

### <a name="number-of-replicas"></a>Aantal replica's

Hogere replicatiefactor resulteert in extra aanvragen tussen de partitieleider en volgers. Bijgevolg verbruikt een hogere replicatiefactor meer schijf en CPU om extra aanvragen af te handelen, waardoor de schrijflatentie toeneemt en de doorvoer daalt.

We raden u aan ten minste 3x replicatie te gebruiken voor Kafka in Azure HDInsight. De meeste Azure-regio's hebben drie foutdomeinen, maar in regio's met slechts twee foutdomeinen moeten gebruikers 4x-replicatie gebruiken.

Zie [Apache Kafka: replicatie](https://kafka.apache.org/documentation/#replication) en Apache [Kafka: toenemende replicatiefactor](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)voor meer informatie over replicatie.

## <a name="next-steps"></a>Volgende stappen

* [Biljoenen gebeurtenissen per dag verwerken met Apache Kafka in Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Wat is Apache Kafka in HDInsight?](apache-kafka-introduction.md)
