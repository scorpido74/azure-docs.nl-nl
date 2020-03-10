---
title: Scheduled Events voor virtuele Windows-machines in azure
description: Geplande gebeurtenissen met behulp van de Azure meta data-service voor op uw virtuele Windows-machines.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 2b3aa5d50822863e3aa46fcf9970e0b3e67a6f69
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944466"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Scheduled Events voor Windows-Vm's

Scheduled Events is een Azure-Metadata Service die uw toepassings tijd biedt voor het voorbereiden van onderhoud van virtuele machines. Het bevat informatie over aanstaande onderhouds gebeurtenissen (bijvoorbeeld opnieuw opstarten), zodat uw toepassing deze kan voorbereiden en de onderbreking kan beperken. Het is beschikbaar voor alle typen virtuele machines van Azure, waaronder PaaS en IaaS in Windows en Linux. 

Zie [Scheduled Events voor Linux-vm's](../linux/scheduled-events.md)voor meer informatie over Scheduled Events in Linux.

> [!Note] 
> Scheduled Events is algemeen beschikbaar in alle Azure-regio's. Zie de [Beschik baarheid van versie en regio](#version-and-region-availability) voor de meest recente release-informatie.

## <a name="why-scheduled-events"></a>Waarom Scheduled Events?

Veel toepassingen kunnen profiteren van de tijd om het onderhoud van virtuele machines voor te bereiden. De tijd kan worden gebruikt voor het uitvoeren van toepassingsspecifieke taken die de beschik baarheid, betrouw baarheid en service verbeteren, waaronder: 

- Controle punt en herstellen
- Verwerkingsstop voor verbindingen
- Failover van primaire replica 
- Verwijderen uit load balancer groep
- Gebeurtenis registratie
- Correct afsluiten 

Het gebruik van Scheduled Events uw toepassing kan detecteren wanneer er onderhoud wordt uitgevoerd en taken activeren om de impact te beperken. Door geplande gebeurtenissen in te scha kelen krijgt de virtuele machine een minimale hoeveelheid tijd voordat de onderhouds activiteit wordt uitgevoerd. Zie de sectie gebeurtenis planning hieronder voor meer informatie.

Scheduled Events bevat gebeurtenissen in de volgende use-cases:
- Door het [platform geïnitieerd onderhoud](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (bijvoorbeeld VM opnieuw opstarten, Livemigratie of geheugen behoud van updates voor host)
- De virtuele machine wordt uitgevoerd op hardware die is [gedegradeerd als host](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) die binnenkort wordt voorspeld
- Door de gebruiker geïnitieerd onderhoud (bijvoorbeeld het opnieuw starten van een gebruiker of opnieuw implementeren van een VM)
- [Spot-VM](spot-vms.md) en verwijderingen van exemplaren van de set met steun [schalen](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>De basisbeginselen  

Azure meta data service geeft informatie weer over het uitvoeren van Virtual Machines met behulp van een REST-eind punt dat toegankelijk is vanuit de VM. De informatie is beschikbaar via een niet-routeerbaar IP-adres, zodat het niet buiten de virtuele machine wordt weer gegeven.

### <a name="endpoint-discovery"></a>Eindpunt detectie
Voor VNET ingeschakelde Vm's is de meta gegevens service beschikbaar vanaf een statisch niet-routeerbaar IP-adres, `169.254.169.254`. Het volledige eind punt voor de meest recente versie van Scheduled Events is: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Als de virtuele machine niet is gemaakt binnen een Virtual Network, zijn de standaard aanvragen voor Cloud Services en klassieke Vm's vereist om het te gebruiken IP-adres te ontdekken. Raadpleeg dit voor beeld voor meer informatie over [het detecteren van het eind punt van de host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Beschik baarheid van versie en regio
Er is een versie van de Scheduled Events-service. Versies zijn verplicht en de huidige versie is `2019-01-01`.

| Version | Releasetype | Regio's | Releaseopmerkingen | 
| - | - | - | - |
| 2019-01-01 | Algemene beschikbaarheid | Alle | <li> Er is ondersteuning toegevoegd voor de gebeurtenis ' Terminate ' voor de virtuele-machine schaal sets |
| 2017-11-01 | Algemene beschikbaarheid | Alle | <li> Er is ondersteuning toegevoegd voor de gebeurtenis ' preempt ' voor VM-verwijdering van de publicatie<br> | 
| 2017-08-01 | Algemene beschikbaarheid | Alle | <li> Achterliggend onderstrepings teken verwijderd uit resource namen voor IaaS-Vm's<br><li>Er is een vereiste voor de meta gegevens header afgedwongen voor alle aanvragen | 
| 2017-03-01 | Preview | Alle |<li>Eerste release |

> [!NOTE] 
> Eerdere Preview-versies van geplande gebeurtenissen die {nieuwste} worden ondersteund als de API-versie. Deze indeling wordt niet meer ondersteund en zal in de toekomst worden afgeschaft.

### <a name="enabling-and-disabling-scheduled-events"></a>Scheduled Events in-en uitschakelen
De eerste keer dat u een aanvraag voor gebeurtenissen maakt, wordt Scheduled Events ingeschakeld voor uw service. U moet een vertraagde reactie verwachten in uw eerste aanroep van Maxi maal twee minuten. U moet periodiek een query uitvoeren op het eind punt om toekomstige onderhouds gebeurtenissen te detecteren, evenals de status van onderhouds activiteiten die worden uitgevoerd.

Scheduled Events is uitgeschakeld voor uw service als deze niet 24 uur een aanvraag doet.

### <a name="user-initiated-maintenance"></a>Door de gebruiker geïnitieerd onderhoud
Door de gebruiker geïnitieerd onderhoud van virtuele machines via de Azure Portal, API, CLI of Power shell resulteert in een geplande gebeurtenis. Hierdoor kunt u de logica voor onderhouds voorbereiding in uw toepassing testen en kan uw toepassing voorbereiden op door de gebruiker geïnitieerde onderhouds werkzaamheden.

Als u een virtuele machine opnieuw opstart, wordt een gebeurtenis gepland met het type `Reboot`. Als u een virtuele machine opnieuw implementeert, wordt een gebeurtenis met het type `Redeploy`.

## <a name="using-the-api"></a>De API gebruiken

### <a name="headers"></a>Headers
Wanneer u een query uitvoert op de Metadata Service, moet u de header-`Metadata:true` opgeven om ervoor te zorgen dat de aanvraag niet per ongeluk is omgeleid. De `Metadata:true`-header is vereist voor alle aanvragen voor geplande gebeurtenissen. Als u de header in de aanvraag niet opneemt, resulteert dit in een antwoord op de Metadata Service.

### <a name="query-for-events"></a>Query's uitvoeren op gebeurtenissen
U kunt een query uitvoeren voor Scheduled Events door eenvoudigweg de volgende aanroep te nemen:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

Een antwoord bevat een matrix met geplande gebeurtenissen. Een lege matrix betekent dat er momenteel geen gebeurtenissen zijn gepland.
Als er geplande gebeurtenissen zijn, bevat het antwoord een matrix met gebeurtenissen: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
De DocumentIncarnation is een ETag en biedt een eenvoudige manier om te controleren of de nettolading van de gebeurtenissen sinds de laatste query is gewijzigd.

### <a name="event-properties"></a>Gebeurtenis eigenschappen
|Eigenschap  |  Beschrijving |
| - | - |
| Gebeurtenis-id | De wereld wijde unieke id voor deze gebeurtenis. <br><br> Voorbeeld: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Dit heeft invloed op deze gebeurtenis. <br><br> Waarden: <br><ul><li> `Freeze`: de virtuele machine is gepland om enkele seconden te worden onderbroken. De CPU-en netwerk verbinding wordt mogelijk onderbroken, maar er is geen invloed op het geheugen of geopende bestanden. <li>`Reboot`: de virtuele machine is gepland voor opnieuw opstarten (niet-permanent geheugen gaat verloren). <li>`Redeploy`: de virtuele machine is ingepland om te worden verplaatst naar een ander knoop punt (tijdelijke schijven gaan verloren). <li>`Preempt`: de locatie van de virtuele machine wordt verwijderd (tijdelijke schijven gaan verloren). <li> `Terminate`: de virtuele machine is gepland om te worden verwijderd. |
| ResourceType | Het type resource dat door deze gebeurtenis wordt beïnvloed. <br><br> Waarden: <ul><li>`VirtualMachine`|
| Bronnen| Lijst met resources die van invloed zijn op deze gebeurtenis. Dit is gegarandeerd dat machines van Maxi maal één [update domein](manage-availability.md)worden opgenomen, maar mag niet alle computers in de UD bevatten. <br><br> Voorbeeld: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Gebeurtenis status | De status van deze gebeurtenis. <br><br> Waarden: <ul><li>`Scheduled`: deze gebeurtenis is gepland om te starten na het tijdstip dat is opgegeven in de eigenschap `NotBefore`.<li>`Started`: deze gebeurtenis is gestart.</ul> Er wordt nooit `Completed` of een vergelijk bare status gegeven; de gebeurtenis wordt niet meer geretourneerd wanneer de gebeurtenis is voltooid.
| NotBefore| Tijdstip waarna deze gebeurtenis kan worden gestart. <br><br> Voorbeeld: <br><ul><li> Ma, 19 sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Gebeurtenissen plannen
Elke gebeurtenis wordt een minimale tijd in de toekomst gepland op basis van het gebeurtenis type. Deze tijd wordt weer gegeven in de eigenschap `NotBefore` van een gebeurtenis. 

|EventType  | Minimale kennisgeving |
| - | - |
| Kering| 15 minuten |
| Opnieuw opstarten | 15 minuten |
| Opnieuw implementeren | 10 minuten |
| Preempt | 30 seconden |
| Tijdig | [Configureer bare gebruiker](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 tot 15 minuten |

> [!NOTE] 
> In sommige gevallen kan Azure host-uitval voors pellen vanwege gedegradeerde hardware en zal proberen de onderbreking van uw service te verhelpen door een migratie te plannen. De betrokken virtuele machines ontvangen een geplande gebeurtenis met een `NotBefore` dat in de toekomst meestal een paar dagen is. De werkelijke tijd is afhankelijk van de voorspelde risico analyse. Azure probeert zo de voorafgaande kennisgeving van 7 dagen te geven, maar de werkelijke tijd varieert en kan kleiner zijn als de voor spelling is dat er een hoge kans op het onmiddellijk uitvallen van de hardware is. Om het risico voor uw service tot een minimum te beperken, wordt het aanbevolen om uw virtuele machine zo snel mogelijk zelf opnieuw te implementeren.

### <a name="event-scope"></a>Gebeurtenis bereik     
Geplande gebeurtenissen worden verzonden naar:
 - Zelfstandige Virtual Machines
 - Alle Virtual Machines in een Cloud service      
 - Alle Virtual Machines in een Beschikbaarheidsset      
 - Alle Virtual Machines in een plaatsings groep met schaal sets.         

Daarom moet u het `Resources` veld in de gebeurtenis controleren om te bepalen welke Vm's worden beïnvloed. 

### <a name="starting-an-event"></a>Een gebeurtenis starten 

Zodra u een aanstaande gebeurtenis hebt geleerd en de logica hebt voltooid om het probleem op te starten, kunt u de openstaande gebeurtenis goed keuren door een `POST` de meta gegevens service met de `EventId`aan te roepen. Dit geeft aan dat Azure de minimale meldings tijd kan verkorten (indien mogelijk). 

Hieronder wordt de JSON verwacht in de hoofd tekst van de `POST` aanvraag. De aanvraag moet een lijst met `StartRequests`bevatten. Elke `StartRequest` bevat de `EventId` voor de gebeurtenis die u wilt versnellen:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Door een gebeurtenis te bevestigen, kan de gebeurtenis worden voortgezet voor alle `Resources` in de gebeurtenis, niet alleen de virtuele machine die de gebeurtenis erkent. U kunt er daarom voor kiezen om een leider te selecteren voor het coördineren van de bevestiging. Dit kan zo eenvoudig zijn als de eerste computer in het `Resources` veld.


## <a name="powershell-sample"></a>Voorbeeld van PowerShell 

Het volgende voor beeld voert een query uit op de meta gegevens service voor geplande gebeurtenissen en keurt elke openstaande gebeurtenis goed.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2019-01-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Volgende stappen 

- Bekijk een [Scheduled Events demo](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) over Azure vrijdag. 
- Bekijk de Scheduled Events code voorbeelden in de [meta gegevens van het Azure-exemplaar Scheduled Events de GitHub-opslag plaats](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Meer informatie over de Api's die beschikbaar zijn in de [meta gegevens service](instance-metadata-service.md)van het exemplaar.
- Meer informatie over [gepland onderhoud voor virtuele Windows-machines in azure](planned-maintenance.md).
