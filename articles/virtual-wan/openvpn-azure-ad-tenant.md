---
title: 'Azure AD-Tenant voor VPN-verbindingen van gebruikers: Azure AD-verificatie'
description: U kunt Azure Virtual WAN User VPN (punt-naar-site) gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 76c65d194d03dd1b7ff4cc2f3b45d84ff7909968
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753364"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Een Azure Active Directory-Tenant maken voor verbindingen van het OpenVPN-protocol voor gebruikers

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor virtuele WAN-gebruikers VPN (punt-naar-site) open VPN-verificatie.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN- &reg; Protocol verbindingen.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. de Azure AD-Tenant maken

Maak een Azure AD-Tenant met behulp van de stappen in het artikel [een nieuwe Tenant maken](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisatie naam
* Oorspronkelijke domeinnaam

Voorbeeld:

   ![Nieuwe Azure AD-Tenant](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. gebruikers van Azure AD-tenants maken

Maak vervolgens twee gebruikers accounts. Maak één globaal beheerders account en één hoofd gebruikers account. Het hoofd gebruikers account wordt gebruikt als uw hoofd account voor insluiten (Service-account). Wanneer u een Azure AD-Tenant gebruikers account maakt, past u de Directory-rol aan voor het type gebruiker dat u wilt maken.

Volg de stappen in [dit artikel](../active-directory/fundamentals/add-users-azure-active-directory.md) om ten minste twee gebruikers te maken voor uw Azure AD-Tenant. Zorg ervoor dat u de **Directory-rol** wijzigt om de volgende account typen te maken:

* Globale beheerder
* Gebruiker

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Azure AD-verificatie inschakelen op de VPN-gateway

1. Zoek de Directory-ID van de map die u voor verificatie wilt gebruiken. Deze wordt weer gegeven in de sectie eigenschappen van de pagina Active Directory.

    ![Map-ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Kopieer de Map-id.

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

    ![Map-ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecteer **accepteren** wanneer u hierom wordt gevraagd.

    ![Accepteren](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Onder uw Azure AD, in **bedrijfs toepassingen**, wordt **Azure VPN** weer gegeven.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Configureer Azure AD-verificatie voor gebruikers-VPN en wijs dit toe aan een virtuele hub door de stappen in [Azure AD-verificatie configureren voor punt-naar-site-verbinding met Azure te](virtual-wan-point-to-site-azure-ad.md) volgen

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren en dit koppelen aan een virtuele hub. Zie [Azure AD-verificatie configureren voor punt-naar-site-verbinding met Azure](virtual-wan-point-to-site-azure-ad.md).
