---
title: Aanmelden en aanmelden met een WeChat-account
titleSuffix: Azure AD B2C
description: Geef aanmeldingen en aanmelding aan klanten met WeChat-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184431"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een WeChat-account instellen met Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Een WeChat-toepassing maken

Als u een WeChat-account wilt gebruiken als identiteitsprovider in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw tenant die deze vertegenwoordigt. Als je nog geen WeChat-account hebt, kun [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)je informatie krijgen op .

### <a name="register-a-wechat-application"></a>Een WeChat-toepassing registreren

1. Meld u [https://open.weixin.qq.com/](https://open.weixin.qq.com/) aan bij uw WeChat-referenties.
1. Selecteer **het** beheercentrum.
1. Volg de stappen om een nieuwe toepassing te registreren.
1. Voer `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` de URL (callback) **in.** Als uw tenantnaam bijvoorbeeld contoso is, stelt `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`u de URL in als .
1. Kopieer de **app-id** en **app-sleutel**. U hebt deze nodig om de identiteitsprovider aan uw tenant toe te voegen.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>WeChat configureren als identiteitsprovider in uw tenant

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **Vervolgens WeChat (Voorbeeld).**
1. Voer een **naam**in . Bijvoorbeeld, *WeChat*.
1. Voer voor de **client-id**de APP-id in van de WeChat-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client de APP-sleutel in die u hebt opgenomen.
1. Selecteer **Opslaan**.
