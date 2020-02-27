---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619760"
---
U hebt door deze stappen te volgen een VNet gemaakt met behulp van het Resource Manager-implementatiemodel en de Azure-portal. Zie [Overzicht van virtuele netwerken](../articles/virtual-network/virtual-networks-overview.md) voor meer informatie over virtuele netwerken.

>[!NOTE]
>Overleg met uw on-premises netwerkbeheerder om een IP-adresbereik te reserveren dat u specifiek voor dit virtuele netwerk kunt gebruiken, voordat dit VNet verbinding met een on-premises locatie kan maken. Als er een dubbel adresbereik bestaat aan beide zijden van de VPN-verbinding, wordt verkeer niet correct gerouteerd. Als u dit VNet met een ander VNet wilt verbinden, kan de adresruimte niet met het andere VNet overlappen. Houd hier rekening mee als u uw netwerkconfiguratie gaan plannen.
>
>

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. In **resources, services en documenten zoeken (G +/)** , type *virtueel netwerk*.

   ![Virtual Network resource pagina zoeken](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "De resource pagina van het virtuele netwerk zoeken")
1. Selecteer **Virtual Network** in de **Marketplace** -resultaten.

   ![Virtueel netwerk selecteren](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "De resource pagina van het virtuele netwerk zoeken")
1. Klik op de pagina **Virtual Network** op **maken**.

   ![pagina virtueel netwerk](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Klik op Maken")
1. Nadat u op maken hebt geklikt, wordt de pagina **virtueel netwerk maken** geopend.

   ![Pagina virtueel netwerk maken](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "De pagina Virtueel netwerk maken")
1. Configureer de VNet-instellingen op de pagina **Virtueel netwerk maken**. Wanneer u de velden invult, verandert het rode uitroepteken in een groen vinkje als de tekens die u in het veld invoert, zijn gevalideerd. Sommige waarden worden automatisch ingevuld. Deze kunt u door uw eigen waarden vervangen:

   - **Naam**: geef de naam op voor het virtuele netwerk.
   - **Adresruimte**: voer de adresruimte. Als er meerdere adresruimten moeten worden toegevoegd, voert u de eerste adresruimte hier toe. U kunt later, nadat u het VNet hebt gemaakt, extra adresruimten toevoegen. Als voor uw configuratie IPv6-adres ruimte is vereist, schakelt u het selectie vakje in om die informatie in te voeren.
   - **Abonnement**: controleer of het weergegeven abonnement het juiste is. U kunt abonnementen wijzigen met behulp van de vervolgkeuzelijst.
   - **Resource groep**: Selecteer een bestaande resource groep of maak een nieuwe door een naam in te voeren voor de nieuwe resource groep. Geef de resourcegroep een naam die aansluit bij de geplande configuratiewaarden als u een nieuwe groep aanmaakt. Zie [Overzicht van Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups) voor meer informatie over resourcegroepen.
   - **Locatie**: selecteer de locatie voor uw VNet. De locatie bepaalt waar de resources die u naar dit VNet implementeert, zich bevinden.
   - **Subnet**: Voeg de **naam** en het **adres bereik**van het subnet toe. U kunt later, nadat u het VNet hebt gemaakt, extra subnetten toevoegen.
   - **DDoS-beveiliging**: Selecteer **basis**, tenzij u de standaard service wilt gebruiken.
   - **Service-eind punten**: u kunt deze instelling **uitgeschakeld**laten, tenzij u met uw configuratie deze instelling opgeeft.
   - **Firewall**: u kunt deze instelling **uitgeschakeld**laten, tenzij u met uw configuratie deze instelling opgeeft.
1. Klik op **maken** om de implementatie van het virtuele netwerk te starten.
