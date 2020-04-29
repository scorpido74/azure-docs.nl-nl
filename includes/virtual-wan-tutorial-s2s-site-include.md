---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73488887"
---
1. Selecteer op de pagina Portal voor uw virtuele WAN in het gedeelte **connectiviteit** de optie **VPN-sites** om de pagina VPN-sites te openen.
2. Op de pagina **VPN sites** klikt u op **+Site maken**.

   ![Basisbeginselen](./media/virtual-wan-tutorial-site-include/basics.png "Basisbeginselen")
3. Vul op de pagina **VPN-site maken** op het tabblad **basis beginselen** de volgende velden in:

    * **Regio** -eerder aangeduid als locatie. Dit is de locatie waar u deze site resource wilt maken in.
    * **Naam** : de naam waarmee u naar uw on-premises site wilt verwijzen.
    * **Leverancier van apparaat** : de naam van de leverancier van het VPN-apparaat (bijvoorbeeld: Citrix, Cisco, barracuda). Als u dit doet, kan het Azure-team beter inzicht krijgen in uw omgeving om in de toekomst extra optimalisatie mogelijkheden toe te voegen of om u te helpen bij het oplossen van problemen.
    * **Border Gateway Protocol** : inschakelen impliceert alle verbindingen van de site BGP ingeschakeld. Uiteindelijk moet u de BGP-gegevens voor elke koppeling van de VPN-site instellen in de sectie koppelingen. Het configureren van BGP op een virtueel WAN is gelijk aan het configureren van BGP op een virtuele Azure-netwerk gateway VPN. Het adres van uw on-premises BGP-peer mag niet hetzelfde zijn als het open bare IP-adres van uw VPN naar het apparaat of de VNet-adres ruimte van de VPN-site. Gebruik een ander IP-adres op het VPN-apparaat voor uw BGP-peer-IP. Het kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat. Het kan echter geen APIPA-adres (169.254. x. x) zijn. Geef dit adres op in de bijbehorende VPN-site die de locatie vertegenwoordigt. Zie [over BGP met Azure VPN gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)voor de BGP-vereisten. U kunt altijd een VPN-verbinding bewerken om de BGP-para meters (peering-IP op de koppeling en de als #) bij te werken zodra de BGP-instelling VPN-site is ingeschakeld.
    * **Persoonlijke adres ruimte** : de IP-adres ruimte die zich op uw on-premises site bevindt. Verkeer dat bestemd is voor deze adresruimte wordt doorgestuurd naar uw lokale site. Dit is vereist wanneer BGP niet is ingeschakeld voor de site.
    * **Hubs** : de hub waarmee u de site wilt verbinden. Een site kan alleen worden verbonden met hubs die een VPN Gateway hebben. Als u geen hub ziet, maakt u eerst een VPN-gateway in die hub.
4. Selecteer **koppelingen** om informatie over de fysieke koppelingen op de vertakking toe te voegen. Als u een virtueel WAN-partner CPE-apparaat hebt, kunt u contact opnemen met de apparaten om te zien of deze gegevens worden uitgewisseld met Azure als onderdeel van de gegevens die vanuit hun systemen worden geüpload.

   ![koppelen](./media/virtual-wan-tutorial-site-include/links.png "Koppelingen")

    * **Naam van koppeling** : een naam die u wilt opgeven voor de fysieke koppeling op de VPN-site. Voor beeld: mylink1.
    * **Provider naam** : de naam van de fysieke koppeling op de VPN-site. Voor beeld: ATT, Verizon.
    * **Snelheid** : dit is de snelheid van het VPN-apparaat op de vertakkings locatie. Voor beeld: 50, wat betekent dat 50 Mbps de snelheid van het VPN-apparaat op de filiaal site is.
    * **IP-adres** : het open bare IP-adres van het on-premises apparaat met behulp van deze koppeling. U kunt desgewenst het privé-IP-adres van uw on-premises VPN-apparaat opgeven dat zich achter ExpressRoute bevindt.
5. U kunt het selectie vakje gebruiken om extra koppelingen te verwijderen of toe te voegen. Vier koppelingen per VPN-site worden ondersteund. Als u bijvoorbeeld vier ISP'S (Internet provider) op de vertakkings locatie hebt, kunt u vier koppelingen maken. Eén per provider en geef de gegevens voor elke koppeling op.
6. Wanneer u klaar bent met het invullen van de velden, selecteert u **controleren + maken** om de site te controleren en te maken.
7. Bekijk de status op de pagina VPN-sites. De site gaat naar de **verbinding die nodig** is, omdat de site nog niet is verbonden met de hub.