---
title: Automatische instantiereparaties met Azure-schaalsets voor virtuele machines
description: Meer informatie over het configureren van automatisch reparatiebeleid voor VM-exemplaren in een schaalset
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274812"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Voorbeeld: Automatische instantiereparaties voor azure-schaalsets voor virtuele machines

Door automatische instantiereparaties in te schakelen voor azure-eenvoudigstel voor virtuele machines, u een hoge beschikbaarheid voor toepassingen bereiken door een reeks gezonde exemplaren te behouden. Als een instantie in de schaalset ongezond blijkt te zijn, zoals gerapporteerd door [application health extension](./virtual-machine-scale-sets-health-extension.md) of Load [balancer health probes,](../load-balancer/load-balancer-custom-probe-overview.md)voert deze functie automatisch instantiereparatie uit door de niet-gezonde instantie te verwijderen en een nieuwe te maken om deze te vervangen.

> [!NOTE]
> Deze voorbeeldfunctie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Vereisten voor het gebruik van automatische instantiereparaties

**Opt-in voor de automatische voorbeeld van instantiereparaties**

Gebruik de REST API of Azure PowerShell om je aan te melden voor de preview van automatische instantiereparaties. Met deze stappen wordt uw abonnement voor de voorbeeldfunctie geregistreerd. Let op: dit is slechts een eenmalige installatie die nodig is voor het gebruik van deze functie. Als uw abonnement al is geregistreerd voor automatische voorbeeldreparaties, hoeft u zich niet opnieuw te registreren. 

REST API gebruiken 

1. Registreren voor de functie met [Functies - Registreren](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Wacht een paar minuten tot de *staat* is overgeboekt naar *Geregistreerd.* U de volgende API gebruiken om dit te bevestigen.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. Zodra de *staat* is gewijzigd in *Geregistreerd,* voer dan het volgende uit.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Azure PowerShell gebruiken

1. Registreer u voor de functie met behulp van cmdlet [Register-AzureRmResourceProvider,](/powershell/module/azurerm.resources/register-azurermresourceprovider) gevolgd door [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Wacht een paar minuten tot de *registratiestaat* is gewijzigd in *Geregistreerd.* U de volgende cmdlet gebruiken om dit te bevestigen.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 Het antwoord moet als volgt zijn.

| FeatureName                           | ProviderName            | RegistratieStaat       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Geregistreerd              |

3. Zodra de *registratiestaat* om te veranderen naar *Geregistreerd,* voer dan de volgende cmdlet.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Toepassingsstatusbewaking inschakelen voor schaalset**

De schaalset moet de statusbewaking van toepassingen hebben voor instanties die zijn ingeschakeld. Dit kan worden gedaan met behulp van [application health extensie](./virtual-machine-scale-sets-health-extension.md) of Load [balancer health probes](../load-balancer/load-balancer-custom-probe-overview.md). Slechts één van deze kan tegelijk worden ingeschakeld. De toepassingsstatusextensie of de load balancer-sondes pingen het toepassingseindpunt dat is geconfigureerd op virtuele machine-exemplaren om de status van de toepassing te bepalen. Deze status wordt gebruikt door de schaalsetorchestrator om de instantiestatus te controleren en reparaties uit te voeren wanneer dat nodig is.

**Eindpunt configureren om de status van de status te bieden**

Voordat u het beleid voor automatische instantiereparaties inschakelt, moet u ervoor zorgen dat het eindpunt van de toepassingsset is geconfigureerd om de status van de toepassing uit te zenden. Wanneer een instantie status 200 (OK) op dit toepassingseindpunt retourneert, wordt de instantie gemarkeerd als 'In orde'. In alle andere gevallen wordt de instantie gemarkeerd als 'Niet in orde', inclusief de volgende scenario's:

- Wanneer er geen toepassingseindpunt is geconfigureerd in de virtuele machine-exemplaren om de status van de toepassing te bieden
- Wanneer het eindpunt van de toepassing onjuist is geconfigureerd
- Wanneer het eindpunt van de toepassing niet bereikbaar is

Voor exemplaren die zijn gemarkeerd als 'Niet in orde', worden automatische reparaties geactiveerd door de schaalset. Zorg ervoor dat het eindpunt van de toepassing correct is geconfigureerd voordat u het automatische reparatiebeleid inschakelt om onbedoelde instantiereparaties te voorkomen, terwijl het eindpunt wordt geconfigureerd.

**Eén plaatsingsgroep inschakelen**

Deze voorbeeld is momenteel alleen beschikbaar voor schaalsets die als één plaatsingsgroep worden geïmplementeerd. De eigenschap *singlePlacementGroup* moet worden ingesteld op *true* voor uw schaal ingesteld op automatische instantie reparaties functie te gebruiken. Meer informatie over [plaatsingsgroepen](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**API-versie**

Automatisch reparatiebeleid wordt ondersteund voor compute API-versie 2018-10-01 of hoger.

**Beperkingen op resource- of abonnementsverplaatsingen**

Als onderdeel van deze preview worden resource- of abonnementsverplaatsingen momenteel niet ondersteund voor schaalsets wanneer het beleid voor automatische reparaties is ingeschakeld.

**Beperking voor schaalsets voor servicefabric**

Deze voorbeeldfunctie wordt momenteel niet ondersteund voor schaalsets voor servicefabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Hoe werken automatische instantiereparaties?

De functie Automatische instantiereparatie is afhankelijk van statusbewaking van afzonderlijke exemplaren in een schaalset. VM-exemplaren in een schaalset kunnen worden geconfigureerd om de status van de toepassing uit te zenden met behulp van de [extensie Toepassingsstatus](./virtual-machine-scale-sets-health-extension.md) of [de statussondes van de load balancer.](../load-balancer/load-balancer-custom-probe-overview.md) Als wordt vastgesteld dat een instantie niet in orde is, voert de schaalset herstelactie uit door de niet-gezonde instantie te verwijderen en een nieuwe instantie te maken om deze te vervangen. Deze functie kan worden ingeschakeld in het model van de virtuele machineschaalset met behulp van het object *AutomaticRepairsPolicy.*

### <a name="batching"></a>Batchverwerking

De automatische instantiereparatiebewerkingen worden in batches uitgevoerd. Op een gegeven moment wordt niet meer dan 5% van de exemplaren in de schaalset gerepareerd via het automatische reparatiebeleid. Dit helpt gelijktijdige verwijdering en opnieuw maken van een groot aantal exemplaren te voorkomen als ze tegelijkertijd ongezond worden bevonden.

### <a name="grace-period"></a>Respijtperiode

Wanneer een instantie een statuswijzigingsbewerking doorloopt vanwege een actie PUT, PATCH of POST die op de schaalset wordt uitgevoerd (bijvoorbeeld reimage, redeploy, update, enz.), wordt elke reparatieactie op die instantie alleen uitgevoerd na het wachten op de respijtperiode. Respijtperiode is de hoeveelheid tijd om de instantie terug te laten keren naar een gezonde staat. De respijtperiode begint nadat de statuswijziging is voltooid. Dit helpt voortijdige of toevallige reparatieoperaties te voorkomen. De respijtperiode wordt gehonoreerd voor elk nieuw gemaakt exemplaar in de schaalset (inclusief de instantie die is gemaakt als gevolg van reparatiebewerking). De respijtperiode is opgegeven in minuten in ISO 8601-indeling en kan worden ingesteld met behulp van de *eigenschap automaticRepairsPolicy.gracePeriod*. De respijtperiode kan variëren tussen 30 minuten en 90 minuten en heeft een standaardwaarde van 30 minuten.

Het automatische reparatieproces van de instantie werkt als volgt:

1. [Application Health extension](./virtual-machine-scale-sets-health-extension.md) or [Load balancer health probes](../load-balancer/load-balancer-custom-probe-overview.md) ping the application endpoint inside each virtual machine in the scale set to get application health status for each instance.
2. Als het eindpunt reageert met een status 200 (OK), wordt de instantie gemarkeerd als 'Gezond'. In alle andere gevallen (ook als het eindpunt onbereikbaar is), wordt de instantie gemarkeerd als 'Niet in orde'.
3. Wanneer wordt vastgesteld dat een instantie niet in orde is, activeert de schaalset een herstelactie door de niet-gezonde instantie te verwijderen en een nieuwe instantie te maken om deze te vervangen.
4. Reparatie van bijvoorbeelden wordt in batches uitgevoerd. Op een gegeven moment wordt niet meer dan 5% van de totale exemplaren in de schaalset gerepareerd. Als een schaalset minder dan 20 exemplaren heeft, worden de reparaties uitgevoerd voor één ongezonde instantie tegelijk.
5. Het bovenstaande proces gaat door totdat alle ongezonde instantie in de schaalset is hersteld.

## <a name="instance-protection-and-automatic-repairs"></a>Bescherming van de instantie en automatische reparaties

Als een instantie in een schaalset wordt beschermd door het *[beveiligingsbeleid Beschermen tegen schaalinstellingen](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* toe te passen, worden automatische reparaties niet uitgevoerd op die instantie.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatisch reparatiebeleid inschakelen bij het maken van een nieuwe schaalset

Als u het automatische reparatiebeleid wilt inschakelen terwijl u een nieuwe schaalset maakt, moet u ervoor zorgen dat aan alle [vereisten](#requirements-for-using-automatic-instance-repairs) voor deelname aan deze functie wordt voldaan. Het eindpunt van de toepassing moet correct zijn geconfigureerd voor schaalset-instanties om te voorkomen dat onbedoelde reparaties worden geactiveerd terwijl het eindpunt wordt geconfigureerd. Voor nieuw gemaakte schaalsets worden eventuele instantiereparaties alleen uitgevoerd na het wachten op de duur van de respijtperiode. Als u de automatische instantiereparatie in een schaalset wilt inschakelen, gebruikt u het object *AutomaticRepairsPolicy* in het model van de virtuele machineschaalset.

### <a name="rest-api"></a>REST API

In het volgende voorbeeld ziet u hoe u automatische instantiereparatie in een schaalsetmodel inschakelt. Gebruik API-versie 2018-10-01 of hoger.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

De automatische instantiereparatiefunctie kan worden ingeschakeld terwijl u een nieuwe weegschaal maakt met de cmdlet [Nieuw-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Dit voorbeeldscript doorloopt het maken van een schaalset en bijbehorende resources met behulp van het configuratiebestand: [Maak een complete virtuele machineschaalset.](./scripts/powershell-sample-create-complete-scale-set.md) U het beleid voor automatische instantiereparaties configureren door de parameters *EnableAutomaticRepair* en *AutomaticRepairGracePeriod* toe te voegen aan het configuratieobject voor het maken van de schaalset. In het volgende voorbeeld wordt de functie met een respijtperiode van 30 minuten mogelijk gemaakt.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatisch reparatiebeleid inschakelen bij het bijwerken van een bestaande schaalset

Voordat u het automatische reparatiebeleid inschakelt in een bestaande schaalset, moet u ervoor zorgen dat aan alle [vereisten](#requirements-for-using-automatic-instance-repairs) voor deelname aan deze functie wordt voldaan. Het eindpunt van de toepassing moet correct zijn geconfigureerd voor schaalset-instanties om te voorkomen dat onbedoelde reparaties worden geactiveerd terwijl het eindpunt wordt geconfigureerd. Als u de automatische instantiereparatie in een schaalset wilt inschakelen, gebruikt u het object *AutomaticRepairsPolicy* in het model van de virtuele machineschaalset.

### <a name="rest-api"></a>REST API

In het volgende voorbeeld wordt het beleid met een respijtperiode van 40 minuten mogelijk. Gebruik API-versie 2018-10-01 of hoger.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om de configuratie van de automatische instantiereparatiefunctie in een bestaande schaalset te wijzigen. In het volgende voorbeeld wordt de respijtperiode bijgewerkt tot 40 minuten.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Problemen oplossen

**Het niet inschakelen van automatisch reparatiebeleid**

Als u een fout van 'BadRequest' krijgt met een bericht met de vermelding 'Kon lid 'automatisch Reparatiebeleid' niet vinden op object van type 'eigenschappen'", controleer dan de API-versie die wordt gebruikt voor de detectie van virtuele machineschaal. API-versie 2018-10-01 of hoger is vereist voor deze functie.

**Instantie wordt niet gerepareerd, zelfs niet als het beleid is ingeschakeld**

De instantie kan in respijtperiode zijn. Dit is de hoeveelheid tijd om te wachten na een statuswijziging in de instantie voordat u reparaties uitvoert. Dit om voortijdige of toevallige reparaties te voorkomen. De reparatie actie moet gebeuren zodra de respijtperiode is voltooid voor de instantie.

**Status van toepassing weergeven voor schaalsetinstanties**

U de [API voor instantieweergave opdoen](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) voor instanties in een virtuele machineschaalset gebruiken om de status van de toepassingsstatus weer te geven. Met Azure PowerShell u de cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) gebruiken met de *-InstanceView-vlag.* De status van de toepassingsstatus wordt verstrekt onder de *eigenschap vmHealth*.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren [van application health extensie](./virtual-machine-scale-sets-health-extension.md) of Load [balancer health probes](../load-balancer/load-balancer-custom-probe-overview.md) voor uw schaalsets.
