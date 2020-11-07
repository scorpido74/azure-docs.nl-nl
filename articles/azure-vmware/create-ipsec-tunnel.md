---
title: Een IPSec-tunnel maken in een Azure VMware-oplossing
description: Meer informatie over het maken van een virtuele WAN-hub voor het tot stand brengen van een IPSec-tunnel in azure VMware-oplossingen.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 63318b9fdd0de5e0ce102fafe332f40f595f38f1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357841"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Een IPSec-tunnel maken in een Azure VMware-oplossing

In dit artikel gaan we de stappen door lopen om een VPN-verbinding (IPsec IKEv1 en IKEv2) tot stand te brengen die in de Microsoft Azure virtuele WAN-hub wordt beëindigd. We maken een virtuele WAN-hub met Azure en een VPN-gateway waaraan een openbaar IP-adres is gekoppeld. Vervolgens gaan we een Azure ExpressRoute-gateway maken en een Azure VMware-oplossings eindpunt opstellen. We gaan ook meer over de details van het inschakelen van een op beleid gebaseerde VPN on-premises installatie. 

## <a name="topology"></a>Topologie

![VPN-site-naar-site-tunnel architectuur.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

De virtuele Azure-hub bevat de Azure VMware Solution ExpressRoute-gateway en de site-naar-site-VPN-gateway. Er wordt verbinding gemaakt met een on-premises VPN-apparaat met een Azure VMware-oplossings eindpunt.

## <a name="before-you-begin"></a>Voordat u begint

Als u de site-naar-site-VPN-tunnel wilt maken, moet u een openbaar IP-adres maken dat wordt beëindigd op een on-premises VPN-apparaat.

## <a name="create-a-virtual-wan-hub"></a>Een virtuele WAN-hub maken

1. Zoek in de Azure Portal op **virtuele wan's**. Selecteer **+Toevoegen**. De WAN-pagina maken wordt geopend.  

2. Voer de vereiste velden in op de WAN-pagina **maken** en selecteer vervolgens **controleren + maken**.
   
   | Veld | Waarde |
   | --- | --- |
   | **Abonnement** | De waarde is vooraf ingevuld met het abonnement dat deel uitmaakt van de resource groep. |
   | **Resourcegroep** | Het virtuele WAN is een globale resource en is niet beperkt tot een bepaalde regio.  |
   | **Resourcegroeplocatie** | Als u de virtuele WAN-hub wilt maken, moet u een locatie voor de resource groep instellen.  |
   | **Naam** |   |
   | **Type** | Selecteer **standaard** , waarmee meer dan alleen het VPN gateway verkeer wordt toegestaan.  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Maak WAN.":::

3. Selecteer in de Azure Portal het virtuele WAN dat u in de vorige stap hebt gemaakt, selecteer **virtuele hub maken** , voer de vereiste velden in en selecteer **volgende: site naar site**. 

   | Veld | Waarde |
   | --- | --- |
   | **Regio** | Het selecteren van een regio is vereist vanuit een beheer perspectief.  |
   | **Naam** |    |
   | **Privé-adres ruimte van hub** | Voer het subnet in met behulp van een `/24` (mini maal).  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Virtuele hub maken.":::

4. Op het tabblad **site-naar-site** definieert u de site-naar-site-gateway door de geaggregeerde door Voer in te stellen op basis van de vervolg keuzelijst met **schaal eenheden** voor de gateway. 

   >[!TIP]
   >Eén schaal eenheid = 500 Mbps. De schaal eenheden bevinden zich in paren voor redundantie, elke ondersteuning van 500 Mbps.
  
5. Op het tabblad **ExpressRoute** maakt u een ExpressRoute-gateway. 

   >[!TIP]
   >Een waarde voor de schaal eenheid is 2 Gbps. 

    Het duurt ongeveer 30 minuten om elke hub te maken. 

## <a name="create-a-vpn-site"></a>Een VPN-site maken 

1. Selecteer in **recente resources** in het Azure Portal het virtuele WAN dat u in de vorige sectie hebt gemaakt.

2. In het **overzicht** van de virtuele hub selecteert u **Connectivity**  >  **VPN-verbinding (site-naar-site)** en selecteert u **nieuwe VPN-site maken**.


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="VPN-site maken.":::  
 
3. Op het tabblad **basis beginselen** voert u de vereiste velden in en selecteert u **volgende: koppelingen**. 

   | Veld | Waarde |
   | --- | --- |
   | **Regio** | De regio die u hebt opgegeven in de vorige sectie.  |
   | **Naam** |  |
   | **Leverancier van apparaat** |  |
   | **Border Gateway Protocol** | Stel deze **optie** in om ervoor te zorgen dat zowel de Azure VMware-oplossing als de on-premises servers hun routes via de tunnel adverteren. Als deze is uitgeschakeld, moeten de subnetten die moeten worden aangekondigd hand matig worden onderhouden. Als er subnetten worden gemist, mislukt de HCX het service-net. Zie  [over BGP met Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie. |
   | **Privé adres ruimte**  | Voer het on-premises CIDR-blok in.  Dit wordt gebruikt voor het routeren van al het verkeer dat is gebonden voor on-premises via de tunnel.  Het CIDR-blok is alleen vereist als u BGP niet inschakelt. |
   | **Verbinding maken met** |   |

4. Vul de vereiste velden in op het tabblad **koppelingen** en selecteer vervolgens **controleren + maken**. Als u namen van koppelingen en providers opgeeft, kunt u onderscheid maken tussen een wille keurig aantal gateways dat uiteindelijk kan worden gemaakt als onderdeel van de hub. BGP en autonoom systeem nummer (ASN) moet uniek zijn binnen uw organisatie.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>Beschrijving Een VPN-site definiëren voor op beleid gebaseerde VPN-site-naar-site-tunnels

Deze sectie is alleen van toepassing op op beleid gebaseerde Vpn's. Op beleid gebaseerde VPN-instellingen (of statisch, op route gebaseerd) worden in de meeste gevallen aangestuurd door on-premises VPN-apparaat. Hiervoor moeten on-premises en Azure VMware-oplossings netwerken worden opgegeven. Voor de Azure VMware-oplossing met een virtuele WAN-hub van Azure kunt u *geen* netwerk selecteren. In plaats daarvan moet u alle relevante bereiken voor virtuele WAN-hub op locatie en Azure VMware Solution opgeven. Deze hub-bereiken worden gebruikt voor het opgeven van het versleutelings domein van het beleid basis VPN-tunnel op het lokale eind punt. Voor de oplossings kant van de Azure VMware moet alleen de op beleid gebaseerde Traffic selector-indicator worden ingeschakeld. 

1. Ga in het Azure Portal naar de site van uw virtuele WAN-hub. onder **connectiviteit** selecteert u **VPN (site-naar-site)**.

2. Selecteer de naam van uw VPN-site en vervolgens het weglatings teken (...) helemaal rechts. Selecteer vervolgens **VPN-verbinding naar deze hub bewerken**.
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Bewerk de VPN-verbinding met deze hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Bewerk de verbinding tussen de VPN-site en de hub en selecteer vervolgens **Opslaan**.
   - Selecteer in Internet Protocol Security (IPSec) de optie **aangepast**.
   - Gebruik op beleid gebaseerde verkeers selectie, selecteer **inschakelen**
   - Geef de details op voor **IKE fase 1** en **IKE fase 2 (IPSec)**. 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="VPN-verbinding bewerken"::: 
 
    De selectie vakjes voor verkeer of subnetten die deel uitmaken van het op beleid gebaseerde versleutelings domein moeten zijn:
    
    - Virtuele WAN-hub/24
    - De Azure VMware-oplossing Private Cloud/22
    - Het verbonden virtuele netwerk van Azure (indien aanwezig)

## <a name="connect-your-vpn-site-to-the-hub"></a>Uw VPN-site verbinden met de hub

1. Schakel het selectie vakje in naast de naam van de VPN-site (Zie voor gaande **VPN-site naar site** scherm afbeelding) en selecteer vervolgens **VPN-sites verbinden**. Voer in het veld **vooraf gedeelde sleutel** de sleutel in die u eerder hebt gedefinieerd voor het on-premises eind punt. Als u nog geen eerder gedefinieerde sleutel hebt, kunt u dit veld leeg laten en wordt er automatisch een sleutel voor u gegenereerd. 
 
    Schakel **standaard route door geven** alleen in als u een firewall in de hub implementeert en de volgende hop is voor verbindingen via die tunnel.

    Selecteer **Verbinding maken**. In het scherm verbindings status ziet u de status van het maken van de tunnel.

2. Ga naar het virtuele WAN-overzicht. Open de pagina VPN-site en down load het VPN-configuratie bestand om het toe te passen op het on-premises eind punt.  

3. Nu gaan we de Azure VMware-oplossing ExpressRoute in de virtuele WAN-hub. (Voor deze stap moet u eerst uw privécloud maken.)

    Ga naar het gedeelte **connectiviteit** van de Privécloud van Azure VMware-oplossing. Selecteer op het tabblad **ExpressRoute** **+ aanvraag een autorisatie sleutel**. Geef deze een naam en selecteer **maken**. (Het duurt ongeveer 30 seconden om de sleutel te maken.) Kopieer de ExpressRoute-ID en de autorisatie sleutel. 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="Kopieer de Express route-ID en autorisatie sleutel.":::

    > [!NOTE]
    > De autorisatie sleutel verdwijnt na enige tijd, dus kopieer deze zo snel als deze wordt weer gegeven.

4. Vervolgens koppelen we een Azure VMware-oplossing en de VPN-gateway samen in de virtuele WAN-hub. Open in de Azure Portal de virtuele WAN die u eerder hebt gemaakt. Selecteer de virtuele WAN-hub die u hebt gemaakt en selecteer vervolgens **ExpressRoute** in het linkerdeel venster. Selecteer **+ autorisatie sleutel voor inwisselen**.

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Autorisatie sleutel inwisselen.":::

    Plak de autorisatie sleutel in het veld autorisatie sleutel en de ExpressRoute-ID in het URL-veld van het **peer circuit** . Zorg ervoor dat u **Dit ExpressRoute-circuit automatisch koppelt aan de hub.** Selecteer **toevoegen** om de koppeling tot stand te brengen. 

5. Als u de verbinding wilt testen, [maakt u een NSX-T-segment](./tutorial-nsx-t-network-segment.md) en richt u een VM in op het netwerk. Test door zowel de on-premises als de Azure VMware-oplossings eindpunten te pingen.
