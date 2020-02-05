---
title: 'VPN Gateway: Azure AD-Tenant voor verschillende gebruikers groepen: Azure AD-verificatie'
description: U kunt P2S VPN gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985642"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-Tenant maken voor P2S OpenVPN-protocol verbindingen

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Als u wilt dat een andere set gebruikers verbinding kan maken met verschillende VPN-gateways, kunt u meerdere apps in AD registreren en deze koppelen aan verschillende VPN-gateways. Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor P2S OpenVPN-verificatie en het maken en registreren van meerdere apps in azure AD voor het toestaan van verschillende toegang voor verschillende gebruikers en groepen.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN®-protocol verbindingen.
>

## <a name="tenant"></a>1. de Azure AD-Tenant maken

Maak een Azure AD-Tenant met behulp van de stappen in het artikel [een nieuwe Tenant maken](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisatie naam
* Initiële domein naam

Voorbeeld:

   ![Nieuwe Azure AD-Tenant](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. gebruikers van Azure AD-tenants maken

Maak vervolgens twee gebruikers accounts. Maak één globaal beheerders account en één hoofd gebruikers account. Het hoofd gebruikers account wordt gebruikt als uw hoofd account voor insluiten (Service-account). Wanneer u een Azure AD-Tenant gebruikers account maakt, past u de Directory-rol aan voor het type gebruiker dat u wilt maken.

Volg de stappen in [dit artikel](../active-directory/fundamentals/add-users-azure-active-directory.md) om ten minste twee gebruikers te maken voor uw Azure AD-Tenant. Zorg ervoor dat u de **Directory-rol** wijzigt om de volgende account typen te maken:

* Globale beheerder
* Gebruiker

## <a name="enable-authentication"></a>3. Azure VPN-client registreren in azure AD-Tenant

1. Zoek de Directory-ID van de map die u voor verificatie wilt gebruiken. Deze wordt weer gegeven in de sectie eigenschappen van de pagina Active Directory.

    ![Map-ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Kopieer de map-ID.

3. Meld u aan bij de Azure Portal als gebruiker aan wie de rol van **globale beheerder** is toegewezen.

4. Geef vervolgens toestemming voor de beheerder. Kopieer en plak de URL die betrekking heeft op uw implementatie locatie in de adres balk van uw browser:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Duitsland

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecteer het account van de **globale beheerder** als u hierom wordt gevraagd.

    ![Map-ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecteer **accepteren** wanneer u hierom wordt gevraagd.

    ![Zodat](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Onder uw Azure AD, in **bedrijfs toepassingen**, wordt **Azure VPN** weer gegeven.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. aanvullende toepassingen voor verschillende gebruikers/groepen registreren

1. Klik onder uw Azure Active Directory op **app-registraties** en vervolgens op **+ nieuwe registratie**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. In de Blade **een toepassing registreren** voert u de **naam** in en selecteert u de gewenste **ondersteunde account typen** en klikt u op **registreren** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Zodra de nieuwe app is geregistreerd, klikt u op **een API beschikbaar** maken onder de Blade app

4. Klik op **+ een bereik toevoegen**
5. Behoud de standaard **-URI voor de toepassings-id** en klik op **opslaan en door gaan**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Vul de vereiste velden in en zorg ervoor dat de **status** is **ingeschakeld**. Klik op **bereik toevoegen**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Klik op **een API beschikbaar** maken en vervolgens op **een client toepassing toevoegen**.  Voer voor **client-id**de volgende waarden in, afhankelijk van de Cloud:
    -   Voer **41b23e61-6c1e-4545-b367-cd054e0ed4b4** voor Azure **Public** in
    -   Voer **51bb15d4-3a4f-4ebf-9dca-40096fe32426** in voor Azure **Government**
    -   **538ee9e6-310A-468d-afef-ea97365856a9** invoeren voor Azure **Duitsland**
    -   **49f817b6-84ae-4cc0-928c-73f27289b3aa** invoeren voor Azure **China 21vianet**


8. Klik op **toepassing toevoegen**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Kopieer de **toepassings-id (client)** van de pagina **overzicht** . U hebt deze nodig voor het configureren van uw VPN-gateway (s)

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Herhaal de stappen in deze sectie (4) om zoveel toepassingen te maken die nodig zijn voor uw beveiligings vereiste. Elke toepassing wordt gekoppeld aan een VPN-gateway en kan een andere set gebruikers hebben. Er kan slechts één toepassing aan een gateway worden gekoppeld.

## <a name="enable-authentication"></a>5. gebruikers toewijzen aan uw toepassingen

1. Onder Azure AD, **zakelijke toepassingen**, selecteert u de zojuist geregistreerde toepassing en klikt u op **Eigenschappen**. Zorg ervoor dat de **gebruikers toewijzing vereist** is. is ingesteld op **Ja**. Klik op **Opslaan**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Klik op de pagina app op **gebruikers en groepen** en vervolgens op **gebruiker toevoegen**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. Klik onder **toewijzing toevoegen**op **gebruikers en groepen**. Selecteer de gebruikers die u toegang wilt geven tot deze VPN-toepassing. Klik op **Selecteren**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="site"></a>6. een nieuwe P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Stel de volgende variabelen in en vervang waarden naar behoefte voor uw omgeving.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Voer de volgende opdrachten uit om de configuratie te maken:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

> [!NOTE]
> Gebruik de toepassings-ID van de Azure VPN-client niet in de bovenstaande opdrachten, omdat alle gebruikers toegang krijgen tot de VPN-gateway. Gebruik de ID van de toepassing (en) die u hebt geregistreerd.

## <a name="hub"></a>7. hub-toewijzing bewerken

1. Navigeer naar de Blade **hubs** onder het virtuele WAN.
2. Selecteer de hub waaraan u de VPN-server configuratie wilt koppelen en klik op het weglatings teken (...).

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klik op **virtuele hub bewerken**.
4. Schakel het selectie vakje een **punt-naar-site-gateway insluiten** in en selecteer de gewenste **Gateway-schaal eenheid** .

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Voer de **adres groep** in van waaruit de VPN-clients IP-adressen worden toegewezen.
6. Klik op **Bevestigen**.
7. Het kan tot 30 minuten duren voordat de bewerking is voltooid.

## <a name="device"></a>8. VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **VPN-configuraties voor gebruikers**.
2. Klik boven aan de pagina op **VPN-configuratie voor gebruiker downloaden**.
3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.
4. Gebruik het profiel bestand voor het configureren van de VPN-clients.

4. Pak het gedownloade zip-bestand uit.

5. Blader naar de map ungezipte ' AzureVPN '.

6. Noteer de locatie van het bestand azurevpnconfig. XML. Azurevpnconfig. XML bevat de instelling voor de VPN-verbinding en kan rechtstreeks in de Azure VPN-client toepassing worden geïmporteerd. U kunt dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of een andere manier. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te kunnen maken.
## <a name="configure-user-vpn-clients"></a>VPN-clients voor gebruikers configureren

Als u verbinding wilt maken, moet u de Azure VPN-client (preview) downloaden en het VPN-client profiel importeren dat in de vorige stappen is gedownload op elke computer die verbinding wil maken met het VNet.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN®-protocol verbindingen.
>

#### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) om de Azure VPN-client te downloaden (preview).

#### <a name="import"></a>Een client profiel importeren

1. Selecteer op de pagina **importeren**.

    ![wederinvoer](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Blader naar het profiel XML-bestand en selecteer het. Selecteer **openen**terwijl het bestand is geselecteerd.

    ![wederinvoer](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![wederinvoer](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![wederinvoer](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![wederinvoer](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Een client profiel verwijderen

1. Selecteer het beletsel teken (...) naast het client profiel dat u wilt verwijderen. Selecteer vervolgens **verwijderen**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecteer **verwijderen** om te verwijderen.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Problemen met de verbinding vaststellen

1. Als u verbindings problemen wilt vaststellen, kunt u het hulp programma voor **diagnose** gebruiken. Selecteer het beletsel teken (...) naast de VPN-verbinding die u wilt diagnosticeren om het menu weer te geven. Selecteer vervolgens **diagnose**.

    ![vaststellen](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Selecteer op de pagina **Eigenschappen van verbinding** de optie **diagnose uitvoeren**.

    ![vaststellen](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![vaststellen](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Bekijk de resultaten van de diagnose.

    ![vaststellen](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Uw virtuele WAN weer geven

1. Navigeer naar uw virtuele WAN.
2. Op de pagina Overzicht vertegenwoordigt elk punt op de kaart een hub. Houd de muisaanwijzer boven een punt om de statussamenvatting voor de hub weer te geven.
3. In de sectie Hubs en verbindingen vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="viewhealth"></a>De status van uw resource weer geven

1. Navigeer naar uw WAN.
2. Klik op de pagina van uw WAN, in de sectie **Ondersteuning en probleemoplossing**, op **Status** en bekijk de status van uw resource.


## <a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
