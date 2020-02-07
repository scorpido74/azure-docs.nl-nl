---
title: Azure Monitor Logboeken gebruiken voor het bewaken van Azure HDInsight-clusters
description: Meer informatie over het gebruik van Azure Monitor-logboeken voor het bewaken van taken die worden uitgevoerd in een HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: 980569edf8322c6c22a4357a5b946ded85f0ebe4
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063731"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor Logboeken gebruiken om HDInsight-clusters te bewaken

Informatie over het inschakelen van Azure Monitor logboeken voor het bewaken van Hadoop-cluster bewerkingen in HDInsight en het toevoegen van een HDInsight-bewakings oplossing.

[Azure monitor-logboeken](../log-analytics/log-analytics-overview.md) is een service in azure monitor die uw Cloud-en on-premises omgevingen bewaakt om hun Beschik baarheid en prestaties te behouden. De service verzamelt gegevens afkomstig van resources in uw cloud- en on-premises omgevingen en van andere bewakingsprogramma's om analyse over meerdere resources aan te bieden.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Log Analytics-werkruimte. U kunt deze werk ruimte zien als een unieke Azure Monitor-logboek omgeving met een eigen gegevensopslag plaats, gegevens bronnen en oplossingen. Zie [een log Analytics-werk ruimte maken](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)voor instructies.

* Een Azure HDInsight-cluster. Op dit moment kunt u Azure Monitor-Logboeken gebruiken met de volgende HDInsight-cluster typen:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Zie [aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)voor instructies over het maken van een HDInsight-cluster.  

* Azure PowerShell AZ-module.  Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Het verdient aanbeveling om zowel het HDInsight-cluster en de Log Analytics-werkruimte in dezelfde regio voor betere prestaties. Azure Monitor-logboeken zijn niet beschikbaar in alle Azure-regio's.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Azure Monitor logboeken inschakelen met behulp van de portal

In deze sectie configureert u een bestaand HDInsight Hadoop-cluster voor het gebruik van een Azure Log Analytics-werkruimte voor het bewaken van taken, logboeken voor foutopsporing, enz.

1. Selecteer uw cluster in de [Azure Portal](https://portal.azure.com/).  Zie de [lijst en clusters weer geven](./hdinsight-administer-use-portal-linux.md#showClusters) voor de instructies. Het cluster wordt geopend op een nieuwe portal-pagina.

1. Selecteer aan de linkerkant, onder **bewaking**, de optie **Azure monitor**.

1. Selecteer in de hoofd weergave onder **Azure monitor-integratie**de optie **inschakelen**.

1. Selecteer een bestaande Log Analytics-werk ruimte in de vervolg keuzelijst **Selecteer een werk ruimte** .

1. Selecteer **Opslaan**.  Het duurt een paar minuten om op te slaan van de instelling.

    ![Bewaking voor HDInsight-clusters inschakelen](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Bewaking voor HDInsight-clusters inschakelen")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure Monitor logboeken inschakelen met behulp van Azure PowerShell

U kunt Azure Monitor logboeken inschakelen met behulp van de Azure PowerShell AZ module [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) cmdlet.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Als u wilt uitschakelen, gebruikt u de cmdlet [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) :

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installeren van HDInsight-cluster-beheeroplossingen

HDInsight biedt platformspecifieke beheer oplossingen die u kunt toevoegen voor Azure Monitor-Logboeken. [Beheer oplossingen](../log-analytics/log-analytics-add-solutions.md) voegen functionaliteit toe aan Azure monitor logboeken, waardoor er aanvullende gegevens-en analyse Programma's worden geleverd. Deze oplossingen belangrijke prestatiegegevens verzamelen van uw HDInsight-clusters en over de hulpprogramma's om te zoeken naar de metrische gegevens. Deze oplossingen bieden ook visualisaties en dashboards voor de meeste clustertypen in HDInsight wordt ondersteund. Met behulp van de metrische gegevens die u verzamelt van de oplossing, kunt u aangepaste regels voor bewaking en waarschuwingen kunt maken.

Dit zijn de beschikbare HDInsight-oplossingen:

* HDInsight Hadoop-bewaking
* HDInsight HBase-bewaking
* HDInsight Interactive Query-bewaking
* HDInsight Kafka-bewaking
* HDInsight Spark-bewaking
* HDInsight Storm-bewaking

Zie [beheer oplossingen in azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)voor instructies voor het installeren van een beheer oplossing. Als u wilt experimenteren, installeert u een HDInsight Hadoop-bewakings oplossing. Wanneer u klaar bent, ziet u een **HDInsightHadoop** -tegel die wordt weer gegeven onder **samen vatting**. Selecteer de tegel **HDInsightHadoop** . De oplossing HDInsightHadoop ziet eruit zoals:

![Bewakingsweergave oplossing voor HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Omdat het cluster een nieuwe cluster is, weergeven niet alle activiteiten in het rapport.

## <a name="configuring-performance-counters"></a>Prestatie meter items configureren

Azure monitor biedt ook ondersteuning voor het verzamelen en analyseren van prestatie gegevens voor de knoop punten in uw cluster. Zie [Linux-prestatie gegevens bronnen in azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)voor meer informatie over het inschakelen en configureren van deze functie.

## <a name="cluster-auditing"></a>Cluster controle

HDInsight ondersteunt cluster controle met Azure Monitor-logboeken door de volgende typen logboeken te importeren:

* `log_gateway_audit_CL`: deze tabel bevat audit logboeken van cluster gateway knooppunten die geslaagde en mislukte aanmeldings pogingen tonen.
* `log_auth_CL`: deze tabel bevat SSH-logboeken met geslaagde en mislukte aanmeldings pogingen.
* `log_ambari_audit_CL`: deze tabel bevat audit logboeken van Ambari.
* `log_ranger_audti_CL`: deze tabel bevat audit logboeken van Apache zwerver op ESP-clusters.

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor logboeken voor het controleren van HDInsight-clusters doorzoeken](hdinsight-hadoop-oms-log-analytics-use-queries.md)
