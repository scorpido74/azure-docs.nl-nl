---
title: 'Een verbinding maken tussen VNets: klassiek: Azure Portal'
description: Verbinding maken met virtuele Azure-netwerken met behulp van Power shell en het Azure Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: bdd27645045195016b7a563787470bf6f2187115
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84985463"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Een VNet-naar-VNet-verbinding configureren (klassiek)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Dit artikel helpt u bij het maken van een VPN-gateway verbinding tussen virtuele netwerken. De virtuele netwerken kunnen zich in dezelfde of verschillende regio's bevinden en tot dezelfde of verschillende abonnementen behoren. De stappen in dit artikel zijn van toepassing op het klassieke implementatie model en de Azure Portal. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram van VNet-naar-VNet-connectiviteit](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Over VNet-naar-VNet-verbindingen

Het verbinden van een virtueel netwerk met een ander virtueel netwerk (VNet-naar-VNet) in het klassieke implementatie model met behulp van een VPN-gateway is vergelijkbaar met het verbinden van een virtueel netwerk met een on-premises site locatie. Voor beide connectiviteitstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden.

De VNets die u verbindt, kunnen zich in verschillende abonnementen en verschillende regio's bevindt. U kunt VNet combi neren met VNet-communicatie met configuraties met meerdere locaties. Zo kunt u netwerktopologieën maken waarin cross-premises connectiviteit is gecombineerd met connectiviteit tussen virtuele netwerken.

![VNet-naar-VNet-verbindingen](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Waarom virtuele netwerken koppelen?

U wilt virtuele netwerken wellicht koppelen om de volgende redenen:

* **Geografische redundantie en aanwezigheid tussen regio's**

  * U kunt uw eigen geo-replicatie of synchronisatie met beveiligde connectiviteit instellen zonder gebruik te maken van internetgerichte eindpunten.
  * Met Azure Load Balancer en de clustering technologie van micro soft of derden kunt u een werk belasting met hoge Beschik baarheid instellen met georedundantie over meerdere Azure-regio's. Een belangrijk voorbeeld hiervan is het instellen van SQL Always On met beschikbaarheidsgroepen verspreid over meerdere Azure-regio's.
* **Regionale toepassingen met meerdere lagen met een sterke isolatie grens**

  * Binnen dezelfde regio kunt u multi-tier-toepassingen met meerdere VNets die zijn verbonden met een sterke isolatie en beveiligde communicatie tussen lagen instellen.
* **Cross-abonnement, communicatie tussen organisaties in azure**

  * Als u meerdere Azure-abonnementen hebt, kunt u werk belastingen van verschillende abonnementen samen met een beveiligde verbinding tussen virtuele netwerken verbinden.
  * Voor ondernemingen of service providers kunt u communicatie tussen organisaties met beveiligde VPN-technologie in azure inschakelen.

Zie voor meer informatie over verbindingen tussen VNets de [Aandachtspunten bij VNet-naar-VNet](#faq) aan het einde van dit artikel.

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>Werken met Azure PowerShell

We gebruiken de portal voor de meeste stappen, maar u moet Power shell gebruiken om de verbindingen tussen de VNets te maken. U kunt de verbindingen niet maken met behulp van de Azure Portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Stap 1: De IP-adresbereiken plannen

Het is belang rijk om te bepalen welke bereiken u gaat gebruiken voor het configureren van uw virtuele netwerken. Voor deze configuratie moet u ervoor zorgen dat geen van uw VNet-bereiken met elkaar overlappen, of met een van de lokale netwerken waarmee ze verbinding maken.

In de volgende tabel ziet u een voor beeld van hoe u uw VNets definieert. Gebruik de bereiken alleen als richt lijn. Noteer de bereiken voor uw virtuele netwerken. U hebt deze informatie nodig voor latere stappen.

**Voorbeeld**

| Virtual Network | Adresruimte | Regio | Maakt verbinding met de lokale netwerk site |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |VS - oost |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |VS - west |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Stap 2: de virtuele netwerken maken

Maak twee virtuele netwerken in de [Azure Portal](https://portal.azure.com). Zie [een klassiek virtueel netwerk maken](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)voor de stappen voor het maken van klassieke virtuele netwerken. 

Wanneer u de portal gebruikt om een klassiek virtueel netwerk te maken, moet u naar de pagina virtueel netwerk gaan door de volgende stappen uit te voeren. anders wordt de optie voor het maken van een klassiek virtueel netwerk niet weer gegeven:

1. Klik op ' + ' om de pagina ' nieuw ' te openen.
2. Typ ' Virtual Network ' in het veld Marketplace doorzoeken. Als u in plaats daarvan netwerk-> Virtual Network selecteert, krijgt u niet de mogelijkheid om een klassiek VNet te maken.
3. Zoek naar ' Virtual Network ' in de lijst met resultaten en klik erop om de pagina Virtual Network te openen. 
4. Selecteer op de pagina virtueel netwerk de optie klassiek om een klassiek VNet te maken. 

Als u dit artikel gebruikt als oefening, kunt u de volgende voorbeeld waarden gebruiken:

**Waarden voor TestVNet1**

Naam: TestVNet1<br>
Adres ruimte: 10.11.0.0/16, 10.12.0.0/16 (optioneel)<br>
Subnetnaam: standaard<br>
Adres bereik van subnet: 10.11.0.1/24<br>
Resource groep: ClassicRG<br>
Locatie: VS - oost<br>
GatewaySubnet: 10.11.1.0/27

**Waarden voor TestVNet4**

Naam: TestVNet4<br>
Adres ruimte: 10.41.0.0/16, 10.42.0.0/16 (optioneel)<br>
Subnetnaam: standaard<br>
Adres bereik van subnet: 10.41.0.1/24<br>
Resource groep: ClassicRG<br>
Locatie: VS - west<br>
GatewaySubnet: 10.41.1.0/27

**Houd bij het maken van uw VNets de volgende instellingen in acht:**

* **Virtual Network adres ruimten** : Geef op de pagina adres ruimten Virtual Network het adres bereik op dat u wilt gebruiken voor het virtuele netwerk. Dit zijn de dynamische IP-adressen die worden toegewezen aan de Vm's en andere rolinstanties die u op dit virtuele netwerk implementeert.<br>De adres ruimten die u selecteert, mogen niet overlappen met de adres ruimten voor een van de andere VNets of on-premises locaties waarmee dit VNet verbinding maakt.

* **Locatie** : wanneer u een virtueel netwerk maakt, koppelt u dit aan een Azure-locatie (regio). Als u bijvoorbeeld wilt dat uw virtuele machines die worden geïmplementeerd in uw virtueel netwerk zich fysiek in VS West bevinden, selecteert u die locatie. U kunt de locatie die is gekoppeld aan het virtuele netwerk niet wijzigen nadat u het hebt gemaakt.

**Nadat u uw VNets hebt gemaakt, kunt u de volgende instellingen toevoegen:**

* **Adres ruimte** : er is geen extra adres ruimte vereist voor deze configuratie, maar u kunt extra adres ruimte toevoegen nadat u het VNet hebt gemaakt.

* **Subnetten** : extra subnetten zijn niet vereist voor deze configuratie, maar mogelijk wilt u uw virtuele machines in een subnet hebben dat gescheiden is van uw andere rolinstanties.

* **DNS-servers** : Voer de naam en het IP-adres van de DNS-server in. Met deze instelling wordt geen DNS-server gemaakt. U kunt hiermee de DNS-servers opgeven die u wilt gebruiken voor de naamomzetting voor dit virtuele netwerk.

In deze sectie configureert u het verbindings type, de lokale site, en maakt u de gateway.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Stap 3: de lokale site configureren

Azure gebruikt de instellingen die zijn opgegeven op elke lokale netwerk site om te bepalen hoe verkeer tussen de VNets moet worden gerouteerd. Elk VNet moet verwijzen naar het respectieve lokale netwerk waarnaar u verkeer wilt door sturen. U bepaalt de naam die u wilt gebruiken om naar elke lokale netwerk site te verwijzen. Het is raadzaam om een beschrijvende naam te gebruiken.

TestVNet1 maakt bijvoorbeeld verbinding met een lokale netwerk site die u maakt met de naam ' VNet4Local '. De instellingen voor VNet4Local bevatten de adres voorvoegsels voor TestVNet4.

De lokale site voor elk VNet is het andere VNet. De volgende voorbeeld waarden worden gebruikt voor de configuratie:

| Virtual Network | Adresruimte | Regio | Maakt verbinding met de lokale netwerk site |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |VS - oost |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |VS - west |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Zoek TestVNet1 in de Azure Portal. Klik in de sectie **VPN-verbindingen** van de pagina op **Gateway**.

    ![Geen gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Selecteer op de pagina **nieuwe VPN-verbinding** de optie **site-naar-site**.
3. Klik op **lokale site** om de pagina lokale site te openen en de instellingen te configureren.
4. Geef op de pagina **lokale site** uw lokale site een naam. In ons voor beeld noemen we de lokale site ' VNet4Local '.
5. Voor het IP-adres van de **VPN-gateway**kunt u elk gewenst IP-adres gebruiken, mits dit een geldige indeling heeft. Normaal gesp roken gebruikt u het daad werkelijke externe IP-adres voor een VPN-apparaat. Voor een klassieke VNet-naar-VNet-configuratie gebruikt u echter het open bare IP-adres dat is toegewezen aan de gateway voor uw VNet. Gezien dat u de gateway van het virtuele netwerk nog niet hebt gemaakt, geeft u een geldig openbaar IP-adres op als tijdelijke aanduiding.<br>Laat dit veld niet leeg. het is niet optioneel voor deze configuratie. In een latere stap gaat u terug naar deze instellingen en configureert u deze met de bijbehorende IP-adressen van de virtuele netwerk gateway zodra deze door Azure worden gegenereerd.
6. Gebruik de adres ruimte van het andere VNet voor de **adres ruimte**van de client. Raadpleeg uw plannings voorbeeld. Klik op **OK** om uw instellingen op te slaan en terug te keren naar de pagina **nieuwe VPN-verbinding** .

    ![lokale site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Stap 4: de gateway van het virtuele netwerk maken

Elk virtueel netwerk moet een virtuele netwerk gateway hebben. De gateway van het virtuele netwerk routeert en versleutelt verkeer.

1. Schakel op de pagina **Nieuwe VPN-verbinding** het selectievakje **Gateway onmiddellijk maken** in.
2. Klik op **subnet, grootte en routerings type**. Klik op het tabblad **Gateway configuratie** op **subnet**.
3. De naam van het gateway-subnet wordt automatisch ingevuld met de vereiste naam ' GatewaySubnet '. Het **adres bereik** bevat de IP-adressen die zijn toegewezen aan de VPN-gateway services. Sommige configuraties bieden een gateway-subnet van/29, maar het is het beste om een/28 of/27 te gebruiken voor toekomstige configuraties waarvoor meer IP-adressen voor de Gateway Services nodig zijn. In onze voorbeeld instellingen gebruiken we 10.11.1.0/27. Pas de adres ruimte aan en klik vervolgens op **OK**.
4. De **Gateway grootte**configureren. Deze instelling verwijst naar de [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configureer het **routerings type**. Het routerings type voor deze configuratie moet **dynamisch**zijn. U kunt het routerings type later pas wijzigen als u de gateway hebt afgebroken en een nieuwe hebt gemaakt.
6. Klik op **OK**.
7. Klik op de pagina **nieuwe VPN-verbinding** op **OK** om te beginnen met het maken van de gateway van het virtuele netwerk. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Stap 5: TestVNet4-instellingen configureren

Herhaal de stappen voor het [maken van een lokale site](#localsite) en [het maken van de gateway van het virtuele netwerk](#gw) om TestVNet4 te configureren, waarbij u de waarden vervangt wanneer dat nodig is. Als u dit doet als oefening, gebruikt u de [voorbeeld waarden](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Stap 6: de lokale sites bijwerken

Nadat de gateways voor het virtuele netwerk zijn gemaakt voor beide VNets, moet u de **IP-adres** waarden van de lokale sites van de VPN-gateway aanpassen.

|VNet-naam|Verbonden site|IP-adres van de gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|IP-adres van de VPN-gateway voor TestVNet4|
|TestVNet4|VNet1Local|IP-adres van de VPN-gateway voor TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Deel 1: het open bare IP-adres van de gateway van het virtuele netwerk ophalen

1. Zoek het virtuele netwerk in de Azure Portal.
2. Klik hier om de pagina VNet- **overzicht** te openen. Op de pagina, in **VPN-verbindingen**, kunt u het IP-adres voor de gateway van uw virtuele netwerk weer geven.

   ![Openbare IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Kopieer het IP-adres. U gebruikt deze in de volgende sectie.
4. Herhaal deze stappen voor TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Deel 2: de lokale sites wijzigen

1. Zoek het virtuele netwerk in de Azure Portal.
2. Klik op de pagina VNet **Overview** op de lokale site.

   ![Lokale site gemaakt](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Klik op de pagina **site-naar-site-VPN-verbindingen** op de naam van de lokale site die u wilt wijzigen.

   ![Open lokale site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klik op de **lokale site** die u wilt wijzigen.

   ![site wijzigen](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Werk het **IP-adres van de VPN-gateway** bij en klik op **OK** om de instellingen op te slaan.

   ![gateway-IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Sluit de andere pagina's.
7. Herhaal deze stappen voor TestVNet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Stap 7: waarden uit het netwerk configuratie bestand ophalen

Wanneer u een klassiek VNets maakt in de Azure Portal, is de naam die u wilt weer geven niet de volledige naam die u gebruikt voor Power shell. Zo kan een VNet dat **TestVNet1** in de portal heet, een veel langere naam hebben in het netwerk configuratie bestand. De naam kan er ongeveer als volgt uitzien: **Group ClassicRG TestVNet1**. Wanneer u uw verbindingen maakt, is het belang rijk dat u de waarden gebruikt die u in het netwerk configuratie bestand ziet.

In de volgende stappen maakt u verbinding met uw Azure-account en downloadt en bekijkt u het netwerk configuratie bestand om de vereiste waarden voor uw verbindingen te verkrijgen.

1. Down load en installeer de meest recente versie van de Power shell-cmdlets voor Azure Service Management (SM). Zie [werken met Azure PowerShell](#powershell)voor meer informatie.

2. Open de Power shell-console met verhoogde bevoegdheden. Gebruik de volgende voor beelden om u te helpen verbinding te maken. U moet deze opdrachten lokaal uitvoeren met de module Power shell-Service beheer. Als u wilt overschakelen naar Service beheer, gebruikt u deze opdracht:

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
6. Het netwerk configuratie bestand exporteren en weer geven. Maak een map op de computer en exporteer vervolgens het netwerkconfiguratiebestand naar de map. In dit voor beeld wordt het netwerk configuratie bestand geëxporteerd naar **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Open het bestand met een tekst editor en Bekijk de namen voor uw VNets en sites. Deze namen zijn de namen die u gebruikt wanneer u uw verbindingen maakt.<br>VNet-namen worden vermeld als **VirtualNetworkSite name =**<br>Site namen worden vermeld als **LocalNetworkSiteRef name =**

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Stap 8: de VPN-gateway verbindingen maken

Wanneer alle vorige stappen zijn voltooid, kunt u de vooraf gedeelde IPsec/IKE-sleutels instellen en de verbinding maken. Deze reeks stappen maakt gebruik van Power shell. VNet-naar-VNet-verbindingen voor het klassieke implementatie model kunnen niet worden geconfigureerd in de Azure Portal.

In de voor beelden ziet u dat de gedeelde sleutel precies hetzelfde is. De gedeelde sleutel moet altijd overeenkomen. Zorg ervoor dat u de waarden in deze voor beelden vervangt door de exacte namen voor uw VNets en lokale netwerk sites.

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
3. Wacht totdat de verbindingen zijn geïnitialiseerd. Zodra de gateway is geïnitialiseerd, is de status geslaagd.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>Overwegingen voor VNet-naar-VNet voor klassieke VNets
* De virtuele netwerken kunnen zich in dezelfde of verschillende abonnementen bevindt.
* De virtuele netwerken kunnen zich in dezelfde of verschillende Azure-regio's (locaties) bevinden.
* Een Cloud service of een taakverdelings eindpunt kan geen virtuele netwerken omvatten, zelfs niet als ze met elkaar zijn verbonden.
* Voor het verbinden van meerdere virtuele netwerken hoeven geen VPN-apparaten te worden gebruikt.
* VNet-naar-VNet biedt ondersteuning voor het verbinden van virtuele netwerken van Azure. Het biedt geen ondersteuning voor het verbinden van virtuele machines of Cloud Services die niet zijn geïmplementeerd in een virtueel netwerk.
* VNet-naar-VNet vereist dynamische routerings gateways. Azure static-routerings gateways worden niet ondersteund.
* U kunt virtuele-netwerkverbindingen tegelijk gebruiken met multi-site-VPN’s. Er zijn Maxi maal 10 VPN-tunnels voor een VPN-gateway van een virtueel netwerk die verbinding maakt met andere virtuele netwerken of on-premises sites.
* De adresruimten van de virtuele netwerken en on-premises lokale netwerksites mogen elkaar niet overlappen. Door overlappende adres ruimten wordt het maken van virtuele netwerken of het uploaden van netcfg-configuratie bestanden mislukt.
* Redundante tunnels tussen een paar virtuele netwerken worden niet ondersteund.
* Alle VPN-tunnels voor het VNet, met inbegrip van P2S Vpn's, delen de beschik bare band breedte voor de VPN-gateway en dezelfde SLA voor de VPN-gateway in Azure.
* VNet-naar-VNet-verkeer wordt verplaatst over de Azure-backbone.

## <a name="next-steps"></a>Volgende stappen
Controleer uw verbindingen. Zie [een VPN gateway verbinding controleren](vpn-gateway-verify-connection-resource-manager.md).
