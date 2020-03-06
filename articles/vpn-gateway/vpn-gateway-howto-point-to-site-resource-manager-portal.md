---
title: 'Verbinding maken met een VNet met behulp van P2S VPN-& certificaat verificatie: Portal'
titleSuffix: Azure VPN Gateway
description: Verbind Windows-, Mac OS X-en Linux-clients veilig met een virtueel Azure-netwerk met behulp van P2S en zelfondertekende of door certificerings instanties uitgegeven certificaten. In dit artikel wordt gebruikgemaakt van Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: 013ebc2a1343c8eab3d477023e36660c93fa6da5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373710"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Configureer een punt-naar-site-VPN-verbinding met een VNet met behulp van systeem eigen Azure-certificaat verificatie: Azure Portal

Dit artikel helpt u bij het veilig verbinden van afzonderlijke clients met Windows, Linux of Mac OS X naar een Azure-VNet. P2S-verbindingen zijn nuttig als u verbinding wilt maken met uw VNet vanaf een externe locatie, bijvoorbeeld als u ook thuis werkt of op een congres verbinding wilt maken. U kunt P2S ook in plaats van een site-naar-site-VPN gebruiken wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Punt-naar-site-verbindingen hebben geen VPN-apparaat of openbaar IP-adres nodig. P2S maakt de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol) of IKEv2. Voor meer informatie over punt-naar-site-VPN leest u [About Point-to-Site VPN](point-to-site-about.md) (Over punt-naar-site-VPN).

![Diagram: een computer verbinden met een Azure VNet-punt-naar-site-verbinding](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architectuur

Native punt-naar-site-verbindingen voor certificaatverificatie in Azure gebruiken de volgende items, die u in deze oefening configureert:

* Een RouteBased VPN-gateway.
* De openbare sleutel (CER-bestand) voor een basiscertificaat dat is geüpload naar Azure. Nadat het certificaat is geüpload, wordt het beschouwd als een vertrouwd certificaat en wordt het gebruikt voor verificatie.
* Een clientcertificaat dat is gegenereerd op basis van het basiscertificaat. Het clientcertificaat dat is geïnstalleerd op elke clientcomputer die met het VNet verbinding zal maken. Dit certificaat wordt gebruikt voor clientverificatie.
* Een VPN-clientconfiguratie. De configuratiebestanden voor de VPN-client bevatten de gegevens die nodig zijn om de client te verbinden met het VNet. Met de bestanden wordt de bestaande VPN-client geconfigureerd die is ingebouwd in het besturingssysteem. Elke client die verbinding maakt, moet worden geconfigureerd met behulp van de instellingen in de configuratiebestanden.

#### <a name="example"></a>Voorbeeldwaarden

U kunt de volgende waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel:

* **VNet-naam:** VNet1
* **Adres ruimte:** 10.1.0.0/16<br>In dit voorbeeld gebruiken we slechts één adresruimte. U kunt meer dan één adresruimte voor uw VNet hebben.
* **Subnetnaam:** FrontEnd
* **Adres bereik van subnet:** 10.1.0.0/24
* **Abonnement:** controleer of u het juiste abonnement hebt in het geval u er meer dan één hebt.
* **Resourcegroep:** TestRG1
* **Locatie:** VS - oost
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Gatewaynaam van het virtuele netwerk:** VNet1GW
* **Gatewaytype:** VPN
* **VPN-type:** op route gebaseerd
* **Openbare IP-adresnaam:** VNet1GWpip
* **Verbindingstype:** punt-naar-site-verbinding
* **Clientadresgroep:** 172.16.201.0/24<br>VPN-clients die verbinding maken met het VNet via deze punt-naar-site-verbinding, ontvangen een IP-adres van de clientadresgroep.

## <a name="createvnet"></a>1. een virtueel netwerk maken

Controleer eerst of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="creategw"></a>2. een virtuele netwerk gateway maken

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

>[!NOTE]
>De basis gateway-SKU biedt geen ondersteuning voor IKEv2-of RADIUS-verificatie. Als u van plan bent Mac-clients verbinding te laten maken met uw virtuele netwerk, moet u de basis-SKU niet gebruiken.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>3. certificaten genereren

Certificaten worden in Azure gebruikt om clients te verifiëren die verbinding willen maken met een VNet met behulp van een punt-naar-site-VPN-verbinding. Als u beschikt over het basiscertificaat, [uploadt](#uploadfile) u de gegevens van de openbare sleutel naar Azure. Het basiscertificaat wordt vervolgens als 'vertrouwd' beschouwd door Azure voor verbinding met het virtuele netwerk via P2S. U kunt ook clientcertificaten genereren op basis van het vertrouwde basiscertificaat en deze vervolgens op elke clientcomputer installeren. Het clientcertificaat wordt gebruikt om de client te verifiëren bij het maken van verbinding met het VNet. 

### <a name="getcer"></a>1. het. cer-bestand voor het basis certificaat verkrijgen

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2. Genereer een client certificaat

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>4. Voeg de client adres groep toe

De clientadrespool bestaat uit een privé-IP-adresbereik dat u opgeeft. De clients die dynamisch verbinding maken via een punt-naar-site-VPN-verbinding krijgen een IP-adres uit dit bereik. Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding wilt maken. Als u meerdere protocollen configureert en SSTP een van de protocollen is, wordt de geconfigureerde adres groep op dezelfde manier verdeeld tussen de geconfigureerde protocollen.

1. Nadat de virtuele netwerkgateway is gemaakt, gaat u naar de sectie **Instellingen** van de pagina van de virtuele netwerkgateway. Selecteer in de sectie **instellingen** de optie **punt-naar-site-configuratie**. Selecteer **nu configureren** om de configuratie pagina te openen.

   ![Punt-naar-site-pagina](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-configure.png "Punt-naar-site-configuratie nu configureren")
2. Op de pagina **punt-naar-site-configuratie** kunt u verschillende instellingen configureren. Als u het tunnel type of verificatie type niet op deze pagina ziet, wordt de basis-SKU gebruikt voor uw gateway. De basis-SKU biedt geen ondersteuning voor IKEv2- of RADIUS-verificatie. Als u deze instellingen wilt gebruiken, moet u de gateway verwijderen en opnieuw maken met behulp van een andere gateway-SKU.

   [![De pagina punt-naar-site-configuratie](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-address.png "adres groep opgeven")](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-expanded.png#lightbox)
3. Voeg in het vak **adres groep** het privé-IP-adres bereik toe dat u wilt gebruiken. VPN-clients ontvangen dynamisch een IP-adres uit het bereik dat u opgeeft. Het minimale subnetmasker is 29 bits voor actief/passief en 28 bits voor actieve/actieve configuratie.
4. Ga naar de volgende sectie om het tunnel type te configureren.

## <a name="tunneltype"></a>5. tunnel type configureren

U kunt het tunneltype selecteren. De tunnel opties zijn OpenVPN, SSTP en IKEv2.

* De strongSwan-client op Android en Linux en de systeemeigen IKEv2 VPN-client op iOS en OS x gebruiken alleen de IKEv2-tunnel om verbinding te maken.
* Windows-clients proberen eerst IKEv2 en als dat niet het geval is, gaan ze terug naar SSTP.
* U kunt de OpenVPN-client gebruiken om verbinding te maken met het tunnel type OpenVPN.

![Tunnel Type](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel.png "Geef het tunnel type op")

## <a name="authenticationtype"></a>6. verificatie type configureren

Selecteer voor **verificatie type** **Azure-certificaat**.

  ![Verificatie type](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication-type.png "verificatie type opgeven")

## <a name="uploadfile"></a>7. de gegevens van het open bare certificaat van het basis certificaat uploaden

U kunt aanvullende vertrouwde basiscertificaatbestanden uploaden (maximaal 20). Als het uploaden is voltooid, kan Azure daarmee clients met een geïnstalleerd clientcertificaat (gemaakt op basis van het vertrouwde basiscertificaat) verifiëren. Upload de informatie van de openbare sleutel voor het basiscertificaat naar Azure.

1. Certificaten worden toegevoegd aan de pagina **Punt-naar-site-configuratie**, in de sectie **Basiscertificaat**.
2. Zorg ervoor dat u het basiscertificaat hebt geëxporteerd als een Base-64 encoded X.509 (.cer)-bestand. U moet het certificaat in deze indeling exporteren, zodat u het certificaat met een teksteditor kunt openen.
3. Open het certificaat met een teksteditor zoals Kladblok. Zorg er bij het kopiëren van de certificaatgegevens voor dat u de tekst als één ononderbroken regel kopieert zonder regelterugloop of regelinvoer. Mogelijk moet u de weergave in de teksteditor wijzigen naar Symbool weergeven/Alle tekens weergeven om de regelterugloop en regelinvoer te zien. Kopieer alleen het volgende gedeelte als één ononderbroken regel:

   ![Certificaat gegevens](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png "basis certificaat gegevens kopiëren")
4. Plak de certificaatgegevens in het veld **Gegevens van openbaar certificaat**. Geef het certificaat een **naam** en selecteer vervolgens **Opslaan**. U kunt maximaal 20 vertrouwde basiscertificaten toevoegen.

   ![Certificaat gegevens plakken](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png "certificaat gegevens plakken")
5. Selecteer **Opslaan** boven aan de pagina om alle configuratie-instellingen op te slaan.

   ![Configuratie opslaan](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png "configuratie opslaan")

## <a name="installclientcert"></a>8. een geëxporteerd client certificaat installeren

Als u een P2S-verbinding wilt maken vanaf een andere clientcomputer dan de computer die u gebruikt om de clientcertificaten te genereren, moet u een clientcertificaat installeren. Wanneer u een clientcertificaat installeert, hebt u het wachtwoord nodig dat is gemaakt tijdens het exporteren van het clientcertificaat.

Zorg ervoor dat het certificaat is geëxporteerd als een PFX-bestand, samen met de volledige certificaatketen (dit is de standaardinstelling). Anders zijn de gegevens van het basiscertificaat niet aanwezig op de clientcomputer en kan de client niet worden geverifieerd.

Zie voor de installatiestappen [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md) (Een clientcertificaat installeren).

## <a name="clientconfig"></a>9. het configuratie pakket voor de VPN-client genereren en installeren

De configuratiebestanden van de VPN-clients bevatten de instellingen voor het configureren van apparaten om verbinding te maken met een VNet via een P2S-verbinding. Zie voor instructies voor het genereren en installeren van VPN-clientconfiguratiebestanden [Create and install VPN client configuration files for native Azure certificate authentication P2S configurations](point-to-site-vpn-client-configuration-azure-cert.md) (VPN-clientconfiguratiebestanden voor P2S-configuraties voor systeemeigen Azure-certificaatverificatie maken en installeren).

## <a name="connect"></a>10. verbinding maken met Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Verbinding maken vanaf een Windows-VPN-client

>[!NOTE]
>U moet beheerdersrechten hebben op de Windows-clientcomputer waarmee u de verbinding tot stand brengt.
>
>

1. Als u met uw VNet wilt verbinden, gaat u op de clientcomputer naar de VPN-verbindingen en zoekt u de VPN-verbinding die u hebt gemaakt. Deze heeft dezelfde naam als het virtuele netwerk. Selecteer **Verbinden**. Er verschijnt mogelijk een pop-upbericht dat verwijst naar het certificaat. Selecteer **door gaan** om verhoogde bevoegdheden te gebruiken.

2. Selecteer op de pagina **Verbindingsstatus** de optie **Verbinden** om de verbinding te starten. Als het scherm **Certificaat selecteren** wordt geopend, controleert u of het weergegeven clientcertificaat het certificaat is dat u voor de verbinding wilt gebruiken. Als dat niet het geval is, gebruikt u de vervolg keuze pijl om het juiste certificaat te selecteren en selecteert u **OK**.

   ![VPN-client maakt verbinding met Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png "verbinding maken")
3. De verbinding is tot stand gebracht.

   ![Verbinding tot stand gebracht](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png "verbinding tot stand gebracht")

#### <a name="troubleshoot-windows-p2s-connections"></a>Problemen met Windows-P2S-verbindingen oplossen

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Verbinding maken vanaf een Mac-VPN-client

Zoek in het dialoog venster netwerk het client profiel dat u wilt gebruiken, geef de instellingen op uit de [VpnSettings. XML](point-to-site-vpn-client-configuration-azure-cert.md#installmac)en selecteer vervolgens **verbinding maken**.

Controleer de [installatie-Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) voor gedetailleerde instructies. Als u problemen ondervindt bij het verbinding maken, controleert u of de gateway van het virtuele netwerk geen basis-SKU gebruikt. Basis-SKU wordt niet ondersteund voor Mac-clients.

  ![Mac-verbinding](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png "Verbinding maken")

## <a name="verify"></a>De verbinding verifiëren

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

## <a name="connectVM"></a>Verbinding maken met een virtuele machine

Deze instructies zijn van toepassing op Windows-clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>Vertrouwde basiscertificaten toevoegen of verwijderen

U kunt vertrouwde basiscertificaat toevoegen in en verwijderen uit Azure. Wanneer u een basiscertificaat verwijdert, kunnen clients met een certificaat dat is gegenereerd op basis van dat basiscertificaat niet worden geverifieerd, en kunnen ze dus geen verbinding maken. Als u wilt dat clients kunnen worden geverifieerd en verbinding kunnen maken, moet u een nieuw clientcertificaat installeren dat is gegenereerd op basis van een basiscertificaat dat wordt vertrouwd (is geüpload) in Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat toevoegen

U kunt maximaal 20 vertrouwde .cer-basiscertificaatbestanden toevoegen aan Azure. Zie voor instructies de sectie [Een vertrouwd basiscertificaat uploaden](#uploadfile) in dit artikel.

### <a name="to-remove-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat verwijderen

1. Als u een vertrouwd basiscertificaat wilt verwijderen, gaat u naar de pagina **Punt-naar-site-configuratie** voor uw virtuele netwerkgateway.
2. Zoek in de sectie **Basiscertificaat** van de pagina het certificaat dat u wilt verwijderen.
3. Selecteer het beletsel teken naast het certificaat en selecteer vervolgens verwijderen.

## <a name="revokeclient"></a>Een clientcertificaat intrekken

U kunt clientcertificaten intrekken. Met de certificaatintrekkingslijst kunt u selectief punt-naar-site-verbinding weigeren op basis van afzonderlijke clientcertificaten. Dit is anders van het verwijderen van een vertrouwd basiscertificaat. Als u een vertrouwd .cer-basiscertificaat uit Azure verwijdert, wordt de toegang ingetrokken voor alle clientcertificaten die zijn gegenereerd/ondertekend door het ingetrokken basiscertificaat. Als u in plaats van een basiscertificaat een clientcertificaat intrekt, kunt u de certificaten die zijn gegenereerd op basis van het basiscertificaat, blijven gebruiken voor verificatie.

De algemene procedure is het basiscertificaat te gebruiken om de toegang te beheren op het team- of organisatieniveau, terwijl u ingetrokken clientcertificaten gebruikt voor nauwkeuriger toegangsbeheer bij afzonderlijke gebruikers.

### <a name="revoke-a-client-certificate"></a>Een clientcertificaat intrekken

U kunt een clientcertificaat intrekken door de vingerafdruk toe te voegen aan de intrekkingslijst.

1. Haal de vingerafdruk voor het clientcertificaat op. Zie voor meer informatie [De vingerafdruk van een certificaat ophalen](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopieer de gegevens naar een teksteditor en verwijder alle spaties, zodat u een doorlopende tekenreeks overhoudt.
3. Navigeer naar de pagina **Punt-naar-site-configuratie** van de virtuele netwerkgateway. Dit is de pagina die u ook hebt gebruikt voor het [uploaden van een vertrouwd basiscertificaat](#uploadfile).
4. In het gedeelte **Ingetrokken certificaten** voert u een beschrijvende naam in voor het certificaat (dit hoeft niet de CN van het certificaat te zijn).
5. Kopieer en plak de vingerafdruk naar het veld **Vingerafdruk**.
6. De vingerafdruk wordt gevalideerd en automatisch toegevoegd aan de intrekkingslijst. Er wordt een bericht op het scherm weergegeven met de melding dat de lijst wordt bijgewerkt. 
7. Wanneer het bijwerken is voltooid, kan het certificaat niet langer worden gebruikt om verbinding te maken. Clients die verbinding proberen te maken met het certificaat, ontvangen een bericht waarin wordt gemeld dat het certificaat niet meer geldig is.

## <a name="faq"></a>Veelgestelde vragen over punt-naar-site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Volgende stappen
Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](https://docs.microsoft.com/azure/) voor meer informatie. Zie [Azure and Linux VM Network Overview](../virtual-machines/linux/azure-vm-network-overview.md) (Overzicht van Azure- en Linux-VM-netwerken) voor meer informatie over netwerken en virtuele machines.

Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshooting Azure point-to-site connections](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).
