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
ms.openlocfilehash: 3d9826e3eae2a60b217df1406d26d83c78fbdefb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "67673315"
---
Volg onderstaande stappen als u in het Resource Manager-implementatiemodel een VNet wilt maken met behulp van Azure Portal. De schermafbeeldingen dienen alleen als voorbeeld. Zorg dat u de waarden vervangt door die van uzelf. Bekijk het [Virtual Network-overzicht](../articles/virtual-network/virtual-networks-overview.md) voor meer informatie over het gebruik van virtuele netwerken.

>[!NOTE]
>U dient eerst met uw on-premises netwerkbeheerder een IP-adresbereik te reserveren dat u specifiek voor dit virtuele netwerk kunt gebruiken (en een P2S-configuratie te maken), voordat dit VNet verbinding met een on-premises locatie kan maken. Als er een dubbel adresbereik bestaat aan beide zijden van de VPN-verbinding, wordt verkeer mogelijk niet zoals verwacht gerouteerd. Als u dit VNet met een ander VNet wilt verbinden, kan de adresruimte niet met het andere VNet overlappen. Plan daarom uw netwerkconfiguratie zorgvuldig.
>
>

1. Navigeer via een browser naar de [Azure Portal](https://portal.azure.com) en log, indien nodig, in met uw Azure-account.
2. Klik op **+** . In het veld **Marketplace doorzoeken** typt u 'virtueel netwerk'. Zoek **Virtual Network** in de lijst met resultaten en klik erop om de pagina **Virtual Network** te openen.

   ![Virtual Network resource pagina zoeken](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/newvnetportal700.png "De resource pagina van het virtuele netwerk zoeken")
3. Ga onder aan de pagina Virtual Network naar de lijst **Selecteer een implementatiemodel**, selecteer de optie **Resource Manager** in de lijst en klik vervolgens op **Maken**.

   ![Resource Manager selecteren](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "Selecteer Resource Manager")
4. Configureer de VNet-instellingen op de pagina **Virtueel netwerk maken**. Wanneer u de velden invult, verandert het rode uitroepteken in een groen vinkje als de tekens die zijn ingevoerd in het veld, geldig zijn. Mogelijk zijn er waarden die automatisch worden ingevuld. Als dat het geval is, vervangt u die waarden door die van uzelf. De pagina **Virtueel netwerk maken** ziet er ongeveer uit zoals in het volgende voorbeeld:

   ![Veld validatie](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Velden valideren")
5. **Naam**: geef de naam op voor het virtuele netwerk.
6. **Adresruimte**: voer de adresruimte. Als er meerdere adresruimten moeten worden toegevoegd, voegt u de eerste adresruimte toe. U kunt later, nadat u het VNet hebt gemaakt, extra adresruimten toevoegen.
7. **Abonnement**: controleer of het weergegeven abonnement het juiste is. U kunt abonnementen wijzigen met behulp van de vervolgkeuzelijst.
8. **Resourcegroep**: selecteer een bestaande resourcegroep of maak een nieuwe door een naam te typen voor uw nieuwe resourcegroep. Geef de resourcegroep een naam die aansluit bij de geplande configuratiewaarden wanneer u een nieuwe groep aanmaakt. Zie [Azure Resource Manager Overview](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (Overzicht van Azure Resource Manager) voor meer informatie over resourcegroepen.
9. **Locatie**: selecteer de locatie voor uw VNet. De locatie bepaalt waar de resources die u naar dit VNet implementeert, worden opgeslagen.
10. **Subnet**: voeg de naam en het adresbereik van het subnet toe. U kunt later, nadat u het VNet hebt gemaakt, extra subnetten toevoegen.
11. Selecteer **Vastmaken aan dashboard** als u uw VNet gemakkelijk wilt terugvinden op het dashboard en klik vervolgens op **Aanmaken**.

    ![Vastmaken aan dashboard](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "vastmaken aan dash board")
12. Nadat u op **Maken** klikt, ziet u een tegel op uw dashboard die de voortgang van uw VNet aangeeft. De tegel wordt gewijzigd wanneer het VNet wordt gemaakt.

    ![Tegel virtueel netwerk maken](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "Tegel Virtueel netwerk aanmaken")
