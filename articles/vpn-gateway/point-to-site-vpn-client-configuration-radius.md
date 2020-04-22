---
title: 'Azure VPN-gateway: maak & VPN-clientconfig-bestanden te installeren - P2S RADIUS-verbindingen'
description: Maak Windows-, Mac OS X- en Linux VPN-clientconfiguratiebestanden voor verbindingen die RADIUS-verificatie gebruiken.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 69517d69a26364cf1cc950d7aaa849522decacf1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732741"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>VPN-clientconfiguratiebestanden maken en installeren voor P2S RADIUS-verificatie

Als u verbinding wilt maken met een virtueel netwerk via point-to-site (P2S), moet u het clientapparaat configureren waarvan u verbinding maakt. U P2S VPN-verbindingen maken vanaf Windows-, Mac OS X- en Linux-clientapparaten. 

Wanneer u RADIUS-verificatie gebruikt, zijn er meerdere verificatieopties: gebruikersnaam/wachtwoordverificatie, certificaatverificatie en andere verificatietypen. De VPN-clientconfiguratie is verschillend voor elk type verificatie. Om de VPN-client te configureren, gebruikt u clientconfiguratiebestanden die de vereiste instellingen bevatten. Met dit artikel u de VPN-clientconfiguratie maken en installeren voor het RADIUS-verificatietype dat u wilt gebruiken.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

De configuratieworkflow voor P2S RADIUS-verificatie is als volgt:

1. [Stel de Azure VPN-gateway in voor P2S-connectiviteit.](point-to-site-how-to-radius-ps.md)
2. [Stel uw RADIUS-server in voor verificatie.](point-to-site-how-to-radius-ps.md#radius) 
3. **Verkrijg de VPN-clientconfiguratie voor de verificatieoptie van uw keuze en gebruik deze om de VPN-client in te stellen** (dit artikel).
4. [Voltooi uw P2S-configuratie en maak verbinding.](point-to-site-how-to-radius-ps.md)

>[!IMPORTANT]
>Als er wijzigingen zijn in de point-to-site VPN-configuratie nadat u het VPN-clientconfiguratieprofiel hebt gegenereerd, zoals het type VPN-protocol of verificatietype, moet u een nieuwe VPN-clientconfiguratie genereren en installeren op de apparaten van uw gebruikers.
>
>

Als u de secties in dit artikel wilt gebruiken, bepaalt u eerst welk type verificatie u wilt gebruiken: gebruikersnaam/wachtwoord, certificaat of andere vormen van verificatie. Elke sectie heeft stappen voor Windows, Mac OS X en Linux (beperkte stappen beschikbaar op dit moment).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Verificatie van gebruikersnaam/wachtwoord

U verificatie van gebruikersnaam/wachtwoord configureren om Active Directory te gebruiken of Active Directory niet te gebruiken. Met beide scenario's moet u ervoor zorgen dat alle verbindende gebruikers gebruikersnaam/wachtwoordreferenties hebben die via RADIUS kunnen worden geverifieerd.

Wanneer u verificatie van gebruikersnaam/wachtwoord configureert, u alleen een configuratie maken voor het EAP-MSCHAPv2-verificatieprotocol voor gebruikersnaam/wachtwoord. In de commando's, `-AuthenticationMethod` is `EapMSChapv2`.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a>1. VPN-clientconfiguratiebestanden genereren

U de CONFIGURATIEbestanden van de VPN-client genereren met behulp van de Azure-portal of met Azure PowerShell.

#### <a name="azure-portal"></a>Azure Portal

1. Navigeer naar de virtuele netwerkgateway.
2. Klik **op Point-to-Site-configuratie**.
3. Klik **op VPN-client downloaden**.
4. Selecteer de client en vul alle gevraagde informatie in.
5. Klik **op Downloaden** om het .zip-bestand te genereren.
6. Het .zip-bestand wordt gedownload, meestal naar de map Downloads.

#### <a name="azure-powershell"></a>Azure PowerShell

Vpn-clientconfiguratiebestanden genereren voor gebruik met verificatie van gebruikersnaam/wachtwoord. U de VPN-clientconfiguratiebestanden genereren met behulp van de volgende opdracht:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Als u de opdracht uitvoert, wordt een koppeling geretourneerd. Kopieer en plak de link naar een webbrowser om **VpnClientConfiguration.zip**te downloaden. Rits het bestand uit om de volgende mappen weer te geven: 
 
* **WindowsAmd64** en **WindowsX86**: Deze mappen bevatten respectievelijk de Windows 64-bits en 32-bits installatiepakketten. 
* **Algemeen:** deze map bevat algemene informatie die u gebruikt om uw eigen VPN-clientconfiguratie te maken. U hebt deze map niet nodig voor configuratie van verificatie van gebruikersnaam/wachtwoord.
* **Mac:** Als u IKEv2 hebt geconfigureerd toen u de virtuele netwerkgateway hebt gemaakt, ziet u een map met de naam **Mac** die een **mobileconfig-bestand** bevat. U gebruikt dit bestand om Mac-clients te configureren.

Als u al clientconfiguratiebestanden hebt gemaakt, `Get-AzVpnClientConfiguration` kunt u deze ophalen met behulp van de cmdlet. Maar als u wijzigingen aanbrengt in uw P2S VPN-configuratie, zoals het type VPN-protocol of verificatietype, wordt de configuratie niet automatisch bijgewerkt. U moet `New-AzVpnClientConfiguration` de cmdlet uitvoeren om een nieuwe configuratiedownload te maken.

Als u eerder gegenereerde clientconfiguratiebestanden wilt ophalen, gebruikt u de volgende opdracht:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. VPN-clients configureren

U de volgende VPN-clients configureren:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux met behulp van strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Windows VPN-clientinstellen

U hetzelfde VPN-clientconfiguratiepakket gebruiken op elke Windows-clientcomputer, zolang de versie overeenkomt met de architectuur voor de client. Zie de [veelgestelde vragen voor](vpn-gateway-vpn-faq.md#P2S)de lijst met clientbesturingssystemen die worden ondersteund.

Gebruik de volgende stappen om de native Windows VPN-client te configureren voor certificaatverificatie:

1. Selecteer de VPN-clientconfiguratiebestanden die overeenkomen met de architectuur van de Windows-computer. Kies voor een 64-bits processorarchitectuur het **VpnClientSetupAmd64-installatiepakket.** Kies voor een 32-bits processorarchitectuur het **VpnClientSetupX86-installatiepakket.** 
2. Als u het pakket wilt installeren, dubbelklikt u erop. Als u een SmartScreen-pop-up ziet, selecteert u **Meer info** > **Uitvoeren.**
3. Blader op de clientcomputer naar **Netwerkinstellingen** en selecteer **VPN.** De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Mac (OS X) VPN-clientinstellen

1. Selecteer het **VpnClientSetup mobileconfig-bestand** en stuur het naar elk van de gebruikers. U e-mail of een andere methode gebruiken.

2. Zoek het **mobileconfig-bestand** op de Mac.

   ![Locatie van het mobileconfig-bestand](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Optionele stap - Als u een aangepaste DNS wilt opgeven, voegt u de volgende regels toe aan het **mobileconfig-bestand:**

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Dubbelklik op het profiel om het te installeren en selecteer **Doorgaan**. De profielnaam is dezelfde als de naam van uw virtuele netwerk.

   ![Installatiebericht](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Selecteer **Doorgaan** om de afzender van het profiel te vertrouwen en ga verder met de installatie.

   ![Bevestigingsbericht](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Tijdens de profielinstallatie hebt u de mogelijkheid om de gebruikersnaam en het wachtwoord voor VPN-verificatie op te geven. Het is niet verplicht om deze informatie in te voeren. Als u dit doet, wordt de informatie opgeslagen en automatisch gebruikt wanneer u een verbinding start.Selecteer **Installeren** om door te gaan.

   ![Gebruikersnaam en wachtwoordvakken voor VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Voer een gebruikersnaam en wachtwoord in voor de bevoegdheden die nodig zijn om het profiel op uw computer te installeren. Selecteer **OK**.

   ![Gebruikersnaam- en wachtwoordvakken voor profielinstallatie](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Nadat het profiel is geïnstalleerd, is het zichtbaar in het dialoogvenster **Profielen.** U dit dialoogvenster later ook openen vanuit **Systeemvoorkeuren.**

   ![Dialoogvenster 'Profielen'](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Als u toegang wilt krijgen tot de VPN-verbinding, opent u het dialoogvenster **Netwerk** vanuit **Systeemvoorkeuren**.

   ![Pictogrammen in systeemvoorkeuren](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. De VPN-verbinding wordt weergegeven als **IkeV2-VPN.** U de naam wijzigen door het **mobileconfig-bestand bij te** werken.

    ![Details voor de VPN-verbinding](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Selecteer **Verificatie-instellingen**. Selecteer **Gebruikersnaam** in de lijst en voer uw referenties in. Als u de referenties eerder hebt ingevoerd, wordt **gebruikersnaam** automatisch gekozen in de lijst en worden de gebruikersnaam en het wachtwoord vooraf ingevuld. Selecteer **OK** om de instellingen op te slaan.

    ![Verificatie-instellingen](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Selecteer Opnieuw in het dialoogvenster **Netwerk** de optie **Toepassen** om de wijzigingen op te slaan. Als u de verbinding wilt starten, selecteert u **Verbinding maken**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Linux VPN client setup via strongSwan

De volgende instructies zijn gemaakt via strongSwan 5.5.1 op Ubuntu 17.0.4. Werkelijke schermen kunnen anders zijn, afhankelijk van uw versie van Linux en strongSwan.

1. Open de **Terminal** om **strongSwan** en zijn netwerkbeheer te installeren door de opdracht in het voorbeeld uit te voeren. Als u een fout ontvangt `libcharon-extra-plugins`die verband `strongswan-plugin-eap-mschapv2`houdt met , vervangt u deze door .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecteer het pictogram **Netwerkbeheer** (pijl-omhoog/pijl-omlaag) en selecteer **Verbindingen bewerken**.

   ![Selectie 'Verbindingen bewerken' in Netwerkbeheer](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Selecteer de knop **Toevoegen** om een nieuwe verbinding te maken.

   ![Knop 'Toevoegen' voor een verbinding](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Selecteer **IPsec/IKEv2 (strongswan)** in het vervolgkeuzemenu en selecteer **Vervolgens Maken**. U uw verbinding in deze stap een andere naam geven.

   ![Het verbindingstype selecteren](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Open het bestand **VpnSettings.xml** vanuit de **map Generic** van de gedownloade clientconfiguratiebestanden. Zoek de `VpnServer` tag genaamd en kopieer `azuregateway` de `.cloudapp.net`naam, te beginnen met en eindigend met .

   ![Inhoud van het bestand VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Plak deze naam in het **veld Adres** van uw nieuwe VPN-verbinding in de sectie **Gateway.** Selecteer vervolgens het mappictogram aan het einde van **het** certificaatveld, blader naar de **map Algemeen** en selecteer het **VpnServerRoot-bestand.**
7. Selecteer **e-ap** voor verificatie en voer in het gedeelte **Client** van de verbinding eap voor **verificatie**in en voer uw gebruikersnaam en wachtwoord in. Mogelijk moet u het vergrendelingspictogram aan de rechterkant selecteren om deze informatie op te slaan. Selecteer vervolgens **Opslaan**.

   ![Verbindingsinstellingen bewerken](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Selecteer het pictogram **Netwerkbeheer** (pijl-omhoog/pijl-omlaag) en plaats de plaats boven **VPN-verbindingen**. U ziet de VPN-verbinding die u hebt gemaakt. Als u de verbinding wilt starten, selecteert u deze.

   !["VPN Radius"-verbinding in Network Manager](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Verificatie via certificaat
 
U VPN-clientconfiguratiebestanden maken voor RADIUS-certificaatverificatie die het EAP-TLS-protocol gebruikt. Doorgaans wordt een door een onderneming uitgegeven certificaat gebruikt om een gebruiker te authenticeren voor VPN. Zorg ervoor dat alle verbindingsgebruikers een certificaat op hun apparaten hebben geïnstalleerd en dat uw RADIUS-server het certificaat kan valideren.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

In de commando's, `-AuthenticationMethod` is `EapTls`. Tijdens certificaatverificatie valideert de client de RADIUS-server door het certificaat te valideren. `-RadiusRootCert`is het .cer-bestand dat het hoofdcertificaat bevat dat wordt gebruikt om de RADIUS-server te valideren.

Elk VPN-clientapparaat vereist een geïnstalleerd clientcertificaat. Soms heeft een Windows-apparaat meerdere clientcertificaten. Tijdens de verificatie kan dit resulteren in een pop-updialoogvenster met alle certificaten. De gebruiker moet vervolgens het te gebruiken certificaat kiezen. Het juiste certificaat kan worden gefilterd door het basiscertificaat op te geven waaraan het clientcertificaat moet worden geketend. 

`-ClientRootCert`is het .cer-bestand dat het hoofdcertificaat bevat. Het is een optionele parameter. Als het apparaat waarvan u verbinding wilt maken slechts één clientcertificaat heeft, hoeft u deze parameter niet op te geven.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. VPN-clientconfiguratiebestanden genereren

Vpn-clientconfiguratiebestanden genereren voor gebruik met certificaatverificatie. U de VPN-clientconfiguratiebestanden genereren met behulp van de volgende opdracht:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Als u de opdracht uitvoert, wordt een koppeling geretourneerd. Kopieer en plak de link naar een webbrowser om VpnClientConfiguration.zip te downloaden. Rits het bestand uit om de volgende mappen weer te geven:

* **WindowsAmd64** en **WindowsX86**: Deze mappen bevatten respectievelijk de Windows 64-bits en 32-bits installatiepakketten. 
* **GenericDevice**: Deze map bevat algemene informatie die wordt gebruikt om uw eigen VPN-clientconfiguratie te maken.

Als u al clientconfiguratiebestanden hebt gemaakt, `Get-AzVpnClientConfiguration` kunt u deze ophalen met behulp van de cmdlet. Maar als u wijzigingen aanbrengt in uw P2S VPN-configuratie, zoals het type VPN-protocol of verificatietype, wordt de configuratie niet automatisch bijgewerkt. U moet `New-AzVpnClientConfiguration` de cmdlet uitvoeren om een nieuwe configuratiedownload te maken.

Als u eerder gegenereerde clientconfiguratiebestanden wilt ophalen, gebruikt u de volgende opdracht:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. VPN-clients configureren

U de volgende VPN-clients configureren:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (ondersteund, nog geen artikelstappen)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Windows VPN-clientinstellen

1. Selecteer een configuratiepakket en installeer het op het clientapparaat. Kies voor een 64-bits processorarchitectuur het **VpnClientSetupAmd64-installatiepakket.** Kies voor een 32-bits processorarchitectuur het **VpnClientSetupX86-installatiepakket.** Als u een SmartScreen-pop-up ziet, selecteert u **Meer info** > **Uitvoeren.** U kunt het pakket ook opslaan en op andere clientcomputers installeren.
2. Elke client vereist een clientcertificaat voor verificatie. Installeer het clientcertificaat. Zie [Clientcertificaten voor clientcertificaten voor client-to-site voor](vpn-gateway-certificates-point-to-site.md)informatie over clientcertificaten. Zie [Een certificaat installeren op Windows-clients](point-to-site-how-to-vpn-client-install-azure-cert.md)als u een certificaat wilt installeren dat is gegenereerd.
3. Blader op de clientcomputer naar **Netwerkinstellingen** en selecteer **VPN.** De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Mac (OS X) VPN-clientinstellen

U moet een apart profiel maken voor elk Mac-apparaat dat verbinding maakt met het virtuele Azure-netwerk. Dit komt omdat voor deze apparaten het gebruikerscertificaat moet worden opgegeven voor verificatie in het profiel. De **map Algemeen** bevat alle informatie die nodig is om een profiel te maken:

* **VpnSettings.xml** bevat belangrijke instellingen zoals serveradres en tunneltype.
* **VpnServerRoot.cer** bevat het rootcertificaat dat nodig is om de VPN-gateway te valideren tijdens het instellen van de P2S-verbinding.
* **RadiusServerRoot.cer** bevat het basiscertificaat dat nodig is om de RADIUS-server te valideren tijdens verificatie.

Gebruik de volgende stappen om de native VPN-client op een Mac te configureren voor certificaatverificatie:

1. Importeer de **Rootcertificaten VpnServerRoot** en **RadiusServerRoot** naar je Mac. Kopieer elk bestand naar uw Mac, dubbelklik erop en selecteer **Toevoegen**.

   ![Het VpnServerRoot-certificaat toevoegen](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Het RadiusServerRoot-certificaat toevoegen](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Elke client vereist een clientcertificaat voor verificatie. Installeer het clientcertificaat op het clientapparaat.
3. Open het dialoogvenster **Netwerk** onder **Netwerkvoorkeuren**. Selecteer **+** om een nieuw VPN-clientverbindingsprofiel te maken voor een P2S-verbinding met het virtuele Azure-netwerk.

   De **interfacewaarde** is **VPN**en de WAARDE van het **VPN-type** **is IKEv2**. Geef een naam op voor het profiel in het vak **Servicenaam** en selecteer **Maken** om het VPN-clientverbindingsprofiel te maken.

   ![Informatie over interface- en servicenamen](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Kopieer in de map **Generic** vanuit het bestand **VpnSettings.xml** de waarde van de **VpnServer-tag.** Plak deze waarde in de vakken **Serveradres** en **Extern id** van het profiel. Laat het **vak Lokale id** leeg.

   ![Servergegevens](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Selecteer **Verificatie-instellingen**en selecteer **Certificaat**. 

   ![Verificatie-instellingen](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klik **op Selecteren** om het certificaat te kiezen dat u wilt gebruiken voor verificatie.

   ![Een certificaat voor verificatie selecteren](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Kies Een identiteit** geeft een lijst met certificaten weer waaruit u kiezen. Selecteer het juiste certificaat en selecteer **Doorgaan**.

   ![Lijst 'Een identiteit kiezen'](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Geef **in** het vak Lokale id de naam van het certificaat op (vanaf stap 6). In dit voorbeeld is het **ikev2Client.com.** Selecteer vervolgens de knop **Toepassen** om de wijzigingen op te slaan.

   ![Vak "Lokale ID"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Selecteer **Toepassen** om alle wijzigingen op te slaan in het dialoogvenster **Netwerk.** Selecteer vervolgens **Verbinding maken** om de P2S-verbinding met het virtuele Azure-netwerk te starten.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Werken met andere verificatietypen of -protocollen

Als u een ander verificatietype wilt gebruiken (bijvoorbeeld OTP) of een ander verificatieprotocol wilt gebruiken (zoals PEAP-MSCHAPv2 in plaats van EAP-MSCHAPv2), moet u uw eigen VPN-clientconfiguratieprofiel maken. Als u het profiel wilt maken, hebt u informatie nodig, zoals het IP-adres van de virtuele netwerkgateway, het tunneltype en de split-tunnelroutes. U deze informatie krijgen met behulp van de volgende stappen:

1. Gebruik `Get-AzVpnClientConfiguration` de cmdlet om de VPN-clientconfiguratie voor EapMSChapv2 te genereren.

2. Rits het vpnclientconfiguratie.zip-bestand uit en zoek naar de map **GenericDevice.** Negeer de mappen die de Windows-installatieprogramma's bevatten voor 64-bits en 32-bits architecturen.
 
3. De map **GenericDevice** bevat een XML-bestand met de naam **VpnSettings**. Dit bestand bevat alle vereiste informatie:

   * **VpnServer**: FQDN van de Azure VPN-gateway. Dit is het adres waarmee de klant verbinding maakt.
   * **VpnType**: Tunneltype dat u gebruikt om verbinding te maken.
   * **Routes:** routes die u in uw profiel moet configureren, zodat alleen verkeer dat is ingesteld voor het virtuele Azure-netwerk via de P2S-tunnel wordt verzonden.
   
   De **map GenericDevice** bevat ook een .cer-bestand genaamd **VpnServerRoot**. Dit bestand bevat het hoofdcertificaat dat nodig is om de Azure VPN-gateway te valideren tijdens de installatie van de P2S-verbinding. Installeer het certificaat op alle apparaten die verbinding maken met het virtuele Azure-netwerk.

## <a name="next-steps"></a>Volgende stappen

Ga terug naar het artikel om [uw P2S-configuratie](point-to-site-how-to-radius-ps.md)te voltooien.

Zie Problemen met Azure [point-to-site-verbindingen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)oplossen voor informatie over het oplossen van problemen met P2S.
