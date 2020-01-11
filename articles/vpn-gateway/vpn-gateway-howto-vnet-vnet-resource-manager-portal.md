---
title: 'Een VNet-naar-VNet-VPN Gateway verbinding configureren: Azure Portal'
description: Een VPN-gatewayverbinding maken tussen VNets met behulp van Resource Manager en Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: c8ed01af9574ce10c95fb4af61f1da1c72c858ad
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860500"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Een VPN-gatewayverbinding tussen VNet's configureren met behulp van Azure Portal

Dit artikel helpt u om virtuele netwerken (VNet's) te verbinden met behulp van het verbindingstype VNet-naar-VNet. Virtuele netwerken kunnen zich in verschillende regio's bevinden en tot verschillende abonnementen behoren. Wanneer u VNet's uit verschillende abonnementen koppelt, hoeven de abonnementen niet aan dezelfde Active Directory-tenant gekoppeld te zijn. 

![v2v-diagram](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

De stappen in dit artikel zijn van toepassing op het Azure Resource Manager-implementatiemodel en maken gebruik van Azure Portal. U kunt deze configuratie ook maken met een ander implementatieprogramma of -model, met behulp van de opties die worden beschreven in de volgende artikelen:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Over het verbinden van VNet's

In de volgende secties worden de verschillende manieren voor het koppelen van virtuele netwerken beschreven.

### <a name="vnet-to-vnet"></a>VNet-naar-VNet

Het configureren van een VNet-naar-VNet-verbinding is een eenvoudige manier om VNet's te koppelen. Het verbinden van een virtueel netwerk met een ander virtueel netwerk met behulp van het verbindingstype VNet-naar-VNet (VNet2VNet) is vergelijkbaar met het maken van een site-naar-site-IPsec-verbinding met een on-premises locatie. Voor beide verbindingstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden en beide typen werken tijdens het communiceren op dezelfde manier. Het verschil zit hem in de configuratie van de lokale netwerkgateway. 

Als u een VNet-naar-VNet-verbinding maakt, wordt de adresruimte voor de lokale netwerkgateway automatisch gemaakt en gevuld. Wanneer u de adresruimte voor een VNet bijwerkt, wordt de route naar de bijgewerkte adresruimte automatisch ingesteld voor het andere VNet. Het maken van een VNet-naar-VNet-verbinding gaat meestal sneller en makkelijker dan het maken van een site-naar-site-verbinding.

### <a name="site-to-site-ipsec"></a>Site-naar-site (IPsec)

Als u met een gecompliceerde netwerkconfiguratie werkt, kunt u de VNet's wellicht beter koppelen met een [Site-naar-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)-verbinding. Als u de stappen voor site-naar-site-IPsec volgt, maakt en configureert u de lokale netwerkgateways handmatig. De lokale netwerkgateway voor elke VNet behandelt de andere VNet als een lokale site. Hiermee kunt u extra adresruimten voor de lokale netwerkgateway opgeven om het verkeer te routeren. Als de adresruimte voor een VNet wordt gewijzigd, moet u de bijbehorende lokale netwerkgateway handmatig bijwerken.

### <a name="vnet-peering"></a>VNet-peering

U kunt uw Vnet's ook verbinden met behulp van VNet-peering. Bij VNET-peering wordt geen VPN-gateway gebruikt en er gelden diverse beperkingen voor. Bovendien worden de [prijzen voor VNET-peering](https://azure.microsoft.com/pricing/details/virtual-network) anders berekend dan voor [VNet-naar-VNet-VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

## <a name="why-create-a-vnet-to-vnet-connection"></a>Waarom een VNet-met-VNet-verbinding maken?

U kunt omwille van de volgende redenen virtuele netwerken koppelen met een VNet-naar-VNet-verbinding:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geografische redundantie en aanwezigheid tussen regio's

  * U kunt uw eigen geo-replicatie of synchronisatie met beveiligde connectiviteit instellen zonder gebruik te maken van internetgerichte eindpunten.
  * Met Azure Traffic Manager en Azure Load Balancer kunt u workloads met maximale beschikbaarheid instellen met behulp van geografische redundantie over meerdere Azure-regio's. U kunt bijvoorbeeld SQL Server AlwaysOn-beschikbaarheidsgroepen instellen voor meerdere Azure-regio's.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionale toepassingen met meerdere lagen met isolatie- of beheergrenzen

  * Binnen dezelfde regio kunt u vanwege isolatie- of beheervereisten toepassingen met meerdere lagen instellen met meerdere virtuele netwerken die met elkaar zijn verbonden.

VNet-naar-VNet-communicatie kan worden gecombineerd met configuraties voor meerdere locaties. Zoals u in het volgende diagram kunt zien, kunt u met dergelijke configuraties netwerktopologieën maken waarin cross-premises connectiviteit wordt gecombineerd met connectiviteit tussen virtuele netwerken:

![Over verbindingen](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Over verbindingen")

In dit artikel leest u hoe u VNet's verbindt met behulp van het verbindingstype VNet-naar-VNet. Wanneer u deze stappen uitvoert als oefening, kunt u de volgende voorbeeldinstellingswaarden gebruiken. In het voorbeeld vallen de virtuele netwerken onder hetzelfde abonnement, maar behoren ze tot verschillende resourcegroepen. Als uw VNet's onder verschillende abonnementen vallen, kunt u de verbinding niet via de portal maken. Gebruik in plaats daarvan [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) of de [CLI](vpn-gateway-howto-vnet-vnet-cli.md). Zie [Veelgestelde vragen over VNet-naar-VNet](#vnet-to-vnet-faq) voor meer informatie over VNet-naar-VNet-verbindingen.

### <a name="example-settings"></a>Voorbeeldinstellingen

**Waarden voor TestVNet1:**

- **Virtuele-netwerkinstellingen**
    - **Naam**: Voer *TestVNet1*in.
    - **Adres ruimte**: Voer *10.11.0.0/16*in.
    - **Abonnement**: Selecteer het abonnement dat u wilt gebruiken.
    - **Resource groep**: Voer *TestRG1*in.
    - **Locatie**: Selecteer **VS-Oost**.
    - **Subnet**
        - **Naam**: Voer een *frontend*in.
        - **Adres bereik**: Voer *10.11.0.0/24*in.
    - **Gatewaysubnet**:
        - **Naam**: *GatewaySubnet* wordt aangevuld.
        - **Adres bereik**: Voer *10.11.255.0/27*in.

- **Instellingen voor virtuele-netwerkgateway** 
    - **Naam**: Voer *TestVNet1GW*in.
    - **Gatewaytype**: selecteer **VPN**.
    - **VPN-type**: Selecteer **op Route gebaseerde**.
    - **SKU**: Selecteer de gateway-SKU die u wilt gebruiken.
    - **Naam van openbaar IP-adres**: Voer *TestVNet1GWIP* in
    - **Verbinding** 
       - **Naam**: Voer *TestVNet1toTestVNet4*in.
       - **Gedeelde sleutel**: Voer *abc123*in. U kunt de gedeelde sleutel zelf maken. Wanneer u de verbinding tussen de VNets maakt, moeten de waarden overeenkomen.

**Waarden voor TestVNet4:**

- **Virtuele-netwerkinstellingen**
   - **Naam**: Voer *TestVNet4*in.
   - **Adres ruimte**: Voer *10.41.0.0/16*in.
   - **Abonnement**: Selecteer het abonnement dat u wilt gebruiken.
   - **Resource groep**: Voer *TestRG4*in.
   - **Locatie**: selecteer **US - west**.
   - **Subnet** 
      - **Naam**: Voer een *frontend*in.
      - **Adres bereik**: Voer *10.41.0.0/24*in.
   - **GatewaySubnet** 
      - **Naam**: *GatewaySubnet* wordt aangevuld.
      - **Adres bereik**: Voer *10.41.255.0/27*in.

- **Instellingen voor virtuele-netwerkgateway** 
    - **Naam**: Voer *TestVNet4GW*in.
    - **Gatewaytype**: selecteer **VPN**.
    - **VPN-type**: Selecteer **op Route gebaseerde**.
    - **SKU**: Selecteer de gateway-SKU die u wilt gebruiken.
    - **Naam van openbaar IP-adres**: Voer *TestVNet4GWIP*in.
    - **Verbinding** 
       - **Naam**: Voer *TestVNet4toTestVNet1*in.
       - **Gedeelde sleutel**: Voer *abc123*in. U kunt de gedeelde sleutel zelf maken. Wanneer u de verbinding tussen de VNets maakt, moeten de waarden overeenkomen.

## <a name="create-and-configure-testvnet1"></a>TestVNet1 maken en configureren
Als u al beschikt over een VNet, controleert u of de instellingen compatibel zijn met het ontwerp van de VPN-gateway. Let vooral op eventuele subnetten die met andere netwerken overlappen. Als u overlappende subnetten hebt, werkt de verbinding mogelijk niet goed.

### <a name="to-create-a-virtual-network"></a>Een virtueel netwerk maken
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>Extra adresruimte toevoegen en subnetten maken
Wanneer het VNet is gemaakt, kunt u er extra adresruimte en subnetten aan toevoegen.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-virtual-network-gateway"></a>De gateway van een virtueel netwerk maken
In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU. Zie de [voorbeeldinstellingen](#example-settings) als u deze configuratie bij wijze van oefening maakt.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>De gateway van een virtueel netwerk maken
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-testvnet4"></a>TestVNet4 maken en configureren
Nadat u TestVNet1 hebt geconfigureerd, maakt u TestVNet4 door de vorige stappen te herhalen en de waarden te vervangen door die van TestVNet4. U hoeft niet te wachten tot de gateway van het virtuele netwerk voor TestVNet1 is gemaakt voordat u TestVNet4 configureert. Als u uw eigen waarden gebruikt, mogen de adresruimten niet overlappen met een van de VNet's waarmee u verbinding wilt maken.

## <a name="configure-the-testvnet1-gateway-connection"></a>De TestVNet1-gatewayverbinding configureren
Wanneer de virtuele netwerkgateways voor TestVNet1 en TestVNet4 zijn voltooid, kunt u de verbindingen voor uw virtuele netwerkgateway maken. In deze sectie maakt u een verbinding tussen VNet1 en VNet4. Deze stappen werken alleen voor VNets in hetzelfde abonnement. Als uw VNet's onder verschillende abonnementen vallen, moet u [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) gebruiken om de verbinding te maken. Als uw VNet's echter behoren tot verschillende resourcegroepen die onder hetzelfde abonnement vallen, kunt u deze verbinden met behulp van de portal.

1. Selecteer in Azure Portal **Alle resources**, typ *gateway van virtueel netwerk* in het zoekvak en navigeer vervolgens naar de gateway voor uw VNet. Bijvoorbeeld **TestVNet1GW**. Selecteer deze om de pagina **Gateway van virtueel netwerk** te openen.

   ![Pagina verbindingen](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Pagina verbindingen")
2. Onder **Instellingen** selecteert u **Verbindingen**, en vervolgens **Toevoegen** om de pagina **Verbinding toevoegen** te openen.

   ![Verbinding toevoegen](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Een verbinding toevoegen")
3. Vul op de pagina **Verbinding toevoegen** de waarden voor uw verbinding in:

   - **Naam**: Voer een naam in voor de verbinding. Bijvoorbeeld *TestVNet1toTestVNet4*.

   - **Verbindings type**: Selecteer **VNet-naar-VNet** in de vervolg keuzelijst.

   - **Eerste virtuele netwerk gateway**: deze veld waarde wordt automatisch ingevuld omdat u deze verbinding maakt op basis van de opgegeven virtuele netwerk gateway.

   - **Tweede virtuele netwerk gateway**: dit veld is de virtuele netwerk gateway van het VNet waarmee u een verbinding wilt maken. Selecteer **Een andere virtuele netwerkgateway kiezen** om de pagina **Virtuele netwerkgateway kiezen** te openen.

     - Bekijk de virtuele netwerkgateways die op deze pagina worden vermeld. U ziet dat er alleen virtuele netwerkgateways worden vermeld die binnen uw abonnement vallen. Als u verbinding wilt maken met een virtuele netwerkgateway die geen deel uitmaakt van uw abonnement, gebruikt u [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Selecteer de virtuele netwerkgateway waarmee u verbinding wilt maken.

     - **Gedeelde sleutel (PSK)** : Voer in dit veld een gedeelde sleutel in voor uw verbinding. U kunt deze sleutel ook zelf maken of genereren. In een verbinding tussen sites is de sleutel gelijk aan de sleutel voor het on-premises apparaat en uw virtuele netwerkgatewayverbinding. Het concept is hier vergelijkbaar, maar in plaats van een verbinding te maken met een VPN-apparaat, maakt u verbinding met een andere virtuele netwerkgateway.
    
4. Selecteer **OK** om uw wijzigingen op te slaan.

## <a name="configure-the-testvnet4-gateway-connection"></a>De TestVNet4-gatewayverbinding configureren
Maak vervolgens een verbinding tussen TestVNet4 en TestVNet1. Zoek in de portal de virtuele netwerkgateway die is gekoppeld aan TestVNet4. Volg de stappen uit de vorige sectie, maar maak nu een verbinding van TestVNet4 naar TestVNet1. Zorg ervoor dat u dezelfde gedeelde sleutel gebruikt.

## <a name="verify-your-connections"></a>Controleer uw verbindingen

Zoek de virtuele netwerkgateway in Azure Portal. Selecteer **Verbindingen** op de pagina **Virtuele netwerkgateway** om de pagina **Verbindingen** weer te geven voor de virtuele netwerkgateway. Nadat de verbinding tot stand is gebracht, veranderen de waarden van **Status** in **Geslaagd** en **Verbonden**. Selecteer een verbinding op de pagina **Essentials** met meer informatie te openen.

![Geslaagd](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Geslaagd")

Wanneer de gegevensstromen op gang komen, ziet u waarden voor **Inkomende gegevens** en **Uitgaande gegevens**.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="add-additional-connections"></a>Extra verbindingen toevoegen

Als u extra verbindingen wilt toevoegen, gaat u naar de virtuele netwerkgateway waarvoor u de verbinding wilt maken en selecteert u **Verbindingen**. U kunt een andere VNet-naar-VNet-verbinding maken, of een site-naar-site-verbinding (IPsec) met een on-premises locatie. Stel **Verbindingstype** in op het gewenste type voor de verbinding die u wilt maken. Controleer voordat u extra verbindingen maakt of de adresruimte voor het virtuele netwerk niet overlapt met een van de adresruimten waarmee u verbinding wilt maken. Zie [Een site-naar-site-verbinding maken](vpn-gateway-howto-site-to-site-resource-manager-portal.md) voor stappen voor het maken van een site-naar-site-verbinding.

## <a name="vnet-to-vnet-faq"></a>Veelgestelde vragen over VNet-naar-VNet
Bekijk de Veelgestelde vragen voor meer informatie over VNet-naar-VNet-verbindingen.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Netwerkbeveiliging](../virtual-network/security-overview.md) voor informatie over het beperken van netwerkverkeer tot resources in een virtueel netwerk.

Zie [Routering van verkeer in virtuele netwerken](../virtual-network/virtual-networks-udr-overview.md) voor informatie over hoe Azure verkeer routeert tussen Azure-resources, on-premises resources en resources op internet.
