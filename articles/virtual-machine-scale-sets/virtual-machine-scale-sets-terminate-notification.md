---
title: Melding beëindigen voor instanties van virtuele-machine schaal sets van Azure | Microsoft Docs
description: Meer informatie over het inschakelen van beëindigings meldingen voor instanties van de schaalset voor virtuele machines van Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: shandilvarun
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2019
ms.author: vashan
ms.openlocfilehash: 7269c76236b7cbe60995d84e85857da596bec961
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264682"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances-preview"></a>Melding beëindigen voor instanties van virtuele-machine schaal sets van Azure (preview-versie)
Instanties van een schaalset kunnen zich aanmelden voor het ontvangen van meldingen voor het beëindigen van een exemplaar en het instellen van een vooraf gedefinieerde vertragings time-out voor de bewerking beëindigen. De beëindigings melding wordt verzonden via Azure Metadata Service – [Scheduled Events](../virtual-machines/windows/scheduled-events.md), waarmee impact bewerkingen kunnen worden vertraagd, zoals het opnieuw opstarten en opnieuw implementeren. Met de preview-oplossing wordt een andere gebeurtenis toegevoegd: beëindigen: aan de lijst met Scheduled Events en de bijbehorende vertraging van de gebeurtenis Terminate is afhankelijk van de vertragings limiet, zoals opgegeven door gebruikers in de model configuraties van de schaalset.

Wanneer instanties van een schaalset eenmaal zijn geregistreerd in de-functie, hoeven niet te worden gewacht tot de opgegeven time-out is verlopen voordat het exemplaar wordt verwijderd. Nadat de melding is beëindigd, kan de instantie ervoor kiezen om op elk gewenst moment te verwijderen voordat de time-out voor beëindigen verloopt.

> [!IMPORTANT]
> Melding beëindigen voor instanties van een schaalset is momenteel beschikbaar als open bare preview. Er is geen opt-in-procedure nodig om de open bare Preview-functionaliteit te gebruiken die hieronder wordt beschreven.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="enable-terminate-notifications"></a>Meldingen voor beëindigen inschakelen
Er zijn meerdere manieren om beëindigings meldingen in te scha kelen voor instanties van de schaalset, zoals beschreven in de onderstaande voor beelden.

### <a name="rest-api"></a>REST-API

In het volgende voor beeld wordt de melding beëindigen ingeschakeld voor het model met de schaalset.

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

In het bovenstaande blok is een time-outvertraging van 5 minuten (zoals aangegeven door *PT5M*) voor elke beëindiging van alle exemplaren in uw schaalset opgegeven. Het veld *notBeforeTimeout* kan elke waarde tussen 5 en 15 minuten in ISO 8601-indeling hebben. U kunt de standaardtime-out voor de bewerking beëindigen wijzigen door de eigenschap *notBeforeTimeout* te wijzigen onder *terminateNotificationProfile* hierboven beschreven.

Nadat u *scheduledEventsProfile* op het model voor de schaalset hebt ingeschakeld en de *notBeforeTimeout*hebt ingesteld, werkt u de afzonderlijke exemplaren bij naar het [meest recente model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) om de wijzigingen weer te geven.

> [!NOTE]
>Beëindigen van meldingen op instanties van schaal sets kunnen alleen worden ingeschakeld met API-versie 2019-03-01 en hoger

### <a name="azure-powershell"></a>Azure PowerShell
Wanneer u een nieuwe schaalset maakt, kunt u beëindigings meldingen inschakelen voor de schaalset met behulp van de cmdlet [New-AzVmssVM](/powershell/module/az.compute/new-azvmss) .

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents
```

In het bovenstaande voor beeld wordt een nieuwe schaalset gemaakt waarbij meldingen voor beëindigen worden ingeschakeld met een standaard time-out van 5 minuten. Bij het maken van een nieuwe schaalset heeft de para meter *TerminateScheduledEvents* geen waarde nodig. Als u de time-outwaarde wilt wijzigen, geeft u de gewenste time-out op via de para meter *TerminateScheduledEventNotBeforeTimeoutInMinutes* .

Gebruik de cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmss) om beëindigings meldingen in te scha kelen voor een bestaande schaalset.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
In het bovenstaande voor beeld worden meldingen voor een bestaande schaalset beëindigd en wordt een time-out van 15 minuten ingesteld voor de gebeurtenis Terminate.

Wanneer u geplande gebeurtenissen in het model van de schaalset hebt ingeschakeld en de time-out hebt ingesteld, werkt u de afzonderlijke exemplaren bij naar het [meest recente model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) om de wijzigingen weer te geven.

## <a name="get-terminate-notifications"></a>Meldingen over beëindigen ophalen

U kunt meldingen beëindigen via [Scheduled Events](../virtual-machines/windows/scheduled-events.md), een Azure-metadata service. Azure meta data service geeft informatie weer over het uitvoeren van Virtual Machines met behulp van een REST-eind punt dat toegankelijk is vanuit de VM. De informatie is beschikbaar via een niet-routeerbaar IP-adres, zodat het niet buiten de virtuele machine wordt weer gegeven.

De eerste keer dat u een aanvraag voor gebeurtenissen maakt, wordt Scheduled Events ingeschakeld voor uw schaalset. U kunt een vertraagd antwoord verwachten in uw eerste aanroep van Maxi maal twee minuten. Vraag het eind punt periodiek aan om toekomstige onderhouds gebeurtenissen en de status van voortdurende onderhouds activiteiten te detecteren.

Scheduled Events is uitgeschakeld voor de schaalset als de instanties van de schaalset 24 uur geen aanvraag indienen.

### <a name="endpoint-discovery"></a>Eindpunt detectie
Voor VNET ingeschakelde Vm's is de Metadata Service beschikbaar vanaf een statisch niet-routeerbaar IP-adres, 169.254.169.254.

Het volledige eind punt voor de meest recente versie van Scheduled Events voor deze preview is:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="query-response"></a>Query-antwoord
Een antwoord bevat een matrix met geplande gebeurtenissen. Een lege matrix betekent dat er momenteel geen gebeurtenissen zijn gepland.

Als er geplande gebeurtenissen zijn, bevat het antwoord een matrix met gebeurtenissen. Voor een ' Terminate ' gebeurtenis ziet het antwoord er als volgt uit:
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
De DocumentIncarnation is een ETag en biedt een eenvoudige manier om te controleren of de nettolading van de gebeurtenissen sinds de laatste query is gewijzigd.

Zie de Scheduled Events-documentatie voor [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) en [Linux](../virtual-machines/linux/scheduled-events.md#event-properties)voor meer informatie over elk van de bovenstaande velden.

### <a name="respond-to-events"></a>Reageren op gebeurtenissen
Zodra u een geplande gebeurtenis hebt gemaakt en de logica hebt voltooid voor een correcte afsluiting, kunt u de openstaande gebeurtenis goed keuren door een POST-oproep naar de meta gegevens service te maken met de gebeurtenis-Event. De aanroep POST geeft aan dat Azure het verwijderen van de VM kan voortzetten.

Hieronder ziet u de JSON die wordt verwacht in de hoofd tekst van de POST-aanvraag. De aanvraag moet een lijst met StartRequests bevatten. Elk StartRequest bevat de gebeurtenis-gebeurtenis voor de gebeurtenissen die u wilt versnellen:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Zorg ervoor dat elke virtuele machine in de schaalset alleen de alleen-lezen gebeurtenis alleen goedkeurt die relevant is voor die VM. Een virtuele machine kan zijn eigen VM-naam ophalen [via de meta gegevens](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)van het exemplaar. Deze naam heeft de notatie {Scale-set-name} _ {instance-id} en wordt weer gegeven in de sectie resources van het query-antwoord dat hierboven wordt beschreven.

U kunt ook verwijzen naar voorbeeld scripts voor het uitvoeren van query's en het reageren op gebeurtenissen met behulp van [Power shell](../virtual-machines/windows/scheduled-events.md#powershell-sample) en [python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Tips en aanbevolen procedures
-   Meldingen alleen beëindigen bij ' delete '-bewerkingen: alle Verwijder bewerkingen (hand matig verwijderen of automatisch schalen gestarte inschalen) genereren Terminate-gebeurtenissen als voor uw schaalset *scheduledEventsProfile* is ingeschakeld. Voor andere bewerkingen, zoals opnieuw opstarten, opnieuw instellen van installatie kopieën, opnieuw implementeren en stoppen/toewijzing, worden geen Terminate-gebeurtenissen gegenereerd. Beëindigen van meldingen kan niet worden ingeschakeld voor Vm's met een lage prioriteit.
-   Geen verplichte wacht tijd voor time-out: u kunt de bewerking beëindigen op elk gewenst moment starten nadat de gebeurtenis is ontvangen en voordat de *NotBefore* -tijd van de gebeurtenis is verlopen.
-   Verplicht verwijderen bij time-out: de preview biedt geen mogelijkheid om de time-outwaarde uit te breiden nadat een gebeurtenis is gegenereerd. Zodra de time-out is verlopen, wordt de gebeurtenis pending Terminate verwerkt en wordt de virtuele machine verwijderd.
-   Aanpas bare time-outwaarde: u kunt de time-outwaarde op elk moment wijzigen voordat een instantie wordt verwijderd, door de eigenschap *notBeforeTimeout* in het model van de schaalset te wijzigen en de VM-exemplaren bij te werken naar het meest recente model.
-   Alle in behandeling zijnde verwijderingen goed keuren: als er een verwijdering in behandeling is op VM_1 die niet is goedgekeurd en u een andere Terminate-gebeurtenis op VM_2 hebt goedgekeurd, wordt VM_2 niet verwijderd totdat de gebeurtenis Terminate voor VM_1 is goedgekeurd of omdat de time-out is verstreken. Zodra u de gebeurtenis Terminate voor VM_1 goedkeurt, worden zowel VM_1 als VM_2 verwijderd.
-   Alle gelijktijdige verwijderingen goed keuren – met uitbrei ding van het bovenstaande voor beeld, als VM_1 en VM_2 dezelfde *NotBefore* -tijd hebben, moeten beide Terminate-gebeurtenissen worden goedgekeurd of wordt de VM niet verwijderd voordat de time-out is verstreken.

## <a name="troubleshoot"></a>Problemen oplossen
### <a name="failure-to-enable-scheduledeventsprofile"></a>Kan scheduledEventsProfile niet inschakelen
Als er een fout bericht ' onjuiste aanvraag ' wordt weer gegeven met de melding ' kan geen scheduledEventsProfile vinden voor het object van het type ' VirtualMachineProfile ', controleert u de API-versie die wordt gebruikt voor de bewerkingen van de schaalset. Compute API-versie **2019-03-01** of hoger is vereist voor deze preview.

### <a name="failure-to-get-terminate-events"></a>Kan geen beëindigings gebeurtenissen ophalen
Als er geen **Terminate** -gebeurtenissen worden ontvangen via Scheduled Events, controleert u vervolgens de API-versie die wordt gebruikt voor het ophalen van de gebeurtenissen. Metadata Service API-versie **2019-01-01** of hoger is vereist voor Terminate-gebeurtenissen.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>De gebeurtenis Terminate met een onjuiste NotBefore-tijd ophalen  
Nadat u *scheduledEventsProfile* op het model voor de schaalset hebt ingeschakeld en de *notBeforeTimeout*hebt ingesteld, werkt u de afzonderlijke exemplaren bij naar het [meest recente model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) om de wijzigingen weer te geven.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machine schaal sets.
