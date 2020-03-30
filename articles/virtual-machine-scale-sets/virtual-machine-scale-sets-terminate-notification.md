---
title: Melding voor azure-exemplaren voor het instellen van de virtuele machine
description: Meer informatie over het inschakelen van beëindigingsmeldingen voor azure-exemplaren voor het instellen van virtuele machines
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250749"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Melding voor azure-exemplaren voor het instellen van de virtuele machine
Schaalset-instanties kunnen zich aanmelden om meldingen voor het beëindigen van instanties te ontvangen en een vooraf gedefinieerde time-out voor vertraging instellen op de beëindigingsbewerking. De beëindigingsmelding wordt verzonden via Azure Metadata Service – [Geplande gebeurtenissen](../virtual-machines/windows/scheduled-events.md), die meldingen biedt voor en het uitstellen van impactvolle bewerkingen zoals opnieuw opstarten en opnieuw implementeren. De oplossing voegt een andere gebeurtenis toe : Beëindigen – aan de lijst met geplande gebeurtenissen en de bijbehorende vertraging van de gebeurtenis terminate is afhankelijk van de vertragingslimiet die door gebruikers is opgegeven in hun modelconfiguraties voor schaalsets.

Zodra u zich bij de functie hebt ingeschreven, hoeven schaalset-instanties niet te wachten tot de opgegeven time-out verloopt voordat de instantie wordt verwijderd. Na ontvangst van een melding terminate kan de instantie ervoor kiezen om te worden verwijderd op elk gewenst moment voordat de beëindiging van de time-out verloopt.

## <a name="enable-terminate-notifications"></a>Meldingen beëindigen inschakelen
Er zijn meerdere manieren om beëindigingsmeldingen in te schakelen op uw schaalsetinstanties, zoals beschreven in de onderstaande voorbeelden.

### <a name="azure-portal"></a>Azure Portal

Met de volgende stappen u de melding beëindigen bij het maken van een nieuwe schaalset. 

1. Ga naar **virtuele machineschaalsets**.
1. Selecteer **+ Toevoegen** om een nieuwe schaalset te maken.
1. Ga naar het tabblad **Beheer.** 
1. Zoek de sectie **Instantiebeëindiging.**
1. Bijvoorbeeld **de beëindigingsmelding**selecteren , selecteer **Aan**.
1. Voor **beëindigingsvertraging (minuten)** stelt u de gewenste standaardtime-out in.
1. Wanneer u klaar bent met het maken van de nieuwe schaalset, selecteert u De knop **Controleren + maken.** 

> [!NOTE]
> U geen terminate meldingen instellen op bestaande schaalsets in Azure-portal

### <a name="rest-api"></a>REST API

In het volgende voorbeeld u de melding op het schaalsetmodel beëindigen.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

Het bovenstaande blok geeft een time-outvertraging van 5 minuten (zoals aangegeven door *PT5M)* op voor elke beëindigingsbewerking op alle instanties in uw schaalset. Het veld *notBeforeTimeout* kan elke waarde tussen 5 en 15 minuten in ISO 8601-indeling gebruiken. U de standaardtime-out voor de beëindigingsbewerking wijzigen door de eigenschap *notBeforeTimeout* te wijzigen onder hierboven *beschreven TerminateNotificationProfile.*

Nadat *u scheduledEventsProfile* hebt ingeschakeld op het schaalsetmodel en de *notBeforeTimeout hebt*ingesteld, wordt de afzonderlijke exemplaren bijgewerkt naar het [nieuwste model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) om de wijzigingen weer te geven.

> [!NOTE]
>Meldingen op schaalinstellen-exemplaren kunnen alleen worden ingeschakeld met API-versie 2019-03-01 en hoger

### <a name="azure-powershell"></a>Azure PowerShell
Wanneer u een nieuwe schaalset maakt, u beëindigingsmeldingen inschakelen op de schaal die is ingesteld met de cmdlet [Nieuw-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

Dit voorbeeldscript doorloopt het maken van een schaalset en bijbehorende resources met behulp van het configuratiebestand: [Maak een complete virtuele machineschaalset.](./scripts/powershell-sample-create-complete-scale-set.md) U de configuratiemelding configureren door de parameters *TerminateScheduledEvents* en *TerminateScheduledScheduledSNotBeforeTimeoutInMinutes* toe te voegen aan het configuratieobject voor het maken van schaalset. In het volgende voorbeeld wordt de functie met een time-out van 10 minuten uitgesteld.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Gebruik de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om beëindigingsmeldingen in te schakelen op een bestaande schaalset.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
In het bovenstaande voorbeeld kunnen meldingen op een bestaande schaalset worden beëindigd en wordt een time-out van 15 minuten ingesteld voor de gebeurtenis terminate.

Nadat u geplande gebeurtenissen in het schaalsetmodel hebt ingeschakeld en de time-out hebt ingesteld, werkt u de afzonderlijke exemplaren bij naar het [nieuwste model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) om de wijzigingen weer te geven.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Het volgende voorbeeld is het inschakelen van beëindigingsmelding tijdens het maken van een nieuwe schaalset.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

In het bovenstaande voorbeeld wordt eerst een resourcegroep gemaakt en wordt vervolgens een nieuwe schaalset gemaakt met terminatemeldingen die zijn ingeschakeld voor een standaardtime-out van 10 minuten.

Het volgende voorbeeld is het inschakelen van beëindigingsmelding in een bestaande schaalset.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Meldingen beëindigen ontvangen

Meldingen beëindigen worden geleverd via [geplande gebeurtenissen](../virtual-machines/windows/scheduled-events.md), een Azure Metadata Service. Azure Metadata-service onthult informatie over het uitvoeren van virtuele machines met behulp van een REST-eindpunt dat toegankelijk is vanuit de VM. De informatie is beschikbaar via een niet-routeerbaar IP, zodat deze niet buiten de VM wordt blootgesteld.

Geplande gebeurtenissen zijn ingeschakeld voor uw schaalset wanneer u voor het eerst een aanvraag voor gebeurtenissen indient. U een vertraagde reactie verwachten in uw eerste gesprek van maximaal twee minuten. Bevraag het eindpunt regelmatig om aankomende onderhoudsgebeurtenissen en de status van lopende onderhoudsactiviteiten te detecteren.

Geplande gebeurtenissen zijn uitgeschakeld voor uw schaalset als de schaalset-exemplaren gedurende 24 uur geen aanvraag indienen.

### <a name="endpoint-discovery"></a>Eindpuntdetectie
Voor VNET-vm's is de Metadata Service beschikbaar via een statisch niet-routable IP, 169.254.169.254.

Het volledige eindpunt voor de nieuwste versie van geplande gebeurtenissen is:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Queryrespons
Een antwoord bevat een reeks geplande gebeurtenissen. Een lege array betekent dat er momenteel geen gebeurtenissen zijn gepland.

In het geval dat er geplande gebeurtenissen zijn, bevat het antwoord een reeks gebeurtenissen. Voor een gebeurtenis Beëindigen ziet het antwoord er als volgt uit:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
De *DocumentIncarnation* is een ETag en biedt een eenvoudige manier om te controleren of de gebeurtenis payload is veranderd sinds de laatste query.

Zie de documentatie geplande gebeurtenissen voor [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) en [Linux](../virtual-machines/linux/scheduled-events.md#event-properties)voor meer informatie over elk van de bovenstaande velden.

### <a name="respond-to-events"></a>Reageren op gebeurtenissen
Zodra je van een aankomend evenement hebt gehoord en je logica hebt voltooid voor gracieuze afsluiting, kun je de uitstaande gebeurtenis goedkeuren door een POST-oproep te doen naar de metadataservice met de EventId. De POST-oproep geeft Azure aan dat deze kan doorgaan met het verwijderen van de VM.

Hieronder is de json verwacht in de POST aanvraag lichaam. De aanvraag moet een lijst met StartRequests bevatten. Elke StartRequest bevat de EventId voor het evenement dat u wilt versnellen:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Zorg ervoor dat elke vm in de schaalset alleen de Gebeurtenis-ID goedkeurt die alleen voor die vm relevant is. Een VM kan zijn eigen VM-naam krijgen [via metagegevens van instanties.](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name) Deze naam heeft de vorm "{scale-set-name}_{instance-id}", en wordt weergegeven in het gedeelte 'Resources' van het hierboven beschreven queryantwoord.

U ook verwijzen naar voorbeeldscripts voor het opvragen en reageren op gebeurtenissen met [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) en [Python.](../virtual-machines/linux/scheduled-events.md#python-sample)

## <a name="tips-and-best-practices"></a>Tips en best practices
-   Meldingen alleen beëindigen bij 'verwijderen'-bewerkingen : Alle verwijderingsbewerkingen (handmatig verwijderen of automatisch inschalen) genereren Gebeurtenissen terminate als uw *schaalsetGebeurtenissenhebben* ingeschakeld. Andere bewerkingen zoals opnieuw opstarten, opnieuw imageeren, opnieuw implementeren en stoppen/detoewijzen genereren geen Terminate-gebeurtenissen. Meldingen beëindigen kan niet worden ingeschakeld voor VM's met een lage prioriteit.
-   Geen verplichte wachttijd voor time-out : u de beëindigingsbewerking op elk gewenst moment starten nadat de gebeurtenis is ontvangen en voordat de *gebeurtenis notbefore-tijd* verstrijkt.
-   Verplichte verwijdering bij een time-out : er is geen mogelijkheid om de time-outwaarde uit te breiden nadat een gebeurtenis is gegenereerd. Zodra de time-out is verlopen, wordt de gebeurtenis in behandeling worden verwerkt en wordt de VM verwijderd.
-   Aanpasbare time-outwaarde : u de time-outwaarde op elk gewenst moment wijzigen voordat een instantie wordt verwijderd, door de eigenschap *notBeforeTimeout* op het schaalsetmodel te wijzigen en de VM-exemplaren bij te werken naar het nieuwste model.
-   Alle in behandeling zijnde verwijderingen goedkeuren : als er een in behandeling zijnde verwijdering is op VM_1 die niet is goedgekeurd en u een andere gebeurtenis voor beëindiging op VM_2 hebt goedgekeurd, wordt VM_2 pas verwijderd als de gebeurtenis terminate voor VM_1 is goedgekeurd of de time-out is verstreken. Zodra u de gebeurtenis terminate voor VM_1 goedkeurt, worden zowel VM_1 als VM_2 verwijderd.
-   Alle gelijktijdige verwijderingen goedkeuren - Als VM_1 en VM_2 dezelfde *NotBefore-tijd* hebben, moeten beide gebeurtenissen voor beëindiging worden goedgekeurd of wordt geen enkele VM verwijderd voordat de time-out verloopt.

## <a name="troubleshoot"></a>Problemen oplossen
### <a name="failure-to-enable-scheduledeventsprofile"></a>Het niet inschakelen van scheduledEventsProfile
Als u een fout van 'BadRequest' krijgt met een foutbericht met de vermelding 'Kon lid 'scheduledEventsProfile' niet vinden op object van type 'VirtualMachineProfile', controleert u de API-versie die wordt gebruikt voor de schaalsetbewerkingen. Compute API versie **2019-03-01** of hoger is vereist. 

### <a name="failure-to-get-terminate-events"></a>Gebeurtenissen beëindigen niet krijgen
Als u geen **terminate-gebeurtenissen** krijgt via geplande gebeurtenissen, controleert u de API-versie die wordt gebruikt voor het verkrijgen van de gebeurtenissen. Metadata Service API versie **2019-01-01** of hoger is vereist voor Gebeurtenissen beëindigen.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Gebeurtenis Beëindigen met onjuiste NotBefore-tijd krijgen  
Nadat *u scheduledEventsProfile* hebt ingeschakeld op het schaalsetmodel en de *notBeforeTimeout hebt*ingesteld, wordt de afzonderlijke exemplaren bijgewerkt naar het [nieuwste model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) om de wijzigingen weer te geven.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele machineschaalsets.
