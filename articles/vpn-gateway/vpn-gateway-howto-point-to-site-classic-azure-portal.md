---
title: 'Via een computer verbinding maken met een virtueel netwerk met behulp van een punt-naar-site-verbinding en verificatie via een certificaat: klassieke Azure Portal | Microsoft Docs'
description: Maak een klassieke punt-naar-site VPN-gatewayverbinding met Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: f68631771b8f86d995108112b1243ab38bf826bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984796"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Een punt-naar-site-verbinding configureren met behulp van verificatie via een certificaat (klassiek)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

In dit artikel wordt beschreven hoe u een VNet maakt met een punt-naar-site-verbinding. U maakt het VNet volgens het klassieke implementatiemodel via de Azure-portal. Deze configuratie maakt gebruik van certificaten, zelfondertekende door een certificeringsinstantie uitgegeven, om de client te verifiëren waarmee verbinding wordt gemaakt. U kunt deze configuratie ook maken met een ander implementatieprogramma of -model, met behulp van de opties die worden beschreven in de volgende artikelen:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

U gebruikt een punt-naar-site-VPN-gateway (P2S) om vanaf een afzonderlijke clientcomputer een beveiligde verbinding te maken met uw virtuele netwerk. Point-to-site-verbindingen zijn nuttig als u verbinding wilt maken met uw VNet vanaf een externe locatie. Een P2S-VPN is een uitstekende oplossing in plaats van een site-naar-site-VPN wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer.

> [!IMPORTANT]
> Het klassieke implementatiemodel ondersteunt alleen Windows VPN-clients en maakt gebruik van het SSTP-protocol (Secure Socket Tunneling Protocol), een VPN-protocol op basis van SSL. Voor de ondersteuning van niet-Windows VPN-clients, moet uw VNet worden gemaakt met het implementatiemodel van Resource Manager. Het Resource Manager-implementatiemodel biedt naast ondersteuning voor SSTP ook ondersteuning voor het VPN-type IKEv2. Zie [Informatie over P2S-verbindingen](point-to-site-about.md) voor meer informatie.
>
>

![Punt-naar-site-diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Vereisten

Voor punt-naar-site-verbindingen met verificatie via een certificaat is het volgende vereist:

* Een dynamische VPN-gateway.
* De openbare sleutel (CER-bestand) voor een basiscertificaat dat is geüpload naar Azure. Deze sleutel wordt beschouwd als een vertrouwd certificaat en wordt gebruikt voor verificatie.
* Op basis van het basiscertificaat wordt een clientcertificaat gegenereerd voor installatie op elke clientcomputer die wordt verbonden. Dit certificaat wordt gebruikt voor clientverificatie.
* Op elke clientcomputer die wordt verbonden, moet bovendien een VPN-clientconfiguratiepakket worden gegenereerd en geïnstalleerd. Het clientconfiguratiepakket configureert de systeemeigen VPN-client die zich al op het systeem bevindt met de benodigde gegevens om verbinding te maken met het VNet.

Punt-naar-site-verbindingen hebben geen VPN-apparaat of on-premises openbaar IP-adres nodig. De VPN-verbinding is gemaakt via SSTP (Secure Socket Tunneling Protocol). Wij ondersteunen SSTP versies 1.0, 1.1 en 1.2 aan de serverzijde. De client besluit welke versie moet worden gebruikt. Voor Windows 8.1 en hoger, gebruikt SSTP standaard 1.2. 

Voor meer informatie over point-to-site-verbindingen leest u [Point-to-Site FAQ](#point-to-site-faq) (Veelgestelde vragen over punt-naar-site-verbindingen).

### <a name="example-settings"></a>Voorbeeldinstellingen

Gebruik de volgende waarden om een testomgeving te maken of verwijs ernaar om meer inzicht te krijgen in de voorbeelden in dit artikel:

- **Instellingen voor (klassiek) virtueel netwerk maken**
   - **Naam**: Voer *VNet1*in.
   - **Adres ruimte**: Voer *192.168.0.0/16*in. In dit voorbeeld gebruiken we slechts één adresruimte. Zoals u in het diagram ziet, kunt u meer dan één adresruimte voor uw VNet hebben.
   - **Subnetnaam**: Voer een *frontend*in.
   - **Adres bereik van subnet**: Voer *192.168.1.0/24*in.
   - **Abonnement**: Selecteer een abonnement in de lijst met beschik bare abonnementen.
   - **Resource groep**: Voer *TestRG*in. Selecteer **Nieuwe maken** als de resourcegroep niet bestaat.
   - **Locatie**: Selecteer **VS-Oost** in de lijst.

  - **Instellingen voor VPN-verbinding**
    - **Verbindings type**: Selecteer **punt-naar-site**.
    - **Adres ruimte van client**: Voer *172.16.201.0/24*in. VPN-clients die verbinding maken met het VNet via deze punt-naar-site-verbinding, ontvangen een IP-adres van de opgegeven pool.

- **Configuratie-instellingen voor gatewaysubnet**
   - **Naam**: aangevuld met *GatewaySubnet*.
   - **Adres bereik**: Voer *192.168.200.0/24*in. 

- **Configuratie-instellingen voor gateway**:
   - **Grootte**: Selecteer de gateway-SKU die u wilt gebruiken.
   - **Routerings type**: Selecteer **dynamisch**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Een virtueel netwerk en een VPN-gateway maken

Controleer eerst of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Stap 1: Een virtueel netwerk maken

Als u nog geen virtueel netwerk (VNet) hebt, maakt u er een. De schermafbeeldingen dienen alleen als voorbeeld. Zorg dat u de waarden vervangt door die van uzelf. Volg de volgende stappen om een VNet te maken met behulp van Azure Portal:

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) of op de **Start** pagina de optie **een resource maken**. De pagina **Nieuw** wordt geopend.

2. Voer in het veld **Search the marketplace***virtueel netwerk* in en selecteer **Virtueel netwerk** in de geretourneerde lijst. De pagina **Virtueel netwerk** wordt geopend.

3. Selecteer in de lijst **Select a deployment model** de optie **Klassiek** en selecteer vervolgens **Maken**. De pagina **Virtueel netwerk maken** wordt geopend.

4. Configureer de VNet-instellingen op de pagina **Virtueel netwerk maken**. Voeg op deze pagina de eerste adresruimte en één adresbereik voor het subnet toe. Nadat u het VNet hebt aangemaakt, kunt u terugkeren en aanvullende subnets en adresruimten toevoegen.

   ![De pagina Virtueel netwerk maken](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Selecteer in de vervolgkeuzelijst het **abonnement** dat u wilt gebruiken.

6. Selecteer een bestaande **resourcegroep**. Of maak een nieuwe resourcegroep door **Nieuwe maken** te selecteren en een naam in te voeren. Geef de resourcegroep een naam die aansluit bij de geplande configuratiewaarden wanneer u een nieuwe resourcegroep maakt. Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md#resource-groups)voor meer informatie over resource groepen.

7. Selecteer een **locatie** voor uw VNet. Deze instelling bepaalt de geografische locatie van de resources die u voor dit VNet implementeert.

8. Selecteer **Maken** om het VNet te maken. Op de pagina **Meldingen** ziet u het bericht **Deployment in progress**.

8. Nadat het virtuele netwerk is gemaakt, verandert het bericht op de pagina **Meldingen** in **Implementatie voltooid**. Selecteer **Vastmaken aan dashboard** als u uw VNet gemakkelijk wilt terugvinden op het dashboard. 

10. Voeg een DNS-server toe (optioneel). Nadat u uw virtuele netwerk hebt gemaakt, kunt u het IP-adres van een DNS-server voor naamomzetting toevoegen. Het IP-adres dat u opgeeft voor de DNS-server, moet het adres zijn van een DNS-server die de namen van de resources in uw VNet kan omzetten.

    Als u een DNS-server wilt toevoegen, selecteert u **DNS-servers** op de VNet-pagina. Voer vervolgens het IP-adres in van de DNS-server die u wilt gebruiken en selecteer **Opslaan**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Deel 2: een gateway-subnet en een gateway voor dynamische route ring maken

In deze stap maakt u een gatewaysubnet en een gateway voor dynamische routering. In de Azure-portal voor het klassieke implementatiemodel maakt u het gatewaysubnet en de gateway via dezelfde configuratiepagina's. Gebruik het gatewaysubnet alleen voor de gatewayservices. Daarom moet u nooit zaken rechtstreeks implementeren in het gatewaysubnet, zoals virtuele machines of andere services.

1. Ga in het Azure Portal naar het virtuele netwerk waarvoor u een gateway wilt maken.

2. Selecteer op de pagina voor uw virtuele netwerk de optie **Overzicht** en selecteer in de sectie **VPN-verbindingen** de optie **Gateway**.

   ![Selecteren om een gateway te maken](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Selecteer op de pagina **Nieuwe VPN-verbinding** de optie **Punt-naar-site**.

   ![Verbindingstype punt-naar-site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Voor **Client-adresruimte** voegt u het IP-adresbereik toe vanwaaruit de VPN-clients een IP-adres ontvangen wanneer er verbinding wordt gemaakt. Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding maakt. U kunt het automatisch ingevulde bereik overschrijven met het privé-IP-adresbereik dat u wilt gebruiken. In dit voorbeeld ziet u het automatisch ingevulde bereik. 

   ![Clientadresruimte](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Selecteer **Gateway onmiddellijk maken** en vervolgens **Optionele gatewayconfiguratie** om de pagina **Gatewayconfiguratie** te openen.

   ![Optionele gatewayconfiguratie selecteren](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Selecteer op de pagina **Gatewayconfiguratie** de optie **Subnet** om het gatewaysubnet toe te voegen. Het is mogelijk om een gatewaysubnet van slechts /29 te maken. U wordt echter aangeraden een groter subnet met meer adressen te maken door ten minste /28 of /27 te selecteren. Hierdoor beschikt u over genoeg adressen voor eventuele aanvullende configuraties. Als u met gatewaysubnetten werkt, vermijd dan om een netwerkbeveiligingsgroep (NSG) te koppelen aan het gatewaysubnet. Een koppeling van een netwerkbeveiligingsgroep naar dit subnet zorgt er mogelijk voor dat uw VPN-gateway niet werkt zoals verwacht. Selecteer **OK** om deze instelling op te slaan.

   ![GatewaySubnet toevoegen](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Selecteer de gateway **Grootte**. De grootte is de gateway-SKU voor de gateway van uw virtuele netwerk. In de Azure-portal is de standaard-SKU ingesteld op **Standaard**. Zie [about VPN gateway Settings](vpn-gateway-about-vpn-gateway-settings.md#gwsku)(Engelstalig) voor meer informatie over Gateway-sku's.

   ![Grootte van de gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Selecteer het **Routeringstype** voor uw gateway. P2S-configuraties vereisen een **Dynamisch** routeringstype. Selecteer **OK** wanneer u klaar bent met het configureren van deze pagina.

   ![Routeringstype configureren](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Selecteer **OK** onder aan de pagina **Nieuwe VPN-verbinding** om de virtuele-netwerkgateway te gaan maken. Een VPN-gateway wordt binnen maximaal 45 minuten voltooid. De daadwerkelijke instelduur hangt af van de gateway-SKU die u selecteert.
 
## <a name="create-certificates"></a><a name="generatecerts"></a>Certificaten maken

Certificaten worden in Azure gebruikt om VPN-clients voor punt-naar-site-VPN's te verifiëren. U uploadt de informatie van de openbare sleutel over het basiscertificaat naar Azure. De openbare sleutel wordt dan beschouwd als *vertrouwd*. Clientcertificaten moeten worden gegenereerd op basis van het vertrouwde basiscertificaat en geïnstalleerd op elke clientcomputer. Dit gebeurt in het certificaatarchief Certificates-Current User\Personal\Certificates. Het certificaat wordt gebruikt om de client te verifiëren bij het maken van verbinding met het VNet. 

Als u zelfondertekende certificaten gebruikt, moeten ze worden gemaakt met behulp van specifieke parameters. U kunt een zelfondertekend certificaat maken met behulp van de instructies voor [PowerShell en Windows 10](vpn-gateway-certificates-point-to-site.md) of [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Het is belangrijk dat u de stappen volgt in deze instructies als u zelfondertekende basiscertificaten gebruikt en clientcertificaten uit het zelfondertekende basiscertificaat genereert. Anders zijn de certificaten die u maakt niet compatibel met P2S-verbindingen en treedt er een verbindingsfout op.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>De openbare sleutel (.cer) voor het basiscertificaat verkrijgen

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Een clientcertificaat genereren

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Het CER-bestand van het basiscertificaat uploaden

Wanneer de gateway is gemaakt, uploadt u het CER-bestand (dat de informatie over de openbare sleutel bevat) voor een vertrouwd basiscertificaat voor de Azure-server. Uploadt de persoonlijke sleutel voor het basiscertificaat niet. Nadat u het certificaat hebt geüpload, kan Azure daarmee clients met een geïnstalleerd clientcertificaat (gemaakt op basis van het vertrouwde basiscertificaat) verifiëren. Indien nodig kunt u later aanvullende vertrouwde basiscertificaatbestanden uploaden (maximaal 20).  

1. Selecteer in de sectie **VPN-verbindingen** van de pagina voor uw VNet de afbeelding clients om de pagina **Punt-naar-site VPN-verbinding** te openen.

   ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Selecteer op de pagina **Punt-naar-site-verbinding** de optie **Certificaat beheren** om de pagina **Certificaten** te openen.

   ![De pagina Certificaten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Selecteer op de pagina **Certificaten** de optie **Uploaden** om de pagina **Certificaat uploaden** te openen.

    ![De pagina Certificaat uploaden](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Selecteer de afbeelding van de map om het CER-bestand te zoeken. Selecteer het bestand en selecteer vervolgens **OK**. Het geüploade certificaat wordt weergegeven op de pagina **Certificaten**.

   ![Certificaat uploaden](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>De client configureren

Als u verbinding wilt maken met een VNet met behulp van een punt-naar-site-VPN, moet op elke client een pakket worden geïnstalleerd om de systeemeigen Windows VPN-client te configureren. Met het configuratiepakket wordt de systeemeigen Windows VPN-client geconfigureerd met de instellingen die nodig zijn om verbinding te maken met het virtuele netwerk.

U kunt hetzelfde configuratiepakket voor de VPN-client gebruiken op elke clientcomputer, mits de versie overeenkomt met de architectuur van de client. Zie de [Veelgestelde vragen over punt-naar-site-verbindingen](#point-to-site-faq) voor de lijst met ondersteunde clientbesturingssystemen.

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Een configuratiepakket voor VPN-clients genereren en installeren

1. Ga in de Azure-portal naar de pagina **Overzicht** voor uw VNet en selecteer bij **VPN-verbindingen** de clientafbeelding om de pagina **Punt-naar-site VPN-verbinding** te openen.

2. Selecteer op de pagina **Punt-naar-site VPN-verbinding** het downloadpakket dat overeenkomt met het besturingssysteem van de client waarop het wordt geïnstalleerd:

   * Selecteer voor 64-bits clients **VPN-clients (64-bits)**.
   * Selecteer voor 32-bits clients **VPN-clients (32-bits)**.

   ![Het configuratiepakket voor de VPN-client downloaden](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Nadat het pakket is gegenereerd, downloadt u het en installeert u het op de clientcomputer. Selecteer **Meer info** en vervolgens **Toch uitvoeren** als u een SmartScreen-melding ziet. U kunt het pakket ook opslaan en op andere clientcomputers installeren.

### <a name="install-a-client-certificate"></a>Een clientcertificaat installeren

Als u een P2S-verbinding wilt maken vanaf een andere clientcomputer dan de computer die u gebruikt om de clientcertificaten te genereren, moet u een clientcertificaat installeren. Wanneer u een clientcertificaat installeert, hebt u het wachtwoord nodig dat is gemaakt tijdens het exporteren van het clientcertificaat. Normaal gesproken kunt u het certificaat installeren door erop dubbel te klikken. Zie [Een geëxporteerd clientcertificaat installeren](vpn-gateway-certificates-point-to-site.md#install) voor meer informatie.


## <a name="connect-to-your-vnet"></a>Verbinding maken met uw VNet

>[!NOTE]
>U moet beheerdersrechten hebben op de clientcomputer waarmee u de verbinding tot stand brengt.
>
>

1. Als u verbinding wilt maken met uw VNet, gaat u op de client computer naar **VPN-verbindingen** in de Azure Portal en zoekt u de VPN-verbinding die u hebt gemaakt. De VPN-verbinding heeft dezelfde naam als het virtuele netwerk. Selecteer **Verbinding maken**. Als er een pop-upbericht verschijnt over het certificaat, selecteert u **Doorgaan** om verhoogde bevoegdheden te kunnen gebruiken.

2. Selecteer op de pagina **Verbindingsstatus** de optie **Verbinden** om de verbinding te starten. Als het scherm **Certificaat selecteren** verschijnt, controleert u of het weergegeven clientcertificaat het juiste is. Als dat niet zo is, selecteert u het juiste certificaat in de vervolgkeuzelijst en selecteert u **OK**.

3. Als er verbinding wordt gemaakt, ziet u de melding **Verbonden**.


### <a name="troubleshooting-p2s-connections"></a>Problemen met P2S-verbindingen oplossen

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>De VPN-verbinding controleren

1. Controleer of uw VPN-verbinding actief is. Open een opdrachtprompt met verhoogde bevoegdheid op de clientcomputer en voer **ipconfig/all** uit.
2. Bekijk de resultaten. Het IP-adres dat u hebt ontvangen, is een van de adressen binnen het adresbereik van de punt-naar-site-verbinding dat u hebt opgegeven tijdens het maken van het VNet. De resultaten moeten er als volgt uitzien:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="connect-to-a-virtual-machine"></a>Verbinding maken met een virtuele machine

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Vertrouwde basiscertificaten toevoegen of verwijderen

U kunt vertrouwde basiscertificaat toevoegen in en verwijderen uit Azure. Wanneer u een basiscertificaat verwijdert, kunnen clients met een certificaat dat is gegenereerd op basis van dat basiscertificaat niet meer worden geverifieerd en geen verbinding maken. Om deze clients opnieuw te kunnen verifiëren en verbinding te laten maken, moet u een nieuw clientcertificaat installeren dat is gegenereerd op basis van een basiscertificaat dat in Azure wordt vertrouwd.

### <a name="to-add-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat toevoegen

U kunt maximaal 20 vertrouwde .cer-basiscertificaatbestanden toevoegen aan Azure. Zie Het CER-bestand van het basiscertificaat uploaden voor instructies.

### <a name="to-remove-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat verwijderen

1. Selecteer in de sectie **VPN-verbindingen** van de pagina voor uw VNet de afbeelding clients om de pagina **Punt-naar-site VPN-verbinding** te openen.

   ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Selecteer op de pagina **Punt-naar-site-verbinding** de optie **Certificaat beheren** om de pagina **Certificaten** te openen.

   ![De pagina Certificaten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Selecteer op de pagina **Certificaten** de weglatingstekens naast het certificaat dat u wilt verwijderen en selecteer vervolgens **Verwijderen**.

   ![Basiscertificaat verwijderen](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Een clientcertificaat intrekken

U kunt zo nodig een clientcertificaat intrekken. Met de certificaatintrekkingslijst kunt u selectief punt-naar-site-verbinding weigeren op basis van afzonderlijke clientcertificaten. Deze methode wijkt af van het verwijderen van een vertrouwd basiscertificaat. Als u een vertrouwd .cer-basiscertificaat uit Azure verwijdert, wordt de toegang ingetrokken voor alle clientcertificaten die zijn gegenereerd/ondertekend door het ingetrokken basiscertificaat. Als u in plaats van een basiscertificaat een clientcertificaat intrekt, kunt u de certificaten die zijn gegenereerd op basis van het basiscertificaat, blijven gebruiken voor verificatie van de punt-naar-site-verbinding.

De algemene procedure is het basiscertificaat te gebruiken om de toegang te beheren op het team- of organisatieniveau, terwijl u ingetrokken clientcertificaten gebruikt voor nauwkeuriger toegangsbeheer bij afzonderlijke gebruikers.

### <a name="to-revoke-a-client-certificate"></a>Een clientcertificaat intrekken

U kunt een clientcertificaat intrekken door de vingerafdruk toe te voegen aan de intrekkingslijst.

1. Haal de vingerafdruk voor het clientcertificaat op. Zie voor meer informatie [De vingerafdruk van een certificaat ophalen](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopieer de gegevens naar een teksteditor en verwijder de spaties, zodat u een doorlopende tekenreeks overhoudt.
3. Ga naar het klassieke virtuele netwerk. Selecteer **Punt-naar-site-verbinding** en vervolgens **Certificaat beheren** om de pagina **Certificaten** te openen.
4. Selecteer **Intrekkingslijst** om de pagina **Intrekkingslijst** te openen. 
5. Selecteer **Certificaat toevoegen** om de pagina **Certificaat toevoegen aan intrekkingslijst** te openen.
6. Plak in **Vingerafdruk** de vingerafdruk van het certificaat als een doorlopende regel tekst, zonder spaties. Selecteer ten slotte **OK**.

Wanneer het bijwerken is voltooid, kan het certificaat niet langer worden gebruikt om verbinding te maken. Clients die verbinding proberen te maken met behulp van het certificaat, ontvangen een bericht waarin wordt gemeld dat het certificaat niet meer geldig is.

## <a name="point-to-site-faq"></a>Veelgestelde vragen over punt-naar-site-verbindingen

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Volgende stappen

- Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](https://docs.microsoft.com/azure/) voor meer informatie. 

- Zie [Overzicht van VM-netwerken in Azure en onder Linux](../virtual-machines/linux/network-overview.md) voor meer informatie over netwerken en virtuele Linux-machines.

- Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshoot Azure point-to-site connections](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).
