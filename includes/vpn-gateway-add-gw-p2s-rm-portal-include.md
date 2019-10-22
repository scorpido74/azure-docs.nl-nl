---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ce9fa29a29559a1eaaff6173737159f11aa83d8
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71268897"
---
1. Klik in de portal aan de linkerkant op **Een resource maken** en typ in het zoekvak: 'virtuele netwerkgateway'. Klik op het zoekresultaat **Gateway van het virtuele netwerk**. Klik op de pagina **virtuele netwerk gateway** op **maken**. Hiermee opent u de pagina **Gateway van het virtuele netwerk maken**.

   ![Pagina velden van een virtuele netwerk gateway maken](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Pagina velden van een virtuele netwerk gateway maken")

   ![Pagina velden van een virtuele netwerk gateway maken](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Pagina velden van een virtuele netwerk gateway maken")
2. Vul op de pagina **Virtuele netwerkgateway maken** de waarden in voor uw virtuele netwerkgateway.

   **Project Details**

   - **Abonnement**: Selecteer het abonnement dat u wilt gebruiken in de vervolg keuzelijst.
   - **Resource groep**: deze instelling wordt ingevuld wanneer u het virtuele netwerk op deze pagina selecteert.

   **Exemplaar Details**

   - **Naam**: naam van uw gateway. De naam van uw gateway is niet hetzelfde als de naam van een gateway-subnet. Het is de naam van het gateway-object dat u maakt.
   - **Regio**: Selecteer de regio waarin u deze resource wilt maken. De regio voor de gateway moet hetzelfde zijn als het virtuele netwerk.
   - **Gatewaytype**: selecteer **VPN**. VPN-gateways maken gebruik van een gateway van het virtuele netwerk van het type **VPN**. 
   - **VPN-type**selecteer het VPN-type dat wordt opgegeven voor uw configuratie. De meeste configuraties vereisen een op route gebaseerd VPN-type.
   - **SKU**: selecteer de gateway-SKU in de vervolgkeuzelijst. Welke SKU's worden weergegeven in de vervolgkeuzelijst, is afhankelijk van het VPN-type dat u selecteert. Zie [Gateway-SKU's](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor informatie over gateway-SKU's.

      **Virtueel netwerk**: kies het virtuele netwerk waaraan u deze gateway wilt toevoegen.

      **Adres bereik gateway-subnet**: dit veld wordt alleen weer gegeven als het virtuele netwerk dat u hebt geselecteerd, geen gateway-subnet heeft. Vul het bereik in als u nog geen gateway-subnet hebt. Maak indien mogelijk het bereik/27 of groter (/26,/25 enz.)

   **Openbaar IP-adres**: deze instelling geeft het object van het openbare IP-adres aan dat wordt gekoppeld aan de VPN-gateway. Het openbare IP-adres wordt dynamisch toegewezen aan dit object wanneer de VPN-gateway wordt gemaakt. Het openbare IP-adres verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet wanneer de grootte van uw VPN Gateway verandert, wanneer deze gateway opnieuw wordt ingesteld of wanneer andere interne onderhoudswerkzaamheden of upgrades worden uitgevoerd.

     - **Openbaar IP-adres**: verlof **nieuwe maken** geselecteerd.
     - **Naam van openbaar IP-adres**: Typ in het tekstvak een naam voor het exemplaar van het open bare IP-adres.
     - **Toewijzing**: VPN-gateway ondersteunt alleen dynamische.

   **Modus actief-actief**: Selecteer alleen actief **-actief modus inschakelen** als u een configuratie voor Active-Active gateway maakt. Anders selecteert u deze instelling niet.

   Selecteer **ASN van BGP configureren** niet, tenzij deze instelling voor uw configuratie specifiek vereist is. Als u deze instelling wel nodig hebt, is de ASN standaard 65515. U kunt deze waarde wijzigen.

3. Klik op **beoordeling + maken** om validatie uit te voeren. Zodra de validatie is geslaagd, klikt u op **maken** om de VPN-gateway te implementeren. Het kan Maxi maal 45 minuten duren voordat een gateway volledig is gemaakt en geïmplementeerd. U kunt de implementatie status zien op de overzichts pagina voor uw gateway.

Nadat de gateway is aangemaakt, kunt u het IP-adres dat eraan is toegewezen bekijken door naar het virtuele netwerk in de portal te kijken. De gateway wordt weergegeven als verbonden apparaat.