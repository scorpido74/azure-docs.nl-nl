---
title: Een virtueel Azure-netwerk (klassiek) migreren van een affiniteits groep naar een regio | Microsoft Docs
description: Meer informatie over het migreren van een virtueel netwerk (klassiek) van een affiniteits groep naar een regio.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: d33d9ec4eadeaa3a082103f1ad699e2fc3010e3b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058403"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Een virtueel netwerk (klassiek) migreren van een affiniteits groep naar een regio

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dit artikel gaat over het gebruik van het klassieke implementatiemodel. Micro soft raadt aan de meeste nieuwe implementaties het Resource Manager-implementatie model te gebruiken.

Affiniteits groepen zorgen ervoor dat resources die zijn gemaakt in dezelfde affiniteits groep fysiek worden gehost door servers die dicht bij elkaar staan, waardoor deze bronnen sneller kunnen communiceren. In het verleden waren affiniteits groepen een vereiste voor het maken van virtuele netwerken (klassiek). Op dat moment kan de netwerk beheer service die beheerde virtuele netwerken (klassiek) heeft uitgevoerd, alleen werken in een set fysieke servers of schaal eenheden. De verbeteringen van de architectuur hebben het bereik van netwerk beheer tot een regio verbeterd.

Als gevolg van deze verbeteringen in de architectuur worden affiniteits groepen niet meer aanbevolen of vereist voor virtuele netwerken (klassiek). Het gebruik van affiniteits groepen voor virtuele netwerken (klassiek) wordt vervangen door regio's. Virtuele netwerken (klassiek) die zijn gekoppeld aan regio's, worden regionale virtuele netwerken genoemd.

U wordt aangeraden geen affiniteits groepen te gebruiken in het algemeen. Afgezien van de vereiste van het virtuele netwerk waren affiniteits groepen ook van belang om ervoor te zorgen dat resources, zoals Compute (Classic) en Storage (klassiek), bij elkaar worden geplaatst. Met de huidige Azure-netwerk architectuur zijn deze plaatsings vereisten echter niet meer nodig.

> [!IMPORTANT]
> Hoewel het nog steeds technisch mogelijk is om een virtueel netwerk te maken dat is gekoppeld aan een affiniteits groep, is er geen dwingende reden om dit te doen. Veel functies van een virtueel netwerk, zoals netwerk beveiligings groepen, zijn alleen beschikbaar wanneer u een regionaal virtueel netwerk gebruikt en zijn niet beschikbaar voor virtuele netwerken die zijn gekoppeld aan affiniteits groepen.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Het netwerk configuratie bestand bewerken

1. Exporteer het netwerk configuratie bestand. Zie [een virtueel netwerk configureren met behulp van een netwerk configuratie bestand](virtual-networks-using-network-configuration-file.md#export)voor meer informatie over het exporteren van een netwerk configuratie bestand met Power shell of de Azure-opdracht regel interface (CLI) 1,0.
2. Bewerk het netwerk configuratie bestand, waarbij **AffinityGroup** wordt vervangen door **locatie**. U geeft een Azure- [regio](https://azure.microsoft.com/regions) voor de **locatie**op.
   
   > [!NOTE]
   > De **locatie** is de regio die u hebt opgegeven voor de affiniteits groep die is gekoppeld aan uw virtuele netwerk (klassiek). Als uw virtuele netwerk (klassiek) bijvoorbeeld is gekoppeld aan een affiniteits groep die zich in VS West bevindt en wanneer u migreert, moet uw **locatie** verwijzen naar VS West. 
   > 
   > 
   
    Bewerk de volgende regels in het netwerk configuratie bestand door uw eigen waarden te vervangen: 
   
    **Oude waarde:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Nieuwe waarde:** \<VirtualNetworkSitename = "VNetUSWest" Location = "VS-West"\>
3. Sla uw wijzigingen op en [Importeer](virtual-networks-using-network-configuration-file.md#import) de netwerk configuratie in Azure.

> [!NOTE]
> Deze migratie veroorzaakt geen uitval tijd voor uw services.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Wat te doen als u een virtuele machine (klassiek) in een affiniteits groep hebt
Vm's (klassiek) die zich momenteel in een affiniteits groep bevinden, hoeven niet te worden verwijderd uit de affiniteits groep. Zodra een virtuele machine is geïmplementeerd, wordt deze geïmplementeerd op één schaal eenheid. Met affiniteits groepen kan de set beschik bare VM-grootten voor een nieuwe VM-implementatie worden beperkt, maar elke bestaande VM die wordt geïmplementeerd, is al beperkt tot de set VM-grootten die beschikbaar zijn in de schaal eenheid waarin de VM wordt geïmplementeerd. Omdat de virtuele machine al is geïmplementeerd op een schaal eenheid, heeft het verwijderen van een virtuele machine uit een affiniteits groep geen invloed op de virtuele machine.
