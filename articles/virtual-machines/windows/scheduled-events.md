---
title: Geplande gebeurtenissen voor Windows VM's in Azure
description: Geplande gebeurtenissen met de Azure Metadata-service voor op uw virtuele Windows-machines.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944466"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure-metagegevensservice: geplande gebeurtenissen voor Windows VM's

Geplande gebeurtenissen is een Azure Metadata Service die uw toepassing de tijd geeft om zich voor te bereiden op onderhoud van virtuele machines. Het biedt informatie over aankomende onderhoudsgebeurtenissen (bijvoorbeeld opnieuw opstarten), zodat uw toepassing zich erop kan voorbereiden en onderbrekingen kan beperken. Het is beschikbaar voor alle Azure Virtual Machine-typen, waaronder PaaS en IaaS op zowel Windows als Linux. 

Zie [Geplande gebeurtenissen voor Linux-VM's voor](../linux/scheduled-events.md)meer informatie over geplande gebeurtenissen op Linux.

> [!Note] 
> Geplande gebeurtenissen zijn over het algemeen beschikbaar in alle Azure-regio's. Zie [Beschikbaarheid van versies en regio's](#version-and-region-availability) voor de laatste informatie over de release.

## <a name="why-scheduled-events"></a>Waarom geplande evenementen?

Veel toepassingen kunnen profiteren van de tijd voor te bereiden op virtuele machine onderhoud. De tijd kan worden gebruikt om toepassingsspecifieke taken uit te voeren die de beschikbaarheid, betrouwbaarheid en bruikbaarheid verbeteren, waaronder: 

- Controlepunt en herstel
- Verwerkingsstop voor verbindingen
- Primaire replica-failover 
- Verwijdering uit de groep load balancer
- Logboekregistratie
- Sierlijke shutdown 

Met geplande gebeurtenissen kan uw toepassing ontdekken wanneer onderhoud plaatsvindt en taken activeren om de impact ervan te beperken. Het inschakelen van geplande gebeurtenissen geeft uw virtuele machine een minimale hoeveelheid tijd voordat de onderhoudsactiviteit wordt uitgevoerd. Zie de sectie Logboekplanning hieronder voor meer informatie.

Geplande gebeurtenissen bieden gebeurtenissen in de volgende use cases:
- [Platform gestart onderhoud](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (bijvoorbeeld VM reboot, live migratie of geheugen bewaren updates voor host)
- Virtuele machine draait op [gedegradeerde host hardware](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) die naar verwachting binnenkort mislukken
- Door de gebruiker geïnitieerd onderhoud (bijvoorbeeld dat de gebruiker een vm opnieuw start of opnieuw implementeert)
- [Vm-](spot-vms.md) en [spotschaalsetinstantieuitzettingen](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>De basisbeginselen  

Azure Metadata-service onthult informatie over het uitvoeren van virtuele machines met behulp van een REST-eindpunt dat toegankelijk is vanuit de VM. De informatie is beschikbaar via een niet-routeerbaar IP, zodat deze niet buiten de VM wordt blootgesteld.

### <a name="endpoint-discovery"></a>Eindpuntdetectie
Voor VNET-vm's is de metagegevensservice beschikbaar via `169.254.169.254`een statisch niet-routable IP. Het volledige eindpunt voor de nieuwste versie van geplande gebeurtenissen is: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Als de virtuele machine niet is gemaakt binnen een virtueel netwerk, de standaardaanvragen voor cloudservices en klassieke VM's, is aanvullende logica vereist om het IP-adres te ontdekken dat moet worden gebruikt. Raadpleeg dit voorbeeld om te leren hoe [u het eindpunt van de host ontdekken.](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="version-and-region-availability"></a>Beschikbaarheid van versie en regio
De service voor geplande gebeurtenissen is versie. Versies zijn verplicht en `2019-01-01`de huidige versie is .

| Versie | Releasetype | Regio's | Releaseopmerkingen | 
| - | - | - | - |
| 2019-01-01 | Algemene beschikbaarheid | Alle | <li> Added support for virtual machine scale sets EventType 'Terminate' |
| 2017-11-01 | Algemene beschikbaarheid | Alle | <li> Ondersteuning toegevoegd voor Spot VM-uitzetting EventType 'Preempt'<br> | 
| 2017-08-01 | Algemene beschikbaarheid | Alle | <li> Verwijderde prepended underscore van resourcenamen voor IaaS VM's<br><li>Vereiste voor metagegevenskoppen afgedwongen voor alle aanvragen | 
| 2017-03-01 | Preview | Alle |<li>Eerste release |

> [!NOTE] 
> Eerdere preview-releases van geplande gebeurtenissen ondersteund {latest} als de api-versie. Dit formaat wordt niet langer ondersteund en zal in de toekomst worden afgeschaft.

### <a name="enabling-and-disabling-scheduled-events"></a>Geplande gebeurtenissen in- en uitschakelen
Geplande gebeurtenissen zijn ingeschakeld voor uw service wanneer u voor het eerst een aanvraag voor gebeurtenissen indient. U moet een vertraagde reactie verwachten in uw eerste gesprek van maximaal twee minuten. U moet het eindpunt periodiek opvragen om aankomende onderhoudsgebeurtenissen te detecteren, evenals de status van onderhoudsactiviteiten die worden uitgevoerd.

Geplande gebeurtenissen zijn uitgeschakeld voor uw service als deze gedurende 24 uur geen aanvraag indienen.

### <a name="user-initiated-maintenance"></a>Door de gebruiker geïnitieerd onderhoud
Gebruiker gestart onderhoud van virtuele machines via de Azure portal, API, CLI, of PowerShell resulteert in een geplande gebeurtenis. Hiermee u de logica voor onderhoudsvoorbereiding in uw toepassing testen en kan uw toepassing zich voorbereiden op door de gebruiker geïnitieerd onderhoud.

Het opnieuw starten van een `Reboot`virtuele machine plant een gebeurtenis met type . Het opnieuw implementeren van een virtuele `Redeploy`machine plant een gebeurtenis met tekst .

## <a name="using-the-api"></a>De API gebruiken

### <a name="headers"></a>Headers
Wanneer u de metagegevensservice opvraagt, moet u de koptekst `Metadata:true` opgeven om ervoor te zorgen dat het verzoek niet onbedoeld is doorgestuurd. De `Metadata:true` header is vereist voor alle geplande gebeurtenisaanvragen. Als u de koptekst niet in de aanvraag opneemt, wordt een slecht verzoek-antwoord van de metagegevensservice ontvangen.

### <a name="query-for-events"></a>Query voor gebeurtenissen
U vragen stellen voor geplande gebeurtenissen door simpelweg de volgende oproep te doen:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

Een antwoord bevat een reeks geplande gebeurtenissen. Een lege array betekent dat er momenteel geen gebeurtenissen zijn gepland.
In het geval dat er geplande gebeurtenissen zijn, bevat het antwoord een reeks gebeurtenissen: 
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
De DocumentIncarnation is een ETag en biedt een eenvoudige manier om te controleren of de gebeurtenis payload is veranderd sinds de laatste query.

### <a name="event-properties"></a>Gebeurtenis-eigenschappen
|Eigenschap  |  Beschrijving |
| - | - |
| EventId (EventId) | Wereldwijd unieke id voor deze gebeurtenis. <br><br> Voorbeeld: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType (EventType) | Impact van deze gebeurtenis veroorzaakt. <br><br> Waarden: <br><ul><li> `Freeze`: De virtuele machine is gepland om te pauzeren voor een paar seconden. CPU- en netwerkconnectiviteit kan worden opgeschort, maar er is geen invloed op het geheugen of geopende bestanden. <li>`Reboot`: De virtuele machine is gepland voor opnieuw opstarten (niet-persistentgeheugen gaat verloren). <li>`Redeploy`: De virtuele machine is gepland om te verhuizen naar een ander knooppunt (kortstondige schijven gaan verloren). <li>`Preempt`: De Spot Virtual Machine wordt verwijderd (kortstondige schijven gaan verloren). <li> `Terminate`: De virtuele machine wordt verwijderd. |
| ResourceType | Type resource die van deze gebeurtenis wordt beïnvloed. <br><br> Waarden: <ul><li>`VirtualMachine`|
| Resources| Lijst met bronnen die van deze gebeurtenis van invloed zijn. Dit is gegarandeerd machines bevatten van maximaal een [Update Domein,](manage-availability.md)maar kan niet alle machines in de UD bevatten. <br><br> Voorbeeld: <br><ul><li> [FrontEnd_IN_0", "BackEnd_IN_0"] |
| Gebeurtenisstatus | Status van deze gebeurtenis. <br><br> Waarden: <ul><li>`Scheduled`: Deze gebeurtenis begint volgens de opgegeven `NotBefore` tijd in de eigenschap.<li>`Started`: Dit evenement is begonnen.</ul> Er `Completed` wordt nooit een of vergelijkbare status opgegeven; de gebeurtenis wordt niet meer geretourneerd wanneer de gebeurtenis is voltooid.
| NotBefore| Tijd waarna dit evenement kan beginnen. <br><br> Voorbeeld: <br><ul><li> Maandag 19 sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Gebeurtenisplanning
Elke gebeurtenis is gepland een minimale hoeveelheid tijd in de toekomst op basis van gebeurtenistype. Deze tijd wordt weerspiegeld in `NotBefore` de eigenschap van een evenement. 

|EventType (EventType)  | Minimale kennisgeving |
| - | - |
| Bevriezen| 15 minuten |
| Opnieuw opstarten | 15 minuten |
| Opnieuw implementeren | 10 minuten |
| Voorrang | 30 seconden |
| Beëindigen | [Gebruiker configureerbaar](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 tot 15 minuten |

> [!NOTE] 
> In sommige gevallen kan Azure hostfouten voorspellen als gevolg van gedegradeerde hardware en probeert het de onderbreking van uw service te beperken door een migratie te plannen. Getroffen virtuele machines ontvangen een `NotBefore` geplande gebeurtenis met een die meestal een paar dagen in de toekomst. De werkelijke tijd is afhankelijk van de voorspelde foutrisicobeoordeling. Azure probeert 7 dagen van tevoren te melden wanneer dat mogelijk is, maar de werkelijke tijd varieert en kan kleiner zijn als de voorspelling is dat er een grote kans is dat de hardware op korte termijn niet op tijd uitvalt. Om het risico voor uw service te minimaliseren in het geval dat de hardware uitvalt voordat het systeem de migratie initieerde, wordt aanbevolen om uw virtuele machine zo snel mogelijk opnieuw in te zetten.

### <a name="event-scope"></a>Gebeurtenisbereik     
Geplande gebeurtenissen worden geleverd aan:
 - Zelfstandige virtuele machines
 - Alle virtuele machines in een cloudservice      
 - Alle virtuele machines in een beschikbaarheidsset      
 - Alle virtuele machines in een plaatsingsgroep voor een schaalset.         

Als gevolg hiervan moet `Resources` u het veld in de gebeurtenis controleren om te bepalen welke VM's zullen worden beïnvloed. 

### <a name="starting-an-event"></a>Een evenement starten 

Zodra u hebt geleerd van een aanstaande gebeurtenis en uw logica voor gracieuze afsluiting hebt voltooid, u de uitstaande gebeurtenis goedkeuren door een `POST` oproep te doen naar de metagegevensservice met de `EventId`. Dit geeft Azure aan dat het de minimale meldingstijd kan verkorten (indien mogelijk). 

Het volgende is de json verwacht in het `POST` verzoek lichaam. Het verzoek moet een `StartRequests`lijst van . Elk `StartRequest` bevat `EventId` de voor de gebeurtenis die u wilt versnellen:
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
> Door een gebeurtenis te erkennen, `Resources` kan het evenement voor iedereen in het evenement worden voortgezet, niet alleen de virtuele machine die de gebeurtenis erkent. U er daarom voor kiezen om een leider te kiezen om `Resources` de erkenning te coördineren, die net zo eenvoudig kan zijn als de eerste machine in het veld.


## <a name="powershell-sample"></a>Voorbeeld van PowerShell 

In het volgende voorbeeld wordt de metagegevensservice voor geplande gebeurtenissen opgevraagd en wordt elke openstaande gebeurtenis goedgekeurd.

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

- Bekijk een [demo voor geplande gebeurtenissen](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) op Azure Friday. 
- De codevoorbeelden voor geplande gebeurtenissen controleren in de [GitHub-opslagplaats voor geplande gebeurtenissen met azure-instantie](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Lees meer over de API's die beschikbaar zijn in de [instantiemetagegevensservice](instance-metadata-service.md).
- Meer informatie over [gepland onderhoud voor virtuele Windows-machines in Azure](planned-maintenance.md).
