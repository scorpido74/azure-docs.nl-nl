---
title: Ontwerpoverwegingen voor Azure Virtual Machine Scale Sets
description: Meer informatie over de ontwerpoverwegingen voor uw Azure Virtual Machine Scale Sets. Vergelijk schaalsets functies met VM-functies.
keywords: linux virtuele machine, virtuele machine schaal sets
author: mimckitt
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 06/01/2017
ms.author: mimckitt
ms.openlocfilehash: 20f6cb08781c7c6aca7a4022e75a7be8640ef18a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273763"
---
# <a name="design-considerations-for-scale-sets"></a>Ontwerpoverwegingen voor schaalsets
In dit artikel worden ontwerpoverwegingen voor virtuele machineschaalsets besproken. Raadpleeg overzicht van [virtuele machineschaalsets](virtual-machine-scale-sets-overview.md)voor informatie over wat virtuele machineschaalsets zijn.

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Wanneer schaalsets gebruiken in plaats van virtuele machines?
Over het algemeen zijn schaalsets handig voor het implementeren van zeer beschikbare infrastructuur waarbij een set machines een vergelijkbare configuratie heeft. Sommige functies zijn echter alleen beschikbaar in schaalsets, terwijl andere functies alleen beschikbaar zijn in VM's. Om een weloverwogen beslissing te nemen over wanneer u elke technologie moet gebruiken, moet u eerst een kijkje nemen naar enkele van de veelgebruikte functies die beschikbaar zijn in schaalsets, maar niet VM's:

### <a name="scale-set-specific-features"></a>Setspecifieke functies schalen

- Zodra u de configuratie van de schaalset hebt opgegeven, u de *eigenschap capaciteit* bijwerken om meer VM's parallel te implementeren. Dit proces is beter dan het schrijven van een script om het implementeren van veel individuele VM's parallel te orkestreren.
- U [Azure Autoscale gebruiken om automatisch een schaalset te schalen,](./virtual-machine-scale-sets-autoscale-overview.md) maar geen afzonderlijke VM's.
- U [de vm's van de schaalschaal opnieuw instellen,](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) maar [geen afzonderlijke VM's.](https://docs.microsoft.com/rest/api/compute/virtualmachines)
- U [VM's overprovisionen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) voor meer betrouwbaarheid en snellere implementatietijden. U afzonderlijke VM's niet overbieden, tenzij u aangepaste code schrijft om deze actie uit te voeren.
- U een [upgradebeleid](./virtual-machine-scale-sets-upgrade-scale-set.md) opgeven om het eenvoudig te maken om upgrades uit te rollen in VM's in uw schaalset. Met afzonderlijke VM's moet u zelf updates orkestreren.

### <a name="vm-specific-features"></a>VM-specifieke functies

Sommige functies zijn momenteel alleen beschikbaar in VM's:

- U een afbeelding van een afzonderlijke virtuele machine vastleggen, maar niet van een vm in een schaalset.
- U een afzonderlijke virtuele machine migreren van native schijven naar beheerde schijven, maar u vm-exemplaren niet migreren in een schaalset.
- U IP-adressen van IPv6 toewijzen aan afzonderlijke virtuele VM-netwerkinterfacekaarten (NIC's), maar dit kan niet voor VM-exemplaren in een schaalset. U Ip-adressen van IPv6 toewijzen aan load balancers voor afzonderlijke VM's of vm's voor schaalsets.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Schaalsets met Azure Managed Disks
Schaalsets kunnen worden gemaakt met [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) in plaats van traditionele Azure-opslagaccounts. Beheerde schijven bieden de volgende voordelen:
- U hoeft geen vooraf gemaakte set Azure-opslagaccounts voor de vm's van de schaalset te maken.
- U [gekoppelde gegevensschijven](virtual-machine-scale-sets-attached-disks.md) voor de VM's in uw schaalset definiëren.
- Schaalsets kunnen worden geconfigureerd om [maximaal 1.000 VM's in een set](virtual-machine-scale-sets-placement-groups.md)te ondersteunen. 

Als u een bestaande sjabloon hebt, u de sjabloon ook [bijwerken om Beheerde schijven te gebruiken.](virtual-machine-scale-sets-convert-template-to-md.md)

### <a name="user-managed-storage"></a>Door de gebruiker beheerde opslag
Een schaalset die niet is gedefinieerd met Azure Managed Disks is gebaseerd op door gebruikers gemaakte opslagaccounts om de BE-schijven van de VM's in de set op te slaan. Een verhouding van 20 VM's per opslagaccount of minder wordt aanbevolen om maximale IO te bereiken en ook te profiteren van _overprovisioning_ (zie hieronder). Het wordt ook aanbevolen dat u de begintekens van de namen van het opslagaccount over het alfabet verspreidt. Hierdoor wordt de belasting over verschillende interne systemen gespreid. 


## <a name="overprovisioning"></a>Overprovisioning
Schaalstelt momenteel standaard in op 'overprovisioning' VM's. Met overprovisioning ingeschakeld, de schaal set eigenlijk spins up meer VM's dan u gevraagd, dan verwijdert de extra VM's zodra het gevraagde aantal VM's met succes zijn ingericht. Overprovisioning verbetert de inrichten van slagingspercentages en vermindert de implementatietijd. U wordt niet gefactureerd voor de extra VM's en deze tellen niet mee voor uw quotumlimieten.

Hoewel overprovisioning de provisioning-slagingspercentages verbetert, kan dit verwarrend gedrag veroorzaken voor een toepassing die niet is ontworpen om extra VM's te verwerken die verschijnen en vervolgens verdwijnen. Als u overprovisioning wilt uitschakelen, moet u `"overprovision": "false"`ervoor zorgen dat u de volgende tekenreeks in uw sjabloon hebt: . Meer details zijn te vinden in de [Api-documentatie van de scaleset REST.](/rest/api/virtualmachinescalesets/create-or-update-a-set)

Als uw schaalset door de gebruiker beheerde opslag gebruikt en u overprovisioning uitschakelt, u meer dan 20 VM's per opslagaccount hebben, maar het wordt afgeraden om boven de 40 te gaan om io-prestatieredenen. 

## <a name="limits"></a>Limieten
Een schaalset die is gebouwd op een Marketplace-afbeelding (ook wel een platformafbeelding genoemd) en geconfigureerd voor het gebruik van Azure Managed Disks, ondersteunt een capaciteit van maximaal 1.000 VM's. Als u de schaalset configureert om meer dan 100 VM's te ondersteunen, werken niet alle scenario's hetzelfde (bijvoorbeeld load balancing). Zie [Werken met grote virtuele machineschaalsets](virtual-machine-scale-sets-placement-groups.md)voor meer informatie. 

Een schaalset die is geconfigureerd met door de gebruiker beheerde opslagaccounts is momenteel beperkt tot 100 VM's (en 5 opslagaccounts worden aanbevolen voor deze schaal).

Een schaalset die is gebouwd op een aangepaste afbeelding (een door u gebouwd) kan een capaciteit van maximaal 600 VM's hebben wanneer deze is geconfigureerd met Azure Managed-schijven. Als de schaalset is geconfigureerd met door de gebruiker beheerde opslagaccounts, moeten alle OS-schijf-VHD's binnen één opslagaccount worden gemaakt. Als gevolg hiervan is het maximaal aanbevolen aantal VM's in een schaalset die is gebouwd op een aangepaste afbeelding en door de gebruiker beheerde opslag 20. Als u overprovisioning uitschakelt, u tot 40 gaan.

Voor meer VM's dan deze limieten toestaan, moet u meerdere schaalsets implementeren zoals in deze sjabloon wordt [weergegeven.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)

