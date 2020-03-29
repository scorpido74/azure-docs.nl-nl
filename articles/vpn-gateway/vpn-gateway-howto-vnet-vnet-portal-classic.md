---
title: 'Een verbinding maken tussen VNets: klassiek: Azure-portal'
description: Sluit virtuele Azure-netwerken met elkaar via PowerShell en de Azure-portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 63c6329ad62289cd127902c1438073b28fc8683e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201846"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Een VNet-naar-VNet-verbinding configureren (klassiek)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Met dit artikel u een VPN-gatewayverbinding tussen virtuele netwerken maken. De virtuele netwerken kunnen zich in dezelfde of verschillende regio's bevinden en tot dezelfde of verschillende abonnementen behoren. De stappen in dit artikel zijn van toepassing op het klassieke implementatiemodel en de Azure-portal. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Powershell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet naar VNet-connectiviteitsdiagram](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Over VNet-naar-VNet-verbindingen

Het verbinden van een virtueel netwerk met een ander virtueel netwerk (VNet-to-VNet) in het klassieke implementatiemodel met behulp van een VPN-gateway is vergelijkbaar met het verbinden van een virtueel netwerk met een on-premises locatie. Voor beide connectiviteitstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden.

De VNets die u verbindt, kunnen zich in verschillende abonnementen en verschillende regio's bevinden. U VNet combineren met VNet-communicatie met configuraties met meerdere locaties. Zo kunt u netwerktopologieën maken waarin cross-premises connectiviteit is gecombineerd met connectiviteit tussen virtuele netwerken.

![VNet naar VNet-verbindingen](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Waarom virtuele netwerken koppelen?

U wilt virtuele netwerken wellicht koppelen om de volgende redenen:

* **Geografische redundantie en aanwezigheid tussen regio's**

  * U kunt uw eigen geo-replicatie of synchronisatie met beveiligde connectiviteit instellen zonder gebruik te maken van internetgerichte eindpunten.
  * Met Azure Load Balancer en Microsoft of clusteringtechnologie van derden u zeer beschikbare workloads instellen met georedundantie in meerdere Azure-regio's. Een belangrijk voorbeeld hiervan is het instellen van SQL Always On met beschikbaarheidsgroepen verspreid over meerdere Azure-regio's.
* **Regionale toepassingen met meerdere lagen met een sterke isolatiegrens**

  * Binnen dezelfde regio u toepassingen met meerdere VNet's instellen die zijn gekoppeld aan sterke isolatie en veilige communicatie tussen lagen.
* **Communicatie tussen abonnementen en interorganisaties in Azure**

  * Als u meerdere Azure-abonnementen hebt, u workloads van verschillende abonnementen veilig verbinden tussen virtuele netwerken.
  * Voor ondernemingen of serviceproviders u communicatie tussen organisaties inschakelen met beveiligde VPN-technologie binnen Azure.

Zie voor meer informatie over verbindingen tussen VNets de [Aandachtspunten bij VNet-naar-VNet](#faq) aan het einde van dit artikel.

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>Werken met Azure PowerShell

We gebruiken de portal voor de meeste stappen, maar u moet PowerShell gebruiken om de verbindingen tussen de VNets te maken. U de verbindingen niet maken met de Azure-portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Stap 1: De IP-adresbereiken plannen

Het is belangrijk om te bepalen welke bereiken u gebruikt om uw virtuele netwerken te configureren. Voor deze configuratie moet u ervoor zorgen dat geen van uw VNet-bereiken met elkaar overlapt, of met een van de lokale netwerken waarmee ze verbinding maken.

In de volgende tabel ziet u een voorbeeld van het definiëren van uw VNets. Gebruik de bereiken alleen als richtlijn. Noteer de bereiken voor uw virtuele netwerken. U hebt deze informatie nodig voor latere stappen.

**Voorbeeld**

| Virtual Network | Adresruimte | Regio | Maakt verbinding met lokale netwerksite |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |VS - oost |VNet4Lokaal<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |VS - west |VNet1Lokaal<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Stap 2 - De virtuele netwerken maken

Maak twee virtuele netwerken in de [Azure-portal.](https://portal.azure.com) Zie Een klassiek virtueel netwerk [maken](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)voor de stappen om klassieke virtuele netwerken te maken. 

Wanneer u de portal gebruikt om een klassiek virtueel netwerk te maken, moet u naar de virtuele netwerkpagina navigeren met behulp van de volgende stappen, anders wordt de optie om een klassiek virtueel netwerk te maken niet weergegeven:

1. Klik op '+' om de pagina 'Nieuw' te openen.
2. Typ 'Virtueel netwerk' in het veld 'Zoeken op de marktplaats'. Als u in plaats daarvan Netwerken-> Virtueel netwerk selecteert, krijgt u niet de optie om een klassieke VNet te maken.
3. Zoek 'Virtueel netwerk' in de geretourneerde lijst en klik erop om de pagina Virtueel netwerk te openen. 
4. Selecteer op de virtuele netwerkpagina 'Klassiek' om een klassieke VNet te maken. 

Als u dit artikel als oefening gebruikt, u de volgende voorbeeldwaarden gebruiken:

**Waarden voor TestVNet1**

Naam: TestVNet1<br>
Adresruimte: 10.11.0.0/16, 10.12.0.0/16 (optioneel)<br>
Subnetnaam: standaard<br>
Subnetadresbereik: 10.11.0.1/24<br>
Resourcegroep: ClassicRG<br>
Locatie: VS - oost<br>
GatewaySubnet: 10.11.1.0/27

**Waarden voor TestVNet4**

Naam: TestVNet4<br>
Adresruimte: 10.41.0.0/16, 10.42.0.0/16 (optioneel)<br>
Subnetnaam: standaard<br>
Subnetadresbereik: 10.41.0.1/24<br>
Resourcegroep: ClassicRG<br>
Locatie: VS - west<br>
GatewaySubnet: 10.41.1.0/27

**Houd bij het maken van uw VNets rekening met de volgende instellingen:**

* **Virtuele netwerkadresruimten** – Geef op de pagina Ruimten voor virtuele netwerkadresruimten het adresbereik op dat u wilt gebruiken voor uw virtuele netwerk. Dit zijn de dynamische IP-adressen die worden toegewezen aan de VM's en andere rolexemplaren die u implementeert in dit virtuele netwerk.<br>De adresruimten die u selecteert, kunnen niet overlappen met de adresruimten voor een van de andere VNets of on-premises locaties waarmee deze VNet verbinding maakt.

* **Locatie** : wanneer u een virtueel netwerk maakt, koppelt u het aan een Azure-locatie (regio). Als u bijvoorbeeld wilt dat uw VM's die zijn geïmplementeerd in uw virtuele netwerk fysiek in West-VS worden gevestigd, selecteert u die locatie. U de locatie die aan uw virtuele netwerk is gekoppeld, niet wijzigen nadat u het hebt gemaakt.

**Nadat u uw VNets hebt aanmaken, u de volgende instellingen toevoegen:**

* **Adresruimte** : voor deze configuratie is geen extra adresruimte vereist, maar u extra adresruimte toevoegen na het maken van de VNet.

* **Subnetten** : extra subnetten zijn niet vereist voor deze configuratie, maar u wilt misschien uw VM's in een subnet hebben dat gescheiden is van uw andere rolinstanties.

* **DNS-servers** : voer de naam en het IP-adres van de DNS-server in. Met deze instelling wordt geen DNS-server gemaakt. U kunt hiermee de DNS-servers opgeven die u wilt gebruiken voor de naamomzetting voor dit virtuele netwerk.

In deze sectie configureert u het verbindingstype, de lokale site en maakt u de gateway.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Stap 3 - De lokale site configureren

Azure gebruikt de instellingen die zijn opgegeven in elke lokale netwerksite om te bepalen hoe verkeer tussen de VNets kan worden routerd. Elke VNet moet wijzen op het betreffende lokale netwerk waarnaar u verkeer wilt leiden. U bepaalt de naam die u wilt gebruiken om naar elke lokale netwerksite te verwijzen. Het is het beste om iets beschrijvends te gebruiken.

TestVNet1 maakt bijvoorbeeld verbinding met een lokale netwerksite die u met de naam 'VNet4Local' maakt. De instellingen voor VNet4Local bevatten de adresvoorvoegsels voor TestVNet4.

De lokale site voor elke VNet is de andere VNet. De volgende voorbeeldwaarden worden gebruikt voor onze configuratie:

| Virtual Network | Adresruimte | Regio | Maakt verbinding met lokale netwerksite |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |VS - oost |VNet4Lokaal<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |VS - west |VNet1Lokaal<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Zoek TestVNet1 in de Azure-portal. Klik in het gedeelte **VPN-verbindingen** van de pagina op **Gateway**.

    ![Geen gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Selecteer op de pagina **Nieuwe VPN-verbinding** de optie **Site-to-Site**.
3. Klik **op Lokale site** om de pagina Lokale site te openen en de instellingen te configureren.
4. Geef **op** de pagina Lokale site een naam aan uw lokale site. In ons voorbeeld noemen we de lokale site 'VNet4Local'.
5. Voor **IP-adres van de VPN-gateway**u elk IP-adres gebruiken dat u wilt, zolang het in een geldige indeling is. Normaal gesproken gebruikt u het werkelijke externe IP-adres voor een VPN-apparaat. Maar voor een klassieke VNet-to-VNet-configuratie gebruikt u het openbare IP-adres dat is toegewezen aan de gateway voor uw VNet. Aangezien u de virtuele netwerkgateway nog niet hebt gemaakt, geeft u een geldig openbaar IP-adres op als tijdelijke aanduiding.<br>Laat dit niet leeg - het is niet optioneel voor deze configuratie. In een latere stap gaat u terug naar deze instellingen en configureert u deze met de bijbehorende IP-adressen van de virtuele netwerkgateway zodra Azure deze genereert.
6. Gebruik **voor Clientadresruimte**de adresruimte van het andere VNet. Raadpleeg uw planningsvoorbeeld. Klik op **OK** om uw instellingen op te slaan en terug te keren naar de pagina **Nieuwe VPN-verbinding.**

    ![lokale site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Stap 4 - De virtuele netwerkgateway maken

Elk virtueel netwerk moet een virtuele netwerkgateway hebben. De virtuele netwerkgateway leidt en versleutelt verkeer.

1. Schakel op de pagina **Nieuwe VPN-verbinding** het selectievakje **Gateway onmiddellijk maken** in.
2. Klik **op Subnet, grootte en routeringstype**. Klik op de **configuratiepagina gateway** op **Subnet**.
3. De subnetnaam gateway wordt automatisch ingevuld met de vereiste naam 'GatewaySubnet'. Het **adresbereik** bevat de IP-adressen die zijn toegewezen aan de VPN-gatewayservices. In sommige configuraties is een gatewaysubnet van /29 mogelijk, maar het is het beste om een /28 of /27 te gebruiken voor toekomstige configuraties waarvoor mogelijk meer IP-adressen voor de gatewayservices nodig zijn. In onze voorbeeldinstellingen gebruiken we 10.11.1.0/27. Pas de adresruimte aan en klik op **OK**.
4. Configureer de **gatewaygrootte**. Deze instelling verwijst naar de [Gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configureer het **routeringstype**. Het routeringstype voor deze configuratie moet **Dynamisch**zijn. U het routeringstype later niet wijzigen, tenzij u de gateway afsloopt en een nieuwe gateway maakt.
6. Klik op **OK**.
7. Klik op de pagina **Nieuwe VPN-verbinding** op **OK** om te beginnen met het maken van de virtuele netwerkgateway. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Stap 5 - TestVNet4-instellingen configureren

Herhaal de stappen om [een lokale site](#localsite) te maken en Maak de virtuele [netwerkgateway](#gw) om TestVNet4 te configureren en vervang de waarden indien nodig. Als u dit als oefening doet, gebruikt u de [waarden voorbeeld](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Stap 6 - De lokale sites bijwerken

Nadat uw virtuele netwerkgateways zijn gemaakt voor beide VNets, moet u de **IP-adreswaarden** van de lokale sites VPN-gateway aanpassen.

|VNet-naam|Verbonden site|IP-adres gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Lokaal|VPN-gateway IP-adres voor TestVNet4|
|TestVNet4|VNet1Lokaal|VPN-gateway IP-adres voor TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Deel 1 - Het openbare IP-adres van de virtuele netwerkgateway

1. Zoek uw virtuele netwerk in de Azure-portal.
2. Klik hier om de pagina **VNet-overzicht** te openen. Op de pagina, in **VPN-verbindingen,** u het IP-adres voor uw virtuele netwerkgateway bekijken.

   ![Openbare IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Kopieer het IP-adres. U zult het in de volgende sectie gebruiken.
4. Herhaal deze stappen voor TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Deel 2 - De lokale sites wijzigen

1. Zoek uw virtuele netwerk in de Azure-portal.
2. Klik op de pagina **VNet-overzicht** op de lokale site.

   ![Lokale site gemaakt](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Klik op de pagina **VPN-verbindingen van site naar site** op de naam van de lokale site die u wilt wijzigen.

   ![Lokale site openen](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klik op **de lokale site** die u wilt wijzigen.

   ![site wijzigen](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Werk het **IP-adres van de VPN-gateway bij** en klik op **OK** om de instellingen op te slaan.

   ![gateway-IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Sluit de andere pagina's.
7. Herhaal deze stappen voor TestVNet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Stap 7 - Waarden ophalen uit het netwerkconfiguratiebestand

Wanneer u klassieke VNets maakt in de Azure-portal, is de naam die u bekijkt niet de volledige naam die u voor PowerShell gebruikt. Een VNet met de naam **TestVNet1** in de portal kan bijvoorbeeld een veel langere naam hebben in het netwerkconfiguratiebestand. De naam lijkt misschien op: **Group ClassicRG TestVNet1**. Wanneer u uw verbindingen maakt, is het belangrijk om de waarden te gebruiken die u in het netwerkconfiguratiebestand ziet.

In de volgende stappen maakt u verbinding met uw Azure-account en downloadt en bekijkt u het netwerkconfiguratiebestand om de waarden te verkrijgen die nodig zijn voor uw verbindingen.

1. Download en installeer de nieuwste versie van de PowerShell-cmdlets (Azure Service Management). Zie [Werken met Azure PowerShell](#powershell)voor meer informatie.

2. Open uw PowerShell-console met verhoogde rechten. Gebruik de volgende voorbeelden om verbinding te maken. U moet deze opdrachten lokaal uitvoeren met de PowerShell-servicebeheermodule. Als u wilt overschakelen naar servicebeheer, gebruikt u deze opdracht:

   ```powershell
   azure config mode asm
   ```
3. Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```powershell
   Add-AzureAccount
   ```
4. Controleer de abonnementen voor het account.

   ```powershell
   Get-AzureSubscription
   ```
5. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
6. Exporteer en bekijk het netwerkconfiguratiebestand. Maak een map op de computer en exporteer vervolgens het netwerkconfiguratiebestand naar de map. In dit voorbeeld wordt het netwerkconfiguratiebestand geëxporteerd naar **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Open het bestand met een teksteditor en bekijk de namen voor uw VNets en sites. Deze namen zijn de namen die u gebruikt wanneer u uw verbindingen maakt.<br>VNet-namen worden vermeld als **VirtualNetworkSite-naam =**<br>Sitenamen worden vermeld als **LocalNetworkSiteRef-naam =**

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Stap 8 - De VPN-gatewayverbindingen maken

Wanneer alle vorige stappen zijn voltooid, u de vooraf gedeelde iPsec/IKE-toetsen instellen en de verbinding maken. Deze set stappen maakt gebruik van PowerShell. VNet-naar-VNet-verbindingen voor het klassieke implementatiemodel kunnen niet worden geconfigureerd in de Azure-portal.

In de voorbeelden wordt opgemerkt dat de gedeelde sleutel precies hetzelfde is. De gedeelde sleutel moet altijd overeenkomen. Zorg ervoor dat u de waarden in deze voorbeelden vervangt door de exacte namen voor uw VNets en lokale netwerksites.

1. Maak de verbinding tussen TestVNet1 en TestVNet4.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. Maak de verbinding tussen TestVNet4 en TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. Wacht tot de verbindingen worden geïnitialiseren. Zodra de gateway is geïnitialiseerd, is de status 'succesvol'.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>VNet-to-VNet overwegingen voor klassieke VNets
* De virtuele netwerken kunnen in dezelfde of verschillende abonnementen.
* De virtuele netwerken kunnen zich in dezelfde of verschillende Azure-regio's (locaties) bevinden.
* Een cloudservice of een endpoint voor het balanceren van de load-balancing kan niet over virtuele netwerken heen, zelfs niet als ze met elkaar zijn verbonden.
* Als u meerdere virtuele netwerken met elkaar verbindt, zijn geen VPN-apparaten vereist.
* VNet-to-VNet ondersteunt het verbinden van Azure Virtual Networks. Het ondersteunt geen het aansluiten van virtuele machines of cloudservices die niet worden geïmplementeerd op een virtueel netwerk.
* VNet-to-VNet vereist dynamische routing gateways. Azure statische routeringgateways worden niet ondersteund.
* U kunt virtuele-netwerkverbindingen tegelijk gebruiken met multi-site-VPN’s. Er is een maximum van 10 VPN-tunnels voor een VPN-gateway voor virtueel netwerk die verbinding maakt met andere virtuele netwerken of on-premises sites.
* De adresruimten van de virtuele netwerken en on-premises lokale netwerksites mogen elkaar niet overlappen. Overlappende adresruimten zorgen ervoor dat het maken van virtuele netwerken of het uploaden van netcfg-configuratiebestanden mislukken.
* Redundante tunnels tussen een paar virtuele netwerken worden niet ondersteund.
* Alle VPN-tunnels voor het VNet, inclusief P2S VPN's, delen de beschikbare bandbreedte voor de VPN-gateway en dezelfde VPN-gateway uptime SLA in Azure.
* VNet-naar-VNet-verkeer reist over de Azure-backbone.

## <a name="next-steps"></a>Volgende stappen
Controleer uw verbindingen. Zie [Een VPN-gatewayverbinding verifiëren](vpn-gateway-verify-connection-resource-manager.md).
