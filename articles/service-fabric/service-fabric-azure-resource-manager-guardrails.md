---
title: Service Fabric Azure Resource Manager implementatie Guardrails | Microsoft Docs
description: Dit artikel bevat een overzicht van veelvoorkomende fouten bij het implementeren van een Service Fabric cluster via Azure Resource Manager en hoe u dit kunt voor komen.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: gamonroy
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: 3ea6f850685a695644cfc3073fc939a58901658c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828628"
---
# <a name="service-fabric-guardrails"></a>Service Fabric Guardrails 
Bij het implementeren van een Service Fabric cluster worden Guardrails geplaatst, waardoor een Azure Resource Manager implementatie in het geval van een ongeldige cluster configuratie mislukt. In de volgende secties vindt u een overzicht van algemene problemen met de cluster configuratie en de stappen die nodig zijn om deze problemen te verhelpen. 

## <a name="durability-mismatch"></a>Duurzaamheid komt niet overeen
### <a name="overview"></a>Overzicht
De duurzaamheids waarde voor een Service Fabric knooppunt type wordt gedefinieerd in twee verschillende secties van een Azure Resource Manager sjabloon. Het gedeelte extensie van de virtuele-machine Schaalset van de resource voor de Schaalset van de virtuele machine en de sectie type knoop punt van de Service Fabric cluster bron. Het is een vereiste dat de duurzaamheids waarde in deze secties overeenkomt, anders mislukt de implementatie van de resource.

De volgende sectie bevat een voor beeld van een duurzaamheid die niet overeenkomt tussen de instelling duurzaamheid van de Schaalset voor virtuele machines en de instelling duurzaamheid van het Service Fabric knooppunt type:  

**Duurzaamheids instelling voor virtuele-machine schaal sets**
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

**Instelling duurzaamheid van Service Fabric knooppunt type** 
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
* De duurzaamheid van de Schaalset van de virtuele machine komt niet overeen met het huidige niveau van de duurzaamheid van het Service Fabric knooppunt type
* Duurzaamheid van de Schaalset van de virtuele machine komt niet overeen met het doel Service Fabric duurzaamheids niveau van het knooppunt type
* De duurzaamheid van de virtuele-machine Schaalset komt overeen met het huidige Service Fabric duurzaamheids niveau of het doel type duurzaamheids niveau van het Service Fabric knooppunt 


### <a name="mitigation"></a>Oplossing
Het oplossen van een niet-overeenkomend duurzaamheid, dat wordt aangegeven door een van de bovenstaande fout berichten:
1. Werk het duurzaamheids niveau bij in de uitbrei ding voor de virtuele-machine Schaalset of het gedeelte Service Fabric knooppunt type van de Azure Resource Manager sjabloon om ervoor te zorgen dat de waarden overeenkomen.
2. Implementeer de Azure Resource Manager sjabloon opnieuw met de bijgewerkte waarden.

## <a name="next-steps"></a>Volgende stappen
* Een cluster maken op Vm's of computers met Windows Server: [service Fabric cluster maken voor Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op Vm's of computers met Linux: [een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md)
* Problemen oplossen Service Fabric: [richt lijnen voor probleem oplossing](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
