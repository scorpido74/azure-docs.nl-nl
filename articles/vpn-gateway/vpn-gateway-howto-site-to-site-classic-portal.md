---
title: 'Een on-premises netwerk verbinden met een virtueel Azure-netwerk: site-naar-site-VPN (klassiek): Portal | Microsoft Docs'
description: Een IPSec-verbinding maken vanaf uw on-premises netwerk met een klassiek virtueel Azure-netwerk via het openbare internet.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/11/2020
ms.author: cherylmc
ms.openlocfilehash: 1f096993645aca6999667af88c91d3f55f79d914
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983050"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Een site-naar-site-verbinding maken met behulp van Azure Portal (klassiek)


In dit artikel leest u hoe u Azure Portal gebruikt om een site-naar-site-VPN-gatewayverbinding te maken vanaf uw on-premises netwerk naar het VNet. De stappen in dit artikel zijn van toepassing op het klassieke implementatie model en zijn niet van toepassing op het huidige implementatie model Resource Manager. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Een site-naar-site-VPN-gatewayverbinding wordt gebruikt om een on-premises netwerk via een IPsec-/IKE-VPN-tunnel (IKEv1 of IKEv2) te verbinden met een virtueel Azure-netwerk. Voor dit type verbinding moet er on-premises een VPN-apparaat aanwezig zijn waaraan een extern openbaar IP-adres is toegewezen. Zie [Overzicht van VPN Gateway](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN-gateways.

![Diagram: cross-premises site-naar-site-VPN-gatewayverbinding](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Controleer voordat u met de configuratie begint, of aan de volgende criteria is voldaan:

* U hebt vastgesteld dat u wilt werken met het klassieke implementatiemodel. Als u wilt werken met het Resource Manager-implementatiemodel, gaat u naar [Een site-naar-site-verbinding maken (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). We raden u aan indien mogelijk het Resource Manager-implementatiemodel te gebruiken.
* U hebt een compatibel VPN-apparaat nodig en iemand die dit kan configureren. Zie [Over VPN-apparaten](vpn-gateway-about-vpn-devices.md) voor meer informatie over compatibele VPN-apparaten en -apparaatconfiguratie.
* Controleer of u een extern gericht openbaar IPv4-adres voor het VPN-apparaat hebt.
* Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die u hierbij kan helpen en de benodigde gegevens kan verstrekken. Wanneer u deze configuratie maakt, moet u de IP-adresbereikvoorvoegsels opgeven die Azure naar uw on-premises locatie doorstuurt. Geen van de subnetten van uw on-premises netwerk kan overlappen met de virtuele subnetten waarmee u verbinding wilt maken.
* Power shell is vereist om de gedeelde sleutel op te geven en de VPN-gateway verbinding te maken. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Voorbeeld van configuratiewaarden voor deze oefening

In de voorbeelden in dit artikel worden de volgende waarden gebruikt. U kunt deze waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel.

* **VNet-naam:** TestVNet1
* **Adres ruimte:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (optioneel voor deze oefening)
* **Subnetten**
  * FrontEnd: 10.11.0.0/24
  * Back-end: 10.12.0.0/24 (optioneel voor deze oefening)
* **Gatewaysubnet**: 10.11.255.0/27
* **Resourcegroep: **TestRG1
* **Locatie:** VS - oost
* **DNS-server:** 10.11.0.3 (optioneel voor deze oefening)
* **Lokale sitenaam:** Site2
* **Clientadresruimte:** de adresruimte op uw on-premises site.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. een virtueel netwerk maken

Als u een virtueel netwerk maakt om te gebruiken met een S2S-verbinding, moet u ervoor zorgen dat de opgegeven adresruimten niet overlappen met een of meer van de clientadresruimten voor de lokale sites waarmee u verbinding wilt maken. Als u overlappende subnetten hebt, werkt de verbinding mogelijk niet goed.

* Als u al beschikt over een VNet, controleert u of de instellingen compatibel zijn met het ontwerp van de VPN-gateway. Let vooral op eventuele subnetten die met andere netwerken overlappen. 

* Als u nog geen virtueel netwerk hebt, maakt u er een. De schermafbeeldingen dienen alleen als voorbeeld. Zorg dat u de waarden vervangt door die van uzelf.

### <a name="to-create-a-virtual-network"></a>Een virtueel netwerk maken

1. Navigeer via een browser naar de [Azure Portal](https://portal.azure.com) en log, indien nodig, in met uw Azure-account.
2. Klik op **+ een resource maken*. Typ Virtual Network in het veld **Marketplace doorzoeken** . Zoek **Virtual Network** in de lijst die wordt weer gegeven en klik om de pagina **Virtual Network** te openen.
3. Klik op **(overschakelen naar klassiek)** en klik vervolgens op **maken**.
4. Configureer de VNet-instellingen op de pagina **Virtueel netwerk maken (klassiek)**. Voeg op deze pagina de eerste adresruimte en één adresbereik voor het subnet toe. Nadat u het VNet hebt gemaakt, kunt u teruggaan en extra subnetten en adres ruimten toevoegen.

   ![De pagina Virtueel netwerk maken](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "De pagina Virtueel netwerk maken")
5. Controleer of het **Abonnement** het juiste is. U kunt abonnementen wijzigen met behulp van de vervolgkeuzelijst.
6. Klik op **Resourcegroep** en selecteer een bestaande resourcegroep of maak een nieuwe resourcegroep door een naam in te voeren. Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md#resource-groups) (Overzicht van Azure Resource Manager) voor meer informatie over resourcegroepen.
7. Selecteer vervolgens de **Locatie**-instellingen voor uw VNet. De locatie bepaalt waar de resources die u naar dit VNet implementeert, worden opgeslagen.
8. Klik op **Maken** om het VNet te maken.
9. Wanneer u op Maken klikt, wordt er op het dashboard een tegel weergegeven die de voortgang van uw VNet weergeeft. De tegel wordt gewijzigd wanneer het VNet wordt gemaakt.

## <a name="2-add-additional-address-space"></a><a name="additionaladdress"></a>2. extra adres ruimte toevoegen

Nadat u het virtuele netwerk hebt gemaakt, kunt u extra adresruimte toevoegen. Het toevoegen van extra adresruimte geen vereist onderdeel van een S2S-configuratie, maar als u meerdere adresruimten nodig hebt, voert u de volgende stappen uit:

1. Zoek het virtuele netwerk in de portal.
2. Ga op de pagina van uw virtuele netwerk naar de sectie **Instellingen** en klik op **Adresruimte**.
3. Klik op de pagina Adresruimte op **+Toevoegen** en voer extra adresruimte in.

## <a name="3-specify-a-dns-server"></a><a name="dns"></a>3. Geef een DNS-server op

DNS-instellingen zijn geen vereist onderdeel van een S2S-configuratie, maar een DNS is nodig voor naamomzetting. Het opgeven van een waarde betekent niet dat er een nieuwe DNS-server wordt gemaakt. Het IP-adres van de DNS-server dat u opgeeft, moet het adres zijn van een DNS-server zijn die de namen kan omzetten voor de resources waarmee u verbinding maakt. Voor dit voorbeeld gebruiken we een privé-IP-adres. De kans is zeer klein dat het IP-adres uit het voorbeeld het IP-adres van uw DNS-server is. Zorg ervoor dat u uw eigen waarden gebruikt.

Nadat u uw virtuele netwerk hebt gemaakt, kunt u het IP-adres van een DNS-server toevoegen om de naamomzetting te verwerken. Open de instellingen voor het virtuele netwerk, klik op de DNS-servers en voeg het IP-adres toe van de DNS-server die u wilt gebruiken voor naamomzetting.

1. Zoek het virtuele netwerk in de portal.
2. Ga op de pagina voor uw virtuele netwerk naar de sectie **Instellingen** en klik op **DNS-servers**.
3. Voeg een DNS-server toe.
4. Klik boven aan de pagina op **Opslaan** om uw instellingen op te slaan.

## <a name="4-configure-the-local-site"></a><a name="localsite"></a>4. de lokale site configureren

De lokale site verwijst doorgaans naar uw on-premises locatie. Het bevat het IP-adres van het VPN-apparaat waarmee u verbinding gaat maken en de IP-adresbereiken die via de VPN-gateway naar het VPN-apparaat worden gerouteerd.

1. Klik op de pagina voor uw VNet onder **instellingen**op **diagram**.
1. Klik op de pagina **VPN-verbindingen** op geen **bestaande VPN-verbindingen. Klik hier om aan de slag te gaan**.
1. Voor het **verbindings type**, moet **u site-naar-site** geselecteerd laten.
4. Klik op **Lokale site - vereiste instellingen configureren** om de pagina **Lokale site** te openen. Configureer de instellingen en klik vervolgens op **OK** om de instellingen op te slaan.
   - **Naam:** geef de lokale site een naam waarmee u deze eenvoudig kunt identificeren.
   - **IP-adres voor de VPN-gateway:** dit is het openbare IP-adres van het VPN-apparaat voor uw on-premises netwerk. Voor het VPN-apparaat is een openbaar IPv4-adres vereist. Geef een geldig openbaar IP-adres op voor het VPN-apparaat waarmee u verbinding wilt maken. Het moet bereikbaar zijn voor Azure. Als u het IP-adres van het VPN-apparaat niet kent, kunt u altijd een tijdelijke aanduiding invoegen (zolang deze maar de indeling van een geldig openbaar IP-adres heeft) en dit later wijzigen.
   - **Clientadresruimte:** vermeld de IP-adresbereiken die u via deze gateway naar het lokale on-premises netwerk wilt routeren. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de hier opgegeven bereiken niet overlappen met bereiken van andere netwerken waarmee uw virtuele netwerk is verbonden of met de adresbereiken van het virtuele netwerk zelf.

   ![Lokale site](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Lokale site configureren")

Klik op **OK** om de pagina lokale site te sluiten. **Klik niet op OK om de pagina nieuwe VPN-verbinding te sluiten**.

## <a name="5-configure-the-gateway-subnet"></a><a name="gatewaysubnet"></a>5. het gateway-subnet configureren

U moet een gatewaysubnet maken voor de VPN-gateway. Het gatewaysubnet bevat de IP-adressen waarvan de VPN-gatewayservices gebruikmaken.


1. Schakel op de pagina **Nieuwe VPN-verbinding** het selectievakje **Gateway onmiddellijk maken** in. De pagina Optionele gatewayconfiguratie wordt weergegeven. Als u het selectievakje niet inschakelt, wordt de pagina voor het configureren van het gatewaysubnet niet weergegeven.

   ![Gateway configuratie-subnet, grootte, routerings type](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Gateway configuratie-subnet, grootte, routerings type")
2. Als u de pagina **Gatewayconfiguratie** wilt openen, klikt u op **Optionele gatewayconfiguratie - subnet, grootte en routingtype**.
3. Klik op de pagina **Gatewayconfiguratie** op **Subnet - vereiste instellingen configureren** om de pagina **Subnet toevoegen** te openen. Klik op **OK**als u klaar bent met het configureren van deze instellingen.

   ![Gateway configuratie-gateway-subnet](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Gateway configuratie-gateway-subnet")
4. Voeg op de pagina **Subnet toevoegen** het gatewaysubnet toe. De grootte van het gatewaysubnet dat u opgeeft, is afhankelijk van de VPN-gatewayconfiguratie die u wilt maken. Hoewel het mogelijk is om een gatewaysubnet van slechts /29 te maken, wordt minimaal /27 of /28 aanbevolen. U maakt dan een groter subnet dat meer adressen bevat. Met een groter gatewaysubnet beschikt u over voldoende IP-adressen voor mogelijke toekomstige configuraties.

   ![Gateway-subnet toevoegen](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Gateway-subnet toevoegen")

## <a name="6-specify-the-sku-and-vpn-type"></a><a name="sku"></a>6. Geef het type SKU en VPN op

1. Selecteer de gateway **Grootte**. Dit is de gateway-SKU die u gaat gebruiken om uw virtuele netwerkgateway te maken. Klassieke VPN-gateways gebruiken de oude (verouderde) gateway-SKU's. Zie [Working with virtual network gateway SKUs (old SKUs)](vpn-gateway-about-skus-legacy.md) (Werken met virtuele netwerkgateway-SKU's (oude SKU's)) voor meer informatie over de oude gateway-SKU's.

   ![SKUL en VPN-type selecteren](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Selecteer een SKU-en VPN-type")
2. Selecteer het **Routeringstype** voor uw gateway. Dit staat ook bekend als het VPN-type. Het is belang rijk dat u het juiste type selecteert, omdat u de gateway niet van het ene naar het andere type kunt converteren. Het VPN-apparaat moet compatibel zijn met het routingtype dat u selecteert. Zie [over VPN gateway-instellingen](vpn-gateway-about-vpn-gateway-settings.md#vpntype)voor meer informatie over het routerings type. Mogelijk ziet u artikelen die verwijzen naar de VPN-typen RouteBased en PolicyBased. Het type Dynamisch komt overeen met RouteBased, en het type Vast komt overeen met PolicyBased.
3. Klik op **OK** om de instellingen op te slaan.
4. Klik op de pagina **nieuwe VPN-verbinding** op **OK** onder aan de pagina om te beginnen met het implementeren van de gateway van uw virtuele netwerk. Afhankelijk van de SKU die u selecteert, duurt dit maximaal 45 minuten.

## <a name="7-configure-your-vpn-device"></a><a name="vpndevice"></a>7. uw VPN-apparaat configureren

Voor site-naar-site-verbindingen met een on-premises netwerk is een VPN-apparaat vereist. In deze stap configureert u het VPN-apparaat. Bij de configuratie van uw VPN-apparaat hebt u het volgende nodig:

- Een gedeelde sleutel. Dit is dezelfde gedeelde sleutel die u opgeeft wanneer u uw site-naar-site-VPN-verbinding maakt. In onze voorbeelden gebruiken we een eenvoudige gedeelde sleutel. We raden u aan een complexere sleutel te genereren.
- Het openbare IP-adres van de gateway van uw virtuele netwerk. U kunt het openbare IP-adres weergeven met behulp van Azure Portal, PowerShell of de CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="8-create-the-connection"></a><a name="CreateConnection"></a>8. Maak de verbinding
In deze stap stelt u de gedeelde sleutel in en maakt u de verbinding. De sleutel die u instelt, moet dezelfde sleutel zijn die is gebruikt bij de configuratie van het VPN-apparaat.

> [!NOTE]
> Deze stap is momenteel niet beschikbaar in Azure Portal. Gebruik de SM-versie (Service Management) van de Azure PowerShell-cmdlets. Zie [voordat u begint](#before) voor informatie over het installeren van deze cmdlets.
>

### <a name="step-1-connect-to-your-azure-account"></a>Stap 1. Verbinding maken met uw Azure-account

U moet deze opdrachten lokaal uitvoeren met de module Power shell-Service beheer. 

1. Open de Power shell-console met verhoogde bevoegdheden. Als u wilt overschakelen naar Service beheer, gebruikt u deze opdracht:

   ```powershell
   azure config mode asm
   ```
2. Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```powershell
   Add-AzureAccount
   ```
3. Controleer de abonnementen voor het account.

   ```powershell
   Get-AzureSubscription
   ```
4. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Stap 2. De gedeelde sleutel instellen en de verbinding maken

Wanneer u in de portal een klassiek VNet maakt (niet met behulp van Power shell), voegt Azure de naam van de resource groep toe aan de korte naam. Volgens Azure is de naam van het VNet dat u voor deze oefening hebt gemaakt bijvoorbeeld ' Group TestRG1 TestVNet1 ', niet ' TestVNet1 '. Power shell vereist de volledige naam van het virtuele netwerk, niet de korte naam die wordt weer gegeven in de portal. De lange naam is niet zichtbaar in de portal. De volgende stappen helpen u bij het exporteren van het netwerk configuratie bestand om de exacte waarden voor de naam van het virtuele netwerk op te halen. 

1. Maak een map op de computer en exporteer vervolgens het netwerkconfiguratiebestand naar de map. In dit voorbeeld wordt het netwerkconfiguratiebestand geëxporteerd naar C:\AzureNet.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Open het netwerkconfiguratiebestand met een xml-editor en controleer de waarden voor LocalNetworkSite name en VirtualNetworkSite name. Wijzig het voor beeld voor deze oefening zodat ze overeenkomen met de waarden in de XML. Gebruik voor een naam die spaties bevat, enkele aanhalingstekens rond de waarde.

3. Stel de gedeelde sleutel in en maak de verbinding. -SharedKey is een waarde die u genereert en opgeeft. In het voorbeeld wordt abc123 gebruikt, maar u kunt een ingewikkeldere waarde gebruiken. (aanbevolen) Het is van belang dat de waarde die u hier opgeeft, dezelfde waarde is die u hebt opgegeven bij het configureren van het VPN-apparaat.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   Wanneer de verbinding is gemaakt, is het resultaat: **Status: geslaagd**.

## <a name="9-verify-your-connection"></a><a name="verify"></a>9. Controleer uw verbinding

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Als u verbindingsproblemen ondervindt, raadpleegt u de sectie **Problemen oplossen** van de inhoudsopgave in het linkerdeelvenster.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Een VPN-gateway opnieuw instellen

Het opnieuw instellen van een Azure VPN-gateway is handig als u cross-premises VPN-connectiviteit verliest in een of meer Site-to-Site VPN-tunnels. In een dergelijke situatie functioneren al uw on-premises VPN-apparaten naar behoren, maar kunnen ze geen IPSec-tunnels tot stand brengen met de Azure VPN-gateways. Zie [Een VPN-gateway opnieuw instellen](vpn-gateway-resetgw-classic.md#resetclassic) voor de stappen.

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Een gateway-SKU wijzigen

Zie [Formaat van een gateway-SKU wijzigen](vpn-gateway-about-SKUS-legacy.md#classicresize) voor de stappen voor het wijzigen van een gateway-SKU.

## <a name="next-steps"></a>Volgende stappen

* Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](https://docs.microsoft.com/azure/) voor meer informatie.
* Zie [over geforceerde](vpn-gateway-about-forced-tunneling.md)tunneling voor meer informatie over geforceerde tunneling.
