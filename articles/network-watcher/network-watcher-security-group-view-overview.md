---
title: Inleiding tot de weergave van beveiligingsgroepen in Azure Network Watcher | Microsoft Documenten
description: Deze pagina geeft een overzicht van de mogelijkheden voor de beveiligingsweergave van Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 18619cc0f7a21a39d0802575c484c68c5fa1a624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840754"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Inleiding tot de weergave Effectieve beveiligingsregels in Azure Network Watcher

Netwerkbeveiligingsgroepen worden gekoppeld op subnetniveau of op NIC-niveau. Wanneer deze op subnetniveau is gekoppeld, is dit van toepassing op alle VM-exemplaren in het subnet. Effectieve beveiligingsregels geven retourneert alle geconfigureerde NMG's en regels die zijn gekoppeld op een NIC- en subnetniveau voor een virtuele machine die inzicht geeft in de configuratie. Bovendien worden de effectieve beveiligingsregels geretourneerd voor elk van de NIC's in een VM. Met de weergave Effectieve beveiligingsregels u een VM beoordelen op netwerkkwetsbaarheden, zoals open poorten. U ook valideren of uw netwerkbeveiligingsgroep werkt zoals verwacht op basis van een [vergelijking tussen de geconfigureerde en de goedgekeurde beveiligingsregels.](network-watcher-nsg-auditing-powershell.md)

Een uitgebreidere use case is in security compliance en auditing. U een voorschrijfbare set beveiligingsregels definiëren als model voor beveiligingsbeheer in uw organisatie. Een periodieke compliance audit kan op een programmatische manier worden geïmplementeerd door de prescriptieve regels te vergelijken met de effectieve regels voor elk van de VM's in uw netwerk.

In de portal worden regels weergegeven voor elke netwerkinterface en gegroepeerd op inkomende versus uitgaande. Dit biedt een eenvoudig beeld van de regels die op een virtuele machine worden toegepast. Er is een downloadknop aanwezig om eenvoudig alle beveiligingsregels te downloaden, ongeacht het tabblad in een CSV-bestand.

![weergave beveiligingsgroep][1]

Regels kunnen worden geselecteerd en er wordt een nieuw blad geopend om de netwerkbeveiligingsgroep en bron- en bestemmingsvoorvoegsels weer te geven. Vanuit dit blad u rechtstreeks naar de bron van de netwerkbeveiligingsgroep navigeren.

![drilldown][2]

### <a name="next-steps"></a>Volgende stappen

Meer informatie over het controleren van de instellingen van uw netwerkbeveiligingsgroep door de instellingen van [de beveiligingsgroep voor controlenetwerken met PowerShell](network-watcher-nsg-auditing-powershell.md) te bezoeken

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









