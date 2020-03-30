---
title: Inleiding tot IP-stroom verifiëren in Azure Network Watcher | Microsoft Documenten
description: Deze pagina geeft een overzicht van de ip-stroomverificatiemogelijkheid van Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: damendo
ms.openlocfilehash: 69aca5e0901a0da8aa98fe310ac220898bf650b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845003"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Inleiding tot IP-stroomverifiëren in Azure Network Watcher

Ip-stroom controleert of een pakket is toegestaan of geweigerd aan of van een virtuele machine. De informatie bestaat uit richting, protocol, lokaal IP, extern IP, lokale poort en externe poort. Als het pakket wordt geweigerd door een beveiligingsgroep, wordt de naam van de regel die het pakket heeft geweigerd, geretourneerd. Hoewel elke bron of bestemming-IP kan worden gekozen, helpt IP-stroomverificatie beheerders om snel verbindingsproblemen van of naar het internet en van of naar de on-premises omgeving te diagnosticeren.

Ip-stroom controle kijkt naar de regels voor alle Network Security Groups (NSG's) toegepast op de netwerkinterface, zoals een subnet of virtuele machine NIC. De verkeersstroom wordt vervolgens geverifieerd op basis van de geconfigureerde instellingen van of naar die netwerkinterface. Ip-stroomverificatie is handig om te bevestigen of een regel in een netwerkbeveiligingsgroep het binnendringen of het binnendringen van verkeer van of naar een virtuele machine blokkeert.

Er moet een exemplaar van Network Watcher worden gemaakt in alle regio's die u van plan bent ip-stroom te verifiëren. Network Watcher is een regionale dienst en kan alleen worden uitgevoerd tegen resources in dezelfde regio. De gebruikte instantie heeft geen invloed op de resultaten van ip-stroomverificatie, aangezien elke route die aan het NIC- of subnet is gekoppeld, nog steeds wordt geretourneerd.

![1][1]

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om te zien of een pakket is toegestaan of geweigerd voor een specifieke virtuele machine via de portal. [Controleren of verkeer is toegestaan op een vm met IP-stroomverificatie met behulp van de portal](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

