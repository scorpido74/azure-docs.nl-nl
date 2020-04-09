---
title: 'VPN-gateway: Azure AD-tenant voor P2S VPN-verbindingen: Azure AD-verificatie'
description: U P2S VPN gebruiken om verbinding te maken met uw VNet met Azure AD-verificatie
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: alzam
ms.openlocfilehash: 6f7558a7d382d13c822862b881e310d3a5aaad0f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879408"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-tenant maken voor P2S OpenVPN-protocolverbindingen

Wanneer u verbinding maakt met uw VNet, u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het Open VPN-protocol gebruikt, u ook Azure Active Directory-verificatie gebruiken. Met dit artikel u een Azure AD-tenant instellen voor Vpn-verificatie van P2S Open.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN® protocolverbindingen.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD-tenant verifiëren

Controleer of u een Azure AD-tenant hebt. Als u geen Azure AD-tenant hebt, u er een maken met de stappen in het artikel [Een nieuw tenant maken:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Organisatienaam
* Oorspronkelijke domeinnaam

Voorbeeld:

   ![Nieuwe Azure AD-tenant](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD-tenantgebruikers maken

Uw Azure AD-tenant heeft de volgende accounts nodig: een Global Admin-account en een hoofdgebruikersaccount. Het hoofdgebruikersaccount wordt gebruikt als uw hoofdinsluitingsaccount (serviceaccount). Wanneer u een Azure AD-tenantgebruikersaccount maakt, past u de maprol aan voor het type gebruiker dat u wilt maken.

Gebruik de stappen in [dit artikel](../active-directory/fundamentals/add-users-azure-active-directory.md) om ten minste twee gebruikers voor uw Azure AD-tenant te maken. Wijzig de **maprol** om de accounttypen te maken:

* Globale beheerder
* Gebruiker

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Azure AD-verificatie inschakelen op de VPN-gateway

1. Zoek de directory-id van de map die u wilt gebruiken voor verificatie. Het wordt vermeld in de sectie Eigenschappen van de Active Directory-pagina.

    ![Directory-id](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Kopieer de Map-id.

3. Meld u aan bij de Azure-portal als gebruiker waaraan de **globale beheerdersrol** is toegewezen.

4. Geef vervolgens toestemming van de beheerder. Kopieer en plak de URL die betrekking heeft op uw implementatielocatie in de adresbalk van uw browser:

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
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecteer het globale **beheerdersaccount** als u daarom wordt gevraagd.

    ![Directory-id](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecteer **Accepteren** wanneer daarom wordt gevraagd.

    ![Accepteren](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Onder uw Azure AD ziet u in **Enterprise-toepassingen** **Azure VPN** vermeld.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Als u nog geen functionerende point-to-site-omgeving hebt, volgt u de instructie om er een te maken. Zie [Een point-to-site VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) maken om een point-to-site VPN-gateway te maken en te configureren met native Azure-certificaatverificatie. 

    > [!IMPORTANT]
    > De Basic SKU wordt niet ondersteund voor OpenVPN.

9. Azure AD-verificatie op de VPN-gateway inschakelen door de volgende opdrachten uit te voeren, zodat u de opdracht wijzigt om uw eigen omgeving weer te geven:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Zorg ervoor dat u een trailing `AadIssuerUri` slash aan het einde van de waarde. Anders mislukt de opdracht.

10. Maak en download het profiel door de volgende opdrachten uit te voeren. Wijzig de waarden -ResourceGroupName en -Naam die overeenkomen met die van u.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Nadat u de opdrachten hebt uitgevoerd, ziet u een resultaat dat vergelijkbaar is met het resultaat hieronder. Kopieer de resultaat-URL naar uw browser om het zip-bestand van het profiel te downloaden.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Haal het gedownloade zip-bestand eruit.

13. Blader naar de map "AzureVPN".

14. Noteer de locatie van het bestand "azurevpnconfig.xml". De azurevpnconfig.xml bevat de instelling voor de VPN-verbinding en kan rechtstreeks worden geïmporteerd in de Azure VPN Client-toepassing. U dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of andere middelen. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te maken.

## <a name="next-steps"></a>Volgende stappen

Om verbinding te maken met uw virtuele netwerk, moet u een VPN-clientprofiel maken en configureren. Zie [Een VPN-client configureren voor P2S VPN-verbindingen](openvpn-azure-ad-client.md).
