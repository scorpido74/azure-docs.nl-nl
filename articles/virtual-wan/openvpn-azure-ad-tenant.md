---
title: 'Azure AD-tenant voor VPN-verbindingen voor gebruikers: Azure AD-verificatie'
description: U Azure Virtual WAN User VPN (point-to-site) gebruiken om verbinding te maken met uw VNet met Azure AD-verificatie
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059448"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Een Azure Active Directory-tenant maken voor vpn-protocolverbindingen van gebruikersvpn

Wanneer u verbinding maakt met uw VNet, u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het Open VPN-protocol gebruikt, u ook Azure Active Directory-verificatie gebruiken. Met dit artikel u een Azure AD-tenant instellen voor Virtual WAN User VPN (point-to-site) Open VPN-verificatie.

> [!NOTE]
> Azure AD-verificatie wordt alleen&reg; ondersteund voor OpenVPN-protocolverbindingen.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. De Azure AD-tenant maken

Maak een Azure AD-tenant met de stappen in [het artikel Een nieuw tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) maken:

* Organisatienaam
* Oorspronkelijke domeinnaam

Voorbeeld:

   ![Nieuwe Azure AD-tenant](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD-tenantgebruikers maken

Maak vervolgens twee gebruikersaccounts aan. Maak één Global Admin-account en één hoofdgebruikersaccount. Het hoofdgebruikersaccount wordt gebruikt als uw hoofdinsluitingsaccount (serviceaccount). Wanneer u een Azure AD-tenantgebruikersaccount maakt, past u de maprol aan voor het type gebruiker dat u wilt maken.

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecteer het globale **beheerdersaccount** als u daarom wordt gevraagd.

    ![Directory-id](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecteer **Accepteren** wanneer daarom wordt gevraagd.

    ![Accepteren](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Onder uw Azure AD ziet u in **Enterprise-toepassingen** **Azure VPN** vermeld.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Azure AD-verificatie configureren voor gebruikersvpn en deze toewijzen aan een virtuele hub door de stappen te volgen in [Azure AD-verificatie configureren voor point-to-site-verbinding met Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Volgende stappen

Om verbinding te maken met uw virtuele netwerk, moet u een VPN-clientprofiel maken en configureren en koppelen aan een virtuele hub. Zie [Azure AD-verificatie configureren voor point-to-site-verbinding met Azure](virtual-wan-point-to-site-azure-ad.md).
