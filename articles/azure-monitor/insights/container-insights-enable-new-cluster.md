---
title: Een nieuw AKS-cluster (Azure Kubernetes Service) controleren | Microsoft Documenten
description: Meer informatie over het inschakelen van monitoring voor een nieuw AKS-cluster (Azure Kubernetes Service) met Azure Monitor voor containersabonnement.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275436"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Bewaking van een nieuw AKS-cluster (Azure Kubernetes Service) inschakelen

In dit artikel wordt beschreven hoe u Azure Monitor instelt voor containers om beheerde Kubernetes-cluster te controleren die wordt gehost op [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) en die u voorbereidt om te implementeren in uw abonnement.

U het bewaken van een AKS-cluster inschakelen met een van de ondersteunde methoden:

* Azure-CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Inschakelen met Azure CLI

Als u bewaking wilt inschakelen van een nieuw AKS-cluster dat is gemaakt met Azure CLI, volgt u de stap in het quickstart-artikel onder de sectie [AKS-cluster maken](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Als u ervoor kiest de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u de Azure CLI moet installeren of upgraden. Als u de AKS-Preview CLI-extensieversie 0.4.12 of hoger hebt geïnstalleerd, verwijdert u eventuele wijzigingen die u hebt aangebracht om een voorbeeldextensie in te schakelen, omdat deze het standaard Azure CLI-gedrag kan overschrijven, omdat AKS Preview-functies niet beschikbaar zijn in de Azure US Governmnet-cloud.

## <a name="enable-using-terraform"></a>Inschakelen met Terraform

Als u [een nieuw AKS-cluster implementeert met Terraform,](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)geeft u de argumenten op die in het profiel nodig zijn [om een Log Analytics-werkruimte te maken](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) als u er niet voor kiest om een bestaand cluster op te geven. 

>[!NOTE]
>Als u Terraform wilt gebruiken, moet u de Terraform Azure RM Provider-versie 1.17.0 of hoger uitvoeren.

Als u Azure Monitor voor containers wilt toevoegen aan de werkruimte, raadpleegt u [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) en vult u het profiel in door de [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) op te addon_profile en **oms_agent**op te geven. 

Nadat u bewaking hebt ingeschakeld en alle configuratietaken zijn voltooid, u de prestaties van uw cluster op twee manieren controleren:

* Rechtstreeks in het AKS-cluster door **Status** in het linkerdeelvenster te selecteren.
* Door de tegel **Monitorcontainer-inzichten te** selecteren op de AKS-clusterpagina voor het geselecteerde cluster. Selecteer in Azure Monitor in het linkerdeelvenster de optie **Status**. 

  ![Opties voor het selecteren van Azure Monitor voor containers in AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Nadat u de bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u statusstatistieken voor het cluster weergeven. 

## <a name="verify-agent-and-solution-deployment"></a>Verificatie van de implementatie van agent en oplossing
Met agentversie *06072018* of hoger u controleren of zowel de agent als de oplossing met succes zijn geïmplementeerd. Bij eerdere versies van de agent u alleen de implementatie van de agent verifiëren.

### <a name="agent-version-06072018-or-later"></a>Agent versie 06072018 of hoger
Voer de volgende opdracht uit om te controleren of de agent is geïmplementeerd. 

```
kubectl get ds omsagent --namespace=kube-system
```

De uitvoer moet lijken op de volgende, wat aangeeft dat het correct is geïmplementeerd:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Voer de volgende opdracht uit om de implementatie van de oplossing te verifiëren:

```
kubectl get deployment omsagent-rs -n=kube-system
```

De uitvoer moet lijken op de volgende, wat aangeeft dat het correct is geïmplementeerd:

```
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

```
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

* Als u problemen ondervindt tijdens een poging om de oplossing aan boord te nemen, controleert u de [handleiding voor probleemoplossing](container-insights-troubleshoot.md)

* Als monitoring is ingeschakeld om de status en het gebruik van resources van uw AKS-cluster en workloads die op deze gegevens worden uitgevoerd, te verzamelen, leest u hoe u Azure Monitor voor containers [gebruiken.](container-insights-analyze.md)
