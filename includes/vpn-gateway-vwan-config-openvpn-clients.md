---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986713"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows-clients

1. Download en installeer de OpenVPN client (versie 2.4 of hoger) van de officiële [OpenVPN website.](https://openvpn.net/index.php/open-source/downloads.html)
2. Download het VPN-profiel voor de gateway. Dit kan via het tabblad Point-to-site-configuratie in de Azure-portal of 'New-AzVpnClientConfiguration' in PowerShell.
3. Pak het profiel uit. Open vervolgens het *vpnconfig.ovpn-configuratiebestand* vanuit de OpenVPN-map met Notepad.
4. Exporteer het point-to-site clientcertificaat dat u hebt gemaakt en geüpload naar uw P2S-configuratie op de gateway. Gebruik de volgende artikelkoppelingen:

   * [INSTRUCTIES voor VPN-gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * [Virtuele WAN-instructies](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Haal de privétoets en de base64 duimafdruk uit de *.pfx*. Er zijn meerdere manieren om dit te doen. OpenSSL op uw machine gebruiken is één manier. Het *bestand profileinfo.txt* bevat de privésleutel en de duimafdruk voor de CA en het clientcertificaat. Zorg ervoor dat u de duimafdruk van het clientcertificaat gebruikt.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Open *profileinfo.txt* in Notepad. Als u de duimafdruk van het clientcertificaat (kind) wilt ontvangen, selecteert u de tekst (inclusief en tussen)"-----BEGINCERTIFICAAT-----" en "-----ENDCERTIFICATE-----" voor het onderliggende certificaat en kopieert u deze. U het kindcertificaat identificeren door naar de onderwerp=/ regel te kijken.
7. Schakel over naar het *vpnconfig.ovpn-bestand* dat u vanaf stap 3 in Notepad hebt geopend. Zoek de sectie hieronder en vervang alles tussen "cert" en "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Open de *profielinfo.txt* in Notepad. Als u de privésleutel wilt downloaden, selecteert u de tekst (inclusief en tussen) "-----BEGIN PRIVATE KEY-----" en "-----END PRIVATE KEY-----" en kopieert u deze.
9. Ga terug naar het vpnconfig.ovpn-bestand in Notepad en vind deze sectie. Plak de privésleutel ter vervanging van alles tussen en "key" en "/key".

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

## <a name="mac-clients"></a><a name="mac"></a>Mac-clients

1. Download en installeer een OpenVPN-client, zoals [TunnelBlick.](https://tunnelblick.net/downloads.html) 
2. Download het VPN-profiel voor de gateway. Dit kan worden gedaan vanaf het point-to-site configuratietabblad in de Azure-portal, of met behulp van 'New-AzVpnClientConfiguration' in PowerShell.
3. Pak het profiel uit. Open het vpnconfig.ovpn-configuratiebestand vanuit de OpenVPN-map in een teksteditor.
4. Vul het gedeelte P2S client certificate met de openbare P2S-clientcertificatcode in base64. In een certificaat met PEM-indeling kunt u gewoon het .cer-bestand openen en de base64-code tussen de headers van het certificaat kopiëren. Gebruik de volgende artikelkoppelingen voor informatie over het exporteren van een certificaat om de gecodeerde openbare sleutel te krijgen:

   * [INSTRUCTIES voor VPN-gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * [Virtuele WAN-instructies](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Vul in het gedeelte voor de persoonlijke sleutel de persoonlijke P2S-clientcertificaatsleutel in Base64 in. Zie de [privésleutel exporteren](https://openvpn.net/community-resources/how-to/#pki) op de OpenVPN-site voor informatie over het extraheren van een privésleutel.
6. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
7. Dubbelklik op het profielbestand om het profiel in Tunnelblick te maken.
8. Start Tunnelblick vanuit de map toepassingen.
9. Klik op het tunnelblick-pictogram in de systeemlade en kies verbinding maken.

> [!IMPORTANT]
>Alleen iOS 11.0 en hoger en MacOS 10.13 en hoger worden ondersteund met openvpn-protocol.
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS-clients

1. Installeer de OpenVPN-client (versie 2.4 of hoger) vanuit de App Store.
2. Download het VPN-profiel voor de gateway. Dit kan worden gedaan vanaf het point-to-site configuratietabblad in de Azure-portal, of met behulp van 'New-AzVpnClientConfiguration' in PowerShell.
3. Pak het profiel uit. Open het vpnconfig.ovpn-configuratiebestand vanuit de OpenVPN-map in een teksteditor.
4. Vul het gedeelte P2S client certificate met de openbare P2S-clientcertificatcode in base64. In een certificaat met PEM-indeling kunt u gewoon het .cer-bestand openen en de base64-code tussen de headers van het certificaat kopiëren. Gebruik de volgende artikelkoppelingen voor informatie over het exporteren van een certificaat om de gecodeerde openbare sleutel te krijgen:

   * [INSTRUCTIES voor VPN-gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * [Virtuele WAN-instructies](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Vul in het gedeelte voor de persoonlijke sleutel de persoonlijke P2S-clientcertificaatsleutel in Base64 in. Zie [Uw privésleutel](https://openvpn.net/community-resources/how-to/#pki) exporteren op de OpenVPN-site voor informatie over het extraheren van een privésleutel.
6. Wijzig geen andere velden.
7. E-mail het profielbestand (.ovpn) naar uw e-mailaccount dat is geconfigureerd in de e-mailapp op uw iPhone. 
8. Open de e-mail in de e-mail-app op de iPhone en tik op het bijgevoegde bestand

    ![E-mail openen](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Tik op **Meer** als u de optie **Kopiëren naar OpenVPN** niet ziet

    ![Meer](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Tik op **Kopiëren naar OpenVPN** 

    ![Kopiëren naar OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Tik op **TOEVOEGEN** op de pagina **Profiel importeren**

    ![Toevoegen](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Tik op **TOEVOEGEN** op de pagina **Geïmporteerd profiel**

    ![Tik op TOEVOEGEN](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Start de OpenVPN-app en schuif de schakelaar in de **profielpagina** recht om verbinding te maken

    ![Verbinding maken](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux clients

1. Open een nieuwe Terminal-sessie. U een nieuwe sessie openen door tegelijkertijd op 'Ctrl + Alt + t' te drukken.
2. Voer de volgende opdracht in om benodigde onderdelen te installeren:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Download het VPN-profiel voor de gateway. Dit kan worden gedaan via het tabblad Point-to-site-configuratie in de Azure-portal.
4. Exporteer het P2S-clientcertificaat dat u hebt gemaakt en geüpload naar uw P2S-configuratie op de gateway. Gebruik de volgende artikelkoppelingen:

   * [INSTRUCTIES voor VPN-gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * [Virtuele WAN-instructies](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Haal de private key en de base64 duimafdruk uit de .pfx. Er zijn meerdere manieren om dit te doen. OpenSSL op uw computer gebruiken is één manier.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Het *bestand profileinfo.txt* bevat de privésleutel en de duimafdruk voor de CA en het clientcertificaat. Zorg ervoor dat u de duimafdruk van het clientcertificaat gebruikt.

6. Open *profileinfo.txt* in een teksteditor. Als u de duimafdruk van het clientcertificaat (kind) wilt krijgen, selecteert u de tekst inclusief en tussen -----BEGINCERTIFICAAT-----" en -----END-certificaat----- voor het onderliggende certificaat en kopieert u deze. U het kindcertificaat identificeren door naar de onderwerp=/ regel te kijken.

7. Open het *vpnconfig.ovpn-bestand* en vind het gedeelte hieronder. Vervang alles tussen het en "cert" en "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Open de profielinfo.txt in een teksteditor. Als u de privésleutel wilt downloaden, selecteert u de tekst inclusief en tussen "-----BEGIN PRIVATE KEY-----" en "-----END PRIVATE KEY-----" en kopieert u deze.

9. Open het vpnconfig.ovpn-bestand in een teksteditor en vind deze sectie. Plak de privésleutel ter vervanging van alles tussen en "key" en "/key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
11. Als u verbinding wilt maken met de opdrachtregel, typt u de volgende opdracht:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Als u verbinding wilt maken met de GUI, gaat u naar systeeminstellingen.
13. Klik **+** hier om een nieuwe VPN-verbinding toe te voegen.
14. Kies **onder VPN toevoegen**importeren uit **bestand...**
15. Blader naar het profielbestand en dubbelklik of kies **Openen**.
16. Klik op **Toevoegen** in het **VENSTER VPN toevoegen.**
  
    ![Importeren vanuit bestand](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. U verbinding maken door de VPN **aan te** zetten op de pagina **Netwerkinstellingen** of onder het netwerkpictogram in de systeemlade.
