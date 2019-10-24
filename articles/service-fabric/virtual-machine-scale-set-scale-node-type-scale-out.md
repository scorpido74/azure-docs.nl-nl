---
title: Een knooppunt type toevoegen aan een Azure Service Fabric-cluster | Microsoft Docs
description: Meer informatie over het uitschalen van een Service Fabric cluster door een Schaalset voor virtuele machines toe te voegen.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 1414e656a358af1e258c823cc7ec747fefa986ba
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598687"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Een Service Fabric cluster uitschalen door een schaalset voor virtuele machines toe te voegen
In dit artikel wordt beschreven hoe u een Azure Service Fabric cluster kunt schalen door een nieuw knooppunt type toe te voegen aan een bestaand cluster. Een Service Fabric cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw micro services worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een knoop punt genoemd. Virtuele-machine schaal sets vormen een Azure Compute-resource die u gebruikt om een verzameling virtuele machines als een set te implementeren en te beheren. Elk knooppunt type dat in een Azure-cluster is gedefinieerd, wordt [ingesteld als een afzonderlijke schaalset](service-fabric-cluster-nodetypes.md). Elk knooppunt type kan vervolgens afzonderlijk worden beheerd. Nadat u een Service Fabric cluster hebt gemaakt, kunt u horizon taal een cluster schalen door een nieuw knooppunt type (virtuele-machine schaalset) toe te voegen aan een bestaand cluster.  U kunt het cluster op elk gewenst moment schalen, zelfs wanneer werk belastingen op het cluster worden uitgevoerd.  Naarmate het cluster wordt geschaald, worden uw toepassingen ook automatisch geschaald.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Een extra schaalset toevoegen aan een bestaand cluster
Het toevoegen van een nieuw knooppunt type (dat wordt ondersteund door een virtuele-machine schaalset) naar een bestaand cluster, is vergelijkbaar met het [upgraden van het primaire knooppunt type](service-fabric-scale-up-node-type.md), behalve dat u niet dezelfde NodeTypeRef gebruikt. u kunt natuurlijk geen actieve virtuele-machine schaal sets uitschakelen en de beschik baarheid van het cluster niet verliezen als u het primaire knooppunt type niet bijwerkt. 

De eigenschap NodeTypeRef is gedeclareerd in de virtuele-machine schaalset Service Fabric extensie-eigenschappen:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

U moet dit nieuwe knooppunt type ook toevoegen aan uw Service Fabric cluster Bron:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het schalen van het primaire knooppunt type](service-fabric-scale-up-node-type.md)
* Meer informatie over [schaal baarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in-of uitschalen](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met behulp van de Fluent Azure Compute SDK.
* [Een zelfstandige cluster in-of uitschalen](service-fabric-cluster-windows-server-add-remove-nodes.md).

