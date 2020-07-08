---
title: 'MFA inschakelen voor VPN-gebruikers: Azure AD-verificatie'
description: Multi-factor Authentication inschakelen voor VPN-gebruikers
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: 80a6b342990100b3e79cc8194722a6eb84080ef6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987294"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Azure Multi-Factor Authentication (MFA) inschakelen voor VPN-gebruikers

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Verificatie inschakelen

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Aanmeldings instellingen configureren

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Optie 1: toegang per gebruiker

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Optie 2: voorwaardelijke toegang

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren. Zie [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).