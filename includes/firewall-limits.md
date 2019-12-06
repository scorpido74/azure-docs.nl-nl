---
title: bestand opnemen
description: bestand opnemen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/06/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a7b6867033e750f476b3d995926f0b670965a5d7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875532"
---
| Bron | Standaardlimiet |
| --- | --- |
| Gegevens doorvoer |30 Gbps<sup>1</sup> |
|Regels|10.000. Alle regel typen worden gecombineerd.|
|DNAT-regels per openbaar IP-adres|299|
|Minimale AzureFirewallSubnet-grootte |/26|
|Poortbereik in netwerk- en toepassingsregels|0-64000. Het werk wordt uitgevoerd om deze beperking te versoepelen.|
|Openbare IP-adressen|Maxi maal 100 (momenteel worden SNAT-poorten alleen toegevoegd voor de eerste vijf open bare IP-adressen.)|
|Routetabel|AzureFirewallSubnet heeft standaard een ' 0.0.0.0/0-route waarbij de waarde NextHopType is ingesteld op **Internet**.<br><br>Azure Firewall moet een rechtstreekse Internet verbinding hebben. Als uw AzureFirewallSubnet een standaard route naar uw on-premises netwerk via BGP leert, moet u die vervangen door een 0.0.0.0/0-UDR met de **NextHopType** -waarde ingesteld als **Internet** om direct Internet connectiviteit te onderhouden. Standaard biedt Azure Firewall geen ondersteuning voor geforceerde tunneling naar een on-premises netwerk.<br><br>Als uw configuratie echter geforceerde tunneling voor een on-premises netwerk vereist, zal micro soft deze in het geval per geval ondersteunen. Neem contact op met de ondersteuning zodat we uw aanvraag kunnen controleren. Als u dit hebt geaccepteerd, wordt uw abonnement toegestaan en wordt de vereiste Firewall Internet connectiviteit onderhouden.|

<sup>1</sup> Als u deze limieten wilt verhogen, neemt u contact op met de ondersteuning van Azure.
