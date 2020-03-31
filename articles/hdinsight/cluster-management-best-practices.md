---
title: Aanbevolen procedures voor clusterbeheer - Azure HDInsight
description: Leer best practices voor het beheren van HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782019"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Best practices voor hdinsight-clusterbeheer

Leer best practices voor het beheren van HDInsight-clusters.

## <a name="how-do-i-create-hdinsight-clusters"></a>Hoe maak ik HDInsight-clusters?

| Optie | Documenten |
|---|---|
| Azure Data Factory | [Apache Hadoop-clusters op aanvraag maken in HDInsight met behulp van Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Sjabloon Aangepaste resourcebeheer | [Apache Hadoop-clusters maken in HDInsight met behulp van Resource Manager-sjablonen](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Snelstartsjablonen | [HDInsight Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure-voorbeelden | [HDInsight Azure-voorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure Portal | [Linux-gebaseerde clusters maken in HDInsight met behulp van de Azure-portal](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure-CLI | [HDInsight-clusters maken met de Azure CLI](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Linux-gebaseerde clusters maken in HDInsight met Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Apache Hadoop-clusters maken met behulp van de Azure REST API](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDKs (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Als u een cluster maakt en de clusternaam van een eerder gemaakt cluster opnieuw gebruikt, wacht u totdat de vorige clusterverwijdering is voltooid voordat u het cluster maakt.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Hoe pas ik HDInsight-clusters aan?

| Optie | Documenten |
|---|---|
| Scriptacties | [Azure HDInsight-clusters aanpassen met behulp van scriptacties](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [HDInsight-clusters aanpassen met Bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Externe metastores | [Externe metagegevensopslag gebruiken in Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Aangepaste Ambari-database | [HDInsight-clusters instellen met een aangepaste Ambari DB](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Wat zijn enkele fouten die ik zou kunnen worden geconfronteerd bij het maken van clusters?

| Fout | Meer informatie |
|---|---|
| Geen quotum | Er zijn quota voor het aantal quota dat u in elke regio op uw abonnement maken. Zie [Capaciteitsplanning: quota voor](./hdinsight-capacity-planning.md)meer informatie. |
| Geen IP-adressen meer beschikbaar | Elke VNet heeft een beperkt aantal IP-adressen. Wanneer u een HDInsight-cluster maakt, gebruikt elk knooppunt (inclusief zookeeper en gatewayknooppunten) een aantal van deze toegewezen IP-adressen. Wanneer alle IP-adressen in gebruik zijn, zult u deze fout tegenkomen.  |
| NsG-regels (Network Security Group) staan communicatie met HDInsight-resourceproviders niet toe | Als u NSG's of door de gebruiker gedefinieerde routes (UDR's) gebruikt om binnenkomend verkeer naar uw HDInsight-cluster te beheren, moet u ervoor zorgen dat uw cluster kan communiceren met kritieke Azure-status- en beheerservices. Zie [NSG-servicetags (Network security group) voor Azure HDInsight voor](./hdinsight-service-tags.md) meer informatie |
| Hergebruik van clusternaam | Wanneer u een clusternaam gebruikt die u eerder hebt gebruikt, moet u x-aantal minuten wachten voordat u het cluster opnieuw maakt. Anders ziet u een bericht dat de resource al bestaat. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Hoe beheer ik het uitvoeren van HDInsight-clusters?

| Optie | Documenten |
|---|---|
| Automatisch schalen | [Azure HDInsight-clusters automatisch schalen](./hdinsight-autoscale-clusters.md) |
| Handmatige schaalaanpassing | [Azure HDInsight-clusters schalen](./hdinsight-scaling-best-practices.md) |
| Monitoring met Ambari| [Clusterprestaties bewaken in Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Controle met Azure Monitor-logboeken | [Azure Monitor-logboeken gebruiken om HDInsight-clusters te bewaken](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Hoe controleer ik verwijderde HDInsight-clusters?

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

U de volgende query met Azure Monitor-logboeken gebruiken om verwijderde clusters te controleren.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Volgende stappen

* [Capaciteitsplanning voor HDInsight-clusters](./hdinsight-capacity-planning.md)
* [Wat zijn de standaard- en aanbevolen knooppuntconfiguraties voor Azure HDInsight?](./hdinsight-supported-node-configuration.md)