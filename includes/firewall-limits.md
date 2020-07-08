---
title: Include-bestand
description: bestand opnemen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 06/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b317c2a43352a750d4700fad56d5f7d741b2cc7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805793"
---
| Resource | Limiet |
| --- | --- |
| Gegevens doorvoer |30 Gbps<sup>1</sup> |
|Regels|10.000. Alle regel typen worden gecombineerd.|
|Maximale DNAT-regels|298 voor één openbaar IP-adres.<br>Eventuele extra open bare IP-adressen dragen bij aan de beschik bare SNAT-poorten, maar verminderen het aantal beschik bare DNAT-regels. Zo kunnen twee open bare IP-adressen bijvoorbeeld 297 DNAT-regels. Als het Protocol van een regel is geconfigureerd voor zowel TCP als UDP, telt dit als twee regels.|
|Minimale AzureFirewallSubnet-grootte |/26|
|Poortbereik in netwerk- en toepassingsregels|1 - 65535|
|Openbare IP-adressen|maximum van 250. Alle open bare IP-adressen kunnen worden gebruikt in DNAT-regels en ze zijn allemaal bijdragen aan de beschik bare SNAT-poorten.|
|IP-adressen in IP-groepen|Maxi maal 100 IP-groepen per firewall.<br>Maxi maal 5000 afzonderlijke IP-adressen of IP-voor voegsels per IP-groep.<br><br>Zie [IP-groepen in azure firewall](../articles/firewall/ip-groups.md#ip-address-limits)voor meer informatie.
|Routetabel|AzureFirewallSubnet heeft standaard een ' 0.0.0.0/0-route waarbij de waarde NextHopType is ingesteld op **Internet**.<br><br>Azure Firewall moet een directe verbinding met internet hebben. Als uw AzureFirewallSubnet een standaard route naar uw on-premises netwerk via BGP leert, moet u die vervangen door een 0.0.0.0/0-UDR met de **NextHopType** -waarde ingesteld als **Internet** om direct Internet connectiviteit te onderhouden. Standaard biedt Azure Firewall geen ondersteuning voor geforceerde tunneling naar een on-premises netwerk.<br><br>Als uw configuratie echter geforceerde tunneling voor een on-premises netwerk vereist, zal micro soft deze in het geval per geval ondersteunen. Neem contact op met de ondersteuning zodat we uw aanvraag kunnen controleren. Als u dit hebt geaccepteerd, wordt uw abonnement toegestaan en wordt de vereiste Firewall Internet connectiviteit onderhouden.|

<sup>1</sup> Als u deze limieten wilt verhogen, neemt u contact op met de ondersteuning van Azure.
