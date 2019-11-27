---
title: 'MFA inschakelen voor VPN-gebruikers: Azure AD-verificatie'
description: Multi-factor Authentication inschakelen voor VPN-gebruikers
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 7f05b850a0d886ac0df5c542de647f91fe62eb05
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382220"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Azure Multi-Factor Authentication (MFA) inschakelen voor VPN-gebruikers

Als u wilt dat gebruikers wordt gevraagd om een tweede authenticatie factor voordat ze toegang verlenen, kunt u Azure Multi-Factor Authentication (MFA) configureren voor uw Azure AD-Tenant. Met de stappen in dit artikel kunt u een vereiste voor verificatie in twee stappen inschakelen.

## <a name="prereq"></a>Vereiste

De vereiste voor deze configuratie is een geconfigureerde Azure AD-Tenant met behulp van de stappen in [een Tenant configureren](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>De MFA-pagina openen

1. Meld u aan bij Azure Portal.
2. Ga naar **Azure Active Directory-> alle gebruikers**.
3. Selecteer **multi-factor Authentication** om de pagina multi-factor Authentication te openen.

   ![Aanmelden](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>Gebruikers selecteren

1. Selecteer op de pagina **multi-factor Authentication** de gebruiker (s) waarvoor u MFA wilt inschakelen.
2. Selecteer **Inschakelen**.

   ![Selecteer](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Verificatie inschakelen

1. Ga naar **Azure Active Directory-> bedrijfs toepassingen-> alle toepassingen**.
2. Selecteer op de pagina **bedrijfs toepassingen-alle toepassingen** de optie **Azure VPN**.

   ![Map-ID](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Aanmeldings instellingen configureren

Configureer aanmeldings instellingen op de pagina **Azure VPN-eigenschappen** .

1. **Ingeschakeld instellen dat gebruikers zich kunnen aanmelden?** Hierdoor kunnen alle gebruikers in de AD-Tenant verbinding maken met de VPN.
2. Stel de **gebruikers toewijzing vereist** in op **Ja** als u wilt dat de aanmelding wordt beperkt tot alleen gebruikers met machtigingen voor Azure VPN.
3. Sla uw wijzigingen op.

   ![Machtigingen](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren. Zie [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
