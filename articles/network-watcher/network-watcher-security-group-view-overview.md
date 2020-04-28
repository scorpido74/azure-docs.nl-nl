---
title: Inleiding tot de weer gave van beveiligings groepen in azure Network Watcher | Microsoft Docs
description: Op deze pagina vindt u een overzicht van de mogelijkheden van de Network Watcher-beveiligings weergave
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76840754"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Inleiding tot de weer gave effectief beveiligings regels in azure Network Watcher

Netwerk beveiligings groepen zijn gekoppeld aan een subnetniveau of op een NIC-niveau. Wanneer deze is gekoppeld aan een subnetniveau, is dit van toepassing op alle VM-exemplaren in het subnet. De weer gave effectief beveiligings regels retourneert alle geconfigureerde Nsg's en regels die zijn gekoppeld aan een NIC-en subnetniveau voor een virtuele machine die inzicht in de configuratie biedt. Daarnaast worden de meest efficiënte beveiligings regels geretourneerd voor elk van de Nic's in een VM. Met de weer gave effectief beveiligings regels kunt u een virtuele machine beoordelen op netwerk problemen, zoals open poorten. U kunt ook controleren of uw netwerk beveiligings groep werkt zoals verwacht op basis van een [vergelijking tussen de geconfigureerde en goedgekeurde beveiligings regels](network-watcher-nsg-auditing-powershell.md).

Een uitgebreidere use-case is een beveiligings naleving en controle. U kunt een vooraf ingestelde set beveiligings regels definiëren als een model voor beveiligings beheer in uw organisatie. Een periodieke nalevings controle kan worden geïmplementeerd in een programmatische manier door de regels voor voor Schriften te vergelijken met de doel regels voor elk van de virtuele machines in uw netwerk.

In de portal regels worden weer gegeven voor elke netwerk interface en gegroepeerd op binnenkomend versus uitgaand. Dit biedt een eenvoudige weer gave van de regels die worden toegepast op een virtuele machine. Er wordt een knop downloaden gegeven om eenvoudig alle beveiligings regels te downloaden, ongeacht het tabblad in een CSV-bestand.

![beveiligings groep weer geven][1]

U kunt regels selecteren en er wordt een nieuwe blade geopend om de netwerk beveiligings groep en de voor voegsels van de bron en bestemming weer te geven. Op deze Blade kunt u rechtstreeks naar de resource van de netwerk beveiligings groep navigeren.

![drilldownbewerking][2]

### <a name="next-steps"></a>Volgende stappen

Meer informatie over het controleren van de instellingen van uw netwerk beveiligings groep vindt u in de instellingen voor de netwerk [beveiligings groep controleren met Power shell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









