---
title: Automatische herstel van instanties met virtuele-machine schaal sets van Azure
description: Meer informatie over het configureren van het beleid voor automatische herstel bewerkingen voor VM-exemplaren in een schaalset
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274812"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Voor beeld: automatische herstel van exemplaren voor virtuele-machine schaal sets van Azure

Het inschakelen van automatische herstel van instanties voor virtuele-machine schaal sets van Azure zorgt voor hoge Beschik baarheid voor toepassingen door een set gezonde instanties te behouden. Als een exemplaar in de schaalset een slechte status heeft, zoals gerapporteerd door de status [uitbreiding](./virtual-machine-scale-sets-health-extension.md) van de toepassing of de Health-tests van de [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md), voert deze functie automatisch een exemplaar van het herstel uit door het beschadigde exemplaar te verwijderen en een nieuwe te maken om dit te vervangen.

> [!NOTE]
> Deze preview-functie is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Vereisten voor het gebruik van automatische exemplaar reparaties

**Opt-in voor de preview-versie van automatische exemplaar reparaties**

Gebruik de REST API of Azure PowerShell om u aan te melden voor de preview-versie van automatische exemplaar reparaties. Met deze stappen wordt uw abonnement geregistreerd voor de preview-functie. Houd er rekening mee dat u slechts één keer hoeft in te stellen voor het gebruik van deze functie. Als uw abonnement al is geregistreerd voor de preview-versie van automatische exemplaar reparaties, hoeft u zich niet opnieuw te registreren. 

REST API gebruiken 

1. Registreren voor de functie met behulp van [functies-REGI ster](/rest/api/resources/features/register) 

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

2. Wacht een paar minuten totdat de *status* is gewijzigd in *geregistreerd*. U kunt de volgende API gebruiken om dit te bevestigen.

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

3. Zodra de *status* is gewijzigd in *geregistreerd*, voert u de volgende handelingen uit.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Azure PowerShell gebruiken

1. Registreer u voor de functie met cmdlet [REGI ster-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) gevolgd door [REGI ster-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Wacht enkele minuten totdat de *RegistrationState* is gewijzigd in *geregistreerd*. U kunt de volgende cmdlet gebruiken om dit te bevestigen.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 Het antwoord moet er als volgt uitzien.

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Geregistreerd              |

3. Zodra de *RegistrationState* is gewijzigd in *geregistreerd*, voert u de volgende cmdlet uit.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Status controle van de toepassing inschakelen voor de schaalset**

De schaalset moet controle van de toepassings status hebben voor instanties ingeschakeld. U kunt dit doen met behulp van de status-of [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md)van de [toepassing](./virtual-machine-scale-sets-health-extension.md) . Slechts één van deze kan tegelijkertijd worden ingeschakeld. De uitbrei ding voor de toepassings status of de load balancer test Ping het toepassings eindpunt dat is geconfigureerd op de exemplaren van de virtuele machine om de status van de toepassings status te bepalen. Deze status wordt gebruikt door de orchestrator van de schaalset om de status van het exemplaar te controleren en herstel bewerkingen uit te voeren wanneer dat nodig is.

**Eind punt configureren om de status op te geven**

Voordat u automatisch beleid voor instanties herstellen inschakelt, moet u ervoor zorgen dat het toepassings eindpunt van de schaalset is geconfigureerd voor het verzenden van de status van de toepassings status. Wanneer een exemplaar de status 200 (OK) voor dit toepassings eindpunt retourneert, wordt het exemplaar als ' in orde ' gemarkeerd. In alle andere gevallen is het exemplaar gemarkeerd als ' beschadigd ', met inbegrip van de volgende scenario's:

- Wanneer er geen toepassings eindpunt is geconfigureerd in de exemplaren van de virtuele machine om de status van de toepassing te bieden
- Wanneer het eind punt van de toepassing onjuist is geconfigureerd
- Wanneer het eind punt van de toepassing niet bereikbaar is

Voor instanties die zijn gemarkeerd als ' beschadigd ', worden automatisch reparaties geactiveerd door de schaalset. Zorg ervoor dat het eind punt van de toepassing correct is geconfigureerd voordat u het automatische reparatie beleid inschakelt om onbedoelde exemplaar reparaties te voor komen, terwijl het eind punt wordt geconfigureerd.

**Eén plaatsings groep inschakelen**

Deze preview is momenteel alleen beschikbaar voor schaal sets die zijn geïmplementeerd als één plaatsings groep. De eigenschap *singlePlacementGroup* moet worden ingesteld op *True* voor de schaalset om de functie Automatische exemplaar reparatie te gebruiken. Meer informatie over [plaatsings groepen](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**API-versie**

Het beleid voor automatisch herstellen wordt ondersteund voor Compute API versie 2018-10-01 of hoger.

**Beperkingen voor het verplaatsen van resources of abonnementen**

Als onderdeel van deze preview-fase worden resource-of abonnements bewegingen momenteel niet ondersteund voor schaal sets wanneer automatisch herstel beleid is ingeschakeld.

**Beperking voor service Fabric-schaal sets**

Deze preview-functie wordt momenteel niet ondersteund voor service Fabric-schaal sets.

## <a name="how-do-automatic-instance-repairs-work"></a>Hoe werkt automatische exemplaar reparaties?

De functie voor automatische exemplaar herstel is afhankelijk van de status controle van afzonderlijke exemplaren in een schaalset. VM-exemplaren in een schaalset kunnen worden geconfigureerd voor het verzenden van de status van de toepassing met behulp van de status uitbreiding van de [toepassing](./virtual-machine-scale-sets-health-extension.md) of de Health-tests van de [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md). Als een exemplaar niet in orde is, wordt de herstel actie door de schaalset uitgevoerd door de beschadigde instantie te verwijderen en een nieuwe te maken om deze te vervangen. Deze functie kan worden ingeschakeld in het model voor virtuele-machine schaal sets met behulp van het *automaticRepairsPolicy* -object.

### <a name="batching"></a>Batchverwerking

De automatische herstel bewerkingen voor instanties worden uitgevoerd in batches. Op elk gewenst moment worden niet meer dan 5% van de instanties in de schaalset hersteld via het beleid voor automatische reparaties. Dit helpt gelijktijdige verwijdering te voor komen en een groot aantal exemplaren opnieuw te maken als er een slechte status is gevonden.

### <a name="grace-period"></a>Respijt periode

Wanneer een exemplaar een status wijzigings bewerking doorloopt vanwege een PUT-, PATCH-of POST-actie die is uitgevoerd op de schaalset (bijvoorbeeld het opnieuw instellen van de installatie kopie, opnieuw implementeren, bijwerken, enz.), wordt een herstel actie voor dat exemplaar pas uitgevoerd nadat de respijt periode is verstreken. Respijt periode is de hoeveelheid tijd die het exemplaar kan retour neren naar de status in orde. De respijt periode begint nadat de status wijziging is voltooid. Dit helpt voor komen dat voor tijdig of onbedoeld herstel bewerkingen worden uitgevoerd. De respijt periode wordt gerespecteerd voor een nieuw gemaakt exemplaar in de schaalset (inclusief de waarde die is gemaakt als gevolg van een herstel bewerking). De respijt periode is opgegeven in minuten in de ISO 8601-indeling en kan worden ingesteld met behulp van de eigenschap *automaticRepairsPolicy. gracePeriod*. De respijt periode kan tussen 30 minuten en 90 minuten liggen en heeft een standaard waarde van 30 minuten.

Het proces voor automatische exemplaar reparaties werkt als volgt:

1. Met de status uitbreiding van de [toepassings status](./virtual-machine-scale-sets-health-extension.md) of [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) wordt het toepassings eindpunt gepingd in elke virtuele machine in de schaalset om de status van de toepassings status voor elk exemplaar op te halen.
2. Als het eind punt reageert met de status 200 (OK), wordt het exemplaar als ' in orde ' gemarkeerd. In alle andere gevallen (waaronder als het eind punt onbereikbaar is), is het exemplaar gemarkeerd als ' beschadigd '.
3. Wanneer een exemplaar beschadigd is, wordt door de schaalset een herstel actie geactiveerd door het beschadigde exemplaar te verwijderen en een nieuwe te maken om dit te vervangen.
4. Reparaties van instanties worden uitgevoerd in batches. Op een gegeven moment wordt er niet meer dan 5% van het totale aantal exemplaren in de schaalset hersteld. Als een schaalset minder dan 20 exemplaren heeft, worden de herstel bewerkingen uitgevoerd voor één slechte instantie tegelijk.
5. Het bovenstaande proces wordt voortgezet totdat alle beschadigde instanties in de schaalset zijn gerepareerd.

## <a name="instance-protection-and-automatic-repairs"></a>Instantie beveiliging en automatische reparaties

Als een exemplaar in een schaalset wordt beveiligd door het Toep assen *[van het beveiligings beleid voor het beveiligen van schaal sets](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* , worden er geen automatische herstel bewerkingen uitgevoerd voor dat exemplaar.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatisch herstel beleid inschakelen bij het maken van een nieuwe schaalset

Voor het inschakelen van automatisch reparatie beleid tijdens het maken van een nieuwe schaalset, moet u ervoor zorgen dat aan alle [vereisten](#requirements-for-using-automatic-instance-repairs) voor het aanmelden bij deze functie wordt voldaan. Het eind punt van de toepassing moet correct worden geconfigureerd voor instanties van een schaalset om te voor komen dat onbedoelde reparaties worden geactiveerd terwijl het eind punt wordt geconfigureerd. Voor nieuwe schaal sets worden de reparaties van instanties pas uitgevoerd nadat de duur van de respijt periode is verstreken. Als u het automatisch herstellen van exemplaren in een schaalset wilt inschakelen, gebruikt u *automaticRepairsPolicy* -object in het model voor virtuele-machine schaal sets.

### <a name="rest-api"></a>REST-API

In het volgende voor beeld ziet u hoe u het automatisch herstellen van exemplaren in een schaalset-model inschakelt. Gebruik API-versie 2018-10-01 of hoger.

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

De functie voor het automatisch herstellen van instanties kan worden ingeschakeld tijdens het maken van een nieuwe schaalset met behulp van de cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . In dit voorbeeld script wordt uitgelegd hoe u een schaalset en gekoppelde resources maakt met behulp van het configuratie bestand: [een volledige schaalset voor virtuele machines maken](./scripts/powershell-sample-create-complete-scale-set.md). U kunt automatisch beleid voor het herstellen van instanties configureren door de para meters *EnableAutomaticRepair* en *AutomaticRepairGracePeriod* toe te voegen aan het configuratie object voor het maken van de schaalset. In het volgende voor beeld wordt de functie ingeschakeld met een respijt periode van 30 minuten.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatisch herstel beleid inschakelen bij het bijwerken van een bestaande schaalset

Voordat u automatisch herstel beleid inschakelt in een bestaande schaalset, moet u ervoor zorgen dat aan alle [vereisten](#requirements-for-using-automatic-instance-repairs) voor het aanmelden bij deze functie is voldaan. Het eind punt van de toepassing moet correct worden geconfigureerd voor instanties van een schaalset om te voor komen dat onbedoelde reparaties worden geactiveerd terwijl het eind punt wordt geconfigureerd. Als u het automatisch herstellen van exemplaren in een schaalset wilt inschakelen, gebruikt u *automaticRepairsPolicy* -object in het model voor virtuele-machine schaal sets.

### <a name="rest-api"></a>REST-API

In het volgende voor beeld wordt het beleid met een respijt periode van 40 minuten ingeschakeld. Gebruik API-versie 2018-10-01 of hoger.

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

Gebruik de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om de configuratie van de functie voor het automatisch herstellen van exemplaren in een bestaande schaalset te wijzigen. In het volgende voor beeld wordt de respijt periode bijgewerkt naar 40 minuten.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Problemen oplossen

**Het inschakelen van het beleid voor automatisch herstellen is mislukt**

Als er een fout bericht ' onjuiste aanvraag ' wordt weer gegeven met de tekst ' kan geen automaticRepairsPolicy vinden voor het object van het type ' Eigenschappen ' ', controleert u de API-versie die wordt gebruikt voor de schaalset van de virtuele machine. API-versie 2018-10-01 of hoger is vereist voor deze functie.

**Exemplaar niet gerepareerd, zelfs wanneer beleid is ingeschakeld**

Het exemplaar kan zich in de respijt periode bevindt. Dit is de tijd die moet worden gewacht na een status wijziging in het exemplaar voordat de reparaties worden uitgevoerd. Dit is om te voor komen dat voor tijdig of onopzettelijke reparaties worden hersteld. De reparatie actie moet plaatsvinden zodra de respijt periode voor het exemplaar is voltooid.

**De status van de toepassing weer geven voor instanties van schaal sets**

U kunt de [weer gave-API voor instantie ophalen](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) voor instanties in een schaalset voor virtuele machines gebruiken om de status van de toepassings status weer te geven. Met Azure PowerShell kunt u de cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) gebruiken met de vlag *-InstanceView* . De status van de toepassing wordt gegeven in de eigenschap *vmHealth*.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van de status extensie van de [toepassing](./virtual-machine-scale-sets-health-extension.md) of de [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) voor uw schaal sets.
