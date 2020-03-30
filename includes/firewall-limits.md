---
title: bestand opnemen
description: bestand opnemen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 01/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0df38533afe97f010d1050c3ee2a4a69a54d4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334653"
---
| Resource | Limiet |
| --- | --- |
| Gegevensdoorvoer |30 Gbps<sup>1</sup> |
|Regels|10,000. Alle regeltypen gecombineerd.|
|Maximale DNAT-regels|299|
|Minimale AzureFirewallSubnet-grootte |/26|
|Poortbereik in netwerk- en toepassingsregels|0-64,000. Er wordt gewerkt aan een versoepeling van deze beperking.|
|Openbare IP-adressen|Maximaal 100 (Momenteel worden SNAT-poorten alleen toegevoegd voor de eerste vijf openbare IP-adressen.)|
|Routetabel|AzureFirewallSubnet heeft standaard een route van 0.0.0.0/0 met de NextHopType-waarde ingesteld op **internet.**<br><br>Azure Firewall moet een directe internetverbinding hebben. Als uw AzureFirewallSubnet via BGP een standaardroute naar uw on-premises netwerk leert, moet u dat overschrijven met een 0.0.0.0/0 UDR met de **NextHopType-waarde** die is ingesteld als **internet** om directe internetverbinding te behouden. Azure Firewall biedt standaard geen ondersteuning voor gedwongen tunneling naar een on-premises netwerk.<br><br>Als uw configuratie echter gedwongen tunneling naar een on-premises netwerk vereist, zal Microsoft dit per geval ondersteunen. Neem contact op met de ondersteuning zodat we uw aanvraag kunnen bekijken. Als dit wordt geaccepteerd, staan we uw abonnement toe en zorgen we ervoor dat de vereiste firewall-internetverbinding wordt gehandhaafd.|

<sup>1.</sup> Als u deze limieten wilt verhogen, neemt u contact op met Azure Support.
