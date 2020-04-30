---
title: De Azure Monitor voor de agent voor containers beheren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de meest voorkomende onderhouds taken beheert met de Log Analytics agent die wordt gebruikt door Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275319"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>De Azure Monitor voor de agent voor containers beheren

Azure Monitor voor containers maakt gebruik van een container versie van de Log Analytics-agent voor Linux. Na de eerste implementatie zijn er routine-of optionele taken die u tijdens de levens cyclus moet uitvoeren. In dit artikel wordt beschreven hoe u de agent hand matig bijwerkt en verzameling van omgevings variabelen uit een bepaalde container kunt uitschakelen. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>De Azure Monitor voor de agent voor containers bijwerken

Azure Monitor voor containers maakt gebruik van een container versie van de Log Analytics-agent voor Linux. Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt de agent automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS) en Azure Red Hat open SHIFT. De agent wordt niet beheerd voor een [hybride Kubernetes-cluster](container-insights-hybrid-setup.md) en u moet de agent hand matig bijwerken.

Als de upgrade van de agent mislukt voor een cluster dat wordt gehost op AKS, wordt in dit artikel ook het proces voor het hand matig bijwerken van de agent beschreven. Zie [Release aankondigingen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)van de agent als u de gepubliceerde versies wilt volgen.

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Upgrade agent op het bewaakte Kubernetes-cluster

Het proces voor het upgraden van de agent op clusters, met uitzonde ring van Azure Red Hat open Shift, bestaat uit twee rechte stappen voor door sturen. De eerste stap is het uitschakelen van de bewaking met Azure Monitor voor containers die gebruikmaken van Azure CLI. Volg de stappen die worden beschreven in het artikel [controle uitschakelen](container-insights-optout.md?#azure-cli) . Met behulp van Azure CLI kunnen we de agent van de knoop punten in het cluster verwijderen zonder dat dit van invloed is op de oplossing en de bijbehorende gegevens die in de werk ruimte zijn opgeslagen. 

>[!NOTE]
>Terwijl u deze onderhouds activiteit uitvoert, worden de verzamelde gegevens niet door de knoop punten in het cluster doorgestuurd en worden er geen gegevens weer gegeven tussen de tijd die u de agent verwijdert en de nieuwe versie installeert. 
>

Als u de nieuwe versie van de agent wilt installeren, volgt u de stappen die worden beschreven in de [controle inschakelen met Azure cli](container-insights-enable-new-cluster.md#enable-using-azure-cli)om dit proces te volt ooien.  

Nadat u de bewaking opnieuw hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u bijgewerkte metrische gegevens voor de status voor het cluster kunt weer geven. Voer de volgende opdracht uit om te controleren of de upgrade van de agent is uitgevoerd:`kubectl logs omsagent-484hw --namespace=kube-system`

De status moet lijken op het volgende voor beeld, waarbij de waarde voor *Omi* en *omsagent* moet overeenkomen met de nieuwste versie die is opgegeven in de [release geschiedenis](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)van de agent.  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Upgrade agent op hybride Kubernetes-cluster

Het proces voor het bijwerken van de agent op een Kubernetes-cluster dat on-premises wordt gehost, AKS-engine op Azure en Azure Stack kan worden voltooid door de volgende opdracht uit te voeren:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Als de Log Analytics-werk ruimte zich in azure China bevindt, voert u de volgende opdracht uit:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Als de Log Analytics-werk ruimte zich in azure US Government bevindt, voert u de volgende opdracht uit:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>De verzameling van omgevings variabelen op een container uitschakelen

Azure Monitor voor containers worden omgevings variabelen verzameld uit de containers die worden uitgevoerd in een pod en worden ze weer gegeven in het eigenschappen venster van de geselecteerde container in de weer gave **containers** . U kunt dit gedrag beheren door het verzamelen van een specifieke container tijdens de implementatie van het Kubernetes-cluster uit te scha kelen of door de omgevings variabele *AZMON_COLLECT_ENV*in te stellen. Deze functie is beschikbaar via de agent versie – ciprod11292018 en hoger.  

Als u het verzamelen van omgevings variabelen voor een nieuwe of bestaande container wilt uitschakelen, stelt u de variabele **AZMON_COLLECT_ENV** met de waarde **False** in het yaml-configuratie bestand van de Kubernetes-implementatie. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Voer de volgende opdracht uit om de wijziging toe te passen op andere Kubernetes-clusters dan Azure Red Hat `kubectl apply -f  <path to yaml file>`open Shift):. Voer de volgende opdracht uit om ConfigMap te bewerken en deze wijziging toe te passen voor Azure Red Hat open Shift-clusters:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Hiermee opent u de standaard tekst editor. Sla het bestand op in de editor nadat u de variabele hebt ingesteld.

Als u wilt controleren of de configuratie wijziging is doorgevoerd, selecteert u een container in de weer gave **containers** in azure monitor voor containers en vouwt u **omgevings variabelen**uit in het deel venster Eigenschappen.  In de sectie wordt alleen de variabele weer gegeven die eerder is gemaakt- **AZMON_COLLECT_ENV = False**. Voor alle andere containers moet u in de sectie omgevings variabelen alle gedetecteerde omgevings variabelen weer geven.

Als u de detectie van de omgevings variabelen opnieuw wilt inschakelen, past u hetzelfde proces eerder toe en wijzigt u de waarde van **False** in **True**en voert u de `kubectl` opdracht opnieuw uit om de container bij te werken.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt tijdens het bijwerken van de agent, raadpleegt u de [hand leiding](container-insights-troubleshoot.md) voor het oplossen van problemen voor ondersteuning.
