---
title: Registratie instellen en aanmelden met een WeChat-account
titleSuffix: Azure AD B2C
description: Gebruik Azure Active Directory B2C om u aan te melden en u aan te melden bij klanten met WeChat-accounts in uw toepassingen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 462e33c836d8ca0a904e8f7b2e833dc7103311fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387895"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Stel registratie in en meld u aan met een WeChat-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Een WeChat-toepassing maken

Als u een WeChat-account wilt gebruiken als een id-provider in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen WeChat-account hebt, kunt u informatie opvragen op [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) .

### <a name="register-a-wechat-application"></a>Een WeChat-toepassing registreren

1. Meld u aan [https://open.weixin.qq.com/](https://open.weixin.qq.com/) met uw WeChat-referenties.
1. Selecteer**管理中心**(Management Center).
1. Volg de stappen om een nieuwe toepassing te registreren.
1. Voer `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in**授权回调域**(call back-URL) in. Als uw Tenant naam bijvoorbeeld contoso is, stelt u de URL in op `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Kopieer de **App-ID** en **app-sleutel**. U hebt deze nodig om de ID-provider toe te voegen aan uw Tenant.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>WeChat configureren als een id-provider in uw Tenant

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **WeChat (preview)**.
1. Voer een **naam**in. Bijvoorbeeld *WeChat*.
1. Voer voor de **client-id**de app-id in van de WeChat-toepassing die u eerder hebt gemaakt.
1. Voor het **client geheim**voert u de app-sleutel in die u hebt vastgelegd.
1. Selecteer **Opslaan**.
