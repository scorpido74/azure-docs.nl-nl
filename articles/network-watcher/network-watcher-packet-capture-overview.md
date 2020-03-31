---
title: Inleiding tot packet capture in Azure Network Watcher | Microsoft Documenten
description: Deze pagina geeft een overzicht van de mogelijkheden voor het vastleggen van pakketten van Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1f17463125cead64bd58a2d07e53eee4d3cfcd70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840805"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Inleiding tot variabele pakketopname in Azure Network Watcher

Met variabele pakketopname van Network Watcher u pakketopnamesessies maken om het verkeer van en naar een virtuele machine bij te houden. Packet capture helpt bij het diagnosticeren van netwerkafwijkingen, zowel reactief als proactief. Andere toepassingen zijn het verzamelen van netwerkstatistieken, het verkrijgen van informatie over netwerkinbraken, het opsporen van client-servercommunicatie en nog veel meer.

Packet capture is een virtuele machine extensie die op afstand wordt gestart via Network Watcher. Deze mogelijkheid verlicht de belasting van het handmatig uitvoeren van een pakketopname op de gewenste virtuele machine, wat kostbare tijd bespaart. Packet capture kan worden geactiveerd via de portal, PowerShell, CLI of REST API. Een voorbeeld van hoe packet capture kan worden geactiveerd is met Virtual Machine-waarschuwingen. Er zijn filters beschikbaar voor de opnamesessie om ervoor te zorgen dat u verkeer vastlegt dat u wilt controleren. Filters zijn gebaseerd op 5-tuple (protocol, lokaal IP-adres, extern IP-adres, lokale poort en externe poort) informatie. De vastgelegde gegevens worden opgeslagen in de lokale schijf of een opslagblob. Er is een limiet van 10 packet capture sessies per regio per abonnement. Deze limiet is alleen van toepassing op de sessies en is niet van toepassing op de opgeslagen packet capture-bestanden, lokaal op de vm of in een opslagaccount.

> [!IMPORTANT]
> Packet capture vereist een `AzureNetworkWatcherExtension`virtuele machine-extensie. Voor het installeren van de extensie op een Windows VM bezoek [Azure Network Watcher Agent virtuele machine extensie voor Windows](../virtual-machines/windows/extensions-nwa.md) en voor Linux VM bezoek Azure Network Watcher Agent virtuele machine extensie voor [Linux](../virtual-machines/linux/extensions-nwa.md).

Om de informatie die u vastlegt te beperken tot alleen de gewenste informatie, zijn de volgende opties beschikbaar voor een pakketopnamesessie:

**Opnameconfiguratie**

|Eigenschap|Beschrijving|
|---|---|
|**Maximale bytes per pakket (bytes)** | Het aantal bytes van elk pakket dat wordt vastgelegd, alle bytes worden vastgelegd als ze leeg blijven. Het aantal bytes van elk pakket dat wordt vastgelegd, alle bytes worden vastgelegd als ze leeg blijven. Als u alleen de IPv4-header nodig hebt , geef hier 34 aan |
|**Maximum aantal bytes per sessie (bytes)** | Totaal aantal bytes dat wordt vastgelegd, zodra de waarde is bereikt, eindigt de sessie.|
|**Tijdslimiet (seconden)** | Hiermee stelt u een tijdsbeperking in voor de pakketopnamesessie. De standaardwaarde is 18000 seconden of 5 uur.|

**Filteren (optioneel)**

|Eigenschap|Beschrijving|
|---|---|
|**Protocol** | Het protocol om te filteren voor het vastleggen van het pakket. De beschikbare waarden zijn TCP, UDP en Alles.|
|**Lokaal IP-adres** | Deze waarde filtert het vastleggen van pakketten naar pakketten waarbij het lokale IP-adres overeenkomt met deze filterwaarde.|
|**Lokale poort** | Deze waarde filtert het vastleggen van pakketten naar pakketten waar de lokale poort overeenkomt met deze filterwaarde.|
|**Extern IP-adres** | Deze waarde filtert het vastleggen van pakketten naar pakketten waar het externe IP overeenkomt met deze filterwaarde.|
|**Externe poort** | Deze waarde filtert het vastleggen van pakketten op pakketten waarbij de externe poort overeenkomt met deze filterwaarde.|

### <a name="next-steps"></a>Volgende stappen

Ontdek hoe u pakketopnames via de portal beheren door [naar Pakketopname beheren in de Azure-portal](network-watcher-packet-capture-manage-portal.md) of met PowerShell te bezoeken door [pakketopname met PowerShell te](network-watcher-packet-capture-manage-powershell.md)beheren.

Meer informatie over het maken van proactieve pakketopnames op basis van waarschuwingen voor virtuele machines door te gaan met [Het maken van een waarschuwingspakketopname](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













