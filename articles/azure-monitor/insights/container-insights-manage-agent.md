---
title: De Azure-monitor voor containersagent beheren | Microsoft Documenten
description: In dit artikel worden de meest voorkomende onderhoudstaken beschreven met de containerized Log Analytics-agent die wordt gebruikt door Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275319"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>De Azure-monitor voor containersagent beheren

Azure Monitor voor containers maakt gebruik van een containerversie van de Log Analytics-agent voor Linux. Na de eerste implementatie zijn er routinematige of optionele taken die u mogelijk moet uitvoeren tijdens de levenscyclus. In dit artikel wordt beschreven hoe u de agent handmatig upgraden en het verzamelen van omgevingsvariabelen uit een bepaalde container uitschakelen. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>De Azure-monitor upgraden voor containersagent

Azure Monitor voor containers maakt gebruik van een containerversie van de Log Analytics-agent voor Linux. Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt de agent automatisch geüpgraded op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS) en Azure Red Hat OpenShift. Voor een [hybride Kubernetes-cluster](container-insights-hybrid-setup.md) wordt de agent niet beheerd en moet u de agent handmatig upgraden.

Als de agentupgrade mislukt voor een cluster dat wordt gehost op AKS, wordt in dit artikel ook het proces beschreven om de agent handmatig te upgraden. Als u de uitgebrachte versies wilt volgen, ziet u [de aankondigingen van de agentrelease](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Upgrade-agent op het bewaakte Kubernetes-cluster

Het proces om de agent te upgraden op clusters, met andere dan Azure Red Hat OpenShift, bestaat uit twee rechttoe rechtaan stappen. De eerste stap is het uitschakelen van de bewaking met Azure Monitor voor containers met Azure CLI. Volg de stappen die zijn beschreven in het artikel [Controle uitschakelen.](container-insights-optout.md?#azure-cli) Met Azure CLI kunnen we de agent verwijderen uit de knooppunten in het cluster zonder dat dit gevolgen heeft voor de oplossing en de bijbehorende gegevens die in de werkruimte zijn opgeslagen. 

>[!NOTE]
>Terwijl u deze onderhoudsactiviteit uitvoert, worden de knooppunten in het cluster geen verzamelde gegevens doorsturen en worden er geen gegevens weergegeven tussen het moment dat u de agent verwijdert en de nieuwe versie installeert. 
>

Als u de nieuwe versie van de agent wilt installeren, volgt u de stappen die zijn beschreven in de [bewaking inschakelen met Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)om dit proces te voltooien.  

Nadat u de bewaking opnieuw hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u bijgewerkte statusstatistieken voor het cluster weergeven. Voer de opdracht uit om de upgrade van de agent te verifiëren:`kubectl logs omsagent-484hw --namespace=kube-system`

De status moet lijken op het volgende voorbeeld, waarbij de waarde voor *omi* en *omsagent* moet overeenkomen met de nieuwste versie die is opgegeven in de [releasegeschiedenis van](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)de agent .  

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

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Upgrade-agent op hybride Kubernetes-cluster

Het proces om de agent te upgraden op een Kubernetes-cluster dat on-premises wordt gehost, AKS Engine op Azure en Azure Stack, kan worden voltooid door de volgende opdracht uit te voeren:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Als de werkruimte Log Analytics zich in Azure China bevindt, voert u de volgende opdracht uit:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Als de werkruimte Log Analytics zich in Azure US Government bevindt, voert u de volgende opdracht uit:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Omgevingsvariabeleverzameling uitschakelen op een container

Azure Monitor voor containers verzamelt omgevingsvariabelen uit de containers die in een pod worden uitgevoerd en presenteert deze in het eigenschappenvenster van de geselecteerde container in de weergave **Containers.** U dit gedrag beheren door verzameling voor een specifieke container uit te schakelen tijdens de implementatie van het Kubernetes-cluster of erna door de omgevingsvariabele *AZMON_COLLECT_ENV*in te stellen. Deze functie is beschikbaar in de agentversie – ciprod1292018 en hoger.  

Als u het verzamelen van omgevingsvariabelen op een nieuwe of bestaande container wilt uitschakelen, stelt u de variabele **AZMON_COLLECT_ENV** in met een waarde van **False** in uw Kubernetes-implementatieyaml-configuratiebestand. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Voer de volgende opdracht uit om de wijziging toe te `kubectl apply -f  <path to yaml file>`passen op andere Kubernetes-clusters dan Azure Red Hat OpenShift): . Voer de opdracht uit om ConfigMap te bewerken en deze wijziging toe te passen voor Azure Red Hat OpenShift-clusters:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Hiermee wordt de standaardteksteditor geopend. Nadat u de variabele hebt ingesteld, slaat u het bestand op in de editor.

Als u wilt controleren of de configuratiewijziging van kracht is geworden, selecteert u een container in de weergave **Containers** in Azure Monitor voor containers en vouwt u in het eigenschappenpaneel **Omgevingsvariabelen**uit.  De sectie moet alleen de variabele weergeven die eerder is gemaakt - **AZMON_COLLECT_ENV=FALSE**. Voor alle andere containers moet in de sectie Omgevingsvariabelen alle gedetecteerde omgevingsvariabelen worden vermeld.

Als u de detectie van de omgevingsvariabelen opnieuw wilt inschakelen, past u hetzelfde proces `kubectl` eerder toe en wijzigt u de waarde van **False** naar **True**en voert u de opdracht opnieuw uit om de container bij te werken.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt tijdens het upgraden van de agent, controleert u de handleiding voor [probleemoplossing](container-insights-troubleshoot.md) voor ondersteuning.
