---
title: 'VPN Gateway: Azure AD-Tenant voor verschillende gebruikers groepen: Azure AD-verificatie'
description: U kunt P2S VPN gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485620"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-Tenant maken voor P2S OpenVPN-protocol verbindingen

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Als u wilt dat een andere set gebruikers verbinding kan maken met verschillende VPN-gateways, kunt u meerdere apps in AD registreren en deze koppelen aan verschillende VPN-gateways. Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor P2S OpenVPN-verificatie en het maken en registreren van meerdere apps in azure AD voor het toestaan van verschillende toegang voor verschillende gebruikers en groepen.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN®-protocol verbindingen.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. verificatie inschakelen op de gateway

In deze stap schakelt u Azure AD-verificatie in op de VPN-gateway.

1. Schakel Azure AD-verificatie in op de VPN-gateway door de volgende opdrachten uit te voeren. Zorg ervoor dat u de opdrachten wijzigt, zodat deze overeenkomen met uw eigen omgeving:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Gebruik de toepassings-ID van de Azure VPN-client niet in de bovenstaande opdrachten: alle gebruikers krijgen toegang tot de VPN-gateway. Gebruik de ID van de toepassing (en) die u hebt geregistreerd.

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
