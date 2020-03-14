---
title: 'VPN Gateway: VPN-client voor OpenVPN protocol P2S-verbindingen: Azure AD-verificatie'
description: U kunt P2S VPN gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: alzam
ms.openlocfilehash: 3559a139ff89c949ee691310ae25af7d6950abdf
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138957"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Een VPN-client configureren voor P2S OpenVPN-protocol verbindingen: Azure AD-verificatie

Dit artikel helpt u bij het configureren van een VPN-client om verbinding te maken met een virtueel netwerk met behulp van punt-naar-site-VPN en Azure Active Directory-verificatie. Voordat u verbinding kunt maken met en verifiëren met Azure AD, moet u eerst uw Azure AD-Tenant configureren. Zie [een Azure AD-Tenant configureren](openvpn-azure-ad-tenant.md)voor meer informatie.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN®-protocol verbindingen.
>

## <a name="profile"></a>Werken met client profielen

Als u verbinding wilt maken, moet u de Azure VPN-client downloaden en een VPN-client profiel configureren op elke computer die verbinding wil maken met het VNet. U kunt een client profiel maken op een computer, het exporteren en vervolgens importeren naar extra computers.

### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://go.microsoft.com/fwlink/?linkid=2117554) om de Azure VPN-client te downloaden.

### <a name="cert"></a>Een client profiel op basis van een certificaat maken

Wanneer u werkt met een op een certificaat gebaseerd profiel, moet u ervoor zorgen dat de juiste certificaten zijn geïnstalleerd op de client computer. Zie [client certificaten installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)voor meer informatie over certificaten.

  ![certificaat](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Een RADIUS-client profiel maken

  ![RADIUS](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Het server geheim kan worden geëxporteerd in het P2S VPN-client profiel.  Instructies voor het exporteren van een client profiel vindt u [hier](about-vpn-profile-download.md).
>

### <a name="export"></a>Een client profiel exporteren en distribueren

Wanneer u een werk profiel hebt en dit moet worden gedistribueerd naar andere gebruikers, kunt u het exporteren met behulp van de volgende stappen:

1. Markeer het VPN-client profiel dat u wilt exporteren, selecteer de **...** en selecteer vervolgens **exporteren**.

    ![gegevensexporttabel](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecteer de locatie waar u dit profiel wilt opslaan, behoud de bestands naam en selecteer vervolgens **Opslaan** om het XML-bestand op te slaan.

    ![gegevensexporttabel](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Een client profiel importeren

1. Selecteer op de pagina **importeren**.

    ![wederinvoer](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Blader naar het profiel XML-bestand en selecteer het. Selecteer **openen**terwijl het bestand is geselecteerd.

    ![wederinvoer](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![wederinvoer](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![wederinvoer](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![wederinvoer](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Een client profiel verwijderen

1. Selecteer de weglatings tekens naast het client profiel dat u wilt verwijderen. Selecteer vervolgens **verwijderen**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecteer **verwijderen** om te verwijderen.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Een verbinding maken

1. Selecteer op de pagina **+** en vervolgens **+ toevoegen**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Vul de verbindings gegevens in. Als u niet zeker weet wat de waarden zijn, neemt u contact op met de beheerder. Nadat u de waarden hebt ingevuld, selecteert u **Opslaan**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![verbinding](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecteer de juiste referenties en selecteer vervolgens **door gaan**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Automatisch verbinding maken

Met deze stappen kunt u de verbinding configureren om automatisch verbinding te maken met Always.

1. Selecteer op de start pagina voor uw VPN-client de optie **VPN-instellingen**.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecteer **Ja** in het dialoog venster apps overschakelen.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Controleer of de verbinding die u wilt instellen nog niet is verbonden en selecteer vervolgens het profiel en schakel het selectie vakje **automatisch verbinding maken** in.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecteer **verbinding maken** om de VPN-verbinding te initiëren.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Problemen met de verbinding vaststellen

1. Als u verbindings problemen wilt vaststellen, kunt u het hulp programma voor **diagnose** gebruiken. Selecteer de **..** . naast de VPN-verbinding die u wilt diagnosticeren om het menu weer te geven. Selecteer vervolgens **diagnose**.

    ![vaststellen](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Selecteer op de pagina **Eigenschappen van verbinding** de optie **diagnose uitvoeren**.

    ![vaststellen](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![vaststellen](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Bekijk de resultaten van de diagnose.

    ![vaststellen](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Veelgestelde vragen

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hoe kan ik DNS-achtervoegsels toevoegen aan de VPN-client?

U kunt het gedownloade XML-profiel bestand wijzigen en de **\<dnssuffixes >\<dnssufix > \</dnssufix >\</dnssuffixes >** Tags toevoegen

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Hoe kan ik aangepaste DNS-servers toevoegen aan de VPN-client?

U kunt het gedownloade XML-profiel bestand wijzigen en de **\<dnsservers >\<dnsserver > \</dnsserver >\</dnsservers >** Tags toevoegen

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> De OpenVPN Azure AD-client maakt gebruik van NRPT-vermeldingen (DNS Name Resolution Policy Table), wat betekent dat de DNS-servers niet worden vermeld onder de uitvoer van `ipconfig /all`. Raadpleeg [Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) in Power shell om uw DNS-instellingen in gebruik te bevestigen.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Hoe kan ik aangepaste routes toevoegen aan de VPN-client?

U kunt het gedownloade XML-profiel bestand wijzigen en de **\<includeroutes toevoegen >\<route >\<bestemming >\<masker > \</destination >\</mask >\</route >\</includeroutes >** Tags

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Hoe kan ik blok keren (uitsluiten) routes van de VPN-client?

U kunt het gedownloade XML-profiel bestand wijzigen en de **\<excluderoutes toevoegen >\<route >\<bestemming >\<masker > \</destination >\</mask >\</route >\</excluderoutes >** Tags

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>Volgende stappen

Zie [een Azure Active Directory-Tenant maken voor P2S open VPN-verbindingen die gebruikmaken van Azure AD-verificatie](openvpn-azure-ad-tenant.md)voor meer informatie.
