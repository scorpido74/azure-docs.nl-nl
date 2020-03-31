---
title: Bewaking instellen met Azure Monitor-logboeken
description: Meer informatie over het instellen van Azure Monitor-logboeken voor het visualiseren en analyseren van gebeurtenissen om uw Azure Service Fabric-clusters te controleren.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609924"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Azure Monitor-logboeken instellen voor een cluster

Azure Monitor-logboeken is onze aanbeveling om gebeurtenissen op clusterniveau te controleren. U de werkruimte Log Analytics instellen via Azure Resource Manager, PowerShell of Azure Marketplace. Als u een bijgewerkte Resource Manager-sjabloon van uw implementatie bijhoudt voor toekomstig gebruik, gebruikt u dezelfde sjabloon om uw Azure Monitor-logboekomgeving in te stellen. Implementatie via Marketplace is eenvoudiger als u al een cluster hebt geïmplementeerd met diagnostische gegevens ingeschakeld. Als u geen toegang op abonnementsniveau hebt in het account waarnaar u implementeert, implementeert u met PowerShell of de sjabloon ResourceManager.

> [!NOTE]
> Als u Azure Monitor-logboeken wilt instellen om uw cluster te controleren, moet u diagnostische gegevens hebben ingeschakeld om gebeurtenissen op clusterniveau of platformniveau weer te geven. Raadpleeg [hoe u diagnostische gegevens in Windows-clusters instelt](service-fabric-diagnostics-event-aggregation-wad.md) en [hoe u diagnostische gegevens in Linux-clusters instelt](service-fabric-diagnostics-oms-syslog.md) voor meer

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Een Logboekanalysewerkruimte implementeren met Azure Marketplace

Als u een Log Analytics-werkruimte wilt toevoegen nadat u een cluster hebt geïmplementeerd, gaat u naar Azure Marketplace in de portal en zoekt u naar **Service Fabric Analytics.** Dit is een aangepaste oplossing voor Service Fabric-implementaties met gegevens die specifiek zijn voor Service Fabric. In dit proces maakt u zowel de oplossing (het dashboard om inzichten te bekijken) als werkruimte (de aggregatie van de onderliggende clustergegevens).

1. Selecteer **Nieuw** in het linkernavigatiemenu. 

2. Zoeken naar **Service Fabric Analytics**. Selecteer de resource die wordt weergegeven.

3. Selecteer **Maken**.

    ![Service Fabric Analytics in Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Selecteer in het venster Voor het maken van servicefabric-analyses de optie **Een werkruimte selecteren** voor het veld **OMS-werkruimte** en **maak vervolgens een nieuwe werkruimte**. Vul de benodigde vermeldingen in. De enige vereiste hier is dat het abonnement voor het cluster Service Fabric en de werkruimte hetzelfde is. Wanneer uw vermeldingen zijn gevalideerd, wordt uw werkruimte geïmplementeerd. De implementatie duurt slechts een paar minuten.

5. Als u klaar bent, selecteert u Opnieuw **maken** onder aan het venster Voor het maken van Service Fabric Analytics. Controleer of de nieuwe werkruimte wordt weergegeven onder **OMS Workspace**. Met deze actie wordt de oplossing toegevoegd aan de werkruimte die u hebt gemaakt.

Als u Windows gebruikt, gaat u verder met de volgende stappen om Azure Monitor-logboeken te verbinden met het opslagaccount waar uw clustergebeurtenissen zijn opgeslagen. 

>[!NOTE]
>De Service Fabric Analytics-oplossing wordt alleen ondersteund voor Windows-clusters. Voor Linux-clusters, bekijk ons artikel over [het instellen van Azure Monitor-logboeken voor Linux-clusters.](service-fabric-diagnostics-oms-syslog.md)  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>De werkruimte Log Analytics verbinden met uw cluster 

1. De werkruimte moet worden gekoppeld aan de diagnostische gegevens die afkomstig zijn van uw cluster. Ga naar de resourcegroep waarin u de Service Fabric Analytics-oplossing hebt gemaakt. Selecteer **ServiceFabric\<\> nameOfWorkspace** en ga naar de overzichtspagina. Van daaruit u oplossingsinstellingen, werkruimte-instellingen en toegang tot de werkruimte Log Analytics wijzigen.

2. Selecteer in het linkernavigatiemenu onder **Werkruimtegegevensbronnen**de optie **Opslagaccountslogboeken**.

3. **Selecteer** bovenaan **toevoegen** om de logboeken van uw cluster toe te voegen aan de werkruimte.

4. Selecteer **Opslagaccount** om het juiste account toe te voegen dat in uw cluster is gemaakt. Als u de standaardnaam hebt gebruikt, is het opslagaccount **sfdg\<resourceGroupName\>**. U dit ook bevestigen met de azure resource manager-sjabloon die wordt gebruikt om uw cluster te implementeren, door de waarde te controleren die wordt gebruikt voor **applicationDiagnosticsStorageAccountName**. Als de naam niet wordt weergegeven, scrolt u omlaag en selecteert **u Meer laden**. Selecteer de naam van het opslagaccount.

5. Geef het gegevenstype op. Stel deze in op **Service Fabric-gebeurtenissen.**

6. Zorg ervoor dat de bron automatisch is ingesteld op **WADServiceFabric\*EventTable**.

7. Selecteer **OK** om uw werkruimte te verbinden met de logboeken van uw cluster.

    ![Opslagaccountlogboeken toevoegen aan Azure Monitor-logboeken](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Het account wordt nu weergegeven als onderdeel van uw opslagaccountlogboeken in de gegevensbronnen van uw werkruimte.

U hebt de Service Fabric Analytics-oplossing toegevoegd in een Log Analytics-werkruimte die nu correct is verbonden met de platform- en toepassingslogboektabel van uw cluster. U op dezelfde manier extra bronnen aan de werkruimte toevoegen.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Azure Monitor-logboeken implementeren met Azure Resource Manager

Wanneer u een cluster implementeert met behulp van een resourcemanagersjabloon, maakt de sjabloon een nieuwe werkruimte Log Analytics, voegt u de oplossing Servicefabric toe aan de werkruimte en configureert u deze om gegevens uit de juiste opslagtabellen te lezen.

U [deze voorbeeldsjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) gebruiken en wijzigen om aan uw vereisten te voldoen. Deze sjabloon doet het volgende

* Hiermee maakt u een cluster met 5 node Service Fabric
* Maakt een Workspace and Service Fabric-oplossing voor Log Analytics
* Hiermee configureert u de agent Log Analytics om 2 voorbeeldprestatiemeteritems naar de werkruimte te verzamelen en te verzenden
* Configureert WAD om servicefabric te verzamelen en stuurt deze naar Azure-opslagtabellen (WADServiceFabric*EventTable)
* Hiermee configureert u de werkruimte Log Analytics om de gebeurtenissen uit deze tabellen te lezen


U de sjabloon implementeren als een upgrade `New-AzResourceGroupDeployment` van Resource Manager naar uw cluster met behulp van de API in de Azure PowerShell-module. Een voorbeeldopdracht is:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager detecteert dat deze opdracht een update is voor een bestaande bron. Het verwerkt alleen de wijzigingen tussen de sjabloon die de bestaande implementatie aansturen en de nieuwe sjabloon die wordt geleverd.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Azure Monitor-logboeken implementeren met Azure PowerShell

U uw logboekanalysebron ook implementeren `New-AzOperationalInsightsWorkspace` via PowerShell met behulp van de opdracht. Als u deze methode wilt gebruiken, controleert u of u [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)hebt geïnstalleerd. Gebruik dit script om een nieuwe log analytics-werkruimte te maken en de Service Fabric-oplossing toe te voegen: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Wanneer u klaar bent, volgt u de stappen in de vorige sectie om Azure Monitor-logboeken te verbinden met het juiste opslagaccount.

U ook andere oplossingen toevoegen of andere wijzigingen aanbrengen in uw Log Analytics-werkruimte met PowerShell. Zie [Azure Monitor-logboeken beheren met PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Implementeer de Log Analytics-agent](service-fabric-diagnostics-oms-agent.md) op uw knooppunten om prestatiemeteritems te verzamelen en dockerstatistieken en -logboeken voor uw containers te verzamelen
* Maak kennis met de [functies voor zoeken en query's voor logboeken](../log-analytics/log-analytics-log-searches.md) die worden aangeboden als onderdeel van Azure Monitor-logboeken
* [View Designer gebruiken om aangepaste weergaven te maken in Azure Monitor-logboeken](../azure-monitor/platform/view-designer.md)
