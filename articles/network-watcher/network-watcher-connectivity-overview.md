---
title: Inleiding tot problemen met Azure Network Watcher-verbinding | Microsoft Documenten
description: Op deze pagina vindt u een overzicht van de mogelijkheden voor het oplossen van problemen met de netwerkwatcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: cae3072a3468b232e95d7c1949948b71059695ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283275"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Inleiding tot verbindingsproblemen in Azure Network Watcher

De functie voor het oplossen van verbindingen van Network Watcher biedt de mogelijkheid om een directe TCP-verbinding te controleren van een virtuele machine naar een virtuele machine (VM), volledig gekwalificeerde domeinnaam (FQDN), URI of IPv4-adres. Netwerkscenario's zijn complex, ze worden geïmplementeerd met behulp van netwerkbeveiligingsgroepen, firewalls, door de gebruiker gedefinieerde routes en resources die door Azure worden geleverd. Complexe configuraties maken het oplossen van verbindingsproblemen een uitdaging. Network Watcher helpt de hoeveelheid tijd te verminderen om verbindingsproblemen te vinden en op te sporen. De geretourneerde resultaten kunnen inzicht geven in de vraag of een verbindingsprobleem te wijten is aan een platform of een probleem met de gebruikersconfiguratie. Connectiviteit kan worden gecontroleerd met [PowerShell,](network-watcher-connectivity-powershell.md) [Azure CLI](network-watcher-connectivity-cli.md)en [REST API.](network-watcher-connectivity-rest.md)

> [!IMPORTANT]
> Het oplossen van verbindingsproblemen vereist `AzureNetworkWatcherExtension` dat de VM die u oplost, de VM-extensie heeft geïnstalleerd. Voor het installeren van de extensie op een Windows VM bezoek [Azure Network Watcher Agent virtuele machine extensie voor Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en voor Linux VM bezoek Azure Network Watcher Agent virtuele machine extensie voor [Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). De extensie is niet vereist op het eindpunt van de bestemming.

## <a name="response"></a>Antwoord

In de volgende tabel worden de eigenschappen weergegeven die zijn geretourneerd wanneer het probleem met de verbinding is opgelost.

|Eigenschap  |Beschrijving  |
|---------|---------|
|Verbindingsstatus     | De status van de connectiviteitscontrole. Mogelijke resultaten zijn **bereikbaar** en **onbereikbaar.**        |
|AvgLatencyInMs     | Gemiddelde latentie tijdens de connectiviteitscontrole in milliseconden. (Alleen weergegeven als de controlestatus bereikbaar is)        |
|MinLatencyInMs     | Minimale latentie tijdens de connectiviteitscontrole in milliseconden. (Alleen weergegeven als de controlestatus bereikbaar is)        |
|MaxLatencyInMs     | Maximale latentie tijdens de connectiviteitscontrole in milliseconden. (Alleen weergegeven als de controlestatus bereikbaar is)        |
|ProbesSent     | Aantal sondes verzonden tijdens de controle. De maximale waarde is 100.        |
|ProbesMislukt     | Aantal sondes dat is mislukt tijdens de controle. De maximale waarde is 100.        |
|Hops     | Hop per hoppad van bron naar bestemming.        |
|Hop[]. Type     | Type resource. Mogelijke waarden zijn **Source**, **VirtualAppliance,** **VnetLocal**en **Internet**.        |
|Hop[]. Id | Unieke identificatie van de hop.|
|Hop[]. Adres | IP-adres van de hop.|
|Hop[]. Resourceid | ResourceID van de hop als de hop een Azure-bron is. Als het een internetbron is, is ResourceID **internet.** |
|Hop[]. NextHopIds | De unieke id van de volgende hop genomen.|
|Hop[]. Kwesties | Een verzameling van problemen die werden ondervonden tijdens de controle op die hop. Als er geen problemen waren, is de waarde leeg.|
|Hop[]. Problemen[]. Oorsprong | Op de huidige hop, waar probleem zich heeft voorgedaan. Mogelijke waarden zijn:<br/> **Binnenkomend** - Probleem staat op de link van de vorige hop naar de huidige hop<br/>**Uitgaand** - Probleem is op de link van de huidige hop naar de volgende hop<br/>**Lokaal** - Probleem is op de huidige hop.|
|Hop[]. Problemen[]. Ernst | De ernst van het probleem gedetecteerd. Mogelijke waarden zijn **Fout** en **Waarschuwing**. |
|Hop[]. Problemen[]. Type |Het type probleem gevonden. Mogelijke waarden zijn: <br/>**Cpu**<br/>**Geheugen**<br/>**GuestFirewall (GuestFirewall)**<br/>**DnsResolution**<br/>**Netwerkbeveiligingsregel**<br/>**UserDefinedRoute** |
|Hop[]. Problemen[]. Context |Details met betrekking tot het probleem gevonden.|
|Hop[]. Problemen[]. Context[].sleutel |Sleutel van het sleutelwaardepaar is geretourneerd.|
|Hop[]. Problemen[]. Context[].waarde |Waarde van het sleutelwaardepaar geretourneerd.|

Het volgende is een voorbeeld van een probleem gevonden op een hop.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Fouttypen

Verbindingsproblemen problemen met retourtypen over de verbinding. In de volgende tabel vindt u een lijst met de huidige fouttypen die zijn geretourneerd.

|Type  |Beschrijving  |
|---------|---------|
|CPU     | Hoog CPU-gebruik.       |
|Geheugen     | Hoog geheugengebruik.       |
|GuestFirewall (GuestFirewall)     | Het verkeer is geblokkeerd vanwege een firewallconfiguratie van een virtuele machine.        |
|DNSResolution     | DNS-resolutie is mislukt voor het doeladres.        |
|Netwerkbeveiligingsregel    | Verkeer wordt geblokkeerd door een NSG-regel (regel wordt geretourneerd)        |
|UserDefinedRoute|Het verkeer wordt verwijderd als gevolg van een door de gebruiker gedefinieerde of systeemroute. |

### <a name="next-steps"></a>Volgende stappen

Meer informatie over het oplossen van problemen met verbindingen met de [Azure-portal,](network-watcher-connectivity-portal.md) [PowerShell,](network-watcher-connectivity-powershell.md)de [Azure CLI](network-watcher-connectivity-cli.md)of [REST API](network-watcher-connectivity-rest.md).