---
title: Geplande gebeurtenissen voor Linux VM's in Azure
description: Plan gebeurtenissen met behulp van Azure Metadata Service voor uw Virtuele Linux-machines.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: b3b9914d0e5162f8f8f41b929d7bdfef68f85ad9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263315"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Geplande gebeurtenissen voor Linux VM's

Geplande gebeurtenissen is een Azure Metadata Service die uw toepassing de tijd geeft om zich voor te bereiden op vm-onderhoud (virtual machine). Het biedt informatie over aankomende onderhoudsgebeurtenissen (bijvoorbeeld opnieuw opstarten) zodat uw toepassing zich erop kan voorbereiden en onderbrekingen kan beperken. Het is beschikbaar voor alle Azure Virtual Machines-typen, waaronder PaaS en IaaS op zowel Windows als Linux. 

Zie [Geplande gebeurtenissen voor Windows-VM's voor](../windows/scheduled-events.md)informatie over geplande gebeurtenissen in Windows.

> [!Note] 
> Geplande gebeurtenissen zijn over het algemeen beschikbaar in alle Azure-regio's. Zie [Beschikbaarheid van versies en regio's](#version-and-region-availability) voor de laatste informatie over de release.

## <a name="why-use-scheduled-events"></a>Waarom geplande evenementen gebruiken?

Veel toepassingen kunnen profiteren van de tijd om zich voor te bereiden op VM-onderhoud. De tijd kan worden gebruikt om toepassingsspecifieke taken uit te voeren die de beschikbaarheid, betrouwbaarheid en bruikbaarheid verbeteren, waaronder: 

- Checkpoint en herstel.
- Verbinding aftappen.
- Primaire replica failover.
- Verwijdering uit een load balancer pool.
- Logboekregistratie.
- Sierlijke shutdown.

Met Geplande gebeurtenissen kan uw toepassing ontdekken wanneer onderhoud plaatsvindt en taken activeren om de impact ervan te beperken.  

Geplande gebeurtenissen bieden gebeurtenissen in de volgende use cases:

- [Platform gestart onderhoud](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (bijvoorbeeld VM reboot, live migratie of geheugen bewaren updates voor host)
- Virtuele machine draait op [gedegradeerde host hardware](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) die naar verwachting binnenkort mislukken
- Door de gebruiker geïnitieerd onderhoud (een gebruiker start bijvoorbeeld een VM opnieuw of opnieuw in)
- [Spot VM](spot-vms.md) en [Spot schaal instellen](../../virtual-machine-scale-sets/use-spot.md) instantie uitzettingen.

## <a name="the-basics"></a>De basisbeginselen  

  Metagegevensservice onthult informatie over het uitvoeren van VM's met behulp van een REST-eindpunt dat toegankelijk is vanuit de VM. De informatie is beschikbaar via een niet-routable IP, zodat deze niet buiten de VM wordt blootgesteld.

### <a name="scope"></a>Bereik
Geplande gebeurtenissen worden geleverd aan:

- Standalone virtuele machines.
- Alle VM's in een cloudservice.
- Alle VM's in een beschikbaarheidsset.
- Alle VM's in een plaatsingsgroep met een schaal. 

Controleer daarom het `Resources` veld in de gebeurtenis om te bepalen welke VM's worden beïnvloed.

### <a name="endpoint-discovery"></a>Eindpuntdetectie
Voor VNET-vms is metadataservice beschikbaar vanaf een statisch `169.254.169.254`niet-routable IP. Het volledige eindpunt voor de nieuwste versie van geplande gebeurtenissen is: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Als de VM niet is gemaakt binnen een virtueel netwerk, de standaardaanvragen voor cloudservices en klassieke VM's, is aanvullende logica vereist om het IP-adres te ontdekken dat moet worden gebruikt. Zie dit voorbeeld voor meer informatie over het ontdekken van [het eindpunt van de host.](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="version-and-region-availability"></a>Beschikbaarheid van versie en regio
De service Geplande gebeurtenissen is versie. Versies zijn verplicht; de huidige `2019-01-01`versie is .

| Versie | Releasetype | Regio's | Releaseopmerkingen | 
| - | - | - | - | 
| 2019-01-01 | Algemene beschikbaarheid | Alle | <li> Added support for virtual machine scale sets EventType 'Terminate' |
| 2017-11-01 | Algemene beschikbaarheid | Alle | <li> Ondersteuning toegevoegd voor Spot VM-uitzetting EventType 'Preempt'<br> | 
| 2017-08-01 | Algemene beschikbaarheid | Alle | <li> Verwijderde prepended underscore van resourcenamen voor IaaS VM's<br><li>Vereiste voor metagegevenskoppen die worden afgedwongen voor alle aanvragen | 
| 2017-03-01 | Preview | Alle | <li>Eerste release |


> [!NOTE] 
> Eerdere preview-releases van Geplande gebeurtenissen ondersteund {latest} als de api-versie. Dit formaat wordt niet langer ondersteund en zal in de toekomst worden afgeschaft.

### <a name="enabling-and-disabling-scheduled-events"></a>Geplande gebeurtenissen in- en uitschakelen
Geplande gebeurtenissen zijn ingeschakeld voor uw service wanneer u voor het eerst een aanvraag voor gebeurtenissen indient. U moet een vertraagde reactie verwachten in uw eerste gesprek van maximaal twee minuten.

Geplande gebeurtenissen zijn uitgeschakeld voor uw service als deze gedurende 24 uur geen aanvraag indienen.

### <a name="user-initiated-maintenance"></a>Door de gebruiker geïnitieerd onderhoud
Door de gebruiker geïnitieerdvm-onderhoud via de Azure-portal, API, CLI of PowerShell resulteert in een geplande gebeurtenis. U dan de logica voor onderhoudsvoorbereiding in uw toepassing testen en uw toepassing kan zich voorbereiden op door de gebruiker geïnitieerd onderhoud.

Als u een vm opnieuw start, wordt een gebeurtenis met het type `Reboot` gepland. Als u een vm opnieuw implementeert, wordt een gebeurtenis met het type `Redeploy` gepland.

## <a name="use-the-api"></a>De API gebruiken

### <a name="headers"></a>Headers
Wanneer u de metagegevensservice `Metadata:true` opvraagt, moet u de koptekst opgeven om ervoor te zorgen dat het verzoek niet onbedoeld is doorgestuurd. De `Metadata:true` header is vereist voor alle geplande gebeurtenisaanvragen. Als u de koptekst niet in de aanvraag opneemt, resulteert dit in een antwoord van 'Slecht verzoek' van de Metadata Service.

### <a name="query-for-events"></a>Query voor gebeurtenissen
U de volgende oproep doen voor geplande gebeurtenissen door de volgende oproep te doen:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

Een antwoord bevat een reeks geplande gebeurtenissen. Een lege array betekent dat er momenteel geen gebeurtenissen zijn gepland.
In het geval dat er geplande gebeurtenissen zijn, bevat het antwoord een reeks gebeurtenissen. 
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

### <a name="event-properties"></a>Gebeurtenis-eigenschappen
|Eigenschap  |  Beschrijving |
| - | - |
| EventId (EventId) | Wereldwijd unieke id voor deze gebeurtenis. <br><br> Voorbeeld: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType (EventType) | Impact van deze gebeurtenis veroorzaakt. <br><br> Waarden: <br><ul><li> `Freeze`: De virtuele machine is gepland om te pauzeren voor een paar seconden. CPU- en netwerkconnectiviteit kan worden opgeschort, maar er is geen invloed op het geheugen of geopende bestanden.<li>`Reboot`: De virtuele machine is gepland voor opnieuw opstarten (niet-persistentgeheugen gaat verloren). <li>`Redeploy`: De virtuele machine is gepland om te verhuizen naar een ander knooppunt (kortstondige schijven gaan verloren). <li>`Preempt`: De Spot Virtual Machine wordt verwijderd (kortstondige schijven gaan verloren). <li> `Terminate`: De virtuele machine wordt verwijderd. |
| ResourceType | Type resource die deze gebeurtenis beïnvloedt. <br><br> Waarden: <ul><li>`VirtualMachine`|
| Resources| Lijst met bronnen die deze gebeurtenis beïnvloedt. De lijst bevat gegarandeerd machines van maximaal één [updatedomein,](manage-availability.md)maar bevat mogelijk niet alle machines in de UD. <br><br> Voorbeeld: <br><ul><li> [FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Status van deze gebeurtenis. <br><br> Waarden: <ul><li>`Scheduled`: Deze gebeurtenis begint volgens de opgegeven `NotBefore` tijd in de eigenschap.<li>`Started`: Dit evenement is begonnen.</ul> Er `Completed` wordt nooit een of vergelijkbare status opgegeven. De gebeurtenis wordt niet meer geretourneerd wanneer de gebeurtenis is voltooid.
| NotBefore| Tijd waarna dit evenement kan beginnen. <br><br> Voorbeeld: <br><ul><li> Maandag 19 sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Gebeurtenisplanning
Elke gebeurtenis is gepland een minimale hoeveelheid tijd in de toekomst op basis van het gebeurtenistype. Deze tijd wordt weerspiegeld in `NotBefore` de eigenschap van een evenement. 

|EventType (EventType)  | Minimale kennisgeving |
| - | - |
| Bevriezen| 15 minuten |
| Opnieuw opstarten | 15 minuten |
| Opnieuw implementeren | 10 minuten |
| Voorrang | 30 seconden |
| Beëindigen | [Gebruiker configureerbaar](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 tot 15 minuten |

> [!NOTE] 
> In sommige gevallen kan Azure hostfouten voorspellen als gevolg van gedegradeerde hardware en probeert het de onderbreking van uw service te beperken door een migratie te plannen. Getroffen virtuele machines ontvangen een `NotBefore` geplande gebeurtenis met een die meestal een paar dagen in de toekomst. De werkelijke tijd is afhankelijk van de voorspelde foutrisicobeoordeling. Azure probeert 7 dagen van tevoren te melden wanneer dat mogelijk is, maar de werkelijke tijd varieert en kan kleiner zijn als de voorspelling is dat er een grote kans is dat de hardware op korte termijn niet op tijd uitvalt. Om het risico voor uw service te minimaliseren in het geval dat de hardware uitvalt voordat de door het systeem gestarte migratie is gestart, raden we u aan uw virtuele machine zo snel mogelijk opnieuw in te zetten.

### <a name="start-an-event"></a>Een evenement starten 

Nadat u meer te weten bent over een aankomend evenement en uw `POST` logica voor `EventId`gracieuze afsluiting hebt voltooid, u de uitstaande gebeurtenis goedkeuren door een oproep te doen naar metadataservice met. Deze oproep geeft Azure aan dat het de minimale meldingstijd kan verkorten (indien mogelijk). 

De volgende JSON-steekproef `POST` wordt verwacht in de aanvraaginstantie. Het verzoek moet een `StartRequests`lijst van . Elk `StartRequest` `EventId` bevat voor de gebeurtenis die u wilt versnellen:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash monster
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Door een gebeurtenis te erkennen, `Resources` kan de gebeurtenis voor iedereen in het evenement worden voortgezet, niet alleen de VM die de gebeurtenis erkent. Daarom u ervoor kiezen om een leider te kiezen om de bevestiging `Resources` te coördineren, die net zo eenvoudig kan zijn als de eerste machine in het veld.

## <a name="python-sample"></a>Python-monster 

In de volgende voorbeeldquery's wordt de metagegevensservice voor geplande gebeurtenissen opgevraagd en wordt elke opvallende gebeurtenis goedgekeurd:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Volgende stappen 
- Bekijk [geplande gebeurtenissen op Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) om een demo te bekijken. 
- Controleer de codevoorbeelden voor geplande gebeurtenissen in de [GitHub-opslagplaats geplande gebeurtenissen van Azure Instance](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Lees meer over de API's die beschikbaar zijn in de [Instance Metadata Service](instance-metadata-service.md).
- Meer informatie over [gepland onderhoud voor virtuele Linux-machines in Azure](planned-maintenance.md).
