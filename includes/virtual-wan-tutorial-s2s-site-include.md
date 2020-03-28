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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488887"
---
1. Selecteer op de portalpagina voor uw virtuele wan in de sectie **Connectiviteit** **VPN-sites** om de pagina VPN-sites te openen.
2. Op de pagina **VPN sites** klikt u op **+Site maken**.

   ![Basics](./media/virtual-wan-tutorial-site-include/basics.png "Basisbeginselen")
3. Vul op de pagina **VPN-site maken** op het tabblad **Basisbeginselen** de volgende velden in:

    * **Regio** - Voorheen locatie genoemd. Dit is de locatie waarin u deze sitebron wilt maken.
    * **Naam** - De naam waarmee u wilt verwijzen naar uw on-premises site.
    * **Apparaatleverancier** - De naam van de leverancier van het VPN-apparaat (bijvoorbeeld: Citrix, Cisco, Barracuda). Als u dit doet, kan het Azure-team uw omgeving beter begrijpen om in de toekomst extra optimalisatiemogelijkheden toe te voegen of om u te helpen problemen op te lossen.
    * **Border Gateway Protocol** - Inschakelen impliceert dat alle verbindingen van de site zijn BGP ingeschakeld. Uiteindelijk stelt u de BGP-informatie in voor elke link van de VPN-site in de sectie Links. Het configureren van BGP op een Virtual WAN is gelijk aan het configureren van BGP op een Azure virtual network gateway VPN. Uw on-premises BGP-peeradres mag niet hetzelfde zijn als het openbare IP-adres van uw VPN naar het apparaat of de VNet-adresruimte van de VPN-site. Gebruik een ander IP-adres op het VPN-apparaat voor uw IP-peer-peer. Het kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat. Het kan echter geen APIPA-adres (169.254.x.x) zijn. Geef dit adres op in de bijbehorende VPN-site die de locatie vertegenwoordigt. Zie [Over BGP met Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)voor BGP-vereisten. U altijd een VPN-verbinding bewerken om de BGP-parameters bij te werken (Peering IP op de link en de AS #) zodra de BGP-instelling van de VPN-site is ingeschakeld.
    * **Privéadresruimte** - De IP-adresruimte die zich op uw on-premises locatie bevindt. Verkeer dat bestemd is voor deze adresruimte wordt doorgestuurd naar uw lokale site. Dit is vereist wanneer BGP niet is ingeschakeld voor de site.
    * **Hubs** - De hub waarmee u verbinding wilt maken met uw site. Een site kan alleen worden verbonden met de hubs die een VPN Gateway hebben. Als u geen hub ziet, maakt u eerst een VPN-gateway in die hub.
4. Selecteer **Koppelingen** om informatie toe te voegen over de fysieke koppelingen in de vertakking. Als u een CPE-apparaat voor virtuele wan-partners hebt, neemt u contact met hen op om te zien of deze informatie wordt uitgewisseld met Azure als onderdeel van de uploadvan branch-informatie die is ingesteld vanaf hun systemen.

   ![Verwijzigingen](./media/virtual-wan-tutorial-site-include/links.png "Koppelingen")

    * **Linknaam** - Een naam die u wilt opgeven voor de fysieke link op de VPN-site. Voorbeeld: mylink1.
    * **Providernaam** - De naam van de fysieke link op de VPN-site. Voorbeeld: ATT, Verizon.
    * **Snelheid** - Dit is de snelheid van het VPN-apparaat op de branchlocatie. Voorbeeld: 50, wat betekent dat 50 Mbps de snelheid is van het VPN-apparaat op de branchsite.
    * **IP-adres** - Openbaar IP-adres van het on-prem-apparaat via deze link. Optioneel u het privé-IP-adres opgeven van uw on-premises VPN-apparaat dat zich achter ExpressRoute bevindt.
5. U het selectievakje gebruiken om extra koppelingen te verwijderen of toe te voegen. Vier links per VPN-site worden ondersteund. Als u bijvoorbeeld vier ISP (internetserviceprovider) op de vestigingslocatie hebt, u vier koppelingen maken. één per isp, en de informatie voor elke link.
6. Zodra u klaar bent met het invullen van de velden, selecteert u **Controleren + maken** om de site te verifiëren en te maken.
7. Bekijk de status op de pagina VPN-sites. De site gaat naar **Verbinding nodig** omdat de site nog niet is aangesloten op de hub.