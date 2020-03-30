---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331239"
---
1. Selecteer in het menu [azure portal](https://portal.azure.com) de optie Een **resource maken**. 

   ![Een resource maken in de Azure-portal](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. Typ **'Virtuele** netwerkgateway' in het veld Zoeken in de marketplace. Zoek **virtuele netwerkgateway** in de zoekretour en selecteer het item. Selecteer op de pagina **Virtuele netwerkgateway** de optie **Maken**. Hiermee opent u de pagina **Gateway van het virtuele netwerk maken**.
3. Vul op het tabblad **Basisbeginselen** de waarden in voor uw virtuele netwerkgateway.

   ![Paginavelden voor virtuele netwerkgateways maken](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Paginavelden voor virtuele netwerkgateways maken")

   ![Paginavelden voor virtuele netwerkgateways maken](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Paginavelden voor virtuele netwerkgateways maken")

   **Projectdetails**

   - **Abonnement**: Selecteer het abonnement dat u wilt gebruiken in de vervolgkeuzelijst.
   - **Resourcegroep:** deze instelling wordt automatisch ingevuld wanneer u uw virtuele netwerk op deze pagina selecteert.

   **Instantiedetails**

   - **Naam**: naam van uw gateway. Het benoemen van uw gateway is niet hetzelfde als het benoemen van een gatewaysubnet. Het is de naam van het gateway-object dat u maakt.
   - **Regio**: Selecteer het gebied waarin u deze resource wilt maken. De regio voor de gateway moet hetzelfde zijn als het virtuele netwerk.
   - **Gatewaytype**: selecteer **VPN**. VPN-gateways maken gebruik van een gateway van het virtuele netwerk van het type **VPN**.
   - **VPN-type**selecteer het VPN-type dat wordt opgegeven voor uw configuratie. De meeste configuraties vereisen een op route gebaseerd VPN-type.
   - **SKU**: selecteer de gateway-SKU in de vervolgkeuzelijst. Welke SKU's worden weergegeven in de vervolgkeuzelijst, is afhankelijk van het VPN-type dat u selecteert. Zie [Gateway-SKU's](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor informatie over gateway-SKU's.
   - **Generatie:** Zie [Gateway SKU's](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)voor informatie over VPN Gateway Generation.
   - **Virtueel netwerk:** Selecteer in de vervolgkeuzelijst het virtuele netwerk waaraan u deze gateway wilt toevoegen.
   - **Subnetadresbereik gateway:** dit veld wordt alleen weergegeven als uw VNet geen gateway-subnet heeft. Maak indien mogelijk het bereik /27 of groter (/26,/25 etc.). We raden u af om een bereik te maken dat kleiner is dan /28. Als u al een gateway-subnet hebt, u GatewaySubnet-gegevens bekijken door naar uw virtuele netwerk te navigeren. Klik **op Subnetten** om het bereik weer te geven. Als u het bereik wilt wijzigen, u het GatewaySubnet verwijderen en opnieuw maken.

   **Openbaar IP-adres**: deze instelling geeft het object van het openbare IP-adres aan dat wordt gekoppeld aan de VPN-gateway. Het openbare IP-adres wordt dynamisch toegewezen aan dit object wanneer de VPN-gateway wordt gemaakt. Het openbare IP-adres verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet wanneer de grootte van uw VPN Gateway verandert, wanneer deze gateway opnieuw wordt ingesteld of wanneer andere interne onderhoudswerkzaamheden of upgrades worden uitgevoerd.

     - **Openbaar IP-adres**: Laat **Nieuw maken** geselecteerd.
     - **Naam openbaar IP-adres**: Typ in het tekstvak een naam voor uw ip-adresexemplaar.
     - **Toewijzing:** VPN-gateway ondersteunt alleen Dynamic.

   **Active-Active mode**: Selecteer alleen **Actief-actieve modus inschakelen** als u een actief actieve gatewayconfiguratie maakt. Anders selecteert u deze instelling niet.

   Selecteer **ASN van BGP configureren** niet, tenzij deze instelling voor uw configuratie specifiek vereist is. Als u deze instelling wel nodig hebt, is de ASN standaard 65515. U kunt deze waarde wijzigen.
4. Selecteer **Controleren + maken** om validatie uit te voeren. Zodra de validatie is doorgegeven, selecteert **u Maken** om de VPN-gateway te implementeren. Het kan tot 45 minuten duren voordat een gateway volledig is gemaakt en geïmplementeerd. U de implementatiestatus zien op de pagina Overzicht voor uw gateway.

Nadat de gateway is aangemaakt, kunt u het IP-adres dat eraan is toegewezen bekijken door naar het virtuele netwerk in de portal te kijken. De gateway wordt weergegeven als verbonden apparaat.
