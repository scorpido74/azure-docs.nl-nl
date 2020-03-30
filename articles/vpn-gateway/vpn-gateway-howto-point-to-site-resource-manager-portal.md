---
title: 'Verbinding maken met een VNet via P2S VPN-& certificaatverificatie: portal'
titleSuffix: Azure VPN Gateway
description: Sluit Windows-, Mac OS X- en Linux-clients veilig aan op een virtueel Azure-netwerk met P2S en zelfondertekende of door CA uitgegeven certificaten. In dit artikel wordt gebruikgemaakt van Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: 013ebc2a1343c8eab3d477023e36660c93fa6da5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244483"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Een Point-to-Site VPN-verbinding configureren met een VNet met native Azure-certificaatverificatie: Azure-portal

Met dit artikel u afzonderlijke clients met Windows, Linux of Mac OS X veilig verbinden met een Azure VNet. P2S-verbindingen zijn nuttig als u verbinding wilt maken met uw VNet vanaf een externe locatie, bijvoorbeeld als u ook thuis werkt of op een congres verbinding wilt maken. U kunt P2S ook in plaats van een site-naar-site-VPN gebruiken wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Punt-naar-site-verbindingen hebben geen VPN-apparaat of openbaar IP-adres nodig. P2S maakt de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol) of IKEv2. Voor meer informatie over punt-naar-site-VPN leest u [About Point-to-Site VPN](point-to-site-about.md) (Over punt-naar-site-VPN).

![Diagram: een computer verbinden met een Azure VNet-punt-naar-site-verbinding](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architectuur

Native punt-naar-site-verbindingen voor certificaatverificatie in Azure gebruiken de volgende items, die u in deze oefening configureert:

* Een RouteBased VPN-gateway.
* De openbare sleutel (CER-bestand) voor een basiscertificaat dat is geüpload naar Azure. Nadat het certificaat is geüpload, wordt het beschouwd als een vertrouwd certificaat en wordt het gebruikt voor verificatie.
* Een clientcertificaat dat is gegenereerd op basis van het basiscertificaat. Het clientcertificaat dat is geïnstalleerd op elke clientcomputer die met het VNet verbinding zal maken. Dit certificaat wordt gebruikt voor clientverificatie.
* Een VPN-clientconfiguratie. De configuratiebestanden voor de VPN-client bevatten de gegevens die nodig zijn om de client te verbinden met het VNet. Met de bestanden wordt de bestaande VPN-client geconfigureerd die is ingebouwd in het besturingssysteem. Elke client die verbinding maakt, moet worden geconfigureerd met behulp van de instellingen in de configuratiebestanden.

#### <a name="example-values"></a><a name="example"></a>Voorbeeldwaarden

U kunt de volgende waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel:

* **VNet-naam:** VNet1
* **Adresruimte:** 10.1.0.0/16<br>In dit voorbeeld gebruiken we slechts één adresruimte. U kunt meer dan één adresruimte voor uw VNet hebben.
* **Subnetnaam:** Frontend
* **Subnetadresbereik:** 10.1.0.0/24
* **Abonnement:** controleer of u het juiste abonnement hebt in het geval u er meer dan één hebt.
* **Resourcegroep: **TestRG1
* **Locatie:** VS - oost
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Naam virtuele netwerkgateway:** VNet1GW (VNet1GW)
* **Gatewaytype:** Vpn
* **VPN-type:** Op route gebaseerd
* **Openbare IP-adresnaam:** VNet1GWpip
* **Verbindingstype:** Van point-to-site
* **Klantenadrespool:** 172.16.201.0/24<br>VPN-clients die verbinding maken met het VNet via deze punt-naar-site-verbinding, ontvangen een IP-adres van de clientadresgroep.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. Een virtueel netwerk maken

Controleer eerst of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Een virtuele netwerkgateway maken

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

>[!NOTE]
>De Basic gateway SKU ondersteunt geen IKEv2- of RADIUS-verificatie. Als u van plan bent Mac-clients verbinding te laten maken met uw virtuele netwerk, gebruikt u de Basic SKU niet.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. Certificaten genereren

Certificaten worden in Azure gebruikt om clients te verifiëren die verbinding willen maken met een VNet met behulp van een punt-naar-site-VPN-verbinding. Als u beschikt over het basiscertificaat, [uploadt](#uploadfile) u de gegevens van de openbare sleutel naar Azure. Het basiscertificaat wordt vervolgens als 'vertrouwd' beschouwd door Azure voor verbinding met het virtuele netwerk via P2S. U kunt ook clientcertificaten genereren op basis van het vertrouwde basiscertificaat en deze vervolgens op elke clientcomputer installeren. Het clientcertificaat wordt gebruikt om de client te verifiëren bij het maken van verbinding met het VNet. 

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. Het .cer-bestand voor het basiscertificaat verkrijgen

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. Een clientcertificaat genereren

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. De groep klantadres toevoegen

De clientadrespool bestaat uit een privé-IP-adresbereik dat u opgeeft. De clients die dynamisch verbinding maken via een punt-naar-site-VPN-verbinding krijgen een IP-adres uit dit bereik. Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding wilt maken. Als u meerdere protocollen configureert en SSTP een van de protocollen is, wordt de geconfigureerde adresgroep gelijkmatig verdeeld over de geconfigureerde protocollen.

1. Nadat de virtuele netwerkgateway is gemaakt, gaat u naar de sectie **Instellingen** van de pagina van de virtuele netwerkgateway. Selecteer **in** de sectie Instellingen **de optie Van punt naar locatie configuratie**. Selecteer **Nu configureren** om de configuratiepagina te openen.

   ![De pagina Punt-naar-site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-configure.png "Point-to-site configureren nu")
2. Op de **configuratiepagina Van punt tot site** u verschillende instellingen configureren. Als u tunneltype of verificatietype op deze pagina niet ziet, gebruikt uw gateway de BasissKU. De basis-SKU biedt geen ondersteuning voor IKEv2- of RADIUS-verificatie. Als u deze instellingen wilt gebruiken, moet u de gateway verwijderen en opnieuw maken met behulp van een andere gateway-SKU.

   [![Configuratiepagina van punt naar site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-address.png "adresgroep opgeven")](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-expanded.png#lightbox)
3. Voeg in het vak **Adresgroep** het bereik van het privé-IP-adres toe dat u wilt gebruiken. VPN-clients ontvangen dynamisch een IP-adres uit het bereik dat u opgeeft. Het minimale subnetmasker is 29 bits voor actief/passief en 28 bits voor actieve/actieve configuratie.
4. Naar de volgende sectie gaan om tunneltype te configureren.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. Tunneltype configureren

U kunt het tunneltype selecteren. De tunnelopties zijn OpenVPN, SSTP en IKEv2.

* De strongSwan-client op Android en Linux en de systeemeigen IKEv2 VPN-client op iOS en OS x gebruiken alleen de IKEv2-tunnel om verbinding te maken.
* Windows-clients proberen IKEv2 eerst en als dat geen verbinding maakt, vallen ze terug naar SSTP.
* U de OpenVPN-client gebruiken om verbinding te maken met het OpenVPN-tunneltype.

![Tunneltype](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel.png "tunneltype opgeven")

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. Verificatietype configureren

Selecteer **Azure-certificaat**voor **verificatietype**.

  ![Verificatietype](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication-type.png "verificatietype opgeven")

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Upload de openbare certificaatgegevens van het basiscertificaat

U kunt aanvullende vertrouwde basiscertificaatbestanden uploaden (maximaal 20). Als het uploaden is voltooid, kan Azure daarmee clients met een geïnstalleerd clientcertificaat (gemaakt op basis van het vertrouwde basiscertificaat) verifiëren. Upload de informatie van de openbare sleutel voor het basiscertificaat naar Azure.

1. Certificaten worden toegevoegd aan de pagina **Punt-naar-site-configuratie**, in de sectie **Basiscertificaat**.
2. Zorg ervoor dat u het basiscertificaat hebt geëxporteerd als een Base-64 encoded X.509 (.cer)-bestand. U moet het certificaat in deze indeling exporteren, zodat u het certificaat met een teksteditor kunt openen.
3. Open het certificaat met een teksteditor zoals Kladblok. Zorg er bij het kopiëren van de certificaatgegevens voor dat u de tekst als één ononderbroken regel kopieert zonder regelterugloop of regelinvoer. Mogelijk moet u de weergave in de teksteditor wijzigen naar Symbool weergeven/Alle tekens weergeven om de regelterugloop en regelinvoer te zien. Kopieer alleen het volgende gedeelte als één ononderbroken regel:

   ![Certificaatgegevens](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png "rootcertificaatgegevens kopiëren")
4. Plak de certificaatgegevens in het veld **Gegevens van openbaar certificaat**. **Geef** het certificaat een naam en selecteer **Opslaan**. U kunt maximaal 20 vertrouwde basiscertificaten toevoegen.

   ![Certificaatgegevens plakken](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png "certificaatgegevens plakken")
5. Selecteer **Opslaan** boven aan de pagina om alle configuratie-instellingen op te slaan.

   ![Configuratie opslaan](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png "configuratie opslaan")

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. Een geëxporteerd clientcertificaat installeren

Als u een P2S-verbinding wilt maken vanaf een andere clientcomputer dan de computer die u gebruikt om de clientcertificaten te genereren, moet u een clientcertificaat installeren. Wanneer u een clientcertificaat installeert, hebt u het wachtwoord nodig dat is gemaakt tijdens het exporteren van het clientcertificaat.

Zorg ervoor dat het certificaat is geëxporteerd als een PFX-bestand, samen met de volledige certificaatketen (dit is de standaardinstelling). Anders zijn de gegevens van het basiscertificaat niet aanwezig op de clientcomputer en kan de client niet worden geverifieerd.

Zie voor de installatiestappen [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md) (Een clientcertificaat installeren).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. Het VPN-clientconfiguratiepakket genereren en installeren

De configuratiebestanden van de VPN-clients bevatten de instellingen voor het configureren van apparaten om verbinding te maken met een VNet via een P2S-verbinding. Zie voor instructies voor het genereren en installeren van VPN-clientconfiguratiebestanden [Create and install VPN client configuration files for native Azure certificate authentication P2S configurations](point-to-site-vpn-client-configuration-azure-cert.md) (VPN-clientconfiguratiebestanden voor P2S-configuraties voor systeemeigen Azure-certificaatverificatie maken en installeren).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Verbinding maken met Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Verbinding maken vanaf een Windows-VPN-client

>[!NOTE]
>U moet beheerdersrechten hebben op de Windows-clientcomputer waarmee u de verbinding tot stand brengt.
>
>

1. Als u met uw VNet wilt verbinden, gaat u op de clientcomputer naar de VPN-verbindingen en zoekt u de VPN-verbinding die u hebt gemaakt. Deze heeft dezelfde naam als het virtuele netwerk. Selecteer **Verbinden**. Er verschijnt mogelijk een pop-upbericht dat verwijst naar het certificaat. Selecteer **Doorgaan** met het gebruik van verhoogde bevoegdheden.

2. Selecteer op de pagina **Verbindingsstatus** de optie **Verbinden** om de verbinding te starten. Als het scherm **Certificaat selecteren** wordt geopend, controleert u of het weergegeven clientcertificaat het certificaat is dat u voor de verbinding wilt gebruiken. Als dit niet het is, gebruikt u de vervolgkeuzepijl om het juiste certificaat te selecteren en selecteert u **OK**.

   ![VPN-client maakt verbinding met Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png "verbinding maken")
3. De verbinding is tot stand gebracht.

   ![Verbinding tot stand gebracht](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png "verbinding tot stand gebracht")

#### <a name="troubleshoot-windows-p2s-connections"></a>Problemen met Windows-P2S-verbindingen oplossen

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Verbinding maken vanaf een Mac-VPN-client

Zoek in het dialoogvenster Netwerk het clientprofiel dat u wilt gebruiken, geef de instellingen op via [vpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)en selecteer **Verbinding maken.**

Controleer [Installeren - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) voor gedetailleerde instructies. Als u problemen ondervindt bij het maken van verbinding, controleert u of de virtuele netwerkgateway geen BasisSKU gebruikt. Basic SKU wordt niet ondersteund voor Mac-clients.

  ![Mac-verbinding](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png "Verbinding maken")

## <a name="to-verify-your-connection"></a><a name="verify"></a>De verbinding verifiëren

Deze instructies zijn van toepassing op Windows-clients.

1. Als u wilt controleren of uw VPN-verbinding actief is, opent u een opdrachtprompt met verhoogde bevoegdheid en voert u *ipconfig/all* in.
2. Bekijk de resultaten. Het IP-adres dat u hebt ontvangen is een van de adressen binnen het adresbereik van de punt-naar-site-VPN-clientadresgroep die u hebt opgegeven in uw configuratie. De resultaten zijn vergelijkbaar met het volgende voorbeeld:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Verbinding maken met een virtuele machine

Deze instructies zijn van toepassing op Windows-clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Vertrouwde basiscertificaten toevoegen of verwijderen

U kunt vertrouwde basiscertificaat toevoegen in en verwijderen uit Azure. Wanneer u een basiscertificaat verwijdert, kunnen clients met een certificaat dat is gegenereerd op basis van dat basiscertificaat niet worden geverifieerd, en kunnen ze dus geen verbinding maken. Als u wilt dat clients kunnen worden geverifieerd en verbinding kunnen maken, moet u een nieuw clientcertificaat installeren dat is gegenereerd op basis van een basiscertificaat dat wordt vertrouwd (is geüpload) in Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat toevoegen

U kunt maximaal 20 vertrouwde .cer-basiscertificaatbestanden toevoegen aan Azure. Zie voor instructies de sectie [Een vertrouwd basiscertificaat uploaden](#uploadfile) in dit artikel.

### <a name="to-remove-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat verwijderen

1. Als u een vertrouwd basiscertificaat wilt verwijderen, gaat u naar de pagina **Punt-naar-site-configuratie** voor uw virtuele netwerkgateway.
2. Zoek in de sectie **Basiscertificaat** van de pagina het certificaat dat u wilt verwijderen.
3. Selecteer de ellips naast het certificaat en selecteer 'Verwijderen'.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Een clientcertificaat intrekken

U kunt clientcertificaten intrekken. Met de certificaatintrekkingslijst kunt u selectief punt-naar-site-verbinding weigeren op basis van afzonderlijke clientcertificaten. Dit is anders van het verwijderen van een vertrouwd basiscertificaat. Als u een vertrouwd .cer-basiscertificaat uit Azure verwijdert, wordt de toegang ingetrokken voor alle clientcertificaten die zijn gegenereerd/ondertekend door het ingetrokken basiscertificaat. Als u in plaats van een basiscertificaat een clientcertificaat intrekt, kunt u de certificaten die zijn gegenereerd op basis van het basiscertificaat, blijven gebruiken voor verificatie.

De algemene procedure is het basiscertificaat te gebruiken om de toegang te beheren op het team- of organisatieniveau, terwijl u ingetrokken clientcertificaten gebruikt voor nauwkeuriger toegangsbeheer bij afzonderlijke gebruikers.

### <a name="revoke-a-client-certificate"></a>Een clientcertificaat intrekken

U kunt een clientcertificaat intrekken door de vingerafdruk toe te voegen aan de intrekkingslijst.

1. Haal de vingerafdruk voor het clientcertificaat op. Zie [De duimafdruk van een certificaat ophalen](https://msdn.microsoft.com/library/ms734695.aspx)voor meer informatie.
2. Kopieer de gegevens naar een teksteditor en verwijder alle spaties, zodat u een doorlopende tekenreeks overhoudt.
3. Navigeer naar de pagina **Punt-naar-site-configuratie** van de virtuele netwerkgateway. Dit is de pagina die u ook hebt gebruikt voor het [uploaden van een vertrouwd basiscertificaat](#uploadfile).
4. In het gedeelte **Ingetrokken certificaten** voert u een beschrijvende naam in voor het certificaat (dit hoeft niet de CN van het certificaat te zijn).
5. Kopieer en plak de vingerafdruk naar het veld **Vingerafdruk**.
6. De vingerafdruk wordt gevalideerd en automatisch toegevoegd aan de intrekkingslijst. Er wordt een bericht op het scherm weergegeven met de melding dat de lijst wordt bijgewerkt. 
7. Wanneer het bijwerken is voltooid, kan het certificaat niet langer worden gebruikt om verbinding te maken. Clients die verbinding proberen te maken met het certificaat, ontvangen een bericht waarin wordt gemeld dat het certificaat niet meer geldig is.

## <a name="point-to-site-faq"></a><a name="faq"></a>Veelgestelde vragen over punt-naar-site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Volgende stappen
Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](https://docs.microsoft.com/azure/) voor meer informatie. Zie [Azure and Linux VM Network Overview](../virtual-machines/linux/azure-vm-network-overview.md) (Overzicht van Azure- en Linux-VM-netwerken) voor meer informatie over netwerken en virtuele machines.

Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshooting Azure point-to-site connections](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).
