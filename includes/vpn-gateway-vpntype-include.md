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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175917"
---
* **PolicyBased:** PolicyBased-Vpn's werden voorheen statische routerings gateways genoemd in het klassieke implementatie model. Op beleid gebaseerde Vpn's coderen en direct pakketten via IPsec-tunnels op basis van het IPsec-beleid dat is geconfigureerd met de combi Naties van adres voorvoegsels tussen uw on-premises netwerk en Azure VNet. Het beleid (of de verkeersselector) wordt gewoonlijk gedefinieerd als een toegangslijst in de configuratie van het VPN-apparaat. De waarde voor een PolicyBased VPN-type is *PolicyBased*. Houd bij het gebruik van een PolicyBased VPN de volgende beperkingen in acht:
  
  * PolicyBased-Vpn's kunnen **alleen** worden gebruikt op de Basic gateway-SKU. Dit VPN-type is niet compatibel met andere gateway-Sku's.
  * U kunt slechts één tunnel gebruiken wanneer u een PolicyBased-VPN-verbinding gebruikt.
  * U kunt PolicyBased Vpn's alleen gebruiken voor S2S-verbindingen en alleen voor bepaalde configuraties. Voor de meeste VPN Gateway configuraties is een RouteBased-VPN vereist.
* **RouteBased**: RouteBased-vpn's werden voorheen dynamische routerings gateways genoemd in het klassieke implementatie model. RouteBased-Vpn's gebruiken ' routes ' in de door sturing of routerings tabel om pakketten naar de bijbehorende tunnel interfaces te sturen. De tunnelinterfaces versleutelen of ontsleutelen de pakketten vervolgens naar en vanuit de tunnels. Het beleid (of de optie voor het selecteren van verkeer) voor RouteBased-Vpn's is geconfigureerd als een-op-een-of-Joker (of wild). De waarde voor een RouteBased VPN-type is *RouteBased*.
