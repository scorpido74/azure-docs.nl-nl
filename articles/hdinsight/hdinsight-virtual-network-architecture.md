---
title: Azure HDInsight virtuele netwerkarchitectuur
description: Meer informatie over de beschikbare bronnen wanneer u een HDInsight-cluster maakt in een Azure Virtual Network.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b3f622b360f565ef5b16d5376cb1aa2498655017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272147"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight virtuele netwerkarchitectuur

In dit artikel worden de resources uitgelegd die aanwezig zijn wanneer u een HDInsight-cluster implementeert in een aangepast Azure Virtual Network. Met deze informatie u on-premises bronnen verbinden met uw HDInsight-cluster in Azure. Zie Wat is Azure Virtual Network voor meer informatie over Azure Virtual [Networks?](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Brontypen in Azure HDInsight-clusters

Azure HDInsight-clusters hebben verschillende typen virtuele machines of knooppunten. Elk knooppunttype speelt een rol bij de werking van het systeem. In de volgende tabel worden deze knooppunttypen en hun rollen in het cluster samengevat.

| Type | Beschrijving |
| --- | --- |
| Hoofdknooppunt |  Voor alle clustertypen, behalve Apache Storm, hosten de hoofdknooppunten de processen die de uitvoering van de gedistribueerde toepassing beheren. Het hoofdknooppunt is ook het knooppunt dat u SSH in en uitvoeren van toepassingen die vervolgens worden gecoördineerd om te draaien over de clusterbronnen. Het aantal hoofdknooppunten is vastgesteld op twee voor alle clustertypen. |
| ZooKeeper knooppunt | Zookeeper coördineert taken tussen de knooppunten die gegevensverwerking uitvoeren. Het doet ook leider verkiezing van het hoofdknooppunt, en houdt bij welke hoofdnode een specifieke hoofddienst in werking stelt. Het aantal ZooKeeper-knooppunten is vastgesteld op drie. |
| Worker node | Vertegenwoordigt de knooppunten die de functionaliteit voor gegevensverwerking ondersteunen. Worker-knooppunten kunnen uit het cluster worden toegevoegd of verwijderd om de computercapaciteit te schalen en kosten te beheren. |
| R Server edge-knooppunt | Het randknooppunt van R Server vertegenwoordigt het knooppunt dat u SSH in en voert toepassingen uit die vervolgens worden gecoördineerd om over de clusterbronnen te worden uitgevoerd. Een randknooppunt neemt niet deel aan gegevensanalyse binnen het cluster. Dit knooppunt host ook R Studio Server, zodat u de R-toepassing uitvoeren met een browser. |
| Regioknooppunt | Voor het clustertype HBase wordt op het gebiedsknooppunt (ook wel gegevensknooppunt genoemd) de regioserver uitgevoerd. Regioservers serveren en beheren een deel van de gegevens die door HBase worden beheerd. Regioknooppunten kunnen worden toegevoegd of verwijderd uit het cluster om de computercapaciteit te schalen en kosten te beheren.|
| Nimbus knooppunt | Voor het clustertype Storm biedt het Nimbus-knooppunt functionaliteit die vergelijkbaar is met het hoofdknooppunt. Het Nimbus-knooppunt wijst taken toe aan andere knooppunten in een cluster via Zookeeper, die de werking van Stormtopologieën coördineert. |
| Supervisor knooppunt | Voor het clustertype Storm voert het supervisorknooppunt de instructies uit die door het Nimbus-knooppunt worden gegeven om de gewenste verwerking uit te voeren. |

## <a name="resource-naming-conventions"></a>Conventies voor het benoemen van resources

Gebruik volledig gekwalificeerde domeinnamen (FQDN's) bij het aanpakken van knooppunten in uw cluster. U de FQDN's voor verschillende knooppunttypen in uw cluster krijgen met behulp van de [Ambari API.](hdinsight-hadoop-manage-ambari-rest-api.md) 

Deze FQDN's zijn `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`van het formulier.

Het `<node-type-prefix>` zal *hn* voor headnodes, *wn* voor werknemer knooppunten en *zn* voor zookeeper knooppunten.

Als u alleen de hostnaam nodig hebt, gebruikt u alleen het eerste deel van de FQDN:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Basisbronnen voor virtuele netwerken

In het volgende diagram ziet u de plaatsing van HDInsight-knooppunten en netwerkbronnen in Azure.

![Diagram van HDInsight-entiteiten die zijn gemaakt in het aangepaste VNET voor Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

De standaardresources die aanwezig zijn wanneer HDInsight wordt geïmplementeerd in een Azure Virtual Network, bevatten de clusterknooppunttypen die in de vorige tabel worden genoemd, evenals netwerkapparaten die communicatie tussen het virtuele netwerk en externe netwerken ondersteunen.

In de volgende tabel worden de negen clusterknooppunten samengevat die worden gemaakt wanneer HDInsight wordt geïmplementeerd in een aangepast Azure Virtual Network.

| Resourcetype | Aantal aanwezig | Details |
| --- | --- | --- |
|Hoofdknooppunt | twee |    |
|Zookeeper-knooppunt | drie | |
|Worker node | twee | Dit aantal kan variëren op basis van clusterconfiguratie en schaling. Er zijn minimaal drie werkknooppunten nodig voor Apache Kafka.  |
|Gateway-knooppunt | twee | Gatewayknooppunten zijn Azure virtuele machines die zijn gemaakt op Azure, maar niet zichtbaar zijn in uw abonnement. Neem contact op met de ondersteuning als u deze knooppunten opnieuw wilt opstarten. |

De volgende aanwezige netwerkbronnen worden automatisch gemaakt binnen het virtuele netwerk dat wordt gebruikt met HDInsight:

| Netwerkbron | Aantal aanwezig | Details |
| --- | --- | --- |
|Load balancer | drie | |
|Netwerkinterfaces | Negen | Deze waarde is gebaseerd op een normaal cluster, waarbij elk knooppunt zijn eigen netwerkinterface heeft. De negen interfaces zijn voor de twee hoofdknooppunten, drie zookeeperknooppunten, twee werkknooppunten en twee gatewayknooppunten die in de vorige tabel worden genoemd. |
|Openbare IP-adressen | twee |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Eindpunten voor verbinding met HDInsight

U uw HDInsight-cluster op drie manieren openen:

- Een HTTPS-eindpunt buiten het `CLUSTERNAME.azurehdinsight.net`virtuele netwerk bij .
- Een SSH-eindpunt voor het direct aansluiten `CLUSTERNAME-ssh.azurehdinsight.net`op de headnode op .
- Een HTTPS-eindpunt binnen `CLUSTERNAME-int.azurehdinsight.net`het virtuele netwerk . Let op de "-int" in deze URL. Dit eindpunt wordt opgelost tot een privé-IP in dat virtuele netwerk en is niet toegankelijk vanaf het openbare internet.

Deze drie eindpunten krijgen elk een load balancer toegewezen.

Openbare IP-adressen worden ook verstrekt aan de twee eindpunten die verbinding van buiten het virtuele netwerk mogelijk maken.

1. Eén openbaar IP-adres wordt toegewezen aan de load balancer die de volledig gekwalificeerde domeinnaam (FQDN) kan gebruiken wanneer deze verbinding maakt met het cluster vanaf het internet. `CLUSTERNAME.azurehdinsight.net`
1. Het tweede openbare IP-adres wordt gebruikt `CLUSTERNAME-ssh.azurehdinsight.net`voor de alleen SSH-domeinnaam.

## <a name="next-steps"></a>Volgende stappen

- [Veilig binnenkomend verkeer naar HDInsight-clusters in een virtueel netwerk met privéeindpunt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
