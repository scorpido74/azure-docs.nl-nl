---
title: Knooppunttypen en virtuele-machineschaalsets
description: Lees hoe typen Azure Service Fabric-knooppunttypen zich verhouden tot virtuele machineschaalsets en hoe u op afstand verbinding maken met een instantie of clusterknooppunt voor een schaalset.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199713"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Typen Azure Service Fabric-knooppunttypen en virtuele machineschaalsets

[Virtuele machineschaalsets](/azure/virtual-machine-scale-sets) zijn een Azure-compute resource. U schaalsets gebruiken om een verzameling virtuele machines als set te implementeren en te beheren. Elk knooppunttype dat u definieert in een Azure Service Fabric-cluster stelt precies één schaalset in: meerdere knooppunttypen kunnen niet worden ondersteund door dezelfde schaalset en één knooppunttype mag (in de meeste gevallen) niet worden ondersteund door meerdere schaalsets. Een uitzondering hierop is in de zeldzame situatie van [verticale schaling](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) een knooppunttype, wanneer u tijdelijk twee schaalsets met dezelfde `nodeTypeRef` waarde hebt terwijl replica's worden gemigreerd van het origineel naar de bijgewerkte schaalset.

De runtime van Service Fabric wordt op elke virtuele machine geïnstalleerd in de schaal die is ingesteld door de *Microsoft.Azure.ServiceFabric* Virtual Machine-extensie. U elk knooppunttype zelfstandig omhoog of omlaag schalen, de OS SKU wijzigen die op elk clusterknooppunt wordt uitgevoerd, verschillende sets poorten openen en verschillende capaciteitsstatistieken gebruiken.

De volgende afbeelding toont een cluster met twee knooppunttypen, *frontend* en *backend genaamd.* Elk knooppunttype heeft vijf knooppunten.

![Een cluster met twee knooppunttypen][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Virtuele machineschaalstelinstanties toewijzen aan knooppunten

Zoals in de vorige figuur wordt weergegeven, beginnen schaalset-instanties bij instantie 0 en nemen deze vervolgens toe met 1. De nummering wordt weergegeven in de knooppuntnamen. Knooppunt BackEnd_0 is bijvoorbeeld instantie 0 van de backend-schaalset. Deze specifieke schaalset heeft vijf exemplaren, genaamd BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 en BackEnd_4.

Wanneer u een schaalset opschaalt, wordt een nieuwe instantie gemaakt. De nieuwe naam van de schaalsetinstantie is meestal de naam van de schaalset plus het volgende instantienummer. In ons voorbeeld is het BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Kaartschaal stelt load balancers in op knooppunttypen en schaalsets

Als u uw cluster hebt geïmplementeerd in de Azure-portal of de voorbeeldsjabloon Azure Resource Manager hebt gebruikt, worden alle resources onder een brongroep weergegeven. U de load balancers zien voor elke schaalset of knooppunttype. De naam van de load balancer gebruikt de volgende indeling: **LB-&lt;node type name&gt;**. Een voorbeeld is LB-sfcluster4doc-0, zoals in de volgende afbeelding wordt weergegeven:

![Resources][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric Virtual Machine Extension

Virtual Machine Extension voor servicestructuur wordt gebruikt om servicefabric op te startzetten voor Virtuele Azure-machines en de knooppuntbeveiliging te configureren.

Het volgende is een fragment van Service Fabric Virtual Machine extensie:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Hieronder volgen de beschrijvingen van de eigendommen:

| **Naam** | **Toegestane waarden** | **Richtlijnen of korte beschrijving** |
| --- | --- | --- | --- |
| name | tekenreeks | Unieke naam voor uitbreiding |
| type | "ServiceFabricLinuxNode" of "ServiceFabricWindowsNode" | Identificeert OS Service Fabric is bootstrapping aan |
| autoUpgradeMinorVersion | waar of onwaar | Automatische upgrade van SF Runtime-secundaire versies inschakelen |
| uitgever | Microsoft.Azure.ServiceFabric | Naam van de uitgever van de extensie Service Fabric |
| clusterEndpont | tekenreeks | URI:POORT naar Eindpunt Beheer |
| nodeTypeRef | tekenreeks | Naam van nodeType |
| duurzaamheidNiveau | brons, zilver, goud, platina | Tijd om onveranderlijke Azure-infrastructuur te pauzeren |
| paralleljobs inschakelen | waar of onwaar | Compute ParallelJobs inschakelen, zoals VM verwijderen en VM opnieuw opstarten in dezelfde schaalset parallel |
| nicPrefixOverride | tekenreeks | Subnetvoorvoegsel als "10.0.0.0/24" |
| algemene namen | tekenreeks[] | Algemene namen van geïnstalleerde clustercertificaten |
| x509StoreName | tekenreeks | Naam van store waar het geïnstalleerde clustercertificaat zich bevindt |
| typeHandlerVersie | 1.1 | Versie van Extensie. 1.0 klassieke versie van extensie wordt aanbevolen om te upgraden naar 1.1 |
| dataPath | tekenreeks | Pad naar het station dat wordt gebruikt om status op te slaan voor servicefabric-systeemservices en toepassingsgegevens.

## <a name="next-steps"></a>Volgende stappen

* Bekijk het [overzicht van de functie 'Overal implementeren' en een vergelijking met door Azure beheerde clusters.](service-fabric-deploy-anywhere.md)
* Meer informatie over [clusterbeveiliging](service-fabric-cluster-security.md).
* [Verbinding op afstand met](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) een specifieke schaalsetinstantie
* [De RDP-poortbereikwaarden](./scripts/service-fabric-powershell-change-rdp-port-range.md) na implementatie bijwerken
* [De gebruikersnaam en het wachtwoord van de beheerder voor](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) clusterVM's wijzigen

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
