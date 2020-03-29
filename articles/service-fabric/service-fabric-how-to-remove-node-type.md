---
title: Een knooppunttype verwijderen in Azure Service Fabric | Microsoft Documenten
description: Meer informatie over het verwijderen van een knooppunttype uit een cluster van Servicefabric dat in Azure wordt uitgevoerd.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969410"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Een servicestructuurknooppunt verwijderen
In dit artikel wordt beschreven hoe u een Azure Service Fabric-cluster schalen door een bestaand knooppunttype uit een cluster te verwijderen. Een Service Fabric-cluster is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster wordt een knooppunt genoemd. Virtuele machineschaalsets zijn een Azure-compute resource die u gebruikt om een verzameling virtuele machines als set te implementeren en te beheren. Elk knooppunttype dat is gedefinieerd in een Azure-cluster, is [ingesteld als een afzonderlijke schaalset.](service-fabric-cluster-nodetypes.md) Elk knooppunttype kan vervolgens afzonderlijk worden beheerd. Nadat u een cluster servicestructuur hebt gemaakt, u een cluster horizontaal schalen door een knooppunttype (virtuele machineschaalset) en al zijn knooppunten te verwijderen.  U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd.  Naarmate het cluster schaalt, schalen uw toepassingen ook automatisch.

> [!WARNING]
> Met deze benadering wordt het niet aanbevolen om regelmatig een knooppunttype uit een productiecluster te verwijderen. Het is een gevaarlijke opdracht als het verwijdert de virtuele machine schaal set bron achter het knooppunt type. 

## <a name="durability-characteristics"></a>Duurzaamheidskenmerken
Veiligheid heeft prioriteit boven snelheid bij het gebruik van Remove-AzServiceFabricNodeType. Het knooppunttype moet zilver- of [goudduurzaamheidsniveau](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)zijn, omdat:
- Bronze geeft u geen garanties over het opslaan van staatsinformatie.
- Zilver en Goud duurzaamheid val eventuele wijzigingen in de schaal set.
- Gold geeft u ook controle over de Azure-updates onder de schaalset.

Service Fabric "orkestreert" onderliggende wijzigingen en updates, zodat gegevens niet verloren gaan. Wanneer u echter een knooppunttype met bronzeduurzaamheid verwijdert, u staatsinformatie verliezen. Als u een primair knooppunttype verwijdert en uw toepassing stateloos is, is Brons acceptabel. Wanneer u stateful workloads in productie uitvoert, moet de minimale configuratie Silver zijn. Op dezelfde manier moet voor productiescenario's het primaire knooppunttype altijd zilver of goud zijn.

### <a name="more-about-bronze-durability"></a>Meer over bronzen duurzaamheid

Bij het verwijderen van een knooppunttype dat Brons is, gaan alle knooppunten in het knooppunttype onmiddellijk naar beneden. Service Fabric heeft geen val geen Bronze nodes schaal set updates, dus alle VM's onmiddellijk naar beneden gaan. Als je iets stateful op die knooppunten had, gaan de gegevens verloren. Zelfs als je stateloos was, nemen alle knooppunten in de Service Fabric deel aan de ring, dus een hele buurt kan verloren gaan, wat het cluster zelf zou kunnen destabiliseren.

## <a name="remove-a-node-type"></a>Een knooppunttype verwijderen

1. Zorg voor deze vereisten voordat u het proces start.

    - Het cluster is gezond.
    - Er zal nog steeds voldoende capaciteit zijn nadat het knooppunttype is verwijderd, bijvoorbeeld. aantal knooppunten om het vereiste aantal replica's te plaatsen.

2. Alle services met plaatsingsbeperkingen verplaatsen om knooppunttype uit het knooppunttype te gebruiken.

    - Wijziging sapplication / servicemanifest om niet langer naar het knooppunttype te verwijzen.
    - Implementeer de wijziging.

    Valideer dan dat:
    - Alle bovenstaande services worden niet meer uitgevoerd op het knooppunt van het knooppunttype.
    - Alle diensten zijn gezond.

3. Het knooppunttype ongedaan maken als niet-primair (Overslaan voor niet-primaire knooppunttypen)

    - Zoek de azure resource manager-sjabloon die wordt gebruikt voor implementatie.
    - Zoek de sectie met betrekking tot het knooppunttype in de sectie Servicefabric.
    - Change isPrimary property to false. ** Verwijder de sectie met betrekking tot het knooppunttype in deze taak niet.
    - De gewijzigde azure resource manager-sjabloon implementeren. ** Afhankelijk van de clusterconfiguratie kan deze stap even duren.
    
    Valideer dan dat:
    - Sectie Servicefabric in Portal geeft aan dat het cluster klaar is.
    - Cluster is gezond.
    - Geen van de knooppunten die tot het knooppunttype behoren, zijn gemarkeerd als Seed Node.

4. Gegevens voor het knooppunttype uitschakelen.

    Maak verbinding met het cluster via PowerShell en voer de volgende stap uit.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - Voor bronzen duurzaamheid, wachten tot alle knooppunten te krijgen uitgeschakeld staat
    - Voor zilver en goud duurzaamheid, zullen sommige knooppunten gaan in uitgeschakeld en de rest zal in uitschakelen staat. Controleer het tabblad details van de knooppunten in de uitschakelende status, als ze allemaal vastzitten aan het waarborgen van quorum voor infrastructuurservicepartities, is het veilig om door te gaan.

5. Gegevens voor het knooppunttype stoppen.

    Maak verbinding met het cluster via PowerShell en voer de volgende stap uit.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Wacht tot alle knooppunten voor knooppunttype zijn gemarkeerd Als gemarkeerd Down.
    
6. Gegevens voor het knooppunttype verwijderen.

    Maak verbinding met het cluster via PowerShell en voer de volgende stap uit.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Wacht tot alle knooppunten uit het cluster zijn verwijderd. De knooppunten mogen niet worden weergegeven in SFX.

7. Verwijder het knooppunttype uit de sectie ServiceFabric.

    - Zoek de azure resource manager-sjabloon die wordt gebruikt voor implementatie.
    - Zoek de sectie met betrekking tot het knooppunttype in de sectie Servicefabric.
    - Verwijder de sectie die overeenkomt met het knooppunttype.
    - Alleen voor silver- en clusters met een hogere duurzaamheid werkt u de clusterbron in `applicationDeltaHealthPolicies` de sjabloon `properties` bij en configureert u het gezondheidsbeleid om de fabric:/System-toepassingsstatus te negeren door onder clusterbron toe te voegen zoals hieronder wordt weergegeven. Het onderstaande beleid moet bestaande fouten negeren, maar geen nieuwe gezondheidsfouten toestaan. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - De gewijzigde azure resource manager-sjabloon implementeren. ** Deze stap zal een tijdje duren, meestal tot twee uur. Deze upgrade wijzigt de instellingen in de InfrastructureService, waardoor een herstart van het knooppunt nodig is. In dit `forceRestart` geval wordt genegeerd. 
    De `upgradeReplicaSetCheckTimeout` parameter geeft de maximale tijd aan waarop Service Fabric wacht tot een partitie in een veilige staat is, zo niet al in een veilige staat. Zodra de veiligheidscontroles voor alle partities op een knooppunt passeren, gaat Service Fabric verder met de upgrade op dat knooppunt.
    De waarde voor `upgradeTimeout` de parameter kan worden teruggebracht tot 6 uur, maar voor maximale veiligheid moet 12 uur worden gebruikt.

    Valideer dan dat:
    - Service Fabric Resource in portal toont gereed.

8. Verwijder alle verwijzingen naar de resources met betrekking tot het knooppunttype.

    - Zoek de azure resource manager-sjabloon die wordt gebruikt voor implementatie.
    - Verwijder de virtuele machineschaalset en andere resources die betrekking hebben op het knooppunttype uit de sjabloon.
    - Implementeer de wijzigingen.

    Daarna kunt u het volgende doen:
    - Wacht tot de implementatie is voltooid.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [de duurzaamheidskenmerken van het](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)cluster .
- Meer informatie over [knooppunttypen en virtuele machineschaalsets](service-fabric-cluster-nodetypes.md).
- Meer informatie over [clusterschaling van ServiceFabric](service-fabric-cluster-scaling.md).
