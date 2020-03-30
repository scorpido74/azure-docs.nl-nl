---
title: Knooppunten toevoegen of verwijderen aan een zelfstandig cluster van servicefabric
description: Meer informatie over het toevoegen of verwijderen van knooppunten aan een Azure Service Fabric-cluster op een fysieke of virtuele machine met Windows Server, die on-premises of in elke cloud kan zijn.
author: dkkapur
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: f9bee35ee8e82070b4cf601139b471562ba5e10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934212"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Knooppunten toevoegen aan of verwijderen uit een zelfstandig Service Fabric-cluster dat wordt uitgevoerd op Windows Server
Nadat u [uw standalone Service Fabric-cluster op Windows Server-machines](service-fabric-cluster-creation-for-windows-server.md)hebt gemaakt, kunnen uw (zakelijke) behoeften veranderen en moet u knooppunten aan uw cluster toevoegen of verwijderen. Dit artikel bevat gedetailleerde stappen om dit te bereiken. Houd er rekening mee dat knooppuntfunctionaliteit toevoegen/verwijderen niet wordt ondersteund in lokale ontwikkelingsclusters.

## <a name="add-nodes-to-your-cluster"></a>Knooppunten toevoegen aan uw cluster

1. Bereid de VM/machine voor die u aan uw cluster wilt toevoegen door de stappen te volgen die in Plannen worden beschreven [en de implementatie van uw servicefabriccluster voor te bereiden.](service-fabric-cluster-standalone-deployment-preparation.md)

2. Bepaal aan welk foutdomein en upgradedomein u deze VM/machine gaat toevoegen.

   Als u certificaten gebruikt om het cluster te beveiligen, worden certificaten naar verwachting geïnstalleerd in de lokale certificaatopslag ter voorbereiding op de deelname van het knooppunt aan het cluster. De analoog is van toepassing bij het gebruik van andere vormen van beveiliging.

3. Extern bureaublad (RDP) in de VM/machine die u aan het cluster wilt toevoegen.

4. Kopieer of [download het standalone pakket voor Service Fabric voor Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) naar de VM/machine en rits het pakket uit.

5. Voer PowerShell uit met verhoogde bevoegdheden en ga naar de locatie van het uitgepakte pakket.

6. Voer het *script AddNode.ps1* uit met de parameters die het nieuwe knooppunt beschrijven dat moet worden toegevoegd. In het volgende voorbeeld wordt een nieuw knooppunt met de naam VM5, met type NodeType0 en IP-adres 182.17.34.52, toegevoegd aan UD1 en fd:/dc1/r0. `ExistingClusterConnectionEndPoint`is een verbindingseindpunt voor een knooppunt dat al in het bestaande cluster is, dat het IP-adres van *een* knooppunt in het cluster kan zijn. 

   Onveilig (prototyping):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Veilig (op basis van certificaten):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Wanneer het script klaar is met hardlopen, u controleren of het nieuwe knooppunt is toegevoegd door de [cmdlet Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) uit te voeren.

7. Als u de consistentie tussen de verschillende knooppunten in het cluster wilt garanderen, moet u een configuratie-upgrade starten. Voer [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) uit om het nieuwste configuratiebestand op te halen en het nieuw toegevoegde knooppunt toe te voegen aan de sectie Knooppunten. Het wordt ook aanbevolen om altijd de nieuwste clusterconfiguratie beschikbaar te hebben voor het geval u een cluster met dezelfde configuratie opnieuw moet implementeren.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) uitvoeren om de upgrade te starten.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   U de voortgang van de upgrade op Service Fabric Explorer volgen. U ook [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)uitvoeren.

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Knooppunten toevoegen aan clusters die zijn geconfigureerd met Windows Security met gMSA
Voor clusters die zijn geconfigureerd met Group Managedhttps://technet.microsoft.com/library/hh831782.aspx)Service Account(gMSA)( kan een nieuw knooppunt worden toegevoegd met behulp van een configuratie-upgrade:
1. Voer [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) uit op een van de bestaande knooppunten om het nieuwste configuratiebestand op te halen en details toe te voegen over het nieuwe knooppunt dat u wilt toevoegen in de sectie Knooppunten. Controleer of het nieuwe knooppunt deel uitmaakt van hetzelfde door de groep beheerde account. Dit account moet een beheerder zijn op alle machines.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) uitvoeren om de upgrade te starten.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    U de voortgang van de upgrade op Service Fabric Explorer volgen. U ook [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) uitvoeren

### <a name="add-node-types-to-your-cluster"></a>Knooppunttypen toevoegen aan uw cluster
Als u een nieuw knooppunttype wilt toevoegen, wijzigt u uw configuratie om het nieuwe knooppunttype op te nemen in de sectie Knooppunttypen onder 'Eigenschappen' en begint u een configuratie-upgrade met [Start-ServiceFabricClusterConfigurationUpgrade.](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) Zodra de upgrade is voltooid, u nieuwe knooppunten toevoegen aan uw cluster met dit knooppunttype.

## <a name="remove-nodes-from-your-cluster"></a>Knooppunten uit uw cluster verwijderen
Een knooppunt kan op de volgende manier uit een cluster worden verwijderd met behulp van een configuratie-upgrade:

1. Voer [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) uit om het nieuwste configuratiebestand op te halen en het knooppunt uit de sectie Knooppunten te *verwijderen.*
Voeg de parameter 'NodesToBeRemoved' toe aan de sectie 'Setup' in de sectie FabricSettings. De "waarde" moet een door komma gescheiden lijst met knooppuntnamen zijn van knooppunten die moeten worden verwijderd.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) uitvoeren om de upgrade te starten.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    U de voortgang van de upgrade op Service Fabric Explorer volgen. U ook [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)uitvoeren.

> [!NOTE]
> Verwijdering van knooppunten kan meerdere upgrades initiëren. Sommige knooppunten zijn `IsSeedNode=”true”` gemarkeerd met tag en kunnen worden `Get-ServiceFabricClusterManifest`geïdentificeerd door het clustermanifest op te vragen met behulp van . Het verwijderen van dergelijke knooppunten kan langer duren dan andere, omdat de zaadknooppunten in dergelijke scenario's moeten worden verplaatst. Het cluster moet minimaal 3 primaire knooppuntknooppunten behouden.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Knooppunttypen uit uw cluster verwijderen
Controleer voordat u een knooppunttype verwijdert, of er knooppunten zijn die verwijzen naar het knooppunttype. Verwijder deze knooppunten voordat u het bijbehorende knooppunttype verwijdert. Zodra alle bijbehorende knooppunten zijn verwijderd, u het NodeType uit de clusterconfiguratie verwijderen en een configuratie-upgrade starten met [Start-ServiceFabricClusterConfigurationUpgrade.](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)


### <a name="replace-primary-nodes-of-your-cluster"></a>Primaire knooppunten van uw cluster vervangen
De vervanging van primaire knooppunten moet worden uitgevoerd een knooppunt na het andere, in plaats van het verwijderen en vervolgens toe te voegen in batches.


## <a name="next-steps"></a>Volgende stappen
* [Configuratie-instellingen voor een zelfstandig Windows-cluster](service-fabric-cluster-manifest.md)
* [Een zelfstandig cluster op Windows beveiligen met X509-certificaten](service-fabric-windows-cluster-x509-security.md)
* [Een zelfstandig ServiceFabric-cluster maken met Azure VM's met Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

