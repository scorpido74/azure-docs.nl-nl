---
title: bestand opnemen
description: bestand opnemen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "68229282"
---
| Resource | Standaardlimiet |
| --- | --- |
| Gegevens doorvoer |30 Gbps<sup>1</sup> |
|Regels|10.000, alle regel typen gecombineerd.|
|Minimale AzureFirewallSubnet-grootte |/26|
|Poortbereik in netwerk- en toepassingsregels|0-64000. Het werk wordt uitgevoerd om deze beperking te versoepelen.|
|Openbare IP-adressen|maximum van 100|
|Routetabel|AzureFirewallSubnet heeft standaard een ' 0.0.0.0/0-route waarbij de waarde NextHopType is ingesteld op **Internet**.<br><br>Azure Firewall moet een rechtstreekse Internet verbinding hebben. Als uw AzureFirewallSubnet een standaard route naar uw on-premises netwerk via BGP leert, moet u die vervangen door een 0.0.0.0/0-UDR met de **NextHopType** -waarde ingesteld als **Internet** om direct Internet connectiviteit te onderhouden. Standaard biedt Azure Firewall geen ondersteuning voor geforceerde tunneling naar een on-premises netwerk.<br><br>Als uw configuratie echter geforceerde tunneling voor een on-premises netwerk vereist, zal micro soft deze in het geval per geval ondersteunen. Neem contact op met de ondersteuning zodat we uw aanvraag kunnen controleren. Als u dit hebt geaccepteerd, wordt uw abonnement toegestaan en wordt de vereiste Firewall Internet connectiviteit onderhouden.|

<sup>1</sup> Als u deze limieten wilt verhogen, neemt u contact op met de ondersteuning van Azure.
