---
title: Apache Kafka-workloads migreren naar Azure HDInsight 4.0
description: Meer informatie over het migreren van Apache Kafka-workloads op HDInsight 3.6 naar HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437019"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Apache Kafka-workloads migreren naar Azure HDInsight 4.0

Azure HDInsight 4.0 biedt de nieuwste open-source componenten met aanzienlijke verbeteringen in prestaties, connectiviteit en beveiliging. In dit document wordt uitgelegd hoe apache Kafka-workloads kunnen worden gemigreerd op HDInsight 3.6 naar HDInsight 4.0. Nadat u uw workloads hebt gemigreerd naar HDInsight 4.0, u veel van de nieuwe functies gebruiken die niet beschikbaar zijn op HDInsight 3.6.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Kafka-migratiepaden

HDInsight 3.6 ondersteunt twee versies van Kafka: 1.0.0 en 1.1.0. HDInsight 4.0 ondersteunt versies 1.1.0 en 2.1.0. Afhankelijk van welke versie van Kafka en welke versie van HDInsight u wilt uitvoeren, zijn er meerdere ondersteunde migratiepaden. Deze paden worden hieronder uitgelegd en geïllustreerd in het volgende diagram.

* **Voer zowel Kafka als HDInsight uit op de nieuwste versies (aanbevolen)**: Migreer een HDInsight 3.6 en Kafka 1.0.0 of 1.1.0 applicatie naar HDInsight 4.0 met Kafka 2.1.0 (paden D en E hieronder).
* **Voer HDInsight uit op de nieuwste versie, maar Kafka alleen op een recentere versie**: Migreer een HDInsight 3.6 en Kafka 1.0.0-toepassing naar HDInsight 4.0 met Kafka 1.1.0 (pad B hieronder).
* **Voer HDInsight uit op de nieuwste versie, bewaar kafka-versie**: Migreer een HDInsight 3.6- en Kafka 1.1.0-toepassing naar HDInsight 4.0 met Kafka 1.1.0 (pad C hieronder).
* **Voer Kafka uit op een recentere versie, bewaar de HDInsight-versie**: Migreer een Kafka 1.0.0-toepassing naar 1.1.0 en blijf op HDInsight 3.6 (pad A hieronder). Houd er rekening mee dat voor deze optie nog steeds een nieuw cluster moet worden geïmplementeerd. Het upgraden van de Kafka-versie op een bestaand cluster wordt niet ondersteund. Nadat u een cluster hebt gemaakt met de gewenste versie, migreert u uw Kafka-clients om het nieuwe cluster te gebruiken.

![Upgradepaden voor Apache Kafka op 3.6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka versies

### <a name="kafka-110"></a>Kafka 1.1.0
  
Als u migreert van Kafka 1.0.0 naar 1.1.0 u profiteren van de volgende nieuwe functies:

* Verbeteringen aan de Kafka-controller versnellen de gecontroleerde uitschakeling, zodat u brokers opnieuw starten en sneller herstellen van problemen. 
* Verbeteringen in de [logica FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) waarmee u meer partities (en dus meer onderwerpen) op het cluster hebben. 
* Kafka Connect ondersteunt [recordheaders](https://issues.apache.org/jira/browse/KAFKA-5142) en [reguliere expressies](https://issues.apache.org/jira/browse/KAFKA-3073) voor onderwerpen. 

Zie [Apache Kafka 1.1 release notes](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)voor een volledige lijst met updates.

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Als u migreert naar Kafka 2.1, u profiteren van de volgende functies:

* Betere makelaartolerantie dankzij een verbeterd replicatieprotocol.
* Nieuwe functionaliteit in de KafkaAdminClient API.
* Configureerbaar quotabeheer.
* Ondersteuning voor Zstandard compressie.

Zie [Apache Kafka 2.0 release notes](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) en [Apache Kafka 2.1 release notes](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)voor een volledige lijst met updates.

## <a name="kafka-client-compatibility"></a>Compatibiliteit met Kafka-client

Nieuwe Kafka makelaars ondersteunen oudere klanten. [KIP-35 - Retrieving protocol versie](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) introduceerde een mechanisme voor het dynamisch bepalen van de functionaliteit van een Kafka makelaar en [KIP-97: Verbeterde Kafka Client RPC Compatibiliteitsbeleid](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) introduceerde een nieuw compatibiliteitsbeleid en garanties voor de Java-client. Voorheen moest een Kafka-klant communiceren met een makelaar van dezelfde versie of een nieuwere versie. Nu, nieuwere versies van de Java-clients en andere clients `librdkafka` die KIP-35 ondersteunen, zoals kan terugvallen op oudere aanvraagtypen of gooi passende fouten als functionaliteit niet beschikbaar is.

![Kafka-clientcompatibiliteit bijwerken](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Merk op dat het niet betekent dat de klant oudere makelaars ondersteunt.  Zie [Compatibiliteitsmatrix](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)voor meer informatie .

## <a name="general-migration-process"></a>Algemeen migratieproces

De volgende migratierichtlijnen gaan uit van een Apache Kafka 1.0.0- of 1.1.0-cluster die is geïmplementeerd op HDInsight 3.6 in één virtueel netwerk. De bestaande makelaar heeft een aantal onderwerpen en wordt actief gebruikt door producenten en consumenten.

![Huidige Kafka veronderstelde omgeving](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Ga als volgt te werk om de migratie te voltooien:

1. **Implementeer een nieuw HDInsight 4.0-cluster en clients voor test.** Implementeer een nieuw HDInsight 4.0 Kafka-cluster. Als meerdere Kafka-clusterversies kunnen worden geselecteerd, wordt aanbevolen om de nieuwste versie te selecteren. Stel na implementatie een aantal parameters in en maakt een onderwerp met dezelfde naam als uw bestaande omgeving. Stel ook TLS en bring-your-own-key (BYOK) encryptie in als dat nodig is. Controleer vervolgens of het goed werkt met het nieuwe cluster.

    ![Nieuwe HDInsight 4.0-clusters implementeren](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Schakel het cluster voor de producenttoepassing en wacht tot alle wachtrijgegevens door de huidige consumenten worden verbruikt.** Wanneer het nieuwe HDInsight 4.0 Kafka-cluster klaar is, schakelt u de bestaande productiebestemming over naar het nieuwe cluster. Laat het zoals het is totdat de bestaande Consumenten-app alle gegevens uit het bestaande cluster heeft verbruikt.

    ![Switch-cluster voor producentenapp](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Schakel het cluster in op de consumententoepassing.** Nadat u hebt bevestigd dat de bestaande consumententoepassing klaar is met het consumeren van alle gegevens uit het bestaande cluster, schakelt u de verbinding over naar het nieuwe cluster.

    ![Cluster schakelen op consumenten-app](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Verwijder het oude cluster en test toepassingen indien nodig.** Zodra de schakelaar is voltooid en goed werkt, verwijdert u het oude HDInsight 3.6 Kafka-cluster en de producenten en consumenten die in de test worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

* [Prestaties van Apache Kafka HDInsight-clusters optimaliseren](apache-kafka-performance-tuning.md)
* [Snelstart: Apache Kafka-cluster maken in Azure HDInsight met Azure-portal](apache-kafka-get-started.md)
