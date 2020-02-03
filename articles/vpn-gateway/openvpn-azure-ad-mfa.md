---
title: 'MFA inschakelen voor VPN-gebruikers: Azure AD-verificatie'
description: Multi-factor Authentication inschakelen voor VPN-gebruikers
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964699"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Azure Multi-Factor Authentication (MFA) inschakelen voor VPN-gebruikers

Als u wilt dat gebruikers om een tweede verificatie factor worden gevraagd alvorens toegang te verlenen, kunt u Azure Multi-Factor Authentication (MFA) configureren op basis van de gebruikers-of benutting Multi-Factor Authentication (MFA) via [voorwaardelijke toegang](../active-directory/conditional-access/overview.md) voor meer nauw keurige controle. Het configureren van Multi-Factor Authentication per gebruiker kan gratis worden ingeschakeld, maar wanneer u MFA per gebruiker inschakelt, wordt de gebruiker gevraagd om een tweede factor verificatie voor alle toepassingen die aan de Azure AD-Tenant zijn gekoppeld. Met voorwaardelijke toegang kunt u beter bepalen hoe een tweede factor moet worden bevorderd en hoe MFA kan worden toegewezen aan alleen VPN en niet aan andere toepassingen die aan de Azure AD-Tenant zijn gekoppeld.

## <a name="enableauth"></a>Verificatie inschakelen

1. Ga naar **Azure Active Directory-> bedrijfs toepassingen-> alle toepassingen**.
2. Selecteer op de pagina **bedrijfs toepassingen-alle toepassingen** de optie **Azure VPN**.

   ![Map-ID](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Aanmeldings instellingen configureren

Configureer aanmeldings instellingen op de pagina **Azure VPN-eigenschappen** .

1. **Ingeschakeld instellen dat gebruikers zich kunnen aanmelden?** Hierdoor kunnen alle gebruikers in de AD-Tenant verbinding maken met de VPN.
2. Stel de **gebruikers toewijzing vereist** in op **Ja** als u wilt dat de aanmelding wordt beperkt tot alleen gebruikers met machtigingen voor Azure VPN.
3. Sla uw wijzigingen op.

   ![Machtigingen](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>Optie 1-Multi-Factor Authentication (MFA) inschakelen via voorwaardelijke toegang

Met voorwaardelijke toegang kunt u nauw keurig toegangs beheer per toepassing instellen.  Als u gebruik wilt maken van voorwaardelijke toegang, moet u Azure AD Premium 1 of een grotere licentie hebben die is toegepast op de gebruikers die zijn onderworpen aan de regels voor voorwaardelijke toegang.

1. Selecteer op de pagina **bedrijfs toepassingen-alle toepassingen** de optie **Azure VPN**, selecteer **voorwaardelijke toegang**en klik op **Nieuw beleid**.
2. Onder gebruikers en groepen op het tabblad *include* **selecteert u gebruikers en groepen**, controleert u **gebruikers en groepen**en selecteert u een groep of een set gebruikers die moet worden onderhevig aan MFA.  Klik op **Gereed**.
![Toewijzingen](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Controleer onder **Grant**, **toegang verlenen**, Controleer op **multi-factor Authentication vereisen**, Controleer of **alle geselecteerde besturings elementen zijn vereist**en klik op de knop **selecteren** .
toegang ![MFA verlenen](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Schakel **het selectie vakje** **beleid inschakelen** in en klik op de knop **maken** .
Beleid](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png) ![inschakelen

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>Optie 2: Multi-Factor Authentication (MFA) per gebruiker inschakelen

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren. Zie [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
