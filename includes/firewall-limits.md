---
title: bestand opnemen
description: bestand opnemen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 05/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 5a5f831e72da6bedaf12d3ed82e79f0250a09062
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875996"
---
| Resource | Limiet |
| --- | --- |
| Gegevensdoorvoer |30 Gbps<sup>1</sup> |
|Regels|10.000. Alle regel typen worden gecombineerd.|
|Maximale DNAT-regels|298<br>Als het Protocol van een regel is geconfigureerd voor zowel TCP als UDP, telt dit als twee regels.|
|Minimale AzureFirewallSubnet-grootte |/26|
|Poortbereik in netwerk- en toepassingsregels|1 - 65535|
|Openbare IP-adressen|Maxi maal 250 voor zowel DNAT-als SNAT-poorten.|
|IP-adressen van IP-groepen|50 IP-groepen of minder: Maxi maal 5000 afzonderlijke IP-adressen per firewall exemplaar.<br>51-100 IP-groepen: 500 afzonderlijke IP-adressen elk per firewall exemplaar.<br><br>Zie [IP-groepen (preview) in azure firewall](../articles/firewall/ip-groups.md#ip-address-limits) voor meer informatie
|Routetabel|AzureFirewallSubnet heeft standaard een ' 0.0.0.0/0-route waarbij de waarde NextHopType is ingesteld op **Internet**.<br><br>Azure Firewall moet een rechtstreekse Internet verbinding hebben. Als uw AzureFirewallSubnet een standaard route naar uw on-premises netwerk via BGP leert, moet u die vervangen door een 0.0.0.0/0-UDR met de **NextHopType** -waarde ingesteld als **Internet** om direct Internet connectiviteit te onderhouden. Standaard biedt Azure Firewall geen ondersteuning voor geforceerde tunneling naar een on-premises netwerk.<br><br>Als uw configuratie echter geforceerde tunneling voor een on-premises netwerk vereist, zal micro soft deze in het geval per geval ondersteunen. Neem contact op met de ondersteuning zodat we uw aanvraag kunnen controleren. Als u dit hebt geaccepteerd, wordt uw abonnement toegestaan en wordt de vereiste Firewall Internet connectiviteit onderhouden.|

<sup>1</sup> Als u deze limieten wilt verhogen, neemt u contact op met de ondersteuning van Azure.
