---
title: 'VPN-gateway: Azure AD-tenant voor verschillende gebruikersgroepen: Azure AD-verificatie'
description: U P2S VPN gebruiken om verbinding te maken met uw VNet met Azure AD-verificatie
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485620"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-tenant maken voor P2S OpenVPN-protocolverbindingen

Wanneer u verbinding maakt met uw VNet, u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het Open VPN-protocol gebruikt, u ook Azure Active Directory-verificatie gebruiken. Als u wilt dat verschillende gebruikers verbinding kunnen maken met verschillende VPN-gateways, u meerdere apps in AD registreren en deze koppelen aan verschillende VPN-gateways. Met dit artikel u een Azure AD-tenant instellen voor P2S OpenVPN-verificatie en meerdere apps maken en registreren in Azure AD voor het toestaan van verschillende toegang voor verschillende gebruikers en groepen.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN® protocolverbindingen.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Verificatie inschakelen op de gateway

In deze stap schakelt u Azure AD-verificatie in op de VPN-gateway.

1. Azure AD-verificatie op de VPN-gateway inschakelen door de volgende opdrachten uit te voeren. Zorg ervoor dat u de opdrachten wijzigt om uw eigen omgeving weer te geven:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Gebruik de toepassings-id van de Azure VPN-client niet in de bovenstaande opdrachten: Het verleent alle gebruikers toegang tot de VPN-gateway. Gebruik de id van de door u geregistreerde applicatie(s).

2. Maak en download het profiel door de volgende opdrachten uit te voeren. Wijzig de waarden -ResourcGroupName en -Name op uw eigen waarden.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Nadat u de opdrachten hebt uitgevoerd, ziet u een resultaat dat vergelijkbaar is met het resultaat hieronder. Kopieer de resultaat-URL naar uw browser om het zip-bestand van het profiel te downloaden.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Haal het gedownloade zip-bestand eruit.

5. Blader naar de map "AzureVPN".

6. Noteer de locatie van het bestand "azurevpnconfig.xml". De azurevpnconfig.xml bevat de instelling voor de VPN-verbinding en kan rechtstreeks worden geïmporteerd in de Azure VPN Client-toepassing. U dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of andere middelen. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te maken.

## <a name="next-steps"></a>Volgende stappen

Om verbinding te maken met uw virtuele netwerk, moet u een VPN-clientprofiel maken en configureren. Zie [Een VPN-client configureren voor P2S VPN-verbindingen](openvpn-azure-ad-client.md).
