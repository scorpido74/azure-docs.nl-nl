---
title: Prestatiebewaking met Azure Monitor-logboeken
description: Meer informatie over het instellen van de Log Analytics Agent voor het bewaken van containers en prestatiemeteritems voor uw Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: c3c1bf511f3313e7408d6ce90b73de60bd1309f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366735"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Prestatiebewaking met Azure Monitor-logboeken

In dit artikel vindt u de stappen om de logboekanalyse-agent toe te voegen als een extensie voor een virtuele machineschaalset aan uw cluster en deze te verbinden met uw bestaande Azure Log Analytics-werkruimte. Dit maakt het verzamelen van diagnostische gegevens over containers, toepassingen en prestatiebewaking mogelijk. Door deze toe te voegen als een uitbreiding aan de bron voor de virtuele machineschaalset, zorgt Azure Resource Manager ervoor dat het op elk knooppunt wordt geïnstalleerd, zelfs wanneer het cluster wordt geschaald.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u al een Azure Log Analytics-werkruimte hebt ingesteld. Als u dit niet doet, gaat u naar [Azure Monitor-logboeken instellen](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>De agentextensie toevoegen via Azure CLI

De beste manier om de loganalytics-agent aan uw cluster toe te voegen, is via de API's met de virtuele machineschaalset die beschikbaar is met de Azure CLI. Als Azure CLI nog niet is ingesteld, gaat u naar Azure-portal en opent u een [Cloud Shell-exemplaar](../cloud-shell/overview.md) of [installeert u de Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli)

1. Zodra uw Cloud Shell is aangevraagd, moet u ervoor zorgen dat u in hetzelfde abonnement werkt als uw resource. Controleer dit `az account show` met en zorg ervoor dat de waarde 'naam' overeenkomt met die van het abonnement van uw cluster.

2. Navigeer in de portal naar de resourcegroep waarin de werkruimte Log Analytics zich bevindt. Klik op de bron voor logboekanalyse (het type resource is de werkruimte Log Analytics). Zodra u zich op de pagina met resourceoverzicht bevindt, klikt u op **Geavanceerde instellingen** onder de sectie Instellingen in het linkermenu.

    ![Pagina Eigenschappen van logboekanalyse](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Klik op **Windows-servers** als u een Windows-cluster en **Linux-servers** opstaat als u een Linux-cluster maakt. Op deze pagina `workspace ID` ziet `workspace key` u uw en (vermeld als primaire sleutel in de portal). U zult beide nodig hebben voor de volgende stap.

4. Voer de opdracht uit om de log-analyseagent `vmss extension set` op uw cluster te installeren met behulp van de API:

    Voor een Windows-cluster:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Voor een Linux-cluster:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Hier is een voorbeeld van de Log Analytics-agent die wordt toegevoegd aan een Windows-cluster.

    ![Log Analytics-agent cli-opdracht](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Dit duurt minder dan 15 min om de agent met succes toe te voegen aan uw knooppunten. U controleren of de agents `az vmss extension list` zijn toegevoegd met behulp van de API:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>De agent toevoegen via de sjabloon Resourcemanager

Voorbeeldvan Resource Manager-sjablonen die een Azure Log Analytics-werkruimte implementeren en een agent toevoegen aan elk van uw knooppunten, zijn beschikbaar voor [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) of [Linux.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS)

U deze sjabloon downloaden en wijzigen om een cluster te implementeren dat het beste bij uw behoeften past.

## <a name="view-performance-counters"></a>Prestatiemeteritems weergeven

Nu u de Log Analytics-agent hebt toegevoegd, gaat u naar de Log Analytics-portal om te kiezen welke prestatiemeteritems u wilt verzamelen.

1. Ga in de Azure-portal naar de resourcegroep waarin u de Service Fabric Analytics-oplossing hebt gemaakt. Selecteer **\<ServiceFabric-naamOfLog\>AnalyticsWorkspace**.

2. Klik op **Log Analytics**.

3. Klik **op Geavanceerde instellingen**.

4. Klik op **Gegevens**en klik vervolgens op **Prestatiemeteritems voor Windows of Linux**. Er is een lijst met standaardtellers die u inschakelen en u het interval voor verzameling ook instellen. U ook [extra prestatiemeteritems](service-fabric-diagnostics-event-generation-perf.md) toevoegen om te verzamelen. Het juiste formaat wordt verwezen in dit [artikel](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Klik **op Opslaan**en klik vervolgens op **OK.**

6. Sluit het mes Geavanceerde instellingen.

7. Klik onder de kop Algemeen op **Werkruimteoverzicht**.

8. U ziet tegels in de vorm van een grafiek voor elk van de ingeschakelde oplossingen, waaronder een voor Service Fabric. Klik op de grafiek **Service Fabric** om door te gaan naar de Service Fabric Analytics-oplossing.

9. U ziet een paar tegels met grafieken over operationeel kanaal en betrouwbare diensten gebeurtenissen. De grafische weergave van de gegevens die binnenkomen voor de tellers die u hebt geselecteerd, wordt weergegeven onder Knooppuntstatistieken.

10. Klik op een containerstatistiekgrafiek om meer details te zien. U ook query's op prestatiemetergegevens op dezelfde manier als clustergebeurtenissen en filter op de knooppunten, perf teller naam, en waarden met behulp van de Kusto query taal.

![Log Analytics perf teller query](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Volgende stappen

* Verzamel relevante [prestatiemeteritems](service-fabric-diagnostics-event-generation-perf.md). Als u de agent Log Analytics wilt configureren om specifieke prestatiemeteritems te verzamelen, controleert u [het configureren van gegevensbronnen](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Azure Monitor-logboeken configureren om [geautomatiseerde waarschuwingen](../log-analytics/log-analytics-alerts.md) in te stellen om te helpen bij het detecteren en diagnosticeren
* Als alternatief u prestatiemeteritems verzamelen via [de Azure Diagnostics-extensie en deze naar Application Insights verzenden](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
