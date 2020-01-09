---
title: Een nieuw Azure Kubernetes service-cluster (AKS) bewaken | Microsoft Docs
description: Meer informatie over het inschakelen van bewaking voor een nieuw Azure Kubernetes service-cluster (AKS) met Azure Monitor voor containers-abonnement.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405437"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Bewaking van een nieuw Azure Kubernetes service (AKS)-cluster inschakelen

In dit artikel wordt beschreven hoe u Azure Monitor instelt voor containers voor het bewaken van beheerde Kubernetes-clusters die worden gehost op de [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/) die u wilt implementeren in uw abonnement.

U kunt de bewaking van een AKS-cluster inschakelen met een van de ondersteunde methoden:

* Azure-CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Inschakelen met behulp van Azure CLI

Volg de stappen in dit artikel in de sectie voor bewaking van een nieuw AKS-cluster gemaakt met Azure CLI, [maken-AKS-cluster](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Als u ervoor de Azure CLI gebruiken kiest, moet u eerst installeren en de CLI lokaal gebruikt. U moet de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voor het identificeren van uw versie uitvoeren `az --version`. Als u wilt installeren of upgraden van de Azure CLI, Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli). Als u de AKS-preview CLI-extensie versie 0.4.12 of hoger hebt geïnstalleerd, verwijdert u alle wijzigingen die u hebt aangebracht om een preview-uitbrei ding in te scha kelen, omdat AKS preview-functies niet beschikbaar zijn in de Cloud voor Amerikaanse Governmnet van Azure.

## <a name="enable-using-terraform"></a>Inschakelen met behulp van Terraform

Als u [implementeren van een nieuw AKS-cluster met behulp van Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), geeft u de argumenten vereist in het profiel [een Log Analytics-werkruimte maken](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) als u niet hebt gekozen om op te geven van een bestaande resourcegroep. 

>[!NOTE]
>Als u gebruiken, Terraform wilt, moet u uitvoeren de Terraform Azure DB-Provider versie 1.17.0 of hoger.

Azure Monitor voor containers toevoegen aan de werkruimte, Zie [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) en het profiel te voltooien door de [ **addon_profile** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) en opgeven **oms_agent**. 

Nadat u bewaking hebt ingeschakeld, en alle configuratietaken zijn voltooid, kunt u de prestaties van uw cluster op twee manieren controleren:

* Rechtstreeks in het AKS-cluster door te selecteren **Health** in het linkerdeelvenster.
* Door het selecteren van de **Monitor Container insights** tegel op de pagina van de AKS-cluster voor het geselecteerde cluster. In Azure Monitor in het linkerdeelvenster selecteert **Health**. 

  ![Opties voor het selecteren van Azure Monitor voor containers in AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Wanneer u bewaking inschakelt, is het duurt ongeveer 15 minuten voordat u de gezondheid van metrische gegevens voor het cluster kunt weergeven. 

## <a name="verify-agent-and-solution-deployment"></a>Controleer of de implementatie van agent en de oplossing
Met de versie van agent *06072018* of hoger, kunt u controleren dat zowel de agent en de oplossing zijn geïmplementeerd. U kunt alleen de implementatie van de agent controleren met eerdere versies van de agent.

### <a name="agent-version-06072018-or-later"></a>Agentversie 06072018 of hoger
Voer de volgende opdracht om te controleren dat de agent is geïmplementeerd. 

```
kubectl get ds omsagent --namespace=kube-system
```

De uitvoer moet eruitzien zoals in het volgende, waarmee wordt aangegeven dat deze correct is geïmplementeerd:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Als u wilt controleren of de implementatie van de oplossing, moet u de volgende opdracht uitvoeren:

```
kubectl get deployment omsagent-rs -n=kube-system
```

De uitvoer moet eruitzien zoals in het volgende, waarmee wordt aangegeven dat deze correct is geïmplementeerd:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent-versie ouder is dan 06072018

Om te controleren dat de versie van de Log Analytics-agent vóór uitgebracht *06072018* correct is geïmplementeerd met de volgende opdracht uitvoeren:  

```
kubectl get ds omsagent --namespace=kube-system
```

De uitvoer moet eruitzien zoals in het volgende, waarmee wordt aangegeven dat deze correct is geïmplementeerd:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Configuratie met CLI
Gebruik de `aks show` opdracht om informatie te verkrijgen die de oplossing of niet ingeschakeld is, wat is de Log Analytics-werkruimte resourceID en overzichtsgegevens over het cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Na een paar minuten, de opdracht is voltooid en retourneert JSON opgemaakte informatie over de oplossing.  De resultaten van de opdracht het controle-Add-on-profiel moet worden weergegeven en lijkt op de volgende voorbeelduitvoer:

```
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

* Als u problemen ondervindt tijdens een poging voor de Onboarding van de oplossing, raadpleegt u de [problemen oplossen met](container-insights-troubleshoot.md)

* Als controle is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw AKS-cluster en werk belastingen die erop worden uitgevoerd, leert [u hoe u Azure monitor gebruikt](container-insights-analyze.md) voor containers.
