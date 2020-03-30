---
title: Service Fabric Azure Resource Manager-implementatievangrails
description: In dit artikel vindt u een overzicht van veelvoorkomende fouten die zijn gemaakt bij het implementeren van een Cluster Servicefabric via Azure Resource Manager en hoe u deze voorkomen.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368579"
---
# <a name="service-fabric-guardrails"></a>Service Fabric vangrails 
Bij het implementeren van een Cluster Servicefabric worden vangrails geplaatst, waardoor een Azure Resource Manager-implementatie mislukt in het geval van een ongeldige clusterconfiguratie. In de volgende secties vindt u een overzicht van de algemene clusterconfiguratieproblemen en de stappen die nodig zijn om deze problemen te beperken. 

## <a name="durability-mismatch"></a>Mismatch bij duurzaamheid
### <a name="overview"></a>Overzicht
De duurzaamheidswaarde voor een knooppunttype ServiceFabric wordt gedefinieerd in twee verschillende secties van een Azure Resource Manager-sjabloon. De sectie Extensie virtuele machineschaalset van de resource Virtuele machineschaalset en het gedeelte Knooppunttype van de clusterbron Servicefabric. Het is een vereiste dat de duurzaamheidswaarde in deze secties overeenkomt, anders mislukt de implementatie van de resource.

In de volgende sectie vindt u een voorbeeld van een duurzaamheidsmismatch tussen de duurzaamheidsinstelling voor de extensie Van de Virtuele Machine schaalset en de duurzaamheidsinstelling servicefabric-knooppunt:  

**Duurzaamheidsinstelling virtuele machineschaalset**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Duurzaamheidsinstelling voor servicefabric-knooppunt** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Foutberichten
* De duurzaamheidsmismatch van de Virtual Machine Scale Set komt niet overeen met het huidige duurzaamheidsniveau van het Service Fabric-knooppunt
* De duurzaamheid van de Virtual Machine Scale Set komt niet overeen met het duurzaamheidsniveau van het doelservicestructuurknooppunt
* Virtual Machine Scale Set duurzaamheid komt overeen met het huidige Service Fabric duurzaamheidsniveau of het duurzaamheidsniveau van het doelservicestructuurknooppunt 

### <a name="mitigation"></a>Oplossing
Ga als bedoeld als gevolg van een oplossing voor een duurzaamheidsmismatch, die wordt aangegeven door een van de bovenstaande foutmeldingen:
1. Werk het duurzaamheidsniveau bij in de extensie Virtuele machineschaalset of sectie Service Fabric Node Type van de azure resource manager-sjabloon om ervoor te zorgen dat de waarden overeenkomen.
2. Implementeer de sjabloon Azure Resource Manager opnieuw met de bijgewerkte waarden.


## <a name="seed-node-deletion"></a>Zaadknooppunt verwijderen 
### <a name="overview"></a>Overzicht
Een cluster servicestructuur heeft een eigenschap [van de betrouwbaarheidslaag](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) die wordt gebruikt om het aantal replica's van systeemservices te bepalen die worden uitgevoerd op het primaire knooppunttype van het cluster. Het aantal vereiste replica's bepaalt het minimumaantal knooppunten dat moet worden gehandhaafd in het primaire knooppunttype van het cluster. Als het aantal knooppunten in het primaire knooppunttype onder het vereiste minimum voor de betrouwbaarheidslaag gaat, wordt het cluster instabiel.  

### <a name="error-messages"></a>Foutberichten 
Zaadknooppunt verwijdering operatie is gedetecteerd, en zal worden afgewezen. 
* Deze bewerking zou {0} resulteren in alleen potentiële zaadknooppunten {1} om in het cluster te blijven, terwijl dit minimaal nodig is.
* Het {0} verwijderen van {1} zaadknooppunten uit zou resulteren in de cluster naar beneden als gevolg van verlies van zaad knooppunt quorum. Maximum aantal zaadknooppunten dat tegelijk kan {2}worden verwijderd is .
 
### <a name="mitigation"></a>Oplossing 
Zorg ervoor dat uw primaire knooppunttype voldoende virtuele machines heeft voor de betrouwbaarheid die op uw cluster is opgegeven. U een virtuele machine niet verwijderen als de virtuele machineschaalset onder het minimumaantal knooppunten voor de opgegeven betrouwbaarheidslaag wordt geplaatst.
* Als de betrouwbaarheidslaag correct is opgegeven, controleert u of u voldoende knooppunten in het primaire knooppunttype hebt als dat nodig is voor de betrouwbaarheidslaag. 
* Als de betrouwbaarheidslaag onjuist is, start u een wijziging op de resource Service Fabric om eerst het betrouwbaarheidsniveau te verlagen voordat u een virtuele machineschaalsetbewerkingen start en wacht tot deze is voltooid.
* Als de betrouwbaarheidslaag Brons is, volgt u deze [stappen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) om uw cluster gracieus af te schalen.

## <a name="next-steps"></a>Volgende stappen
* Een cluster maken op VM's of computers met Windows Server: [clustercreatie van servicefabric voor Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op VM's of computers met Linux: [een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md)
* Problemen met de servicefabric: [handleidingen voor het oplossen van problemen oplossen](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
