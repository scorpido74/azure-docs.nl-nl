---
title: Een AKS-cluster (Azure Kubernetes Service) bewaken | Microsoft Documenten
description: Meer informatie over het inschakelen van bewaking van een AKS-cluster (Azure Kubernetes Service) met Azure Monitor voor containers die al in uw abonnement zijn geïmplementeerd.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275449"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Monitoring van het AKS-cluster (Azure Kubernetes Service) inschakelen dat al is geïmplementeerd

In dit artikel wordt beschreven hoe u Azure Monitor instelt voor containers om beheerde Kubernetes-cluster te controleren die wordt gehost op [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) en die al in uw abonnement zijn geïmplementeerd.

U de bewaking inschakelen van een AKS-cluster dat al is geïmplementeerd met een van de ondersteunde methoden:

* Azure-CLI
* Terraform
* [Vanuit Azure Monitor](#enable-from-azure-monitor-in-the-portal) of [rechtstreeks vanuit het AKS-cluster](#enable-directly-from-aks-cluster-in-the-portal) in de Azure-portal
* Met de [meegeleverde Azure Resource Manager-sjabloon](#enable-using-an-azure-resource-manager-template) met `New-AzResourceGroupDeployment` behulp van de Azure PowerShell-cmdlet of met Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Inschakelen met Azure CLI

Met de volgende stap u uw AKS-cluster bewaken met Azure CLI. In dit voorbeeld hoeft u geen bestaande werkruimte per maken of opgeven. Met deze opdracht vereenvoudigt u het proces door een standaardwerkruimte te maken in de standaardbrongroep van het AKS-clusterabonnement als deze nog niet bestaat in de regio.  De standaardwerkruimte die is gemaakt, lijkt op de indeling *DefaultWorkspace-\<\<GUID->- Regio>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

De uitvoer lijkt op het volgende:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integreren met een bestaande werkruimte

Als u liever wilt integreren met een bestaande werkruimte, voert u de volgende stappen `--workspace-resource-id` uit om eerst de volledige resource-id van uw Log Analytics-werkruimte te identificeren die nodig is voor de parameter en voert u de opdracht uit om de bewakingstoepassing in te schakelen tegen de opgegeven werkruimte.  

1. Vermeld alle abonnementen waartoe u toegang hebt met de volgende opdracht:

    ```azurecli
    az account list --all -o table
    ```

    De uitvoer lijkt op het volgende:

    ```output
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

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Zoek in de uitvoer de naam van de werkruimte en kopieer vervolgens de volledige resource-id van die log-analysewerkruimte onder de **veld-id**.

4. Voer de volgende opdracht uit om de invoegtoepassing `--workspace-resource-id` voor bewaking in te schakelen en de waarde voor de parameter te vervangen. De tekenreekswaarde moet zich binnen de dubbele aanhalingstekens bevindt:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    De uitvoer lijkt op het volgende:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Inschakelen met Terraform

1. Het **oms_agent** add-onprofiel toevoegen aan de bestaande [azurerm_kubernetes_cluster resource](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Voeg de [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) de volgende stappen in de Terraform documentatie toe.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Inschakelen vanuit Azure Monitor in de portal

Ga als volgt te werk om bewaking van uw AKS-cluster in de Azure-portal van Azure Monitor in te schakelen:

1. Selecteer In de Azure-portal de optie **Monitor**.

2. Selecteer **Containers** in de lijst.

3. Selecteer op de pagina **Monitor - containers** de optie **Niet-bewaakte clusters**.

4. Zoek in de lijst met niet-bewaakte clusters de container in de lijst en klik op **Inschakelen**.   

5. Als u op de pagina **Onboarding naar Azure Monitor voor containers** een bestaande Log Analytics-werkruimte in hetzelfde abonnement als het cluster hebt, selecteert u deze in de vervolgkeuzelijst.  
    De lijst selecteert vooraf de standaardwerkruimte en locatie waarop de AKS-container in het abonnement is geïmplementeerd.

    ![Monitor van AKS-containerinzichten inschakelen](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Als u een nieuwe werkruimte Log Analytics wilt maken voor het opslaan van de bewakingsgegevens uit het cluster, volgt u de instructies in [Een werkruimte Logboekanalyse maken.](../../azure-monitor/learn/quick-create-workspace.md) Zorg ervoor dat u de werkruimte maakt in hetzelfde abonnement waarop de AKS-container is geïmplementeerd.

Nadat u de bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u statusstatistieken voor het cluster weergeven.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Rechtstreeks inschakelen vanuit AKS-cluster in de portal

Ga als volgt te werk om bewaking rechtstreeks vanuit een van uw AKS-clusters in de Azure-portal in te schakelen:

1. Selecteer **alle services**in de Azure-portal .

2. Begin in de lijst met resources met het typen van **containers**.  De lijstfilters op basis van uw invoer.

3. Selecteer **Kubernetes-services**.  

    ![De koppeling kubernetes-services](./media/container-insights-onboard/portal-search-containers-01.png)

4. Selecteer in de lijst met containers een container.

5. Selecteer op de pagina containeroverzicht de optie **Containers controleren**.  

6. Als u op de pagina **Onboarding naar Azure Monitor voor containers** een bestaande Log Analytics-werkruimte in hetzelfde abonnement als het cluster hebt, selecteert u deze in de vervolgkeuzelijst.  
    De lijst selecteert vooraf de standaardwerkruimte en locatie waarop de AKS-container in het abonnement is geïmplementeerd.

    ![Monitor van AKS-containerstatus inschakelen](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Als u een nieuwe werkruimte Log Analytics wilt maken voor het opslaan van de bewakingsgegevens uit het cluster, volgt u de instructies in [Een werkruimte Logboekanalyse maken.](../../azure-monitor/learn/quick-create-workspace.md) Zorg ervoor dat u de werkruimte maakt in hetzelfde abonnement waarop de AKS-container is geïmplementeerd.

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u operationele gegevens voor het cluster bekijken.

## <a name="enable-using-an-azure-resource-manager-template"></a>Inschakelen met een Azure Resource Manager-sjabloon

Deze methode bevat twee JSON-sjablonen. Eén sjabloon geeft de configuratie op om bewaking in te schakelen en de andere bevat parameterwaarden die u configureert om het volgende op te geven:

* De AKS-containerresource-id.
* De resourcegroep waarin het cluster is geïmplementeerd.

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resourcegroep als het cluster.
>

De werkruimte Log Analytics moet worden gemaakt voordat u bewaking inschakelt met Azure PowerShell of CLI. Als u de werkruimte wilt maken, u deze instellen via [Azure Resource Manager,](../../azure-monitor/platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure-portal.](../../azure-monitor/learn/quick-create-workspace.md)

Zie als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon:

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.59 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u de Azure CLI moet installeren of upgraden.

### <a name="create-and-execute-a-template"></a>Een sjabloon maken en uitvoeren

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Sla dit bestand op als **bestaandeClusterOnboarding.json** in een lokale map.

3. Plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Bewerk de waarden voor **aksResourceId** en **aksResourceLocation** met behulp van de waarden op de pagina **AKS-overzicht** voor het AKS-cluster. De waarde voor **workspaceResourceId** is de volledige resource-id van uw Log Analytics-werkruimte, die de naam van de werkruimte bevat.

    Bewerk de waarden voor **aksResourceTagValues** die overeenkomen met de bestaande tagwaarden die zijn opgegeven voor het AKS-cluster.

5. Sla dit bestand op als **bestaandeClusterParam.json** in een lokale map.

6. U kunt deze sjabloon nu implementeren.

   * Als u wilt implementeren met Azure PowerShell, gebruikt u de volgende opdrachten in de map die de sjabloon bevat:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

       ```output
       provisioningState       : Succeeded
       ```

   * Voer de volgende opdrachten uit om te implementeren met Azure CLI:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

       ```output
       provisioningState       : Succeeded
       ```

       Nadat u de bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u statusstatistieken voor het cluster weergeven.

## <a name="verify-agent-and-solution-deployment"></a>Verificatie van de implementatie van agent en oplossing

Met agentversie *06072018* of hoger u controleren of zowel de agent als de oplossing met succes zijn geïmplementeerd. Bij eerdere versies van de agent u alleen de implementatie van de agent verifiëren.

### <a name="agent-version-06072018-or-later"></a>Agent versie 06072018 of hoger

Voer de volgende opdracht uit om te controleren of de agent is geïmplementeerd.

```
kubectl get ds omsagent --namespace=kube-system
```

De uitvoer moet lijken op de volgende, wat aangeeft dat het correct is geïmplementeerd:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Voer de volgende opdracht uit om de implementatie van de oplossing te verifiëren:

```
kubectl get deployment omsagent-rs -n=kube-system
```

De uitvoer moet lijken op de volgende, wat aangeeft dat het correct is geïmplementeerd:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent versie eerder dan 06072018

Voer de volgende opdracht uit om te controleren of de versie van de Agent Log Analytics die vóór *06072018* is uitgebracht, correct is geïmplementeerd:  

```
kubectl get ds omsagent --namespace=kube-system
```

De uitvoer moet lijken op de volgende, wat aangeeft dat het correct is geïmplementeerd:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Configuratie weergeven met CLI

Gebruik `aks show` de opdracht om details te krijgen, zoals de oplossing is ingeschakeld of niet, wat is de bron-id van de Log Analytics-werkruimte en overzichtsdetails over het cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Na een paar minuten voltooit en retourneert de op JSON geformatteerde informatie over de oplossing.  De resultaten van de opdracht moeten het invoegtoepassingprofiel voor het bewaken weergeven en lijken op de volgende voorbeelduitvoer:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Volgende stappen

* Als u problemen ondervindt tijdens een poging om de oplossing aan boord te nemen, controleert u de [handleiding voor probleemoplossing](container-insights-troubleshoot.md)

* Als monitoring is ingeschakeld om de status en het gebruik van resources van uw AKS-cluster en workloads die op deze gegevens worden uitgevoerd, te verzamelen, leest u hoe u Azure Monitor voor containers [gebruiken.](container-insights-analyze.md)
