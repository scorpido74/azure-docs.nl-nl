---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175915"
---
Als u werkt met het implementatiemodel Resource Manager, u de nieuwe Gateway SKU's wijzigen. Wanneer u overstapt van een verouderde gateway SKU naar een nieuwe SKU, verwijdert u de bestaande VPN-gateway en maakt u een nieuwe VPN-gateway.

Workflow:

1. Verwijder verbindingen van de gateway voor het virtuele netwerk.
2. Verwijder de oude VPN-gateway.
3. Maak de nieuwe VPN-gateway.
4. Werk uw on-premises VPN-apparaten bij met het nieuwe IP-adres van de VPN-gateway (voor site-naar-site-verbindingen).
5. Werk de waarde van het IP-adres voor de gateway bij voor alle lokale VNet-naar-VNet-netwerkgateways die verbinding met deze gateway maken.
6. Download nieuwe client-VPN-configuratiepakketten voor P2S-clients die verbinding maken met het virtuele netwerk via deze VPN-gateway.
7. Herstel de verbindingen met de gateway voor het virtuele netwerk.

Overwegingen:

* Als u naar de nieuwe SKU's wilt gaan, moet uw VPN-gateway zich in het implementatiemodel van Resource Manager bevinden.
* Als u een klassieke VPN-gateway hebt, moet u de oudere verouderde SKU's voor die gateway blijven gebruiken, maar u het formaat tussen de oudere SKU's wijzigen. U niet overstappen op de nieuwe SKU's.
* U hebt downtime voor connectiviteit wanneer u overstapt van een oudere SKU naar een nieuwe SKU.
* Wanneer u overstapt op een nieuwe gateway-SKU, verandert het openbare IP-adres voor uw VPN-gateway. Dit gebeurt zelfs als u hetzelfde openbare IP-adresobject opgeeft dat u eerder hebt gebruikt.