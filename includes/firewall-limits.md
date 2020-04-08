---
title: bestand opnemen
description: bestand opnemen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fb95301d4faa958a677c42bc8092ac52a7c95c8b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813775"
---
| Resource | Limiet |
| --- | --- |
| Gegevensdoorvoer |30 Gbps<sup>1</sup> |
|Regels|10,000. Alle regeltypen gecombineerd.|
|Maximale DNAT-regels|298<br>Als het protocol van een regel is geconfigureerd voor zowel TCP als UDP, telt het als twee regels.|
|Minimale AzureFirewallSubnet-grootte |/26|
|Poortbereik in netwerk- en toepassingsregels|1 - 65535|
|Openbare IP-adressen|Maximaal 100 (Momenteel worden SNAT-poorten alleen toegevoegd voor de eerste vijf openbare IP-adressen.)|
|IP-groepen IP-adressen|50 IP-groepen of minder: maximaal 5000 individuele IP-adressen per firewall-exemplaar.<br>51 - 100 IP-groepen: 500 individueel IP-adres per firewall-exemplaar.<br><br>Zie [IP-groepen (voorbeeld) in Azure Firewall](../articles/firewall/ip-groups.md#ip-address-limits) voor meer informatie
|Routetabel|AzureFirewallSubnet heeft standaard een route van 0.0.0.0/0 met de NextHopType-waarde ingesteld op **internet.**<br><br>Azure Firewall moet een directe internetverbinding hebben. Als uw AzureFirewallSubnet via BGP een standaardroute naar uw on-premises netwerk leert, moet u dat overschrijven met een 0.0.0.0/0 UDR met de **NextHopType-waarde** die is ingesteld als **internet** om directe internetverbinding te behouden. Azure Firewall biedt standaard geen ondersteuning voor gedwongen tunneling naar een on-premises netwerk.<br><br>Als uw configuratie echter gedwongen tunneling naar een on-premises netwerk vereist, zal Microsoft dit per geval ondersteunen. Neem contact op met de ondersteuning zodat we uw aanvraag kunnen bekijken. Als dit wordt geaccepteerd, staan we uw abonnement toe en zorgen we ervoor dat de vereiste firewall-internetverbinding wordt gehandhaafd.|

<sup>1.</sup> Als u deze limieten wilt verhogen, neemt u contact op met Azure Support.
