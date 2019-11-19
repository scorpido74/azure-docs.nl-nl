---
title: 'MFA inschakelen voor VPN-gebruikers: Azure AD-verificatie | Microsoft Docs'
description: Multi-factor Authentication inschakelen voor VPN-gebruikers
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 3f407ecfcbfdff72b79bfe6b5bc3e6b7606b1cf0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174627"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Multi-Factor Authentication (MFA) inschakelen voor VPN-gebruikers

U kunt MFA inschakelen voor gebruikers in uw Azure AD-Tenant, zodat gebruikers om een tweede factor verificatie wordt gevraagd voordat toegang wordt verleend.

> [! Vereiste] u hebt een Azure AD-Tenant geconfigureerd zoals beschreven in het document ' een Tenant configureren '.
>

### <a name="tenant"></a>1. Meld u aan bij de Azure Portal en navigeer naar **Azure Active Directory** , **alle gebruikers** en klik op **multi-factor Authentication**


   ![Nieuwe Azure AD-Tenant](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. Selecteer de gebruiker (s) waarvoor u MFA wilt inschakelen en klik op **inschakelen**

   ![Nieuwe Azure AD-Tenant](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. Ga naar **Azure Active Directory** , **zakelijke toepassingen**, **alle toepassingen** en klik op **Azure VPN**


   ![Map-ID](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. Controleer of de **ingeschakelde gebruikers zich kunnen aanmelden?** is ingesteld op Ja. Als u wilt dat alleen de gebruikers die machtigingen hebben voor de Azure VPN zich kunnen aanmelden, moet u de **gebruikers toewijzing** instellen op Ja. als dat niet het geval is, kunnen alle gebruikers in de AD-Tenant verbinding maken met VPN.

   ![Machtigingen](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren. Zie [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
