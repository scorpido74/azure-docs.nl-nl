---
title: 'VPN-gateway: VPN-client voor OpenVPN-protocol P2S-verbindingen: Azure AD-verificatie'
description: U P2S VPN gebruiken om verbinding te maken met uw VNet met Azure AD-verificatie
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: alzam
ms.openlocfilehash: 9250464e3d28bdac20840aa9f69cfac707f73b30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371492"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Een VPN-client configureren voor P2S OpenVPN-protocolverbindingen: Azure AD-verificatie

Met dit artikel u een VPN-client configureren om verbinding te maken met een virtueel netwerk met behulp van Point-to-Site VPN en Azure Active Directory-verificatie. Voordat u verbinding maken en verifiëren met Azure AD, moet u eerst uw Azure AD-tenant configureren. Zie [Een Azure AD-tenant configureren](openvpn-azure-ad-tenant.md)voor meer informatie.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN® protocolverbindingen.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Werken met klantprofielen

Om verbinding te maken, moet u de Azure VPN-client downloaden en een VPN-clientprofiel configureren op elke computer die verbinding wil maken met het VNet. U een clientprofiel maken op een computer, exporteren en vervolgens importeren naar extra computers.

### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://go.microsoft.com/fwlink/?linkid=2117554) om de Azure VPN-client te downloaden. Zorg ervoor dat de Azure VPN-client toestemming heeft om op de achtergrond te worden uitgevoerd. Als u de machtiging wilt controleren/inschakelen, volgt u de onderstaande stappen:

1. Ga naar Start en selecteer Instellingen > Apps privacy > achtergrond.
2. Controleer onder Achtergrond-apps of **apps op de achtergrond** worden ingeschakeld.
3. Schakel onder Kiezen welke apps op de achtergrond kunnen worden uitgevoerd, instellingen voor Azure VPN-client in **Op**.

  ![Toestemming](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Een clientprofiel op basis van certificaten maken

Controleer bij het werken met een certificaatprofiel of de juiste certificaten op de clientcomputer zijn geïnstalleerd. Zie [Clientcertificaten installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)voor meer informatie over certificaten.

  ![Cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Een RADIUS-clientprofiel maken

  ![Radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Het Servergeheim kan worden geëxporteerd in het P2S VPN-clientprofiel.  Instructies voor het exporteren van een klantprofiel vindt u [hier.](about-vpn-profile-download.md)
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Een klantprofiel exporteren en distribueren

Zodra u een werkprofiel hebt en deze moet distribueren naar andere gebruikers, u het exporteren met de volgende stappen:

1. Markeer het VPN-clientprofiel dat u wilt exporteren, selecteer de **...**, selecteer **vervolgens Exporteren**.

    ![Exporteren](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecteer de locatie waarop u dit profiel wilt opslaan, laat de bestandsnaam zoals deze is en selecteer **Opslaan** om het xml-bestand op te slaan.

    ![Exporteren](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Een klantprofiel importeren

1. Selecteer **importeren**op de pagina .

    ![importeren](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Blader naar het xml-bestand van het profiel en selecteer het. Als het bestand is geselecteerd, selecteert u **Openen**.

    ![importeren](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![importeren](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecteer **Verbinding maken** om verbinding te maken met de VPN.

    ![importeren](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Eenmaal aangesloten, wordt het pictogram groen en zegt **Verbonden**.

    ![importeren](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Een clientprofiel verwijderen

1. Selecteer de ellips naast het clientprofiel dat u wilt verwijderen. Selecteer vervolgens **Verwijderen**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecteer **Verwijderen** om te verwijderen.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Een verbinding maken

1. Selecteer op de **+** pagina , selecteer dan **+ Toevoegen**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Vul de verbindingsgegevens in. Als u niet zeker bent van de waarden, neemt u contact op met de beheerder. Selecteer Na het invullen van de waarden de optie **Opslaan**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecteer **Verbinding maken** om verbinding te maken met de VPN.

    ![verbinding](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecteer de juiste referenties en selecteer **Doorgaan**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Zodra het pictogram is verbonden, wordt het groen en wordt het **verbonden**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Automatisch verbinding maken

Met deze stappen u uw verbinding configureren om automatisch verbinding te maken met Always-on.

1. Selecteer op de startpagina voor uw VPN-client **VPN-instellingen**.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecteer **Ja** in het dialoogvenster Dialoog van switch-apps.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Controleer of de verbinding die u wilt instellen nog niet is verbonden, markeer het profiel en schakel automatisch het selectievakje **Verbinding maken** in.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecteer **Verbinding maken** om de VPN-verbinding te starten.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Verbindingsproblemen diagnosticeren

1. Als u verbindingsproblemen wilt diagnosticeren, u het hulpprogramma **Diagnose** gebruiken. Selecteer de **...** naast de VPN-verbinding die u wilt diagnosticeren om het menu te onthullen. Selecteer vervolgens **Diagnosticeren**.

    ![Diagnosticeren](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Selecteer **Diagnose uitvoeren**op de pagina **Verbindingseigenschappen** .

    ![Diagnosticeren](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![Diagnosticeren](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Bekijk de diagnoseresultaten.

    ![Diagnosticeren](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Veelgestelde vragen

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hoe voeg ik DNS-achtervoegsels toe aan de VPN-client?

U het gedownloade profiel XML-bestand wijzigen en de ** \<dns-navoegsels toevoegen>\<dnssufix \<> /dnssufix>\</dns-navoegsels>** tags

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Hoe voeg ik aangepaste DNS-servers toe aan de VPN-client?

U het gedownloade profiel XML-bestand wijzigen en de ** \<dnsservers>\<dnsserver toevoegen> \</dnsserver>\</dnsservers>** tags

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
> De OpenVPN Azure AD-client maakt gebruik van NRPT-vermeldingen (DNS Name Resolution Policy Table) `ipconfig /all`en dat betekent dat DNS-servers niet worden vermeld onder de uitvoer van . Raadpleeg [Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) in PowerShell om uw dns-instellingen in gebruik te nemen.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Hoe voeg ik aangepaste routes toe aan de VPN-client?

U het gedownloade XML-bestand voor het profiel wijzigen en de ** \<includeroutes \<>route \<>bestemming toevoegen>\<masker> en \<doel>\</masker>\</route>\</includeroutes>-tags**

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Hoe blokkeer ik (uitsluitende) routes van de VPN-client?

U het gedownloade XML-bestand van het profiel wijzigen en de ** \<routes>\<route \<>>\< \<bestemming>masker \<> /bestemming>\</>\<masker /route>/excluderoutes>** tags toevoegen

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

Zie [Een Azure Active Directory-tenant maken voor P2S Open VPN-verbindingen die Azure AD-verificatie gebruiken voor](openvpn-azure-ad-tenant.md)meer informatie.
