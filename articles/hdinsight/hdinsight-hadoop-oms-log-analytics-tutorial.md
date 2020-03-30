---
title: Azure Monitor-logboeken gebruiken om Azure HDInsight-clusters te controleren
description: Meer informatie over het gebruik van Azure Monitor-logboeken om taken te controleren die in een HDInsight-cluster worden uitgevoerd.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162783"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor-logboeken gebruiken om HDInsight-clusters te bewaken

Meer informatie over het inschakelen van Azure Monitor-logboeken om hadoop-clusterbewerkingen in HDInsight te controleren en hoe u een HDInsight-bewakingsoplossing toevoegt.

[Azure Monitor-logboeken](../log-analytics/log-analytics-overview.md) is een service in Azure Monitor die uw cloud- en on-premises omgevingen bewaakt om hun beschikbaarheid en prestaties te behouden. De service verzamelt gegevens afkomstig van resources in uw cloud- en on-premises omgevingen en van andere bewakingsprogramma's om analyse over meerdere resources aan te bieden.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Log Analytics-werkruimte. U deze werkruimte zien als een unieke Azure Monitor-logboekomgeving met een eigen gegevensopslagplaats, gegevensbronnen en oplossingen. Zie [Een werkruimte logboekanalyse maken](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)voor de instructies.

* Een Azure HDInsight-cluster. Momenteel u Azure Monitor-logboeken gebruiken met de volgende HDInsight-clustertypen:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Zie [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)voor de instructies voor het maken van een HDInsight-cluster.  

* Azure PowerShell Az-module.  Zie [Introductie van de nieuwe Azure PowerShell Az-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Zorg ervoor dat u de nieuwste versie hebt. Indien nodig, `Update-Module -Name Az`lopen .

> [!NOTE]  
> Het wordt aanbevolen om zowel het HDInsight-cluster als de loganalytics-werkruimte in dezelfde regio te plaatsen voor betere prestaties. Azure Monitor-logboeken zijn niet in alle Azure-regio's beschikbaar.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Azure Monitor-logboeken inschakelen met behulp van de portal

In deze sectie configureert u een bestaand HDInsight Hadoop-cluster om een Azure Log Analytics-werkruimte te gebruiken om taken, foutopsporingslogboeken, enz.

1. Selecteer uw cluster in de [Azure-portal.](https://portal.azure.com/)  Zie [Clusters weergeven en weergeven](./hdinsight-administer-use-portal-linux.md#showClusters) voor de instructies. Het cluster wordt geopend in een nieuwe portalpagina.

1. Selecteer Azure **Monitor**onder Controle aan de linkerkant . **Monitoring**

1. Selecteer in de hoofdweergave onder **Azure Monitor Integration**de optie **Inschakelen**.

1. Selecteer **in** de vervolgkeuzelijst Een werkruimte selecteren een bestaande werkruimte Log Analytics.

1. Selecteer **Opslaan**.  Het duurt een paar momenten om de instelling op te slaan.

    ![Monitoring inschakelen voor HDInsight-clusters](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Monitoring inschakelen voor HDInsight-clusters")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure Monitor-logboeken inschakelen met Azure PowerShell

U Azure Monitor-logboeken inschakelen met de cmdlet van de Azure PowerShell [Az-module Enable-AzHDInsightMonitoring.](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring)

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Als u het cmdlet [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) wilt uitschakelen:

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight-clusterbeheeroplossingen installeren

HDInsight biedt clusterspecifieke beheeroplossingen die u toevoegen voor Azure Monitor-logboeken. [Beheeroplossingen](../log-analytics/log-analytics-add-solutions.md) voegen functionaliteit toe aan Azure Monitor-logboeken en bieden extra gegevens- en analysetools. Deze oplossingen verzamelen belangrijke prestatiestatistieken uit uw HDInsight-clusters en bieden de tools om de statistieken te doorzoeken. Deze oplossingen bieden ook visualisaties en dashboards voor de meeste clustertypen die worden ondersteund in HDInsight. Door de statistieken te gebruiken die u met de oplossing verzamelt, u aangepaste controleregels en waarschuwingen maken.

Dit zijn de beschikbare HDInsight-oplossingen:

* HDInsight Hadoop Monitoring
* HDInsight HBase-monitoring
* HDInsight Interactive Query Monitoring
* HDInsight Kafka-monitoring
* HDInsight Spark-monitoring
* HDInsight Storm Monitoring

Zie [Beheeroplossingen in Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)voor de instructies voor het installeren van een beheeroplossing. Installeer een HDInsight Hadoop Monitoring-oplossing om te experimenteren. Wanneer dit is gedaan, ziet u een **HDInsightHadoop-tegel** die wordt weergegeven onder **Samenvatting**. Selecteer de tegel **HDInsightHadoop.** De HDInsightHadoop oplossing ziet eruit als:

![HDInsight monitoring oplossingsweergave](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Omdat het cluster een gloednieuw cluster is, worden er geen activiteiten weergegeven in het rapport.

## <a name="configuring-performance-counters"></a>Prestatiemeteritems configureren

Azure-monitor ondersteunt ook het verzamelen en analyseren van prestatiestatistieken voor de knooppunten in uw cluster. Zie [Linux-prestatiegegevensbronnen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)voor meer informatie over het inschakelen en configureren van deze functie.

## <a name="cluster-auditing"></a>Clustercontrole

HDInsight ondersteunt clustercontrole met Azure Monitor-logboeken door de volgende typen logboeken te importeren:

* `log_gateway_audit_CL`- deze tabel biedt controlelogboeken van clustergatewayknooppunten die geslaagde en mislukte inlogpogingen weergeven.
* `log_auth_CL`- deze tabel biedt SSH-logboeken met geslaagde en mislukte inlogpogingen.
* `log_ambari_audit_CL`- deze tabel bevat controlelogboeken van Ambari.
* `log_ranger_audti_CL`- deze tabel bevat auditlogs van Apache Ranger op ESP-clusters.

## <a name="next-steps"></a>Volgende stappen

* [Azure-monitorlogboeken voor query's om HDInsight-clusters te controleren](hdinsight-hadoop-oms-log-analytics-use-queries.md)
