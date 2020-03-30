---
title: Exemplaren van voorbeeldbeveiliging voor Azure-installatievan virtuele machine
description: Meer informatie over hoe u azure-installatie van de virtuele machineschaal beschermen tegen inschaal- en schaalbewerkingen.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254116"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Exemplaren van voorbeeldbeveiliging voor Azure-installatievan virtuele machine

Azure virtual machine scale sets enable better elasticity for your workloads through [Autoscale,](virtual-machine-scale-sets-autoscale-overview.md)so you can configure when your infrastructure scales-out and when it scales-in. Met schaalsets u ook een groot aantal VM's centraal beheren, configureren en bijwerken via verschillende [upgradebeleidsinstellingen.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) U een update configureren op het schaalsetmodel en de nieuwe configuratie wordt automatisch toegepast op elke schaalsetinstantie als u het upgradebeleid hebt ingesteld op Automatisch of rollen.

Naarmate uw toepassing verkeer verwerkt, kunnen er situaties zijn waarin u wilt dat specifieke exemplaren anders worden behandeld dan de rest van de schaalsetinstantie. Bepaalde exemplaren in de schaalset kunnen bijvoorbeeld langlopende bewerkingen uitvoeren en u wilt niet dat deze exemplaren worden ingeschaald totdat de bewerkingen zijn voltooid. Mogelijk hebt u ook een paar exemplaren in de schaalset gespecialiseerd om extra of andere taken uit te voeren dan de andere leden van de schaalset. U vereist dat deze 'speciale' VM's niet worden gewijzigd met de andere instanties in de schaalset. Instantiebeveiliging biedt de extra besturingselementen om deze en andere scenario's voor uw toepassing in te schakelen.

In dit artikel wordt beschreven hoe u de verschillende mogelijkheden voor instantiebescherming toepassen en gebruiken met schaalset-instanties.

## <a name="types-of-instance-protection"></a>Typen instantiebescherming
Schaalsets bieden twee soorten mogelijkheden voor instantiebescherming:

-   **Beschermen tegen scale-in**
    - Ingeschakeld door **protectFromScaleIn-eigenschap** op de schaalsetinstantie
    - Beschermt instantie tegen door Autoscale gestarte scale-in
    - Door de gebruiker geïnitieerde instantiebewerkingen (inclusief instantieverwijderen) worden **niet geblokkeerd**
    - Bewerkingen die zijn gestart op de schaalset (upgraden, opnieuw afbeelding, detoewijzing, enz.) worden **niet geblokkeerd**

-   **Beschermen tegen schaalsetacties**
    - Ingeschakeld door de eigenschap **ProtectFromScaleSetActions** op de schaalsetinstantie
    - Beschermt instantie tegen door Autoscale gestarte scale-in
    - Beschermt instantie tegen bewerkingen die zijn gestart op de schaalset (zoals upgraden, opnieuw afbeelding, detoewijzing, enz.)
    - Door de gebruiker geïnitieerde instantiebewerkingen (inclusief instantieverwijderen) worden **niet geblokkeerd**
    - Verwijderen van de volledige schaalset wordt **niet geblokkeerd**

## <a name="protect-from-scale-in"></a>Beschermen tegen scale-in
Instantiebeveiliging kan worden toegepast op schaalsetinstanties nadat de instanties zijn gemaakt. Bescherming wordt alleen toegepast en gewijzigd op het [instantiemodel](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) en niet op het [schaalmodel](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Er zijn meerdere manieren om schaalbescherming toe te passen op uw schaalsetinstanties, zoals beschreven in de onderstaande voorbeelden.

### <a name="azure-portal"></a>Azure Portal

U schaalbeveiliging via de Azure-portal toepassen op een instantie in de schaalset. U niet meer dan één instantie tegelijk aanpassen. Herhaal de stappen voor elk exemplaar dat u wilt beveiligen.
 
1. Ga naar een bestaande virtuele machineschaalset.
1. Selecteer **Instanties** in het menu aan de linkerkant onder **Instellingen**.
1. Selecteer de naam van de instantie die u wilt beveiligen.
1. Selecteer het tabblad **Beveiligingsbeleid.**
1. Selecteer in het blad **Beveiligingsbeleid** de optie **Beschermen tegen inschalen.**
1. Selecteer **Opslaan**. 

### <a name="rest-api"></a>REST API

In het volgende voorbeeld wordt schaalbeveiliging toegepast op een instantie in de schaalset.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Instantiebeveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) om schaalbeveiliging toe te passen op uw schaalsetinstantie.

In het volgende voorbeeld wordt schaalbeveiliging toegepast op een instantie in de schaalset met instantie-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Gebruik [de AZ VMSS-update](/cli/azure/vmss#az-vmss-update) om schaalbescherming toe te passen op uw schaalsetinstantie.

In het volgende voorbeeld wordt schaalbeveiliging toegepast op een instantie in de schaalset met instantie-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Beschermen tegen schaalsetacties
Instantiebeveiliging kan worden toegepast op schaalsetinstanties nadat de instanties zijn gemaakt. Bescherming wordt alleen toegepast en gewijzigd op het [instantiemodel](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) en niet op het [schaalmodel](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Als u een instantie beschermt tegen schaalsetacties, wordt de instantie ook beschermd tegen de door Autoscale gestarte scale-in.

Er zijn meerdere manieren om schaalsetbeveiliging toe te passen op uw schaalsetsetinstanties zoals beschreven in de onderstaande voorbeelden.

### <a name="azure-portal"></a>Azure Portal

U bescherming tegen schaalsetacties via de Azure-portal toepassen op een instantie in de schaalset. U niet meer dan één instantie tegelijk aanpassen. Herhaal de stappen voor elk exemplaar dat u wilt beveiligen.
 
1. Ga naar een bestaande virtuele machineschaalset.
1. Selecteer **Instanties** in het menu aan de linkerkant onder **Instellingen**.
1. Selecteer de naam van de instantie die u wilt beveiligen.
1. Selecteer het tabblad **Beveiligingsbeleid.**
1. Selecteer in het blad **Beveiligingsbeleid** de optie **Beveiligen van schaalingestelde acties.**
1. Selecteer **Opslaan**. 

### <a name="rest-api"></a>REST API

In het volgende voorbeeld wordt bescherming toegepast tegen schaalsetacties op een instantie in de schaalset.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Instantiebeveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger.</br>
Als u een instantie beschermt tegen schaalsetacties, wordt de instantie ook beschermd tegen de door Autoscale gestarte scale-in. U 'protectFromScaleIn' niet opgeven: false bij het instellen van 'protectFromScaleSetActions': true

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) om bescherming tegen schaalsetacties toe te passen op uw schaalsetinstantie.

In het volgende voorbeeld wordt bescherming toegepast tegen schaalsetacties op een instantie in de schaalset met instantie-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Gebruik [de AZ VMSS-update](/cli/azure/vmss#az-vmss-update) om bescherming tegen schaalsetacties toe te passen op uw schaalsetinstantie.

In het volgende voorbeeld wordt bescherming toegepast tegen schaalsetacties op een instantie in de schaalset met instantie-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Problemen oplossen
### <a name="no-protectionpolicy-on-scale-set-model"></a>Geen beschermingBeleid op schaal setmodel
Instantiebeveiliging is alleen van toepassing op schaalsetinstanties en niet op het schaalsetmodel.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Geen beveiligingBeleid op schaal set instance model
Standaard wordt het beveiligingsbeleid niet toegepast op een instantie wanneer deze wordt gemaakt.

U instantiebeveiliging toepassen om ingestelde instanties te schalen nadat de instanties zijn gemaakt.

### <a name="not-able-to-apply-instance-protection"></a>Niet in staat om instantiebescherming toe te passen
Instantiebeveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger. Controleer de API-versie die wordt gebruikt en update indien nodig. Mogelijk moet u uw PowerShell of CLI ook updaten naar de nieuwste versie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele machineschaalsets.
