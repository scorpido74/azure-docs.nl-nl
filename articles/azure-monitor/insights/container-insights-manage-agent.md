---
title: Over het beheren van de Azure-Monitor voor containers agent | Microsoft Docs
description: Dit artikel wordt beschreven in de meest voorkomende onderhoudstaken beheren met de beperkte Log Analytics-agent die wordt gebruikt door Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275319"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Over het beheren van de Azure-Monitor voor containers-agent

Azure Monitor voor containers maakt gebruik van een beperkte versie van de Log Analytics-agent voor Linux. Na de eerste implementatie zijn routine of optionele taken die u wilt uitvoeren tijdens de levenscyclus. In dit artikel meer informatie over het handmatig bijwerken van de agent en verzameling van omgevingsvariabelen van een bepaalde container uitschakelen. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Upgrade uitvoeren van de Azure-Monitor voor containers-agent

Azure Monitor voor containers maakt gebruik van een beperkte versie van de Log Analytics-agent voor Linux. Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt de agent automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS) en Azure Red Hat open SHIFT. De agent wordt niet beheerd voor een [hybride Kubernetes-cluster](container-insights-hybrid-setup.md) en u moet de agent hand matig bijwerken.

Als de upgrade van de agent mislukt voor een cluster dat wordt gehost op AKS, wordt in dit artikel ook het proces voor het hand matig bijwerken van de agent beschreven. Zie [Release aankondigingen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)van de agent als u de gepubliceerde versies wilt volgen.

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Upgrade agent op het bewaakte Kubernetes-cluster

Het proces voor het upgraden van de agent op clusters, met uitzonde ring van Azure Red Hat open Shift, bestaat uit twee rechte stappen voor door sturen. De eerste stap is om uit te schakelen controleren met Azure Monitor voor containers met behulp van Azure CLI. Volg de stappen die worden beschreven in het artikel [controle uitschakelen](container-insights-optout.md?#azure-cli) . Met behulp van Azure CLI, kunnen we de agent verwijderen van de knooppunten in het cluster zonder gevolgen voor de oplossing en de bijbehorende gegevens die zijn opgeslagen in de werkruimte. 

>[!NOTE]
>Terwijl u deze activiteit onderhoud uitvoert, de knooppunten in het cluster niet van verzamelde gegevens doorsturen zijn en prestatieweergaven worden geen gegevens weergegeven tussen het moment dat u de agent verwijderen en de nieuwe versie installeert. 
>

Als u de nieuwe versie van de agent wilt installeren, volgt u de stappen die worden beschreven in de [controle inschakelen met Azure cli](container-insights-enable-new-cluster.md#enable-using-azure-cli)om dit proces te volt ooien.  

Nadat u de bewaking opnieuw hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u bijgewerkte metrische gegevens voor de status voor het cluster kunt weer geven. Voer de volgende opdracht uit om te controleren of de upgrade van de agent is geslaagd: `kubectl logs omsagent-484hw --namespace=kube-system`

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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Het uitschakelen van de variabele verzameling omgeving voor een container

Azure Monitor voor containers worden omgevings variabelen verzameld uit de containers die worden uitgevoerd in een pod en worden ze weer gegeven in het eigenschappen venster van de geselecteerde container in de weer gave **containers** . U kunt dit gedrag beheren door het verzamelen van een specifieke container tijdens de implementatie van het Kubernetes-cluster uit te scha kelen of door de omgevings variabele *AZMON_COLLECT_ENV*in te stellen. Deze functie is beschikbaar via de agentversie – ciprod11292018 en hoger.  

Als u het verzamelen van omgevings variabelen voor een nieuwe of bestaande container wilt uitschakelen, stelt u de variabele **AZMON_COLLECT_ENV** met de waarde **False** in het yaml-configuratie bestand van de Kubernetes-implementatie. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Voer de volgende opdracht uit om de wijziging toe te passen op andere Kubernetes-clusters dan Azure Red Hat open Shift): `kubectl apply -f  <path to yaml file>`. Voer de volgende opdracht uit om ConfigMap te bewerken en deze wijziging toe te passen voor Azure Red Hat open Shift-clusters:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Hiermee opent u de standaard tekst editor. Sla het bestand op in de editor nadat u de variabele hebt ingesteld.

Als u wilt controleren of de configuratie wijziging is doorgevoerd, selecteert u een container in de weer gave **containers** in azure monitor voor containers en vouwt u **omgevings variabelen**uit in het deel venster Eigenschappen.  In de sectie wordt alleen de variabele weer gegeven die eerder is gemaakt- **AZMON_COLLECT_ENV = False**. Voor alle andere containers, moet de sectie omgevingsvariabelen lijst met alle omgevingsvariabelen die zijn gedetecteerd.

Als u de detectie van de omgevings variabelen opnieuw wilt inschakelen, past u hetzelfde proces eerder toe en wijzigt u de waarde van **False** in **True**en voert u vervolgens de `kubectl` opdracht opnieuw uit om de container bij te werken.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt tijdens het bijwerken van de agent, raadpleegt u de [hand leiding](container-insights-troubleshoot.md) voor het oplossen van problemen voor ondersteuning.
