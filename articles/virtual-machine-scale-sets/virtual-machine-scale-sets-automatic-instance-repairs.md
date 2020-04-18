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
ms.openlocfilehash: 8156c563573183e51e06650914117f8787922e93
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603668"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatische instantiereparaties voor Azure-schaalsets voor virtuele machines

Door automatische instantiereparaties in te schakelen voor azure-eenvoudigstel voor virtuele machines, u een hoge beschikbaarheid voor toepassingen bereiken door een reeks gezonde exemplaren te behouden. Als een instantie in de schaalset ongezond blijkt te zijn, zoals gerapporteerd door [application health extension](./virtual-machine-scale-sets-health-extension.md) of Load [balancer health probes,](../load-balancer/load-balancer-custom-probe-overview.md)voert deze functie automatisch instantiereparatie uit door de niet-gezonde instantie te verwijderen en een nieuwe te maken om deze te vervangen.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Vereisten voor het gebruik van automatische instantiereparaties

**Toepassingsstatusbewaking inschakelen voor schaalset**

De schaalset moet de statusbewaking van toepassingen hebben voor instanties die zijn ingeschakeld. Dit kan worden gedaan met behulp van [application health extensie](./virtual-machine-scale-sets-health-extension.md) of Load [balancer health probes](../load-balancer/load-balancer-custom-probe-overview.md). Slechts één van deze kan tegelijk worden ingeschakeld. De toepassingsstatusextensie of de load balancer-sondes pingen het toepassingseindpunt dat is geconfigureerd op virtuele machine-exemplaren om de status van de toepassing te bepalen. Deze status wordt gebruikt door de schaalsetorchestrator om de instantiestatus te controleren en reparaties uit te voeren wanneer dat nodig is.

**Eindpunt configureren om de status van de status te bieden**

Voordat u het beleid voor automatische instantiereparaties inschakelt, moet u ervoor zorgen dat het eindpunt van de toepassingsset is geconfigureerd om de status van de toepassing uit te zenden. Wanneer een instantie status 200 (OK) op dit toepassingseindpunt retourneert, wordt de instantie gemarkeerd als 'In orde'. In alle andere gevallen wordt de instantie gemarkeerd als 'Niet in orde', inclusief de volgende scenario's:

- Wanneer er geen toepassingseindpunt is geconfigureerd in de virtuele machine-exemplaren om de status van de toepassing te bieden
- Wanneer het eindpunt van de toepassing onjuist is geconfigureerd
- Wanneer het eindpunt van de toepassing niet bereikbaar is

Voor exemplaren die zijn gemarkeerd als 'Niet in orde', worden automatische reparaties geactiveerd door de schaalset. Zorg ervoor dat het eindpunt van de toepassing correct is geconfigureerd voordat u het automatische reparatiebeleid inschakelt om onbedoelde instantiereparaties te voorkomen, terwijl het eindpunt wordt geconfigureerd.

**Eén plaatsingsgroep inschakelen**

Deze functie is momenteel alleen beschikbaar voor schaalsets die als één plaatsingsgroep worden geïmplementeerd. De eigenschap *singlePlacementGroup* moet worden ingesteld op *true* voor uw schaal ingesteld op automatische instantie reparaties functie te gebruiken. Meer informatie over [plaatsingsgroepen](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**API-versie**

Automatisch reparatiebeleid wordt ondersteund voor compute API-versie 2018-10-01 of hoger.

**Beperkingen op resource- of abonnementsverplaatsingen**

Bron- of abonnementsverplaatsingen worden momenteel niet ondersteund voor schaalsets wanneer de functie voor automatische reparaties is ingeschakeld.

**Beperking voor schaalsets voor servicefabric**

Deze functie wordt momenteel niet ondersteund voor schaalsets voor servicefabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Hoe werken automatische instantiereparaties?

De functie Automatische instantiereparatie is afhankelijk van statusbewaking van afzonderlijke exemplaren in een schaalset. VM-exemplaren in een schaalset kunnen worden geconfigureerd om de status van de toepassing uit te zenden met behulp van de [extensie Toepassingsstatus](./virtual-machine-scale-sets-health-extension.md) of [de statussondes van de load balancer.](../load-balancer/load-balancer-custom-probe-overview.md) Als wordt vastgesteld dat een instantie niet in orde is, voert de schaalset herstelactie uit door de niet-gezonde instantie te verwijderen en een nieuwe instantie te maken om deze te vervangen. Het nieuwste model voor de virtuele machineschaalwordt gebruikt om de nieuwe instantie te maken. Deze functie kan worden ingeschakeld in het model van de virtuele machineschaalset met behulp van het object *AutomaticRepairsPolicy.*

### <a name="batching"></a>Batchverwerking

De automatische instantiereparatiebewerkingen worden in batches uitgevoerd. Op een gegeven moment wordt niet meer dan 5% van de exemplaren in de schaalset gerepareerd via het automatische reparatiebeleid. Dit helpt gelijktijdige verwijdering en opnieuw maken van een groot aantal exemplaren te voorkomen als ze tegelijkertijd ongezond worden bevonden.

### <a name="grace-period"></a>Respijtperiode

Wanneer een instantie een statuswijzigingsbewerking doorloopt vanwege een actie PUT, PATCH of POST die op de schaalset wordt uitgevoerd (bijvoorbeeld reimage, redeploy, update, enz.), wordt elke reparatieactie op die instantie alleen uitgevoerd na het wachten op de respijtperiode. Respijtperiode is de hoeveelheid tijd om de instantie terug te laten keren naar een gezonde staat. De respijtperiode begint nadat de statuswijziging is voltooid. Dit helpt voortijdige of toevallige reparatieoperaties te voorkomen. De respijtperiode wordt gehonoreerd voor elk nieuw gemaakt exemplaar in de schaalset (inclusief de instantie die is gemaakt als gevolg van reparatiebewerking). De respijtperiode is opgegeven in minuten in ISO 8601-indeling en kan worden ingesteld met behulp van de *eigenschap automaticRepairsPolicy.gracePeriod*. De respijtperiode kan variëren tussen 30 minuten en 90 minuten en heeft een standaardwaarde van 30 minuten.

### <a name="suspension-of-repairs"></a>Opschorting van reparaties 

Virtuele machineschaalsets bieden de mogelijkheid om automatische revisies tijdelijk op te schorten indien nodig. De *serviceState* voor automatische reparaties onder de property *orchestrationServices* in bijvoorbeeld weergave van virtuele machine schaal set toont de huidige toestand van de automatische reparaties. Wanneer een schaalset wordt gekozen voor automatische reparaties, wordt de waarde van *parameterserviceState* ingesteld op *Uitvoeren*. Wanneer de automatische reparaties worden opgeschort voor een schaalset, wordt de *parameterserviceStaat* ingesteld op *Opgeschort*. Als *automatisch Reparatiebeleid* is gedefinieerd op een schaalset, maar de functie automatische reparaties niet is ingeschakeld, is de *parameterserviceStaat* ingesteld op *Niet actief*.

Als nieuw gemaakte exemplaren voor het vervangen van de ongezonde exemplaren in een schaalset ongezond blijven, zelfs na herhaaldelijk uitvoeren van reparatiebewerkingen, werkt het platform als veiligheidsmaatregel de *serviceState* bij voor automatische reparaties aan *Suspended.* U de automatische reparaties opnieuw hervatten door de waarde van *serviceState* in te stellen voor automatische reparaties op *uitvoeren.* Gedetailleerde instructies worden gegeven in de sectie over [het bekijken en bijwerken van de servicestatus van het automatische reparatiebeleid](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) voor uw schaalset. 

Het automatische reparatieproces van de instantie werkt als volgt:

1. [Application Health extension](./virtual-machine-scale-sets-health-extension.md) or [Load balancer health probes](../load-balancer/load-balancer-custom-probe-overview.md) ping the application endpoint inside each virtual machine in the scale set to get application health status for each instance.
2. Als het eindpunt reageert met een status 200 (OK), wordt de instantie gemarkeerd als 'Gezond'. In alle andere gevallen (ook als het eindpunt onbereikbaar is), wordt de instantie gemarkeerd als 'Niet in orde'.
3. Wanneer wordt vastgesteld dat een instantie niet in orde is, activeert de schaalset een herstelactie door de niet-gezonde instantie te verwijderen en een nieuwe instantie te maken om deze te vervangen.
4. Reparatie van bijvoorbeelden wordt in batches uitgevoerd. Op een gegeven moment wordt niet meer dan 5% van de totale exemplaren in de schaalset gerepareerd. Als een schaalset minder dan 20 exemplaren heeft, worden de reparaties uitgevoerd voor één ongezonde instantie tegelijk.
5. Het bovenstaande proces gaat door totdat alle ongezonde instantie in de schaalset is hersteld.

## <a name="instance-protection-and-automatic-repairs"></a>Bescherming van de instantie en automatische reparaties

Als een instantie in een schaalset wordt beschermd door een van de [beveiligingsbeleidsregels](./virtual-machine-scale-sets-instance-protection.md)toe te passen, worden automatische reparaties niet uitgevoerd op die instantie. Dit geldt zowel voor het beveiligingsbeleid: *Bescherm tegen scale-in* en *Bescherm tegen scale-set* acties. 

## <a name="terminatenotificationandautomaticrepairs"></a>Melding en automatische reparaties beëindigen

Als de [functie voor beëindigingsmeldingen](./virtual-machine-scale-sets-terminate-notification.md) is ingeschakeld op een schaalset en vervolgens tijdens de automatische reparatiebewerking volgt het verwijderen van een niet in orde zijnde instantie de configuratie van de beëindigingsmelding. Een terminate-melding wordt verzonden via azure-metagegevensservice – geplande gebeurtenissen – en het verwijderen van instanties wordt vertraagd voor de duur van de geconfigureerde time-out voor vertraging. Het maken van een nieuw exemplaar ter vervanging van de ongezonde, wacht echter niet tot de time-out van de vertraging is voltooid.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatisch reparatiebeleid inschakelen bij het maken van een nieuwe schaalset

Als u het automatische reparatiebeleid wilt inschakelen terwijl u een nieuwe schaalset maakt, moet u ervoor zorgen dat aan alle [vereisten](#requirements-for-using-automatic-instance-repairs) voor deelname aan deze functie wordt voldaan. Het eindpunt van de toepassing moet correct zijn geconfigureerd voor schaalset-instanties om te voorkomen dat onbedoelde reparaties worden geactiveerd terwijl het eindpunt wordt geconfigureerd. Voor nieuw gemaakte schaalsets worden eventuele instantiereparaties alleen uitgevoerd na het wachten op de duur van de respijtperiode. Als u de automatische instantiereparatie in een schaalset wilt inschakelen, gebruikt u het object *AutomaticRepairsPolicy* in het model van de virtuele machineschaalset.

U deze [snelstartsjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) ook gebruiken om een virtuele machineschaalset te implementeren met load balancer health probe en automatische instantiereparaties ingeschakeld met een respijtperiode van 30 minuten.

### <a name="azure-portal"></a>Azure Portal
 
De volgende stappen waarmee het beleid voor automatische reparaties mogelijk is bij het maken van een nieuwe schaalset.
 
1. Ga naar **virtuele machineschaalsets**.
1. Selecteer **+ Toevoegen** om een nieuwe schaalset te maken.
1. Ga naar het tabblad **Gezondheid.** 
1. Zoek de sectie **Gezondheid.**
1. Schakel de optie **Status van de toepassing Monitor** in.
1. Zoek de sectie **Automatisch reparatiebeleid.**
1. Schakel **On** de optie **Automatische reparaties in.**
1. Geef in **de respijtperiode (min)** de respijtperiode in minuten op, de toegestane waarden liggen tussen de 30 en 90 minuten. 
1. Wanneer u klaar bent met het maken van de nieuwe schaalset, selecteert u De knop **Controleren + maken.**

### <a name="rest-api"></a>REST-API

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

In het volgende voorbeeld wordt het automatische reparatiebeleid mogelijk gemaakt terwijl een nieuwe schaalset wordt gemaakt met behulp van *[az vmss maken.](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* Maak eerst een resourcegroep en maak vervolgens een nieuwe schaalset met automatische herstelperiode voor reparaties ingesteld op 30 minuten.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-period 30
```

In het bovenstaande voorbeeld wordt een bestaande load balancer en statussonde gebruikt voor het bewaken van de status van de status van instanties. Als u liever een toepassingsstatusextensie gebruikt voor monitoring, u een schaalset maken, de toepassingsstatusextensie configureren en vervolgens het beleid voor automatische instantiereparaties inschakelen met behulp van de *update van AZ VMSS,* zoals uitgelegd in de volgende sectie.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatisch reparatiebeleid inschakelen bij het bijwerken van een bestaande schaalset

Voordat u het automatische reparatiebeleid inschakelt in een bestaande schaalset, moet u ervoor zorgen dat aan alle [vereisten](#requirements-for-using-automatic-instance-repairs) voor deelname aan deze functie wordt voldaan. Het eindpunt van de toepassing moet correct zijn geconfigureerd voor schaalset-instanties om te voorkomen dat onbedoelde reparaties worden geactiveerd terwijl het eindpunt wordt geconfigureerd. Als u de automatische instantiereparatie in een schaalset wilt inschakelen, gebruikt u het object *AutomaticRepairsPolicy* in het model van de virtuele machineschaalset.

Nadat u het model van een bestaande schaalset hebt bijgewerkt, moet u ervoor zorgen dat het nieuwste model wordt toegepast op alle exemplaren van de schaal. Raadpleeg de instructie over [het up-to-date brengen van VM's met het nieuwste schaalmodel.](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-portal"></a>Azure Portal

U het automatische reparatiebeleid van een bestaande schaalset wijzigen via de Azure-portal. 
 
1. Ga naar een bestaande virtuele machineschaalset.
1. Selecteer **Gezondheid en reparatie**onder **Instellingen** in het menu aan de linkerkant .
1. Schakel de optie **Status van de toepassing Monitor** in.
1. Zoek de sectie **Automatisch reparatiebeleid.**
1. Schakel **On** de optie **Automatische reparaties in.**
1. Geef in **de respijtperiode (min)** de respijtperiode in minuten op, de toegestane waarden liggen tussen de 30 en 90 minuten. 
1. Selecteer **Opslaan** wanneer u klaar bent. 

### <a name="rest-api"></a>REST-API

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

Hieronder volgt een voorbeeld voor het bijwerken van het automatische instantiereparatiebeleid van een bestaande schaalset met behulp van *[de AZ VMSS-update.](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)*

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Het servicestatus van het beleid voor automatische instantiereparaties weergeven en bijwerken

### <a name="rest-api"></a>REST-API 

Gebruik [Instantieweergave opvragen](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) met API-versie 2019-12-01 of hoger voor de grootte van virtuele machines om de *serviceState* te bekijken voor automatische reparaties onder de property *orchestrationServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Gebruik *setOrchestrationServiceState* API met API-versie 2019-12-01 of hoger op een virtuele machineschaal die is ingesteld om de status van automatische reparaties in te stellen. Zodra de schaalset is gekozen voor de functie automatische reparaties, u deze API gebruiken om automatische reparaties voor uw schaalset op te schorten of te hervatten. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

Gebruik [get-instance-view](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) cmdlet om de *serviceState* te bekijken voor automatische instantiereparaties. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Gebruik de cmdlet [setorchestration-service-state](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) om de *serviceState* bij te werken voor automatische instantiereparaties. Zodra de schaalset is gekozen voor de automatische reparatiefunctie, u deze cmdlet gebruiken om automatische reparaties voor u schaalset op te schorten of te hervatten. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Gebruik [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) cmdlet met parameter *InstanceView* om de *ServiceState* weer te geven voor automatische instantiereparaties.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Gebruik de cmdlet Set-AzVmssOrchestrationServiceState om de *serviceState* bij te werken voor automatische schadeherstel. Zodra de schaalset is gekozen voor de automatische reparatiefunctie, u deze cmdlet gebruiken om automatische reparaties voor u schaalset op te schorten of te hervatten.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Problemen oplossen

**Het niet inschakelen van automatisch reparatiebeleid**

Als u een fout van 'BadRequest' krijgt met een bericht met de vermelding 'Kon lid 'automatisch Reparatiebeleid' niet vinden op object van type 'eigenschappen'", controleer dan de API-versie die wordt gebruikt voor de detectie van virtuele machineschaal. API-versie 2018-10-01 of hoger is vereist voor deze functie.

**Instantie wordt niet gerepareerd, zelfs niet als het beleid is ingeschakeld**

De instantie kan in respijtperiode zijn. Dit is de hoeveelheid tijd om te wachten na een statuswijziging in de instantie voordat u reparaties uitvoert. Dit om voortijdige of toevallige reparaties te voorkomen. De reparatie actie moet gebeuren zodra de respijtperiode is voltooid voor de instantie.

**Status van toepassing weergeven voor schaalsetinstanties**

U de [API voor instantieweergave opdoen](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) voor instanties in een virtuele machineschaalset gebruiken om de status van de toepassingsstatus weer te geven. Met Azure PowerShell u de cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) gebruiken met de *-InstanceView-vlag.* De status van de toepassingsstatus wordt verstrekt onder de *eigenschap vmHealth*.

In de Azure-portal u ook de status van de status zien. Ga naar een bestaande schaalset, selecteer **Instanties** in het menu aan de linkerkant en bekijk de kolom **Status status** voor de status status van elke schaalsetinstantie. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren [van application health extensie](./virtual-machine-scale-sets-health-extension.md) of Load [balancer health probes](../load-balancer/load-balancer-custom-probe-overview.md) voor uw schaalsets.
