---
title: Azure-monitor voor containers oplossen | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u problemen met Azure Monitor voor containers oplossen en oplossen.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403386"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Azure-monitor oplossen voor containers

Wanneer u de bewaking van uw AKS-cluster (Azure Kubernetes Service) configureert met Azure Monitor voor containers, u een probleem tegenkomen dat het verzamelen of rapporteren van gegevens verhindert. In dit artikel worden enkele veelvoorkomende problemen en stappen voor het oplossen van problemen beschreven.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Autorisatiefout tijdens onboarding of updatebewerking
Terwijl u Azure Monitor inschakelt voor containers of een cluster bijwerkt om het verzamelen van statistieken te ondersteunen, ontvangt u mogelijk een fout die lijkt op de volgende - *De> identiteit van de client <gebruiker met object-id '<objectId->' heeft geen autorisatie om actie 'Microsoft.Authorization/roleAssignments/write' uit te voeren over bereik*

Tijdens het onboarding- of updateproces wordt geprobeerd de roltoewijzing **Van de Monitorstatistieken uitgever** toe te kennen op de clusterbron. De gebruiker die het proces start om Azure Monitor in te schakelen voor containers of de update ter ondersteuning van het verzamelen van statistieken, moet toegang hebben tot de machtiging **Microsoft.Authorization/roleAssignments/write** op het AKS-clusterbronbereik. Alleen leden van de ingebouwde rol van de **eigenaar-** en **gebruikerstoegangsbeheerder** krijgen toegang tot deze toestemming. Als in uw beveiligingsbeleid machtigingen voor gedetailleerd niveau moeten worden toegewezen, raden we u aan [aangepaste rollen](../../role-based-access-control/custom-roles.md) te bekijken en toe te wijzen aan de gebruikers die dit nodig hebben. 

U deze rol ook handmatig toestaan vanuit de Azure-portal door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Klik in Azure Portal in de linkerbovenhoek op **Alle services**. Typ **Kubernetes**in de lijst met bronnen . Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Azure Kubernetes**.
3. Selecteer er een in de lijst met Kubernetes-clusters.
2. Klik in het linkermenu op **Toegangsbeheer (IAM).**
3. Selecteer **+ Voeg toe** om een roltoewijzing toe te voegen en selecteer de rol **Publisher-statistieken bewaken** en selecteer onder het vaktekst **AKS** **selecteren** om de resultaten te filteren op alleen de aangevers van de clustersservice die in het abonnement zijn gedefinieerd. Selecteer de lijst die specifiek is voor dat cluster.
4. Selecteer **Opslaan** om de rol toe te wijsen. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor voor containers is ingeschakeld, maar rapporteert geen informatie
Als Azure Monitor voor containers is ingeschakeld en geconfigureerd, maar u geen statusgegevens weergeven of geen resultaten worden geretourneerd van een logboekquery, diagnosticeert u het probleem door de volgende stappen te volgen: 

1. Controleer de status van de agent door de opdracht uit te voeren: 

    `kubectl get ds omsagent --namespace=kube-system`

    De uitvoer moet lijken op de volgende, wat aangeeft dat het correct is geïmplementeerd:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Controleer de implementatiestatus met agentversie *06072018* of hoger met de opdracht:

    `kubectl get deployment omsagent-rs -n=kube-system`

    De uitvoer moet lijken op het volgende voorbeeld, wat aangeeft dat deze correct is geïmplementeerd:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Controleer de status van de pod om te controleren of deze wordt uitgevoerd met de opdracht:`kubectl get pods --namespace=kube-system`

    De uitvoer moet lijken op het volgende voorbeeld met de status *Uitvoeren* voor de omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Controleer de agent logs. Wanneer de containeragent wordt geïmplementeerd, wordt een snelle controle uitgevoerd door OMI-opdrachten uit te voeren en wordt de versie van de agent en provider weergegeven. 

5. Voer de opdracht uit om te controleren of de agent is geïmplementeerd:`kubectl logs omsagent-484hw --namespace=kube-system`

    De status moet lijken op het volgende voorbeeld:

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Foutberichten

In de onderstaande tabel worden bekende fouten samengevat die u tegenkomen tijdens het gebruik van Azure Monitor voor containers.

| Foutberichten  | Actie |  
| ---- | --- |  
| Foutbericht`No data for selected filters`  | Het kan enige tijd duren voordat de bewakingsgegevensstroom voor nieuwe clusters tot stand is gebracht. Geef ten minste 10 tot 15 minuten de tijd voordat gegevens voor uw cluster worden weergegeven. |   
| Foutbericht`Error retrieving data` | Terwijl het Azure Kubernetes Service-cluster is ingesteld voor status- en prestatiebewaking, wordt een verbinding tot stand gebracht tussen het cluster en de Azure Log Analytics-werkruimte. Een werkruimte loganalytics wordt gebruikt om alle bewakingsgegevens voor uw cluster op te slaan. Deze fout kan optreden wanneer uw Log Analytics-werkruimte is verwijderd. Controleer of de werkruimte is verwijderd en als dit het was, moet u de bewaking van uw cluster opnieuw inschakelen met Azure Monitor voor containers en een bestaande of een nieuwe werkruimte opgeven. Als u de bewaking voor het cluster opnieuw wilt [inschakelen,](container-insights-optout.md) moet u de bewaking voor het cluster uitschakelen en Azure Monitor voor containers opnieuw [inschakelen.](container-insights-enable-new-cluster.md) |  
| `Error retrieving data`na het toevoegen van Azure Monitor voor containers via az aks cli | Wanneer u `az aks cli`bewaking inschakelt met behulp van Azure Monitor voor containers, wordt deze mogelijk niet goed geïmplementeerd. Controleer of de oplossing is geïmplementeerd. Ga hiervoor naar de werkruimte Log Analytics en kijk of de oplossing beschikbaar is door **Oplossingen** te selecteren in het deelvenster aan de linkerkant. Als u dit probleem wilt oplossen, moet u de oplossing opnieuw implementeren door de instructies te volgen voor [het implementeren van Azure Monitor voor containers](container-insights-onboard.md) |  

Om het probleem te diagnosticeren, hebben we een probleemoplossingsscript [beschikbaar.](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor voor containersagent ReplicaSet Pods zijn niet gepland op het niet-Azure Kubernetes-cluster

Azure Monitor voor containersagent ReplicaSet Pods is afhankelijk van de volgende knooppuntselecties voor de werknemer (of agent) knooppunten voor de planning:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Als er geen nodelabels voor uw werknemer zijn gekoppeld, wordt agent ReplicaSet Pods niet gepland. Raadpleeg Kubernetes om [labelselectors toe](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) te wijzen voor instructies over het toevoegen van het label.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Prestatiegrafieken tonen geen CPU of geheugen van knooppunten en containers op een niet-Azure-cluster

Azure Monitor voor containersagent Pods gebruikt het cAdvisor-eindpunt op de knooppuntagent om de prestatiestatistieken te verzamelen. Controleer of de containeragent op het knooppunt `cAdvisor port: 10255` is geconfigureerd zodat deze op alle knooppunten in het cluster kan worden geopend om prestatiestatistieken te verzamelen.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Niet-Azure Kubernetes-cluster wordt niet weergegeven in Azure Monitor voor containers

Als u het niet-Azure Kubernetes-cluster in Azure Monitor voor containers wilt weergeven, is leestoegang vereist op de werkruimte Log Analytics die dit inzicht ondersteunt en op de Container **Insights-oplossingsbron ContainerInsights *(werkruimte).***

## <a name="next-steps"></a>Volgende stappen

Als monitoring is ingeschakeld om statusstatistieken vast te leggen voor zowel de AKS-clusterknooppunten als pods, zijn deze statusstatistieken beschikbaar in de Azure-portal. Zie [Azure Kubernetes Service-status weergeven](container-insights-analyze.md)voor meer informatie over het gebruik van Azure Monitor voor containers.
