---
title: 'MFA inschakelen voor VPN-gebruikers: Azure AD-verificatie'
description: Multi-factor Authentication inschakelen voor VPN-gebruikers
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: fcb60e80189da89b3f634c14582be606307536e6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166691"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Azure Multi-Factor Authentication (MFA) inschakelen voor VPN-gebruikers

Als u wilt dat gebruikers wordt gevraagd om een tweede authenticatie factor voordat ze toegang verlenen, kunt u Azure Multi-Factor Authentication (MFA) configureren voor uw Azure AD-Tenant. Met de stappen in dit artikel kunt u een vereiste voor verificatie in twee stappen inschakelen.

## <a name="prereq"></a>Vereiste

De vereiste voor deze configuratie is een geconfigureerde Azure AD-Tenant met behulp van de stappen in [een Tenant configureren](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Aanmeldings instellingen configureren

Configureer aanmeldings instellingen op de pagina **Azure VPN-eigenschappen** .

1. **Ingeschakeld instellen dat gebruikers zich kunnen aanmelden?** Hierdoor kunnen alle gebruikers in de AD-Tenant verbinding maken met de VPN.
2. Stel de **gebruikers toewijzing vereist** in op **Ja** als u wilt dat de aanmelding wordt beperkt tot alleen gebruikers met machtigingen voor Azure VPN.
3. Sla uw wijzigingen op.

   ![Machtigingen](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren. Zie [Azure AD-verificatie configureren voor punt-naar-site-verbinding met Azure](virtual-wan-point-to-site-azure-ad.md).
