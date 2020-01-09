---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02297a45c9560f867e97d7024862ea0de5d3f7b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469773"
---
U hebt door deze stappen te volgen een VNet gemaakt met behulp van het Resource Manager-implementatiemodel en de Azure-portal. Zie [Overzicht van virtuele netwerken](../articles/virtual-network/virtual-networks-overview.md) voor meer informatie over virtuele netwerken.

>[!NOTE]
>Overleg met uw on-premises netwerkbeheerder om een IP-adresbereik te reserveren dat u specifiek voor dit virtuele netwerk kunt gebruiken, voordat dit VNet verbinding met een on-premises locatie kan maken. Als er een dubbel adresbereik bestaat aan beide zijden van de VPN-verbinding, wordt verkeer niet correct gerouteerd. Als u dit VNet met een ander VNet wilt verbinden, kan de adresruimte niet met het andere VNet overlappen. Houd hier rekening mee als u uw netwerkconfiguratie gaan plannen.
>
>

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).  Klik in het menu Azure Portal of op de **Start** pagina en selecteer **een resource maken**. De pagina **Nieuw** wordt geopend.

2. In **de Marketplace zoeken**voert u het *virtuele netwerk* in en selecteert u **Virtual Network** in de resultaten.

   ![Virtual Network resource pagina zoeken](./media/vpn-gateway-basic-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "De resource pagina van het virtuele netwerk zoeken")

   De pagina **Virtueel netwerk** wordt geopend.

3. Selecteer in de lijst **Select a deployment model** onder aan de pagina de optie **Resource Manager** en selecteer vervolgens **Maken**. De pagina **Virtueel netwerk maken** wordt geopend.

   ![Pagina virtueel netwerk maken](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "De pagina Virtueel netwerk maken")

4. Configureer de VNet-instellingen op de pagina **Virtueel netwerk maken**. Wanneer u de velden invult, verandert het rode uitroepteken in een groen vinkje als de tekens die u in het veld invoert, zijn gevalideerd. Sommige waarden worden automatisch ingevuld. Deze kunt u door uw eigen waarden vervangen:

   - **Naam**: geef de naam op voor het virtuele netwerk.

   - **Adresruimte**: voer de adresruimte. Als er meerdere adresruimten moeten worden toegevoegd, voert u de eerste adresruimte hier toe. U kunt later, nadat u het VNet hebt gemaakt, extra adresruimten toevoegen.

   - **Abonnement**: controleer of het weergegeven abonnement het juiste is. U kunt abonnementen wijzigen met behulp van de vervolgkeuzelijst.

   - **Resource groep**: Selecteer een bestaande resource groep of maak een nieuwe door een naam in te voeren voor de nieuwe resource groep. Geef de resourcegroep een naam die aansluit bij de geplande configuratiewaarden als u een nieuwe groep aanmaakt. Zie [Overzicht van Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups) voor meer informatie over resourcegroepen.

   - **Locatie**: selecteer de locatie voor uw VNet. De locatie bepaalt waar de resources die u naar dit VNet implementeert, zich bevinden.

   - **Subnet**: Voeg de **naam** en het **adres bereik**van het subnet toe. U kunt later, nadat u het VNet hebt gemaakt, extra subnetten toevoegen. 
     
5. Selecteer **Maken**.
