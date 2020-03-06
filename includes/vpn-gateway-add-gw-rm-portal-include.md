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
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331239"
---
1. Selecteer in het menu [Azure Portal](https://portal.azure.com) de optie **een resource maken**. 

   ![Een resource maken in de Azure Portal](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. In het veld **Marketplace doorzoeken** typt u ' Virtual Network gateway '. Zoek naar de gateway van het **virtuele netwerk** in het Zoek resultaat en selecteer de vermelding. Selecteer op de pagina **virtuele netwerk gateway** **maken**. Hiermee opent u de pagina **Gateway van het virtuele netwerk maken**.
3. Vul op het tabblad **basis beginselen** de waarden in voor de gateway van uw virtuele netwerk.

   ![Pagina velden van een virtuele netwerk gateway maken](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Pagina velden van een virtuele netwerk gateway maken")

   ![Pagina velden van een virtuele netwerk gateway maken](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Pagina velden van een virtuele netwerk gateway maken")

   **Project Details**

   - **Abonnement**: Selecteer het abonnement dat u wilt gebruiken in de vervolg keuzelijst.
   - **Resource groep**: deze instelling wordt ingevuld wanneer u het virtuele netwerk op deze pagina selecteert.

   **Exemplaar Details**

   - **Naam**: naam van uw gateway. De naam van uw gateway is niet hetzelfde als de naam van een gateway-subnet. Het is de naam van het gateway-object dat u maakt.
   - **Regio**: Selecteer de regio waarin u deze resource wilt maken. De regio voor de gateway moet hetzelfde zijn als het virtuele netwerk.
   - **Gatewaytype**: selecteer **VPN**. VPN-gateways maken gebruik van een gateway van het virtuele netwerk van het type **VPN**.
   - **VPN-type**selecteer het VPN-type dat wordt opgegeven voor uw configuratie. De meeste configuraties vereisen een op route gebaseerd VPN-type.
   - **SKU**: selecteer de gateway-SKU in de vervolgkeuzelijst. Welke SKU's worden weergegeven in de vervolgkeuzelijst, is afhankelijk van het VPN-type dat u selecteert. Zie [Gateway-SKU's](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor informatie over gateway-SKU's.
   - **Genereren**: Zie [Gateway-sku's](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)voor meer informatie over het genereren van VPN gateway.
   - **Virtueel netwerk**: Selecteer in de vervolg keuzelijst het virtuele netwerk waaraan u deze gateway wilt toevoegen.
   - **Adres bereik van Gateway-subnet**: dit veld wordt alleen weer gegeven als uw VNet geen gateway-subnet heeft. Maak, indien mogelijk, het bereik/27 of groter (/26,/25 enz.). Het maken van een bereik dat kleiner is dan/28 wordt niet aanbevolen. Als u al een gateway-subnet hebt, kunt u GatewaySubnet-details weer geven door te navigeren naar uw virtuele netwerk. Klik op **subnetten** om het bereik weer te geven. Als u het bereik wilt wijzigen, kunt u de GatewaySubnet verwijderen en opnieuw maken.

   **Openbaar IP-adres**: deze instelling geeft het object van het openbare IP-adres aan dat wordt gekoppeld aan de VPN-gateway. Het openbare IP-adres wordt dynamisch toegewezen aan dit object wanneer de VPN-gateway wordt gemaakt. Het openbare IP-adres verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet wanneer de grootte van uw VPN Gateway verandert, wanneer deze gateway opnieuw wordt ingesteld of wanneer andere interne onderhoudswerkzaamheden of upgrades worden uitgevoerd.

     - **Openbaar IP-adres**: verlof **nieuwe maken** geselecteerd.
     - **Naam van openbaar IP-adres**: Typ in het tekstvak een naam voor het exemplaar van het open bare IP-adres.
     - **Toewijzing**: VPN-gateway ondersteunt alleen dynamische.

   **Modus actief-actief**: Selecteer alleen actief **-actief modus inschakelen** als u een configuratie voor Active-Active gateway maakt. Anders selecteert u deze instelling niet.

   Selecteer **ASN van BGP configureren** niet, tenzij deze instelling voor uw configuratie specifiek vereist is. Als u deze instelling wel nodig hebt, is de ASN standaard 65515. U kunt deze waarde wijzigen.
4. Selecteer **controleren + maken** om validatie uit te voeren. Wanneer de validatie is geslaagd, selecteert u **maken** om de VPN-gateway te implementeren. Het kan Maxi maal 45 minuten duren voordat een gateway volledig is gemaakt en geïmplementeerd. U kunt de implementatie status zien op de overzichts pagina voor uw gateway.

Nadat de gateway is aangemaakt, kunt u het IP-adres dat eraan is toegewezen bekijken door naar het virtuele netwerk in de portal te kijken. De gateway wordt weergegeven als verbonden apparaat.
