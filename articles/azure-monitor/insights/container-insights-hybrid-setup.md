---
title: Hybride Kubernetes-clusters configureren met Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Azure Monitor configureren voor containers om Kubernetes-clusters te controleren die worden gehost op Azure Stack of een andere omgeving.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 5a973e7e500906ebe833ec4cc6fd2fa8ee79c19e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255427"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Hybride Kubernetes-clusters configureren met Azure Monitor voor containers

Azure Monitor voor containers biedt uitgebreide monitoringervaring voor de Azure Kubernetes Service (AKS) en [AKS Engine op Azure](https://github.com/Azure/aks-engine), een zelfbeheerd Kubernetes-cluster dat wordt gehost op Azure. In dit artikel wordt beschreven hoe u monitoring van Kubernetes-clusters die buiten Azure worden gehost, inschakelen en een vergelijkbare bewakingservaring bereiken.

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint het volgende:

* Een Log Analytics-werkruimte.

    Azure Monitor voor containers ondersteunt een Log Analytics-werkruimte in de regio's die worden vermeld in [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Als u uw eigen werkruimte wilt maken, kan deze worden gemaakt via [Azure Resource Manager,](../platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure-portal.](../learn/quick-create-workspace.md)

    >[!NOTE]
    >Controle van meerdere clusters met dezelfde clusternaam als dezelfde Log Analytics-werkruimte inschakelen, wordt niet ondersteund. Clusternamen moeten uniek zijn.
    >

* U bent lid van de **rol van de bijdrager log Analytics** om containerbewaking mogelijk te maken. Zie [Toegang tot werkruimte en logboekgegevens beheren](../platform/manage-access.md) voor meer informatie over het beheren van toegang tot een Log Analytics-werkruimte

* [HELM-client](https://helm.sh/docs/using_helm/) aan boord van de Azure Monitor voor containersdiagram voor het opgegeven Kubernetes-cluster.

* De volgende proxy- en firewallconfiguratiegegevens zijn vereist voor de containerversie van de Log Analytics-agent voor Linux om te communiceren met Azure Monitor:

    |Agentresource|Poorten |
    |------|---------|   
    |*.ods.opinsights.azure.com |Poort 443 |  
    |*.oms.opinsights.azure.com |Poort 443 |  
    |*.blob.core.windows.net |Poort 443 |  
    |*.dc.services.visualstudio.com |Poort 443 |

* De containeragent vereist dat Kubelet's `cAdvisor secure port: 10250` of `unsecure port :10255` wordt geopend op alle knooppunten in het cluster om prestatiestatistieken te verzamelen. We raden `secure port: 10250` u aan om te configureren op de CAdvisor van de Kubelet als deze nog niet is geconfigureerd.

* De gecontaineriseerde agent vereist dat de volgende omgevingsvariabelen op de container worden opgegeven om te `KUBERNETES_SERVICE_HOST` communiceren `KUBERNETES_PORT_443_TCP_PORT`met de Kubernetes API-service binnen het cluster om voorraadgegevens te verzamelen - en .

>[!IMPORTANT]
>De minimale agentversie die wordt ondersteund voor het monitoren van hybride Kubernetes-clusters is ciprod10182019 of hoger.

## <a name="supported-configurations"></a>Ondersteunde configuraties

Het volgende wordt officieel ondersteund met Azure Monitor voor containers.

- Omgevingen: Kubernetes on-premises, AKS Engine op Azure en Azure Stack. Zie [AKS Engine op Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)voor meer informatie.
- Versies van Kubernetes en ondersteuningsbeleid zijn hetzelfde als versies van [AKS ondersteund](../../aks/supported-kubernetes-versions.md).
- Containerruntime: Docker en Moby
- Linux OS release voor master en worked nodes: Ubuntu (18.04 LTS en 16.04 LTS)
- Ondersteuning voor toegangscontrole: Kubernetes RBAC en niet-RBAC

## <a name="enable-monitoring"></a>Bewaking inschakelen

Het inschakelen van Azure Monitor voor containers voor het hybride Kubernetes-cluster bestaat uit het uitvoeren van de volgende stappen in volgorde.

1. Configureer uw Log Analytics-werkruimte met de Oplossing Container Insights.

2. Schakel de Azure Monitor voor containers HELM-diagram in met Log Analytics-werkruimte.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>De oplossing Azure Monitor Containers toevoegen

U de oplossing implementeren met de meegeleverde Azure Resource `New-AzResourceGroupDeployment` Manager-sjabloon met behulp van de Azure PowerShell-cmdlet of met Azure CLI.

Zie als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon:

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.59 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u de Azure CLI moet installeren of upgraden.

Deze methode bevat twee JSON-sjablonen. Eén sjabloon geeft de configuratie op om bewaking in te schakelen en de andere bevat parameterwaarden die u configureert om het volgende op te geven:

- **workspaceResourceId** - de volledige resource-id van uw Log Analytics-werkruimte.
- **workspaceRegion** - het gebied waarin de werkruimte is gemaakt, dat ook locatie **wordt** genoemd in de werkruimteeigenschappen wanneer deze wordt weergeven vanuit de Azure-portal.

Als u eerst de volledige resource-id van `workspaceResourceId` uw Log Analytics-werkruimte wilt identificeren die vereist is voor de parameterwaarde in het **containerSolutionParams.json-bestand,** voert u de volgende stappen uit en voert u de opdracht PowerShell-cmdlet of Azure CLI uit om de oplossing toe te voegen.

1. Vermeld alle abonnementen waartoe u toegang hebt met de volgende opdracht:

    ```azurecli
    az account list --all -o table
    ```

    De uitvoer lijkt op het volgende:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Kopieer de waarde voor **SubscriptionId**.

2. Schakel met de volgende opdracht over naar het abonnement dat als host van de werkruimte Log Analytics host:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. In het volgende voorbeeld wordt de lijst met werkruimten in uw abonnementen weergegeven in de standaard JSON-indeling.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Zoek in de uitvoer de naam van de werkruimte en kopieer vervolgens de volledige resource-id van die log-analysewerkruimte onder de **veld-ID**.

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

5. Sla dit bestand op als containerSolution.json in een lokale map.

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

7. Bewerk de waarden voor **workspaceResourceId** met de waarde die u in stap 3 hebt gekopieerd en voor **werkruimteKopiëren** de **regiowaarde** nadat de azure CLI-opdracht [az-monitorlogboekanalysewerkruimte is uitgevoerd.](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)

8. Sla dit bestand op als containerSolutionParams.json in een lokale map.

9. U kunt deze sjabloon nu implementeren.

   * Als u wilt implementeren met Azure PowerShell, gebruikt u de volgende opdrachten in de map die de sjabloon bevat:

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

       Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Voer de volgende opdrachten uit om te implementeren met Azure CLI:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Nadat u de bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u statusstatistieken voor het cluster weergeven.

## <a name="install-the-chart"></a>De grafiek installeren

Ga als volgt te werk om de HELM-grafiek in te schakelen:

1. Voeg de Azure charts repository toe aan uw lokale lijst door de volgende opdracht uit te voeren:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Installeer de grafiek door de volgende opdracht uit te voeren:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Als de werkruimte Log Analytics zich in Azure China bevindt, voert u de volgende opdracht uit:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Als de werkruimte Log Analytics zich in Azure US Government bevindt, voert u de volgende opdracht uit:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Agentgegevensverzameling configureren

Staring met grafiekversie 1.0.0, de instellingen voor het verzamelen van agentgegevens worden beheerd vanuit de ConfigMap. Raadpleeg hier documentatie over de instellingen voor het verzamelen van gegevens [van](container-insights-agent-config.md)agenten.

Nadat u de grafiek hebt geïmplementeerd, u de gegevens voor uw hybride Kubernetes-cluster in Azure Monitor controleren op containers vanuit de Azure-portal.  

>[!NOTE]
>Innamelatentie is ongeveer vijf tot tien minuten van agent tot commit in de Azure Log Analytics-werkruimte. Status van het cluster geeft de waarde **Geen gegevens** of **onbekend** weer totdat alle vereiste bewakingsgegevens beschikbaar zijn in Azure Monitor.

## <a name="troubleshooting"></a>Problemen oplossen

Als u een fout ondervindt tijdens een poging om bewaking in te schakelen voor uw hybride Kubernetes-cluster, kopieert u het PowerShell-script [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) en slaat u deze op in een map op uw computer. Dit script is beschikbaar om de ondervonden problemen op te sporen en op te lossen. De problemen die het is ontworpen om op te sporen en te proberen correctie van zijn de volgende:

* De opgegeven Log Analytics-werkruimte is geldig
* De werkruimte Log Analytics is geconfigureerd met de oplossing Azure Monitor for Containers. Zo niet, configureer dan de werkruimte.
* OmsAgent replicaset pods worden uitgevoerd
* OmsAgent daemonset pods worden uitgevoerd
* OmsAgent Health-service wordt uitgevoerd
* De log analytics-werkruimte-id en de sleutel die is geconfigureerd op de containeragent komen overeen met de werkruimte waarmee de Insight is geconfigureerd.
* Valideren van alle Linux `kubernetes.io/role=agent` werknemer knooppunten hebben label te plannen rs pod. Als het niet bestaat, voeg het toe.
* Valideren `cAdvisor secure port:10250` `unsecure port: 10255` of worden geopend op alle knooppunten in het cluster.

Als u wilt uitvoeren met Azure PowerShell, gebruikt u de volgende opdrachten in de map die het script bevat:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Volgende stappen

Als monitoring is ingeschakeld om de status en het gebruik van resources van uw hybride Kubernetes-cluster en workloads die erop worden uitgevoerd, te verzamelen, leest u hoe u Azure Monitor voor containers [gebruiken.](container-insights-analyze.md)
