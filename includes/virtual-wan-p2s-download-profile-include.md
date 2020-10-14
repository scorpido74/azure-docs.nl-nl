---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812681"
---
1. Selecteer op de pagina voor uw virtuele WAN op **Configuraties gebruikers-VPN**.
1. Selecteer bovenaan de pagina **Configuratie gebruikers-VPN downloaden**. Door een WAN-configuratie te downloaden, wordt er een ingebouwd VPN-gebruikersprofiel op basis van Traffic Manager gemaakt. Zie [Hubprofielen](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile) voor meer informatie over globale of op hub gebaseerde profielen. Failover-scenario's worden vereenvoudigd met wereldwijde profielen.

   Als om enige reden een hub niet beschikbaar is, zal het ingebouwde verkeerbeheer dat wordt geboden door de service de verbinding naar Azure-resources voor punt-naar-site-gebruikers garanderen via een andere hub. U kunt altijd een VPN-configuratie specifiek voor een hub downloaden door naar de hub te gaan. Download onder **Gebruikers-VPN (punt-naar-site)** het **Gebruikers-VPN**-profiel van de virtuele hub.
1. Wanneer het bestand gereed is, selecteert u de koppeling om het te downloaden.
1. Gebruik het profielbestand om de VPN-clients te configureren.
