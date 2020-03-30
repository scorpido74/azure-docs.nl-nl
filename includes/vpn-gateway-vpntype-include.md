---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175917"
---
* **PolicyBased:** PolicyBased VPN's werden voorheen statische routeringsgateways genoemd in het klassieke implementatiemodel. Op beleid gebaseerde VPN's versleutelen en sturen pakketten door IPsec-tunnels op basis van het IPsec-beleid dat is geconfigureerd met de combinaties van adresvoorvoegsels tussen uw on-premises netwerk en het Azure VNet. Het beleid (of de verkeersselector) wordt gewoonlijk gedefinieerd als een toegangslijst in de configuratie van het VPN-apparaat. De waarde voor een PolicyBased VPN-type is *PolicyBased*. Houd bij het gebruik van een PolicyBased VPN rekening met de volgende beperkingen:
  
  * PolicyBased VPN's kunnen **alleen** worden gebruikt op de Basic gateway SKU. Dit VPN-type is niet compatibel met andere Gateway SKU's.
  * U slechts 1 tunnel hebben wanneer u een PolicyBased VPN gebruikt.
  * U PolicyBased VPN's alleen gebruiken voor S2S-verbindingen en alleen voor bepaalde configuraties. Voor de meeste VPN-gatewayconfiguraties is een RouteBased VPN vereist.
* **RouteBased**: RouteBased VPN's werden voorheen dynamische routeringsgateways genoemd in het klassieke implementatiemodel. RouteBased VPN's gebruiken "routes" in de IP-doorstuur- of routeringstabel om pakketten naar de bijbehorende tunnelinterfaces te leiden. De tunnelinterfaces versleutelen of ontsleutelen de pakketten vervolgens naar en vanuit de tunnels. Het beleid (of verkeerskiezer) voor RouteBased VPN's is geconfigureerd als any-to-any (of wild cards). De waarde voor een RouteBased VPN-type is *RouteBased*.
