---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "68780271"
---
Volg onderstaande stappen als u in het Resource Manager-implementatiemodel een VNet wilt maken met behulp van Azure Portal. Gebruik de **voorbeeld waarden** als u deze stappen als zelf studie gebruikt. Als u deze stappen niet uitvoert als onderdeel van een zelfstudie, vervangt u de waarden door die van uzelf. Bekijk het [Virtual Network-overzicht](../articles/virtual-network/virtual-networks-overview.md) voor meer informatie over het gebruik van virtuele netwerken.

>[!NOTE]
>U dient eerst met uw on-premises netwerkbeheerder een IP-adresbereik te reserveren dat u specifiek voor dit virtuele netwerk kunt gebruiken, voordat dit VNet verbinding met een on-premises locatie kan maken. Als er een dubbel adresbereik bestaat aan beide zijden van de VPN-verbinding, wordt verkeer mogelijk niet zoals verwacht gerouteerd. Als u dit VNet met een ander VNet wilt verbinden, kan de adresruimte niet met het andere VNet overlappen. Plan daarom uw netwerkconfiguratie zorgvuldig.
>

1. Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.
2. Klik op **Een resource maken**. In het veld **Marketplace doorzoeken** typt u 'virtueel netwerk'. Zoek **Virtual Network** in de lijst met resultaten en klik erop om de pagina **Virtual Network** te openen.
3. Klik op **Maken**. Hiermee opent u de pagina **virtueel netwerk maken** .
4. Configureer de VNet-instellingen op de pagina **Virtueel netwerk maken**. Wanneer u de velden invult, verandert het rode uitroepteken in een groen vinkje als de tekens die zijn ingevoerd in het veld, geldig zijn. Gebruik de volgende waarden:

   - **Naam**: VNet1
   - **Adres ruimte**: 10.1.0.0/16
   - **Abonnement**: Controleer of het abonnement dat u wilt gebruiken, is opgegeven. U kunt abonnementen wijzigen met behulp van de vervolgkeuzelijst.
   - **Resource groep**: TestRG1 (Klik op **Nieuw maken** om een nieuwe groep te maken)
   - **Locatie**: VS-Oost
   - **Subnet**: front-end
   - **Adres bereik**: 10.1.0.0/24

   ![De pagina Virtueel netwerk maken](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. DDoS als Basic, service-eind punten als uitgeschakeld en firewall als uitgeschakeld laten.
6. Klik op **Maken** om het VNet te maken.