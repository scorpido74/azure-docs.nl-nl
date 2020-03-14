---
title: Een nieuw Azure Kubernetes service-cluster (AKS) bewaken | Microsoft Docs
description: Meer informatie over het inschakelen van bewaking voor een nieuw Azure Kubernetes service-cluster (AKS) met Azure Monitor voor containers-abonnement.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275436"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Bewaking van een nieuw Azure Kubernetes service (AKS)-cluster inschakelen

In dit artikel wordt beschreven hoe u Azure Monitor instelt voor containers voor het bewaken van beheerde Kubernetes-clusters die worden gehost op de [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/) die u wilt implementeren in uw abonnement.

U kunt de bewaking van een AKS-cluster inschakelen met een van de ondersteunde methoden:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Inschakelen met behulp van Azure CLI

Als u de bewaking van een nieuw AKS-cluster dat is gemaakt met Azure CLI wilt inschakelen, volgt u de stap in het artikel Quick Start in het gedeelte [AKS-cluster maken](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Als u ervoor de Azure CLI gebruiken kiest, moet u eerst installeren en de CLI lokaal gebruikt. U moet de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voer `az --version`uit om uw versie te identificeren. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli). Als u de AKS-preview CLI-extensie versie 0.4.12 of hoger hebt geïnstalleerd, verwijdert u alle wijzigingen die u hebt aangebracht om een preview-uitbrei ding in te scha kelen, omdat AKS preview-functies niet beschikbaar zijn in de Cloud voor Amerikaanse Governmnet van Azure.

## <a name="enable-using-terraform"></a>Inschakelen met behulp van Terraform

Als u [een nieuw AKS-cluster implementeert met behulp van terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), geeft u de vereiste argumenten in het profiel [op om een log Analytics-werk ruimte te maken](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) als u geen bestaande hebt opgegeven. 

>[!NOTE]
>Als u gebruiken, Terraform wilt, moet u uitvoeren de Terraform Azure DB-Provider versie 1.17.0 of hoger.

Als u Azure Monitor voor containers wilt toevoegen aan de werk ruimte, raadpleegt u [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) en voltooit u het profiel door de [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) op te nemen en **oms_agent**op te geven. 

Nadat u bewaking hebt ingeschakeld, en alle configuratietaken zijn voltooid, kunt u de prestaties van uw cluster op twee manieren controleren:

* Rechtstreeks in het AKS-cluster door de **status** te selecteren in het linkerdeel venster.
* Door de tegel **container Insights bewaken** in de AKS-cluster pagina voor het geselecteerde cluster te selecteren. Selecteer in Azure Monitor in het linkerdeel venster **status**. 

  ![Opties voor het selecteren van Azure Monitor voor containers in AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Wanneer u bewaking inschakelt, is het duurt ongeveer 15 minuten voordat u de gezondheid van metrische gegevens voor het cluster kunt weergeven. 

## <a name="verify-agent-and-solution-deployment"></a>Controleer of de implementatie van agent en de oplossing
Met Agent versie *06072018* of hoger kunt u controleren of zowel de agent als de oplossing is geïmplementeerd. U kunt alleen de implementatie van de agent controleren met eerdere versies van de agent.

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

Voer de volgende opdracht uit om te controleren of de versie van de Log Analytics agent die is uitgebracht vóór *06072018* correct is geïmplementeerd:  

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
Gebruik de `aks show` opdracht om details op te halen, zoals de oplossing die is ingeschakeld of niet, wat is de Log Analytics werk ruimte resourceID en de samenvattings gegevens over het cluster.  

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

* Als u problemen ondervindt bij het voorbereiden van de oplossing, raadpleegt u de [hand leiding](container-insights-troubleshoot.md) voor het oplossen van problemen

* Als controle is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw AKS-cluster en werk belastingen die erop worden uitgevoerd, leert [u hoe u Azure monitor gebruikt](container-insights-analyze.md) voor containers.
