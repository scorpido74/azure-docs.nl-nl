---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78301935"
---
U hebt door deze stappen te volgen een VNet gemaakt met behulp van het Resource Manager-implementatiemodel en de Azure-portal. Zie [Overzicht van virtuele netwerken](../articles/virtual-network/virtual-networks-overview.md) voor meer informatie over virtuele netwerken.

>[!NOTE]
>Wanneer u een virtueel netwerk als onderdeel van een cross-premises architectuur gebruikt, moet u ervoor zorgen dat u met uw on-premises netwerk beheerder een IP-adres bereik reserveren dat u specifiek voor dit virtuele netwerk kunt gebruiken. Als er een dubbel adresbereik bestaat aan beide zijden van de VPN-verbinding, wordt verkeer niet correct gerouteerd. Bovendien, als u dit virtuele netwerk wilt verbinden met een ander virtueel netwerk, kan de adres ruimte niet overlappen met het andere virtuele netwerk. Houd hier rekening mee als u uw netwerkconfiguratie gaan plannen.
>
>

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. In **resources, services en documenten zoeken (G +/)**, type *virtueel netwerk*.

   ![Virtual Network resource pagina zoeken](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "De resource pagina van het virtuele netwerk zoeken")
1. Selecteer **Virtual Network** in de **Marketplace** -resultaten.

   ![Virtueel netwerk selecteren](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "De resource pagina van het virtuele netwerk zoeken")
1. Selecteer op de pagina **Virtual Network** **maken**.

   ![pagina virtueel netwerk](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Selecteer Maken")
1. Zodra u **maken**selecteert, wordt de pagina **virtueel netwerk maken** geopend.
1. Configureer op het tabblad **basis beginselen** de instellingen **Project Details** en **exemplaar Details** VNet.

   ![Tabblad basis beginselen](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Tabblad Basisinformatie") Wanneer u de velden invult, ziet u een groen vinkje wanneer de tekens die u in het veld invoert, worden gevalideerd. Sommige waarden worden automatisch ingevuld. Deze kunt u door uw eigen waarden vervangen:

   - **Abonnement**: Controleer of het weer gegeven abonnement het juiste is. U kunt abonnementen wijzigen met behulp van de vervolgkeuzelijst.
   - **Resource groep**: Selecteer een bestaande resource groep of klik op **Nieuw maken** om een nieuwe te maken. Zie [Azure Resource Manager Overview](../articles/azure-resource-manager/management/overview.md#resource-groups)voor meer informatie over resource groepen.
   - **Naam**: Voer de naam in voor het virtuele netwerk.
   - **Regio**: Selecteer de locatie voor uw VNet. De locatie bepaalt waar de resources die u naar dit VNet implementeert, zich bevinden.

1. Configureer de waarden op het tabblad **IP-adressen** . De waarden die in de onderstaande voor beelden worden weer gegeven, zijn voor demonstratie doeleinden. U kunt deze waarden aanpassen op basis van de instellingen die u nodig hebt.

   ![Tabblad IP-adressen](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Tabblad IP-adressen")  
   - **IPv4-adres ruimte**: standaard wordt er automatisch een adres ruimte gemaakt. U kunt op de adres ruimte klikken om deze aan te passen aan uw eigen waarden. U kunt ook extra adres ruimten toevoegen.
   - **IPv6**: als voor uw configuratie IPv6-adres ruimte is vereist, schakelt u het selectie vakje **IPv6-adres ruimte toevoegen** in om die informatie in te voeren.
   - **Subnet**: als u de standaard adres ruimte gebruikt, wordt automatisch een standaard subnet gemaakt. Als u de adres ruimte wijzigt, moet u een subnet toevoegen. Selecteer **+ subnet toevoegen** om het venster **subnet toevoegen** te openen. Configureer de volgende instellingen en selecteer **toevoegen** om de waarden toe te voegen:
      - **Subnetnaam**: in dit voor beeld noemen we het subnet ' front-end '.
      - **Adres bereik van subnet**: het adres bereik voor dit subnet.

1. Wijzig op het tabblad **beveiliging** op dit moment de standaard waarden:

   - **DDoS-beveiliging**: Basic
   - **Firewall**: uitgeschakeld
1. Selecteer **controleren + maken** om de instellingen van het virtuele netwerk te valideren.
1. Nadat de instellingen zijn gevalideerd, selecteert u **maken**.
