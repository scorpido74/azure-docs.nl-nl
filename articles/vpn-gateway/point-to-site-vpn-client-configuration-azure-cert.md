---
title: 'P2S VPN-clientconfiguratiebestanden & installeren: certificaatverificatie'
titleSuffix: Azure VPN Gateway
description: Maak en installeer Windows, Linux, Linux (strongSwan) en Mac OS X VPN-clientconfiguratiebestanden voor P2S-certificaatverificatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: d15efee635e131d658cd650b7f80eb9e670a0dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279414"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Vpn-clientconfiguratiebestanden maken en installeren voor native Azure-certificaatverificatie P2S-configuraties

VPN-clientconfiguratiebestanden zijn opgenomen in een zip-bestand. Configuratiebestanden bieden de instellingen die nodig zijn voor een native Windows-, Mac IKEv2 VPN- of Linux-clients om verbinding te maken met een virtueel netwerk via Point-to-Site-verbindingen die native Azure-certificaatverificatie gebruiken.

Clientconfiguratiebestanden zijn specifiek voor de VPN-configuratie voor het virtuele netwerk. Als er wijzigingen zijn in de VPN-configuratie van Point-to-Site nadat u de VPN-clientconfiguratiebestanden hebt gegenereerd, zoals het type VPN-protocol of verificatietype, moet u nieuwe VPN-clientconfiguratiebestanden voor uw gebruikersapparaten genereren. 

* Voor meer informatie over point-to-site-verbindingen leest u [About Point-to-Site VPN](point-to-site-about.md) (Over point-to-site-VPN).
* Zie [OpenVPN configureren voor P2S en](vpn-gateway-howto-openvpn.md) [OpenVPN-clients configureren](vpn-gateway-howto-openvpn-clients.md)voor OpenVPN-instructies.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN-clientconfiguratiebestanden genereren

Controleer voordat u begint of alle verbindende gebruikers een geldig certificaat hebben geïnstalleerd op het apparaat van de gebruiker. Zie [Een clientcertificaat installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)voor meer informatie over het installeren van een clientcertificaat.

U clientconfiguratiebestanden genereren met PowerShell of via de Azure-portal. Een van beide methoden retourneert hetzelfde zip-bestand. Rits het bestand uit om de volgende mappen weer te geven:

  * **WindowsAmd64** en **WindowsX86**, die de Windows 32-bits en 64-bits installer pakketten bevatten, respectievelijk. Het **WindowsAmd64** installer pakket is voor alle ondersteunde 64-bits Windows-clients, niet alleen Amd.
  * **Generiek**, dat algemene informatie bevat die wordt gebruikt om uw eigen VPN-clientconfiguratie te maken. De map Generic wordt geleverd als IKEv2 of SSTP+IKEv2 is geconfigureerd op de gateway. Als alleen SSTP is geconfigureerd, is de map Algemeen niet aanwezig.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Bestanden genereren met de Azure-portal

1. Navigeer in de Azure-portal naar de virtuele netwerkgateway voor het virtuele netwerk waarmee u verbinding wilt maken.
2. Klik op de pagina virtuele netwerkgateway op **Point-to-site-configuratie**.

   ![clientportal downloaden](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. Klik boven aan de configuratiepagina Point-to-site op **VPN-client downloaden.** Het duurt een paar minuten voordat het clientconfiguratiepakket is gegenereerd.
4. Uw browser geeft aan dat er een zip-bestand voor clientconfiguratie beschikbaar is. Het heeft dezelfde naam als uw gateway. Rits het bestand uit om de mappen weer te geven.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Bestanden genereren met PowerShell


1. Bij het genereren van VPN-clientconfiguratiebestanden is de waarde voor '-AuthenticationMethod' 'EapTls'. Genereer de VPN-clientconfiguratiebestanden met de volgende opdracht:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Kopieer de URL naar uw browser om het zip-bestand te downloaden en rits het bestand uit om de mappen weer te geven.

## <a name="windows"></a><a name="installwin"></a>Windows

U hetzelfde VPN-clientconfiguratiepakket gebruiken op elke Windows-clientcomputer, zolang de versie overeenkomt met de architectuur voor de client. Zie de sectie Point-to-Site van de VEELgestelde vragen over [vpn-gateway](vpn-gateway-vpn-faq.md#P2S)voor de lijst met clientbesturingssystemen die worden ondersteund.

>[!NOTE]
>U moet beheerdersrechten hebben op de Windows-clientcomputer waarvan u verbinding wilt maken.
>
>

Gebruik de volgende stappen om de native Windows VPN-client te configureren voor certificaatverificatie:

1. Selecteer de VPN-clientconfiguratiebestanden die overeenkomen met de architectuur van de Windows-computer. Kies voor een architectuur met 64-bits processor het installatiepakket ‘VpnClientSetupAmd64’. Kies voor een architectuur met 32-bits processor het installatiepakket ‘VpnClientSetupAmdX86’. 
2. Dubbelklik op het pakket om het te installeren. Als u een SmartScreen-melding ziet, klikt u op **Meer info** en vervolgens op **Toch uitvoeren**.
3. Ga op de clientcomputer naar **Netwerkinstellingen** en klik op **VPN.** De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt. 
4. Controleer of u een clientcertificaat op de clientcomputer hebt geïnstalleerd voordat u verbinding probeert te maken. Een clientcertificaat is vereist voor verificatie als u het systeemeigen verificatietype met Azure-certificaat gebruikt. Zie Certificaten genereren voor meer informatie over het genereren van [certificaten.](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert) Zie [Een clientcertificaat installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)voor informatie over het installeren van een clientcertificaat.

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 U moet de systeemeigen IKEv2 VPN-client handmatig configureren op elke Mac die verbinding met Azure zal maken. Azure biedt geen mobileconfig-bestand voor verificatie van systeemeigen Azure-certificaten. De **Generieke** bevat alle informatie die u nodig hebt voor configuratie. Als u de map Generic niet ziet in uw download, is IKEv2 waarschijnlijk niet geselecteerd als tunneltype. Houd er rekening mee dat de VPN-gateway Basic SKU GEEN IKEv2 ondersteunt. Genereer het zip-bestand opnieuw om de map Generic te verkrijgen nadat IKEv2 is geselecteerd.<br>De map Generic bevat de volgende bestanden:

* **VpnSettings.xml**, die belangrijke instellingen bevat, zoals serveradres en tunneltype. 
* **VpnServerRoot.cer**, dat het basiscertificaat bevat dat nodig is om de Azure VPN Gateway te valideren tijdens het instellen van p2S-verbindingen.

Gebruik de volgende stappen om de native VPN-client op Mac te configureren voor certificaatverificatie. U moet deze stappen uitvoeren op elke Mac die verbinding maakt met Azure:

1. Importeer het **VpnServerRoot-rootcertificaat** naar je Mac. Dit kan worden gedaan door het bestand naar uw Mac te kopiëren en erop te dubbelklikken. Klik **op Toevoegen** om te importeren.

   ![certificaat toevoegen](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dubbelklikken op het certificaat mag niet het dialoogvenster **Toevoegen** weergeven, maar het certificaat is wel in het juiste archief geïnstalleerd. U het certificaat controleren in de inlogsleutelhanger onder de categorie certificaten.
    >
  
2. Controleer of u een clientcertificaat hebt geïnstalleerd dat is uitgegeven door het hoofdcertificaat dat u naar Azure hebt geüpload toen u u P2S-instellingen configureerde. Dit is anders dan de VPNServerRoot die u in de vorige stap hebt geïnstalleerd. Het clientcertificaat wordt gebruikt voor verificatie en is vereist. Zie Certificaten genereren voor meer informatie over het genereren van [certificaten.](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert) Zie [Een clientcertificaat installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)voor informatie over het installeren van een clientcertificaat.
3. Open het dialoogvenster **Netwerk** onder **Netwerkvoorkeuren** en klik op **'+'** om een nieuw VPN-clientverbindingsprofiel te maken voor een P2S-verbinding met het virtuele Azure-netwerk.

   De **interfacewaarde** is 'VPN' en **VPN Type** value is 'IKEv2'. Geef een naam op voor het profiel in het veld **Servicenaam** en klik op **Maken** om het VPN-clientverbindingsprofiel te maken.

   ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Kopieer in de map **Generic** vanuit het bestand **VpnSettings.xml** de waarde van de **VpnServer-tag.** Plak deze waarde in de velden **Serveradres** en **Extern id** van het profiel.

   ![servergegevens](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klik **op Verificatie-instellingen** en selecteer **Certificaat**.Klik voor **Catalina**op **Geen** en vervolgens **op certificaat**

   ![verificatie-instellingen](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * Selecteer Voor Catalina **Geen** en vervolgens **Certificaat**. **Selecteer** het juiste certificaat:
   
   ![Catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Klik **op Selecteren...** om het clientcertificaat te kiezen dat u wilt gebruiken voor verificatie. Dit is het certificaat dat u hebt geïnstalleerd in stap 2.

   ![certificaat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Kies Een identiteit** geeft een lijst met certificaten weer waaruit u kiezen. Selecteer het juiste certificaat en klik op **Doorgaan**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Geef **in** het veld Lokale id de naam van het certificaat op (vanaf stap 6). In dit voorbeeld is het "ikev2Client.com". Klik vervolgens op **Knop Toepassen** om de wijzigingen op te slaan.

   ![toepassen](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Klik in het dialoogvenster **Netwerk** op **Toepassen** om alle wijzigingen op te slaan. Klik vervolgens op **Verbinding maken** om de P2S-verbinding met het virtuele Azure-netwerk te starten.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Installeer sterkSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Certificaten genereren

Als u nog geen certificaten hebt gegenereerd, gebruikt u de volgende stappen:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Installeren en configureren

De volgende instructies zijn gemaakt op Ubuntu 18.0.4. Ubuntu 16.0.10 ondersteunt geen strongSwan GUI. Als u Ubuntu 16.0.10 wilt gebruiken, moet u de [opdrachtregel](#linuxinstallcli)gebruiken. De onderstaande voorbeelden komen mogelijk niet overeen met schermen die u ziet, afhankelijk van uw versie van Linux en strongSwan.

1. Open de **Terminal** om **strongSwan** en zijn netwerkbeheer te installeren door de opdracht in het voorbeeld uit te voeren.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Selecteer **Instellingen**en selecteer **Vervolgens Netwerk**.

   ![verbindingen bewerken](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Klik **+** op de knop om een nieuwe verbinding te maken.

   ![een verbinding toevoegen](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Selecteer **IPsec/IKEv2 (strongSwan)** in het menu en dubbelklik. U uw verbinding in deze stap een naam geven.

   ![een verbindingstype kiezen](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Open het bestand **VpnSettings.xml** uit de **map Generic** in de gedownloade clientconfiguratiebestanden. Zoek de tag genaamd **VpnServer** en kopieer de naam, te beginnen met 'azuregateway' en eindigend met '.cloudapp.net'.

   ![kopieernaam](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Plak deze naam in het **veld Adres** van uw nieuwe VPN-verbinding in de sectie **Gateway.** Selecteer vervolgens het mappictogram aan het einde van **het** certificaatveld, blader naar de **map Algemeen** en selecteer het **VpnServerRoot-bestand.**
7. Selecteer **certificaat/privésleutel**in het gedeelte **Client** van de verbinding voor **Verificatie**. Kies **voor certificaat-** en **privésleutel**het certificaat en de persoonlijke sleutel die eerder zijn gemaakt. Selecteer in **Opties**de optie **Een innerlijk IP-adres aanvragen**. Klik vervolgens op **Toevoegen**.

   ![een innerlijk IP-adres aanvragen](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Schakel de verbinding **in**.

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Installeer sterkSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Certificaten genereren

Als u nog geen certificaten hebt gegenereerd, gebruikt u de volgende stappen:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installeren en configureren

1. Download het VPNClient-pakket van azure-portal.
2. Haal het bestand eruit.
3. Kopieer of verplaats de VpnServerRoot.cer vanuit de **map Generic** naar /etc/ipsec.d/cacerts.
4. Cp client.p12 kopiëren of verplaatsen naar /etc/ipsec.d/private/. Dit bestand is clientcertificaat voor Azure VPN Gateway.
5. Open vpnSettings.xml-bestand `<VpnServer>` en kopieer de waarde. U gebruikt deze waarde in de volgende stap.
6. Pas de waarden in het onderstaande voorbeeld aan en voeg het voorbeeld toe aan de configuratie /etc/ipsec.conf.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. Voeg het volgende toe aan */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Voer de volgende opdrachten uit:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Volgende stappen

Ga terug naar het artikel om [uw P2S-configuratie](vpn-gateway-howto-point-to-site-rm-ps.md)te voltooien.

Zie de volgende artikelen voor het oplossen van P2S-verbindingen:

  * [Azure point-to-site-verbindingen oplossen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Problemen met VPN-verbindingen van Vpn-clients van Mac OS X oplossen](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
