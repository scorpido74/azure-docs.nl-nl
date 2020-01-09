---
title: Over het beheren van de Azure-Monitor voor containers agent | Microsoft Docs
description: Dit artikel wordt beschreven in de meest voorkomende onderhoudstaken beheren met de beperkte Log Analytics-agent die wordt gebruikt door Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 5bd3af7787ee38011c52224f5830d8b719031db8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404268"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Over het beheren van de Azure-Monitor voor containers-agent
Azure Monitor voor containers maakt gebruik van een beperkte versie van de Log Analytics-agent voor Linux. Na de eerste implementatie zijn routine of optionele taken die u wilt uitvoeren tijdens de levenscyclus. In dit artikel meer informatie over het handmatig bijwerken van de agent en verzameling van omgevingsvariabelen van een bepaalde container uitschakelen. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Upgrade uitvoeren van de Azure-Monitor voor containers-agent
Azure Monitor voor containers maakt gebruik van een beperkte versie van de Log Analytics-agent voor Linux. Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt de agent automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost in Azure Kubernetes Service (AKS).  

Als de upgrade van de agent mislukt, wordt in dit artikel wordt beschreven van het proces van de agent handmatig te upgraden. Als u wilt volgen de versies die zijn uitgebracht, Zie [agent aankondigingen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Agent op de bewaakte Kubernetes-cluster upgraden
Het proces voor het bijwerken van de agent bestaat uit twee duidelijk stappen. De eerste stap is om uit te schakelen controleren met Azure Monitor voor containers met behulp van Azure CLI.  Volg de stappen de [uitschakelen bewaking](container-insights-optout.md?#azure-cli) artikel. Met behulp van Azure CLI, kunnen we de agent verwijderen van de knooppunten in het cluster zonder gevolgen voor de oplossing en de bijbehorende gegevens die zijn opgeslagen in de werkruimte. 

>[!NOTE]
>Terwijl u deze activiteit onderhoud uitvoert, de knooppunten in het cluster niet van verzamelde gegevens doorsturen zijn en prestatieweergaven worden geen gegevens weergegeven tussen het moment dat u de agent verwijderen en de nieuwe versie installeert. 
>

Als u de nieuwe versie van de agent wilt installeren, volgt u de stappen die worden beschreven in de [controle inschakelen met Azure cli](container-insights-enable-new-cluster.md#enable-using-azure-cli)om dit proces te volt ooien.  

Wanneer u bewaking opnieuw inschakelt, is het duurt ongeveer 15 minuten voordat u de bijgewerkte status metrische gegevens voor het cluster kunt weergeven. Als u wilt controleren of de agent is bijgewerkt, moet u de opdracht uitvoeren: `kubectl logs omsagent-484hw --namespace=kube-system`

De status is vergelijkbaar met het volgende voorbeeld, waarbij de waarde voor *omi* en *omsagent* moet overeenkomen met de meest recente versie die is opgegeven in de [releasegeschiedenis van agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Het uitschakelen van de variabele verzameling omgeving voor een container
Azure Monitor voor containers verzamelt omgevingsvariabelen van containers die worden uitgevoerd in een schil en geeft deze weer in het eigenschappenvenster van de geselecteerde container in de **Containers** weergeven. U kunt dit gedrag beheren door het verzamelen van een specifieke container tijdens de implementatie van het AKS-cluster uit te scha kelen of door de omgevings variabele *AZMON_COLLECT_ENV*in te stellen. Deze functie is beschikbaar via de agentversie – ciprod11292018 en hoger.  

Als u het verzamelen van omgevings variabelen voor een nieuwe of bestaande container wilt uitschakelen, stelt u de variabele **AZMON_COLLECT_ENV** met de waarde **False** in het yaml-configuratie bestand van de Kubernetes-implementatie.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Voer de volgende opdracht uit om toe te passen van de wijziging naar uw AKS-container: `kubectl apply -f  <path to yaml file>`.

Als u wilt controleren of de configuratie wijziging is doorgevoerd, selecteert u een container in de weer gave **containers** in azure monitor voor containers en vouwt u **omgevings variabelen**uit in het deel venster Eigenschappen.  In de sectie wordt alleen de variabele weer gegeven die eerder is gemaakt- **AZMON_COLLECT_ENV = False**. Voor alle andere containers, moet de sectie omgevingsvariabelen lijst met alle omgevingsvariabelen die zijn gedetecteerd.   

Als u de detectie van de omgevings variabelen opnieuw wilt inschakelen, past u hetzelfde proces eerder toe en wijzigt u de waarde van **False** in **True**en voert u vervolgens de `kubectl` opdracht opnieuw uit om de container bij te werken.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt tijdens het bijwerken van de agent, raadpleegt u de [problemen oplossen met](container-insights-troubleshoot.md) voor ondersteuning.
