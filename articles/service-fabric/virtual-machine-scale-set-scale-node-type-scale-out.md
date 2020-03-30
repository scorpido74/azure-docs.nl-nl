---
title: Een knooppunttype toevoegen aan een Azure Service Fabric-cluster
description: Meer informatie over het uitschalen van een cluster van Servicefabric door een virtuele machineschaalset toe te voegen.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 1e7bae89561d43d717eb4d15e95183761b077443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463970"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Een cluster van servicefabric schalen door een virtuele machineschaalset toe te voegen
In dit artikel wordt beschreven hoe u een Azure Service Fabric-cluster schalen door een nieuw knooppunttype toe te voegen aan een bestaand cluster. Een Service Fabric-cluster is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster wordt een knooppunt genoemd. Virtuele machineschaalsets zijn een Azure-compute resource die u gebruikt om een verzameling virtuele machines als set te implementeren en te beheren. Elk knooppunttype dat is gedefinieerd in een Azure-cluster, is [ingesteld als een afzonderlijke schaalset.](service-fabric-cluster-nodetypes.md) Elk knooppunttype kan vervolgens afzonderlijk worden beheerd. Nadat u een cluster servicestructuur hebt gemaakt, u een cluster horizontaal schalen door een nieuw knooppunttype (virtuele machineschaalset) toe te voegen aan een bestaand cluster.  U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd.  Naarmate het cluster schaalt, schalen uw toepassingen ook automatisch.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Een extra schaalset toevoegen aan een bestaand cluster
Het toevoegen van een nieuw knooppunttype (dat wordt ondersteund door een virtuele machineschaalset) aan een bestaand cluster is vergelijkbaar met [het upgraden van het primaire knooppunttype,](service-fabric-scale-up-node-type.md)behalve dat u niet dezelfde NodeTypeRef gebruikt; uiteraard zal niet worden uitgeschakeld een actief gebruikte virtuele machine schaal sets, en je zult niet verliezen cluster beschikbaarheid als u niet het primaire knooppunt type bij te werken. 

De eigenschap NodeTypeRef wordt gedeclareerd binnen de eigenschappen van de virtuele machineschaalset Service Fabric-extensie:
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

Bovendien moet u dit nieuwe knooppunttype toevoegen aan uw clusterbron servicestructuur:

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
* Meer informatie over het [opschalen van het primaire knooppunttype](service-fabric-scale-up-node-type.md)
* Meer informatie over [schaalbaarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in- of uitchecken](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met de vloeiende Azure compute SDK.
* [Een zelfstandig cluster in- of uitchecken](service-fabric-cluster-windows-server-add-remove-nodes.md).

