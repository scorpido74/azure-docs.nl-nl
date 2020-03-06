---
title: OpenVPN-clients configureren voor Azure VPN Gateway | Microsoft Docs
description: Stappen voor het configureren van OpenVPN-clients voor Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: a45a3412a1ceb8e8a9bd9fd1a34dfdbd10ba1c75
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393566"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>OpenVPN-clients configureren voor Azure VPN Gateway

Dit artikel helpt u bij het configureren van **OpenVPN®-protocol** -clients.

## <a name="before-you-begin"></a>Voordat u begint



Controleer of u de stappen hebt voltooid voor het configureren van OpenVPN voor uw VPN-gateway. Zie [Configure openvpn for Azure VPN gateway](vpn-gateway-howto-openvpn.md)voor meer informatie.

## <a name="windows"></a>Windows-clients

1. Down load en installeer de OpenVPN-Client (versie 2,4 of hoger) van de officiële [openvpn-website](https://openvpn.net/index.php/open-source/downloads.html).
2. Download het VPN-profiel voor de gateway. U kunt dit doen via het tabblad punt-naar-site-configuratie in de Azure Portal, of ' New-AzVpnClientConfiguration ' in Power shell.
3. Pak het profiel uit. Open vervolgens het configuratie bestand *vpnconfig. ovpn* in de map openvpn met behulp van Klad blok.
4. [Exporteer](vpn-gateway-certificates-point-to-site.md#clientexport) het P2S-client certificaat dat u hebt gemaakt en geüpload naar de P2S-configuratie op de gateway.
5. Pak de persoonlijke sleutel en de base64-vinger afdruk uit vanuit *PFX*. Er zijn meerdere manieren om dit te doen. Het gebruik van OpenSSL op uw computer is een manier. Het bestand *profileinfo. txt* bevat de persoonlijke sleutel en de vinger afdruk voor de CA en het client certificaat. Zorg ervoor dat u de vinger afdruk van het client certificaat gebruikt.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Open *profileinfo. txt* in Klad blok. Als u de vinger afdruk van het certificaat van de client (onderliggend) wilt ophalen, selecteert u de tekst (inclusief en tussen) "-----BEGIN CERTIFICATE-----" en "-----END CERTIFICATE-----" voor het onderliggende certificaat en kopieert u het. U kunt het onderliggende certificaat identificeren door te kijken naar het onderwerp =/regel.
7. Ga in stap 3 naar het bestand *vpnconfig. ovpn* dat u in Klad blok hebt geopend. Ga naar de sectie die hieronder wordt weer gegeven en vervang alles tussen "Cert" en "/Cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Open *profileinfo. txt* in Klad blok. Als u de persoonlijke sleutel wilt ophalen, selecteert u de tekst (inclusief en tussen) "-----BEGIN persoonlijke sleutel-----" en "-----END PRIVATE KEY-----" en kopieert u deze.
9. Ga terug naar het bestand vpnconfig. ovpn in Klad blok en zoek deze sectie. Plak de persoonlijke sleutel en vervang alles tussen en "Key" en "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
11. Kopieer het bestand vpnconfig.ovpn naar C:\Program Files\OpenVPN\config folder.
12. Klik met de rechtermuisknop op het pictogram van OpenVPN in het systeemvak en klik op Verbinding maken.

## <a name="mac"></a>Mac-clients

1. Down load en installeer een OpenVPN-Client, zoals [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Download het VPN-profiel voor de gateway. U kunt dit doen via het tabblad punt-naar-site-configuratie in de Azure Portal, of door ' New-AzVpnClientConfiguration ' te gebruiken in Power shell.
3. Pak het profiel uit. Open het configuratie bestand vpnconfig. ovpn in de map OpenVPN in een tekst editor.
4. Vul het gedeelte P2S client certificate met de openbare P2S-clientcertificatcode in base64. In een certificaat met PEM-indeling kunt u gewoon het .cer-bestand openen en de base64-code tussen de headers van het certificaat kopiëren. Zie [de open bare sleutel exporteren](vpn-gateway-certificates-point-to-site.md#cer) voor informatie over het exporteren van een certificaat om de gecodeerde open bare sleutel op te halen.
5. Vul in het gedeelte voor de persoonlijke sleutel de persoonlijke P2S-clientcertificaatsleutel in Base64 in. Zie [uw persoonlijke sleutel exporteren](https://openvpn.net/community-resources/how-to/#pki) voor meer informatie over het extra heren van een persoonlijke sleutel.
6. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
7. Dubbel klik op het profiel bestand om het profiel te maken in Tunnelblick.
8. Start Tunnelblick vanuit de map toepassingen.
9. Klik op het pictogram Tunnelblick in het systeemvak en kies verbinding maken.

> [!IMPORTANT]
>Alleen iOS 11,0 en hoger en MacOS 10,13 en hoger worden ondersteund met het OpenVPN-protocol.
>
## <a name="iOS"></a>iOS-clients

1. Installeer de OpenVPN-Client (versie 2,4 of hoger) uit de App Store.
2. Download het VPN-profiel voor de gateway. U kunt dit doen via het tabblad punt-naar-site-configuratie in de Azure Portal, of door ' New-AzVpnClientConfiguration ' te gebruiken in Power shell.
3. Pak het profiel uit. Open het configuratie bestand vpnconfig. ovpn in de map OpenVPN in een tekst editor.
4. Vul het gedeelte P2S client certificate met de openbare P2S-clientcertificatcode in base64. In een certificaat met PEM-indeling kunt u gewoon het .cer-bestand openen en de base64-code tussen de headers van het certificaat kopiëren. Zie [de open bare sleutel exporteren](vpn-gateway-certificates-point-to-site.md#cer) voor informatie over het exporteren van een certificaat om de gecodeerde open bare sleutel op te halen.
5. Vul in het gedeelte voor de persoonlijke sleutel de persoonlijke P2S-clientcertificaatsleutel in Base64 in. Zie [uw persoonlijke sleutel exporteren](https://openvpn.net/community-resources/how-to/#pki) voor meer informatie over het extra heren van een persoonlijke sleutel.
6. Wijzig geen andere velden.
7. Stuur het profiel bestand (. ovpn) naar uw e-mail account dat is geconfigureerd in de e-mail-app op uw iPhone. 
8. Open het e-mail bericht in de e-mail-app op de iPhone en tik op het bijgevoegde bestand

    ![E-mail openen](./media/vpn-gateway-howto-openvpn-clients/ios2.png)

9. Tik op **meer** als u de optie **kopiëren naar openvpn** niet ziet

    ![Kopiëren naar OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios3.png)

10. Tik op **kopiëren naar openvpn** 

    ![Kopiëren naar OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios4.png)

11. Tik op **toevoegen** op de pagina **profiel importeren**

    ![Kopiëren naar OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios5.png)

12. Tik op **toevoegen** op de pagina voor het **geïmporteerde profiel**

    ![Kopiëren naar OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios6.png)

13. Start de OpenVPN-app en schuif de switch op de **profiel** pagina rechts om verbinding te maken

    ![Verbinding maken](./media/vpn-gateway-howto-openvpn-clients/ios8.png)


## <a name="linux"></a>Linux-clients

1. Open een nieuwe terminal sessie. U kunt een nieuwe sessie openen door op CTRL + ALT + t te drukken.
2. Voer de volgende opdracht in om de vereiste onderdelen te installeren:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Download het VPN-profiel voor de gateway. U kunt dit doen via het tabblad punt-naar-site-configuratie in de Azure Portal.
4. [Exporteer](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) het P2S-client certificaat dat u hebt gemaakt en geüpload naar de P2S-configuratie op de gateway. 
5. Pak de persoonlijke sleutel en de base64-vinger afdruk uit vanuit pfx. Er zijn meerdere manieren om dit te doen. Het gebruik van OpenSSL op uw computer is een manier.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Het bestand *profileinfo. txt* bevat de persoonlijke sleutel en de vinger afdruk voor de CA en het client certificaat. Zorg ervoor dat u de vinger afdruk van het client certificaat gebruikt.

6. Open *profileinfo. txt* in een tekst editor. Als u de vinger afdruk van het client (onderliggend) certificaat wilt ophalen, selecteert u de tekst met inbegrip van en tussen "-----BEGIN CERTIFICATE-----" en "-----END CERTIFICATE-----" voor het onderliggende certificaat en kopieert u het. U kunt het onderliggende certificaat identificeren door te kijken naar het onderwerp =/regel.

7. Open het bestand *vpnconfig. ovpn* en ga naar de sectie die hieronder wordt weer gegeven. Vervang alles tussen de en "Cert" en "/Cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Open profileinfo. txt in een tekst editor. Als u de persoonlijke sleutel wilt ophalen, selecteert u de tekst inclusief en tussen "-----BEGIN persoonlijke sleutel-----" en "-----END PRIVATE KEY-----" en kopieert u deze.

9. Open het bestand vpnconfig. ovpn in een tekst editor en zoek deze sectie. Plak de persoonlijke sleutel en vervang alles tussen en "Key" en "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
11. Als u verbinding wilt maken via de opdracht regel, typt u de volgende opdracht:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. Als u verbinding wilt maken met behulp van de gebruikers interface, gaat u naar systeem instellingen.
13. Klik op **+** om een nieuwe VPN-verbinding toe te voegen.
14. Onder **VPN toevoegen**kiest u **importeren uit bestand...**
15. Blader naar het profiel bestand en dubbel klik of kies **openen**.
16. Klik op **toevoegen** in het venster **VPN toevoegen** .
  
    ![Importeren uit bestand](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. U kunt verbinding maken door **de VPN in** te scha kelen op de pagina **netwerk instellingen** of door onder het netwerk pictogram in het systeemvak te klikken.

## <a name="next-steps"></a>Volgende stappen

Als u wilt dat de VPN-clients toegang kunnen krijgen tot bronnen in een ander VNet, volgt u de instructies in het artikel [vnet-naar-vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om een vnet-naar-vnet-verbinding in te stellen. Zorg ervoor dat BGP op de gateways en de verbindingen inschakelt, anders wordt het verkeer niet stroomt.

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
