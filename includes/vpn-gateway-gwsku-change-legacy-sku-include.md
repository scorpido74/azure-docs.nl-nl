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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175915"
---
Als u werkt met het Resource Manager-implementatie model, kunt u overschakelen naar de nieuwe gateway-Sku's. Wanneer u overstapt van een verouderde gateway-SKU naar een nieuwe SKU, verwijdert u de bestaande VPN-gateway en maakt u een nieuwe VPN-gateway.

Workflowconfiguraties

1. Verwijder verbindingen van de gateway voor het virtuele netwerk.
2. Verwijder de oude VPN-gateway.
3. Maak de nieuwe VPN-gateway.
4. Werk uw on-premises VPN-apparaten bij met het nieuwe IP-adres van de VPN-gateway (voor site-naar-site-verbindingen).
5. Werk de waarde van het IP-adres voor de gateway bij voor alle lokale VNet-naar-VNet-netwerkgateways die verbinding met deze gateway maken.
6. Download nieuwe client-VPN-configuratiepakketten voor P2S-clients die verbinding maken met het virtuele netwerk via deze VPN-gateway.
7. Herstel de verbindingen met de gateway voor het virtuele netwerk.

Overwegingen:

* Als u wilt overstappen op de nieuwe Sku's, moet uw VPN-gateway zich in het Resource Manager-implementatie model bevindt.
* Als u een klassieke VPN-gateway hebt, moet u de oudere oudere Sku's voor die gateway blijven gebruiken, maar u kunt het formaat van de oudere Sku's echter aanpassen. U kunt niet overschakelen naar de nieuwe Sku's.
* U krijgt connectiviteits downtime wanneer u overschakelt van een verouderde SKU naar een nieuwe SKU.
* Wanneer u overstapt naar een nieuwe gateway-SKU, wordt het open bare IP-adres voor uw VPN-gateway gewijzigd. Dit gebeurt ook als u hetzelfde open bare IP-adres object opgeeft dat u eerder hebt gebruikt.