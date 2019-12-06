---
title: Hybride Kubernetes-clusters met Azure Monitor voor containers configureren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers kunt configureren voor het bewaken van Kubernetes-clusters die worden gehost op Azure Stack of een andere omgeving.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2019
ms.openlocfilehash: 0d6615d832059a8b58c0d5d52533b8c8c962640d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841571"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Hybride Kubernetes-clusters met Azure Monitor voor containers configureren

Azure Monitor voor containers biedt uitgebreide bewakings ervaring voor de Azure Kubernetes-service (AKS) en AKS-engine clusters die worden gehost in Azure. In dit artikel wordt beschreven hoe u de bewaking van Kubernetes-clusters die buiten Azure worden gehost, inschakelt en een vergelijk bare bewakings ervaring krijgt.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, zorg ervoor dat u het volgende hebt:

* Een Log Analytics-werkruimte.

    Azure Monitor voor containers ondersteunt een Log Analytics-werk ruimte in de regio's die worden vermeld in azure- [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om uw eigen werk ruimte te maken, kan deze worden gemaakt via [Azure Resource Manager](../platform/template-workspace-configuration.md), via [Power shell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure Portal](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Het inschakelen van de bewaking van meerdere clusters met dezelfde cluster naam op dezelfde Log Analytics werk ruimte wordt niet ondersteund. Cluster namen moeten uniek zijn.
    >

* U bent lid van de **rol log Analytics Inzender** om container bewaking in te scha kelen. Voor meer informatie over het beheren van de toegang tot een Log Analytics werkruimte raadpleegt [u toegang tot de werk ruimte en logboek gegevens](../platform/manage-access.md)

* [Helm-client](https://helm.sh/docs/using_helm/) voor het voorbereiden van de grafiek Azure monitor voor containers voor het opgegeven Kubernetes-cluster.

* De volgende proxy-en firewall configuratie gegevens zijn vereist voor de container versie van de Log Analytics-agent voor Linux om te communiceren met Azure Monitor:

    |Agentresource|Poorten |
    |------|---------|   
    |*.ods.opinsights.azure.com |Poort 443 |  
    |*.oms.opinsights.azure.com |Poort 443 |  
    |*.blob.core.windows.net |Poort 443 |  
    |*. dc.services.visualstudio.com |Poort 443 | 

* Voor de container agent moet `cAdvisor port: 10255` worden geopend op alle knoop punten in het cluster om metrische gegevens over prestaties te verzamelen.

* Voor de container agent moeten de volgende omgevings variabelen worden opgegeven in de container om te communiceren met de Kubernetes API-service binnen het cluster om de `KUBERNETES_SERVICE_HOST` en `KUBERNETES_PORT_443_TCP_PORT`van de inventaris gegevens te verzamelen. 

>[!IMPORTANT]
>De minimale agent versie die wordt ondersteund voor het bewaken van hybride Kubernetes-clusters is ciprod10182019 of hoger. 

## <a name="supported-configurations"></a>Ondersteunde configuraties

Het volgende wordt officieel ondersteund met Azure Monitor voor containers.

- Omgevingen: Kubernetes on-premises, AKS-engine op Azure en Azure Stack. Zie de [AKS-engine op Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)voor meer informatie.
- De versies van Kubernetes en het ondersteunings beleid zijn hetzelfde als de versies van AKS die worden [ondersteund](../../aks/supported-kubernetes-versions.md). 
- Container runtime: docker en Moby
- Linux-besturingssysteem release voor Master-en werk knooppunten: Ubuntu (18,04 LTS en 16,04 LTS)
- Ondersteund toegangs beheer: Kubernetes RBAC en non-RBAC

## <a name="enable-monitoring"></a>Bewaking inschakelen

Het inschakelen van Azure Monitor voor containers voor het hybride Kubernetes-cluster bestaat uit de volgende stappen in de aangegeven volg orde uit te voeren.

1. Configureer uw Log Analytics-werk ruimte met de container Insights-oplossing.

2. Schakel de Azure Monitor-grafiek voor containers HELM in met Log Analytics werk ruimte.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>De Azure Monitor containers-oplossing toevoegen

U kunt de oplossing met de meegeleverde Azure Resource Manager sjabloon implementeren met behulp van de Azure PowerShell-cmdlet `New-AzResourceGroupDeployment` of met Azure CLI.

Als u niet bekend met het concept bent van het implementeren van resources met behulp van een sjabloon, Zie:

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Als u ervoor de Azure CLI gebruiken kiest, moet u eerst installeren en de CLI lokaal gebruikt. U moet de Azure CLI-versie 2.0.59 of hoger uitvoeren. Voor het identificeren van uw versie uitvoeren `az --version`. Als u wilt installeren of upgraden van de Azure CLI, Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Deze methode omvat twee JSON-sjablonen. Een sjabloon Hiermee geeft u de configuratie voor bewaking en de andere bevat parameterwaarden die u configureert voor het volgende opgeven:

- **workspaceResourceId** : de volledige resource-id van uw log Analytics-werk ruimte.
- **workspaceRegion** : de regio waarin de werk ruimte is gemaakt, die ook wel **locatie** in de werkruimte eigenschappen wordt genoemd bij het weer geven van de Azure Portal.

Als u eerst de volledige Resource-ID wilt identificeren van uw Log Analytics werk ruimte die vereist is voor de waarde van de `workspaceResourceId`-para meter in het bestand **containerSolutionParams. json** , voert u de volgende stappen uit en voert u de Power shell-cmdlet of de Azure cli-opdracht uit om de oplossing toe te voegen.

1. Een lijst met alle abonnementen waartoe u toegang hebt met behulp van de volgende opdracht:

    ```azurecli
    az account list --all -o table
    ```

    De uitvoer ziet eruit als in het volgende:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Kopieer de waarde voor **SubscriptionId**.

2. Schakel over naar het abonnement dat als host fungeert voor de Log Analytics-werk ruimte met behulp van de volgende opdracht:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. In het volgende voor beeld wordt de lijst met werk ruimten in uw abonnementen weer gegeven in de standaard JSON-indeling. 

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Zoek in de uitvoer de naam van de werk ruimte en kopieer de volledige Resource-ID van die Log Analytics werk ruimte onder de veld **-id**.

4. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Sla dit bestand op als containerSolution. json naar een lokale map.

6. Plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Bewerk de waarden voor **workspaceResourceId** met de waarde die u in stap 3 hebt gekopieerd en voor **WorkspaceRegion** Kopieer de **regio** waarde na het uitvoeren van de Azure cli-opdracht [AZ monitor log-Analytics werk ruimte show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Sla dit bestand op als containerSolutionParams. json naar een lokale map.

9. U kunt deze sjabloon nu implementeren. 

   * Als u wilt implementeren met Azure PowerShell, gebruikt u de volgende opdrachten in de map met de sjabloon:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```
       
       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```
       
       Wijzigen van de configuratie kan een paar minuten duren. Wanneer deze voltooid, wordt er een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Als u wilt implementeren met Azure CLI, voert u de volgende opdrachten uit:
    
       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Wijzigen van de configuratie kan een paar minuten duren. Wanneer deze voltooid, wordt er een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

       ```azurecli
       provisioningState       : Succeeded
       ```
     
       Wanneer u bewaking inschakelt, is het duurt ongeveer 15 minuten voordat u de gezondheid van metrische gegevens voor het cluster kunt weergeven. 

## <a name="install-the-chart"></a>De grafiek installeren

Ga als volgt te werk om de HELM-grafiek in te scha kelen:

1. Voeg de opslag plaats van Azure Charts toe aan uw lokale lijst door de volgende opdracht uit te voeren:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Installeer de grafiek door de volgende opdracht uit te voeren:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Als de Log Analytics-werk ruimte zich in azure China bevindt, voert u de volgende opdracht uit:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers 
    ```

    Als de Log Analytics-werk ruimte zich in azure US Government bevindt, voert u de volgende opdracht uit:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Gegevens verzameling van agent configureren

Als u een stert met grafiek versie 1.0.0, worden de instellingen voor het verzamelen van agents bepaald vanuit de ConfigMap. Raadpleeg [hier](container-insights-agent-config.md)de documentatie over het verzamelen van instellingen voor agent gegevens. 

Nadat u de grafiek hebt geïmplementeerd, kunt u de gegevens voor uw hybride Kubernetes-cluster in Azure Monitor voor containers in de Azure Portal controleren.  

>[!NOTE]
>De latentie van de opname is ongeveer vijf tot tien minuten van de agent die moet worden doorgevoerd in de Azure Log Analytics-werk ruimte. De status van het cluster toont de waarde **geen gegevens** of is **onbekend** totdat alle vereiste bewakings gegevens beschikbaar zijn in azure monitor. 

## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout optreedt bij het inschakelen van bewaking voor uw hybride Kubernetes-cluster, kopieert u het Power shell-script [TroubleshootError_nonAzureK8s. ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) en slaat u het op in een map op uw computer. Dit script wordt verstrekt om te helpen bij het detecteren en oplossen van de gevonden problemen. De problemen waarmee het volgende kan worden opgelost:

* De opgegeven Log Analytics werk ruimte is geldig 
* De Log Analytics-werk ruimte is geconfigureerd met de Azure Monitor voor containers. Als dat niet het geval is, configureert u de werk ruimte.
* OmsAgent replicaset pod wordt uitgevoerd
* OmsAgent daemonset pod wordt uitgevoerd
* De OmsAgent Health-Service wordt uitgevoerd 
* De Log Analytics werk ruimte-id en-sleutel die zijn geconfigureerd op de container agent komen overeen met de werk ruimte waarin het inzicht is geconfigureerd.
* Valideer alle Linux worker-knoop punten hebben `kubernetes.io/role=agent` label voor het plannen van RS pod. Als deze niet bestaat, voegt u deze toe.
* Valideer `cAdvisor port: 10255` is geopend op alle knoop punten in het cluster.

Als u wilt uitvoeren met Azure PowerShell, gebruikt u de volgende opdrachten in de map die het script bevat:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile>
```

## <a name="next-steps"></a>Volgende stappen

Als bewaking is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw hybride Kubernetes-cluster en werk belastingen die hierop worden uitgevoerd, leert [u hoe u Azure monitor gebruikt](container-insights-analyze.md) voor containers.
