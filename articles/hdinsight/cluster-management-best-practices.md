---
title: Aanbevolen procedures voor cluster beheer-Azure HDInsight
description: Leer de aanbevolen procedures voor het beheren van HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74782019"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Best practices voor het beheer van HDInsight-cluster

Leer de aanbevolen procedures voor het beheren van HDInsight-clusters.

## <a name="how-do-i-create-hdinsight-clusters"></a>Hoe kan ik HDInsight-clusters maken?

| Optie | Documenten |
|---|---|
| Azure Data Factory | [Apache Hadoop-clusters op aanvraag maken in HDInsight met behulp van Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Aangepaste Resource Manager-sjabloon | [Apache Hadoop clusters maken in HDInsight met behulp van Resource Manager-sjablonen](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Snelstartsjablonen | [Quick Start-sjablonen voor HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure-voorbeelden | [Voor beelden van HDInsight Azure](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure Portal | [Op Linux gebaseerde clusters maken in HDInsight met behulp van de Azure Portal](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [HDInsight-clusters maken met behulp van Azure CLI](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Op Linux gebaseerde clusters maken in HDInsight met behulp van Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Apache Hadoop clusters maken met behulp van de Azure-REST API](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| Sdk's (.NET, Python, Java) | [.Net](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Als u een cluster maakt en de cluster naam opnieuw gebruikt vanuit een eerder gemaakt cluster, wacht u totdat het vorige cluster is verwijderd voordat u het cluster maakt.

## <a name="how-do-i-customize-hdinsight-clusters"></a>HDInsight-clusters Hoe kan ik aanpassen?

| Optie | Documenten |
|---|---|
| Script acties | [Azure HDInsight-clusters aanpassen met behulp van script acties](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [HDInsight-clusters aanpassen met Boots trap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Externe meta Stores | [Externe metagegevensopslag gebruiken in Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Aangepaste Ambari-database | [HDInsight-clusters met een aangepaste Ambari-data base instellen](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Wat zijn de fouten die ik kan ondervinden bij het maken van clusters?

| Fout | Meer informatie |
|---|---|
| Geen quota | Er zijn quota's voor het aantal quota's dat u in elke regio kunt maken voor uw abonnement. Zie [capaciteits planning: quota's](./hdinsight-capacity-planning.md)voor meer informatie. |
| Er zijn geen IP-adressen meer beschikbaar | Elk VNet heeft een beperkt aantal IP-adressen. Wanneer u een HDInsight-cluster maakt, gebruikt elk knoop punt (met inbegrip van Zookeeper-en gateway knooppunten) enkele van deze toegewezen IP-adressen. Wanneer alle IP-adressen in gebruik zijn, ontvangt u deze fout melding.  |
| De regels voor de netwerk beveiligings groep (NSG) staan communicatie met HDInsight-resource providers niet toe | Als u Nsg's of door de gebruiker gedefinieerde routes (Udr's) gebruikt om inkomend verkeer naar uw HDInsight-cluster te beheren, moet u ervoor zorgen dat uw cluster kan communiceren met de essentiële Azure-status-en beheer Services. Zie voor meer informatie [service tags voor netwerk beveiligings groepen (NSG) voor Azure HDInsight](./hdinsight-service-tags.md) |
| Cluster naam opnieuw gebruiken | Wanneer u een cluster naam gebruikt die u eerder hebt gebruikt, moet u X aantal minuten wachten voordat u het cluster opnieuw maakt. Anders wordt er een bericht weer gegeven dat de resource al bestaat. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Hoe kan ik actieve HDInsight-clusters beheren?

| Optie | Documenten |
|---|---|
| Automatisch schalen | [Automatisch schalen van Azure HDInsight-clusters](./hdinsight-autoscale-clusters.md) |
| Handmatige schaalaanpassing | [Azure HDInsight-clusters schalen](./hdinsight-scaling-best-practices.md) |
| Bewaken met Ambari| [Cluster prestaties in azure HDInsight bewaken](./hdinsight-key-scenarios-to-monitor.md) |
| Bewaking met Azure Monitor-logboeken | [Azure Monitor-logboeken gebruiken om HDInsight-clusters te bewaken](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Hoe kan ik de verwijderde HDInsight-clusters controleren?

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

U kunt de volgende query met Azure Monitor-Logboeken gebruiken om verwijderde clusters te bewaken.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Volgende stappen

* [Capaciteitsplanning voor HDInsight-clusters](./hdinsight-capacity-planning.md)
* [Wat zijn de standaard en aanbevolen knooppunt configuraties voor Azure HDInsight?](./hdinsight-supported-node-configuration.md)