---
title: Veelgestelde vragen over Apache Kafka in Azure HDInsight
description: Krijg antwoorden op veelgestelde vragen over Apache Kafka op Azure HDInsight, een beheerde Hadoop-cloudservice.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: ff4079263fd7afb02e132a798997687fad7e9961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206976"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Veelgestelde vragen over Apache Kafka in Azure HDInsight

In dit artikel worden enkele veelgestelde vragen over het gebruik van Apache Kafka op Azure HDInsight beantwoord.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Welke Kafka-versies worden ondersteund door HDInsight?

Meer informatie over HDInsight officieel ondersteunde componentversies vinden in [Wat zijn de Apache Hadoop componenten en versies beschikbaar met HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). We raden u aan altijd de nieuwste versie te gebruiken om de best mogelijke prestaties en gebruikerservaring te garanderen.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Welke bronnen worden geleverd in een HDInsight Kafka-cluster en voor welke bronnen moet ik betalen?

Een HDInsight Kafka-cluster bevat de volgende bronnen:

* Hoofdknooppunten
* Dierenverzorgerknooppunten
* Broker (werknemer) knooppunten 
* Azure Managed Disks die zijn gekoppeld aan de broker-knooppunten
* Gatewayknooppunten

Al deze resources worden in rekening gebracht op basis van ons [HDInsight-prijsmodel,](https://azure.microsoft.com/pricing/details/hdinsight/)met uitzondering van gatewayknooppunten. Er worden geen kosten in rekening gebracht voor gatewayknooppunten.

Zie [Azure HDInsight virtuele netwerkarchitectuur](../hdinsight-virtual-network-architecture.md)voor een meer gedetailleerde beschrijving van verschillende knooppunttypen. Prijzen zijn gebaseerd op het gebruik per minuut knooppunt. De prijzen variëren afhankelijk van de grootte van het knooppunt, het aantal knooppunten, het type gebruikte beheerde schijf en de regio.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Werken Apache Kafka API's met HDInsight?

Ja, HDInsight maakt gebruik van native Kafka API's. Uw clienttoepassingscode hoeft niet te worden gewijzigd. Zie [Zelfstudie: Gebruik de Apache Kafka-producenten en consumenten-API's](./apache-kafka-producer-consumer-api.md) om te zien hoe u api's voor java-gebaseerde producenten/consumenten gebruiken met uw cluster.

## <a name="can-i-change-cluster-configurations"></a>Kan ik clusterconfiguraties wijzigen?

Ja, via het Ambari portaal. Elk onderdeel in de portal heeft een **configs** sectie, die kan worden gebruikt om componentconfiguraties te wijzigen. Voor sommige wijzigingen is mogelijk vereist dat de makelaar opnieuw wordt opgestart.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Welk type verificatie ondersteunt HDInsight voor Apache Kafka?

Met behulp van [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md)u beveiliging op onderwerpniveau krijgen voor hun Kafka-clusters. Zie [Zelfstudie: Apache Kafka-beleid configureren in HDInsight met enterprise security package (Preview)](../domain-joined/apache-domain-joined-run-kafka.md)voor meer informatie.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Zijn mijn gegevens versleuteld? Kan ik mijn eigen sleutels gebruiken?

Alle Kafka-berichten op de beheerde schijven worden versleuteld met [Azure Storage Service Encryption (SSE).](../../storage/common/storage-service-encryption.md) Data-in-transit (bijvoorbeeld gegevens die worden verzonden van clients naar makelaars en andersom) worden niet standaard versleuteld. Het is mogelijk om dergelijk verkeer te versleutelen door [ssl op uw eigen.](./apache-kafka-ssl-encryption-authentication.md) Bovendien u met HDInsight hun eigen sleutels beheren om de gegevens in rust te versleutelen. Zie [door de klant beheerde sleutelschijfversleuteling](../disk-encryption.md)voor meer informatie.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Hoe verbind ik clients met mijn cluster?

Om kafka-klanten te laten communiceren met Kafka-makelaars, moeten ze de makelaars via het netwerk kunnen bereiken. Voor HDInsight-clusters is het Virtuele Netwerk (VNet) de beveiligingsgrens. Daarom is de eenvoudigste manier om clients te verbinden met uw HDInsight-cluster door clients te maken binnen hetzelfde VNet als het cluster. Andere scenario's zijn:

* Clients verbinden in een andere Azure VNet – Peer het cluster VNet en de client VNet en configureer het cluster voor [IP Advertising.](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising) Bij het gebruik van IP-advertenties moeten Kafka-clients IP-adressen van Broker gebruiken om verbinding te maken met de brokers, in plaats van volledig gekwalificeerde domeinnamen (FQDN's).

* On-premises clients verbinden – Een VPN-netwerk gebruiken en aangepaste DNS-servers instellen zoals beschreven in [Een virtueel netwerk plannen voor Azure HDInsight.](../hdinsight-plan-virtual-network-deployment.md)

* Een openbaar eindpunt maken voor uw Kafka-service: als uw bedrijfsbeveiligingsvereisten dit toestaan, u een openbaar eindpunt implementeren voor uw Kafka-makelaars of een zelfbeheerd open-source REST-eindpunt met een openbaar eindpunt.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Kan ik meer schijfruimte toevoegen aan een bestaand cluster?

Als u de beschikbare ruimte voor Kafka-berichten wilt vergroten, u het aantal knooppunten verhogen. Momenteel wordt het toevoegen van meer schijven aan een bestaand cluster niet ondersteund.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kan een Kafka-cluster werken met Databricks? 

Ja, Kafka-clusters kunnen werken met Databricks zolang ze zich in hetzelfde VNet bevinden. Als u een Kafka-cluster met Databricks wilt gebruiken, maakt u een VNet met een HDInsight Kafka-cluster erin en geeft u dat VNet op wanneer u uw Databricks-werkruimte maakt en VNet-injectie gebruikt. Zie [Azure Databricks implementeren in uw Azure Virtual Network (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)voor meer informatie. U moet de namen van de bootstrap-makelaar van het Kafka-cluster opgeven bij het maken van de werkruimte Databricks. Zie [De hostinformatie van Apache Zookeeper en Broker voor](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)informatie over het ophalen van de namen van de Kafka-makelaar.

## <a name="how-can-i-have-maximum-data-durability"></a>Hoe kan ik maximale gegevensduurzaamheid hebben?

Met de duurzaamheid van gegevens u het laagste risico op berichtverlies bereiken. Om maximale duurzaamheid van gegevens te bereiken, raden we de volgende instellingen aan:

* gebruik een minimale replicatiefactor van 3 in de meeste regio's
* gebruik een minimale replicatiefactor van 4 in regio's met slechts twee foutdomeinen
* uitschakelen onreine leider verkiezingen
* **min.insync.replica's** instellen op 2 of meer - dit verandert het aantal replica's dat volledig synchroon moet zijn met de leider voordat een schrijven kan doorgaan
* stel de **acks** eigenschap op **alle** - deze eigenschap vereist alle replica's om alle berichten te erkennen

Het configureren van Kafka voor een hogere gegevensconsistentie is van invloed op de beschikbaarheid van makelaars om aanvragen te produceren.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Kan ik mijn gegevens repliceren naar meerdere clusters?

Ja, gegevens kunnen worden gerepliceerd naar meerdere clusters met Kafka MirrorMaker. Zie details over het opzetten van MirrorMaker kan worden gevonden in [Mirror Apache Kafka onderwerpen](apache-kafka-mirroring.md). Daarnaast zijn er andere zelfbeheerde open-source technologieën en leveranciers die kunnen helpen bij het bereiken van replicatie naar meerdere clusters zoals [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Kan ik mijn cluster upgraden? Hoe moet ik mijn cluster upgraden?

We ondersteunen momenteel geen upgrades van in-place clusterversies. Als u uw cluster wilt bijwerken naar een hogere Kafka-versie, maakt u een nieuw cluster met de gewenste versie en migreert u uw Kafka-clients om het nieuwe cluster te gebruiken.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Hoe houd ik mijn Kafka-cluster in de gaten?

Gebruik Azure-monitor om uw [Kafka-logboeken](./apache-kafka-log-analytics-operations-management.md)te analyseren.

## <a name="next-steps"></a>Volgende stappen

* [Ssl-versleuteling en -verificatie (Secure Sockets Layer) instellen voor Apache Kafka in Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [MirrorMaker gebruiken om Apache Kafka-onderwerpen te repliceren met Kafka in HDInsight](./apache-kafka-mirroring.md)
