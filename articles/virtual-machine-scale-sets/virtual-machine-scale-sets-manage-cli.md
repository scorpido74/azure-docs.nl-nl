---
title: Virtuele machineschaalsets beheren met de Azure CLI
description: Algemene Azure CLI-opdrachten voor het beheren van virtuele machineschaalsets, zoals het starten en stoppen van een instantie of het wijzigen van de schaalsetcapaciteit.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: ad07a237dd40d836705b332c6c10356720901481
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011579"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Een virtuele machineschaalset beheren met de Azure CLI
Gedurende de levenscyclus van een schaalset voor virtuele machines moet u mogelijk een of meer beheertaken uitvoeren. Bovendien wilt u misschien scripts maken die verschillende levenscyclustaken automatiseren. In dit artikel worden enkele van de algemene Azure CLI-opdrachten beschreven waarmee u deze taken uitvoeren.

Om deze beheertaken te voltooien, hebt u de nieuwste Azure CLI nodig. Zie Azure [CLI installeren](/cli/azure/install-azure-cli)voor informatie. Als u een virtuele machineschaalset moet maken, u [een schaalset maken met de Azure CLI.](quick-create-cli.md)


## <a name="view-information-about-a-scale-set"></a>Informatie over een schaalset weergeven
Als u de algemene informatie over een schaalset wilt bekijken, gebruikt u [de AZ VMSS-weergave](/cli/azure/vmss). In het volgende voorbeeld wordt informatie over de schaalset met de naam *myScaleSet* in de resourcegroep *myResourceGroup.* Voer als volgt uw eigen namen in:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Virtuele machines weergeven in een schaalset
Als u een lijst met VM-exemplaren in een schaalset wilt weergeven, gebruikt u [de lijst-exemplaren van AZ VMSS.](/cli/azure/vmss) In het volgende voorbeeld worden alle VM-exemplaren weergegeven in de schaalset met de naam *myScaleSet* in de resourcegroep *myResourceGroup.* Geef uw eigen waarden op voor deze namen:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Als u aanvullende informatie over een `--instance-id` specifieke VM-instantie wilt weergeven, voegt u de parameter toe aan [de az vmss-weergave en](/cli/azure/vmss) geeft u een te bekijken instantie op. In het volgende voorbeeld wordt informatie over VM-instantie *0* weergegeven in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Voer als volgt uw eigen namen in:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Verbindingsgegevens voor VM's weergeven
Als u verbinding wilt maken met de VM's in een schaalset, maakt u SSH of RDP een toegewezen openbaar IP-adres en poortnummer. Standaard worden NAT-regels (Network Address Translation) toegevoegd aan de Azure load balancer die extern verbindingsverkeer doorstuurt naar elke vm. Als u het adres en de poorten wilt weergeven om verbinding te maken met VM-exemplaren in een schaalset, gebruikt u [de lijst--verbindingsgegevens van AZ VMSS.](/cli/azure/vmss) In het volgende voorbeeld worden verbindingsgegevens voor VM-exemplaren weergegeven in de schaalset met de naam *myScaleSet* en in de resourcegroep *myResourceGroup.* Geef uw eigen waarden op voor deze namen:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>De capaciteit van een schaalset wijzigen
In de voorgaande opdrachten werden informatie weergegeven over uw schaalset en de VM-exemplaren. Als u het aantal exemplaren in de schaalset wilt verhogen of verkleinen, u de capaciteit wijzigen. De schaalset maakt of verwijdert het vereiste aantal VM's en configureert vervolgens de VM's om toepassingsverkeer te ontvangen.

Als u het aantal instanties wilt weergeven dat zich momenteel in een schaalset bevindt, gebruikt u [az vmss show](/cli/azure/vmss) en voert u een query uit op *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

U kunt vervolgens het aantal virtuele machines in de schaalset handmatig vergroten of verkleinen met [az vmss scale](/cli/azure/vmss). In het volgende voorbeeld wordt het aantal VM's in uw weegschaal ingesteld op *5:*

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Het duurt een paar minuten om de capaciteit van de schaalset bij te werken. Als u de capaciteit van een schaalset verlaagt, worden de VM's met de hoogste instantie-id's eerst verwijderd.


## <a name="stop-and-start-vms-in-a-scale-set"></a>VM’s in een schaalset stoppen en opstarten
Als u een of meer VM's in een schaalset wilt stoppen, gebruikt u [az vmss stop](/cli/azure/vmss#az-vmss-stop). Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om te stoppen. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestopt. Als u meerdere VM's wilt stoppen, scheidt u elke instantie-id met een spatie.

In het volgende voorbeeld wordt instantie *0* gestopt in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Gestopte VM's blijven toegewezen en blijven rekenkosten maken. Als u in plaats daarvan wilt dat de VM's worden toegewezen en alleen opslagkosten in rekening brengen, gebruikt u [az vmss deallocate](/cli/azure/vmss). Als u meerdere VM's wilt toewijzen, scheidt u elke instantie-id met een spatie. In het volgende voorbeeld wordt instantie *0* gestopt en verwijderd in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>VM's starten in een schaalset
Als u een of meer VM's in een schaalset wilt starten, gebruikt u [az vmss start](/cli/azure/vmss). Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestart. Als u meerdere VM's wilt starten, scheidt u elke instantie-id met een spatie.

In het volgende voorbeeld wordt instantie *0* gestart in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>VM’s in een schaalset opnieuw opstarten
Als u een of meer VM's opnieuw wilt starten in een schaalset, gebruikt u [az vmss opnieuw opstarten.](/cli/azure/vmss) Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om opnieuw op te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset opnieuw opgestart. Als u meerdere VM's opnieuw wilt starten, scheidt u elke instantie-id met een spatie.

In het volgende voorbeeld wordt instantie *0* opnieuw gestart in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>VM's uit een schaalset verwijderen
Als u een of meer VM's in een schaalset wilt verwijderen, gebruikt u [de verwijderinstanties van AZ VMSS.](/cli/azure/vmss) Met `--instance-ids` de parameter u een of meer VM's opgeven om te verwijderen. Als u * opgeeft voor de instantie-id, worden alle VM's in de schaalset verwijderd. Als u meerdere VM's wilt verwijderen, scheidt u elke instantie-id met een spatie.

In het volgende voorbeeld wordt instantie *0* verwijderd in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Volgende stappen
Andere veelvoorkomende taken voor schaalsets zijn het [implementeren van een toepassing](virtual-machine-scale-sets-deploy-app.md)en het upgraden van [VM-exemplaren](virtual-machine-scale-sets-upgrade-scale-set.md). U Azure CLI ook gebruiken om regels voor automatische schaal te [configureren.](virtual-machine-scale-sets-autoscale-overview.md)
