---
title: 'VPN Gateway: Azure AD-Tenant voor verschillende gebruikers groepen: Azure AD-verificatie'
description: U kunt P2S VPN gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477172"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-Tenant maken voor P2S OpenVPN-protocol verbindingen

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Als u wilt dat een andere set gebruikers verbinding kan maken met verschillende VPN-gateways, kunt u meerdere apps in AD registreren en deze koppelen aan verschillende VPN-gateways. Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor P2S OpenVPN-verificatie en het maken en registreren van meerdere apps in azure AD voor het toestaan van verschillende toegang voor verschillende gebruikers en groepen.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN®-protocol verbindingen.
>

## <a name="tenant"></a>1. de Azure AD-Tenant maken

Maak een Azure AD-Tenant met behulp van de stappen in het artikel [een nieuwe Tenant maken](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisatie naam
* Initiële domeinnaam

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

    Microsoft Cloud Germany

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

    ![Accepteren](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

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

## <a name="enable-authentication"></a>6. Azure AD-verificatie inschakelen op de VPN-gateway

1. Schakel Azure AD-verificatie in op de VPN-gateway door de volgende opdrachten uit te voeren. Zorg er daarom voor dat u de opdracht wijzigt in overeenstemming met uw eigen omgeving:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> Gebruik de toepassings-ID van de Azure VPN-client niet in de bovenstaande opdrachten, omdat alle gebruikers toegang krijgen tot de VPN-gateway. Gebruik de ID van de toepassing (en) die u hebt geregistreerd.

2. Maak en down load het profiel door de volgende opdrachten uit te voeren. Wijzig de waarden voor-ResourcGroupName en-name zodat deze overeenkomen met uw eigen waarde.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Na het uitvoeren van de opdrachten ziet u een resultaat zoals hieronder. Kopieer de URL van het resultaat naar uw browser om het profiel-zip-bestand te downloaden.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Pak het gedownloade zip-bestand uit.

5. Blader naar de map ungezipte ' AzureVPN '.

6. Noteer de locatie van het bestand azurevpnconfig. XML. Azurevpnconfig. XML bevat de instelling voor de VPN-verbinding en kan rechtstreeks in de Azure VPN-client toepassing worden geïmporteerd. U kunt dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of een andere manier. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te kunnen maken.

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren. Zie [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
