---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc94f31887526f387413f78fe3270784a4e3bd88
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525152"
---
1. Selecteer **VPN-sites** in het onderdeel **Connectiviteit** op de portaalpagina voor uw virtuele WAN om de pagina voor VPN-sites te openen.
2. Op de pagina **VPN sites** klikt u op **+Site maken**.

   ![Basisinstellingen](./media/virtual-wan-tutorial-site-include/basics.png "Basisbeginselen")
3. Op het tabblad **Basisinstellingen** van de pagina **VPN-sites maken** vult u de volgende velden in:

    * **Regio** - voorheen locatie genoemd. Dit is de locatie waar u deze websiteresource in wilt maken.
    * **Naam** - de naam waarmee u naar uw on-premises site wilt verwijzen.
    * **Leverancier van het apparaat** - de naam van de leverancier van het VPN-apparaat (bijvoorbeeld: Citrix, Cisco, Barracuda). Hierdoor krijgt het Azure-team meer inzicht in uw omgeving, zodat het makkelijker wordt om later aanvullende optimalisatiemogelijkheden toe te voegen of u te helpen bij het oplossen van problemen.
    * **Border Gateway Protocol**: als u dit inschakelt, wordt BGP ingeschakeld voor alle verbindingen van de site. Uiteindelijk moet u de BGP-gegevens voor elke koppeling van de VPN-site instellen in de sectie koppelingen. BGP configureren op een virtuele WAN staat gelijk aan BGP configureren op de VPN-gateway van een virtueel Azure-netwerk. Het adres van uw on-premises BGP-peer mag niet gelijk zijn aan het openbare IP-adres van uw VPN-apparaat of het de VNet-adresruimte van de VPN-site. Gebruik een ander IP-adres op het VPN-apparaat voor uw BGP-peer-IP. Dit kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat. Geef dit adres op in de bijbehorende VPN-site die de locatie vertegenwoordigt. Zie [Over BGP met Azure VPN-gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md) voor BGP-vereisten. U kunt altijd een VPN-verbinding bewerken om de BGP-parameters bij te werken (Peering-IP op de koppeling en het AS-nummer) zodra de BGP-instelling voor de VPN-site is ingeschakeld.
    * **Privié-adresruimte** - de IP-adresruimte op uw on-premises site. Verkeer dat bestemd is voor deze adresruimte wordt doorgestuurd naar uw lokale site. Dit is vereist wanneer BGP niet is ingeschakeld voor de site.
    * **Hubs**: de hub waarmee u de site wilt verbinden. Een site kan alleen worden verbonden met hubs die een VPN-gateway hebben. Als u een hub niet ziet, maak dan eerst een VPN-gateway in die hub.
4. Selecteer **koppelingen** om informatie over de fysieke koppelingen op de vertakking toe te voegen. Als u een CPE-apparaat van een Virtual WAN-partner heeft, vraag dan bij hen na of deze informatie wordt uitgewisseld met Azure als onderdeel van de upload van informatie over de vertakking die is ingesteld in hun systemen.

   ![koppelingen](./media/virtual-wan-tutorial-site-include/links.png "Koppelingen")

    * **Koppelingsnaam**: een naam die u wilt opgeven voor de fysieke koppeling op de VPN-site. Voorbeeld: mylink1.
    * **Providernaam**: de naam van de fysieke koppeling op de VPN-site. Voorbeeld: ATT, Verizon.
    * **Snelheid** - Dit is de snelheid van het VPN-apparaat in de filiaallocatie. Voorbeeld: 50, wat betekent dat 50 Mbps de snelheid is van het VPN-apparaat op de vertakkingssite.
    * **IP-adres/FQDN** - Openbaar IP-adres van het on-premises apparaat dat deze koppeling gebruikt. U kunt het privé-IP-adrs van uw on-premises VPN-apparaat dat achter ExpressRoute zit opgeven. U kunt ook een fully qualified somain name opgeven. Bijvoorbeeld *something.contoso.com*. De FQDN moet omzetbaar zijn vanuit de VPN-gateway. Dit is mogelijk als de DNS-server waarop deze FQDN gehost wordt bereikbaar is via internet. IP-adres heeft voorrang wanneer zowel het IP-adres als de FQDN zijn opgegeven.

      >[!NOTE]
      >* Ondersteunt één IPv4-adres per FQDN. Als de FQDN wordt omgezet in meerdere IP-adressen, dan kiest de VPN-gateway het eerste IP4-adres van de lijst. Momenteel worden IPv6-adressen niet ondersteund.
      >* VPN-gateway onderhoudt een DNS-cache die om de vijf minuten wordt vernieuwd. De gateway probeert enkel FQDN's op te lossen voor niet-verbonden tunnels. Een gateway opnieuw instellen of de configuratie ervan wijzigen kan ook FQDN-omzetting activeren.
      >
5. U kunt het selectievakje gebruiken om extra koppelingen te verwijderen of toe te voegen. Er worden vier koppelingen per VPN-site ondersteund. Als u bijvoorbeeld vier ISP's (internet service providers) heeft op de vertakkingslocatie, dan kunt u vier koppelingen maken. één per ISP, en geeft de informatie voor elke koppeling op.
6. Wanneer u klaar bent met het invullen van de velden, selecteert u **Beoordelen + maken** om de site te controleren en te maken.
7. Bekijk de status op de pagina met de VPN-sites. De site gaat naar **Verbinding nodig**, omdat deze nog niet is verbonden met de hub.
