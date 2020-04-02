---
title: bestand opnemen
description: bestand opnemen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/01/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0e8a5960da394b1eac1578d7f757b25995ff8faa
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80573212"
---
| Resource | Limiet |
| --- | --- |
| Gegevensdoorvoer |30 Gbps<sup>1</sup> |
|Regels|10,000. Alle regeltypen gecombineerd.|
|Maximale DNAT-regels|298<br>Als het protocol van een regel is geconfigureerd voor zowel TCP als UDP, telt het als twee regels.|
|Minimale AzureFirewallSubnet-grootte |/26|
|Poortbereik in netwerk- en toepassingsregels|0-64,000. Er wordt gewerkt aan een versoepeling van deze beperking.|
|Openbare IP-adressen|Maximaal 100 (Momenteel worden SNAT-poorten alleen toegevoegd voor de eerste vijf openbare IP-adressen.)|
|Routetabel|AzureFirewallSubnet heeft standaard een route van 0.0.0.0/0 met de NextHopType-waarde ingesteld op **internet.**<br><br>Azure Firewall moet een directe internetverbinding hebben. Als uw AzureFirewallSubnet via BGP een standaardroute naar uw on-premises netwerk leert, moet u dat overschrijven met een 0.0.0.0/0 UDR met de **NextHopType-waarde** die is ingesteld als **internet** om directe internetverbinding te behouden. Azure Firewall biedt standaard geen ondersteuning voor gedwongen tunneling naar een on-premises netwerk.<br><br>Als uw configuratie echter gedwongen tunneling naar een on-premises netwerk vereist, zal Microsoft dit per geval ondersteunen. Neem contact op met de ondersteuning zodat we uw aanvraag kunnen bekijken. Als dit wordt geaccepteerd, staan we uw abonnement toe en zorgen we ervoor dat de vereiste firewall-internetverbinding wordt gehandhaafd.|

<sup>1.</sup> Als u deze limieten wilt verhogen, neemt u contact op met Azure Support.
