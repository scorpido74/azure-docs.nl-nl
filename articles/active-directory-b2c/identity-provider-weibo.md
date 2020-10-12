---
title: Registratie instellen en aanmelden met een Weibo-account
titleSuffix: Azure AD B2C
description: Gebruik Azure Active Directory B2C om u aan te melden en u aan te melden bij klanten met Weibo-accounts in uw toepassingen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 08aa7e4af6dc5d5e5bff470bc4c5d023e25b3014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387878"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Stel registratie in en meld u aan met een Weibo-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Een Weibo-toepassing maken

Als u een Weibo-account wilt gebruiken als een id-provider in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen Weibo-account hebt, kunt u zich aanmelden bij [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us) .

1. Meld u aan bij de [Weibo-ontwikkelaars Portal](https://open.weibo.com/) met uw Weibo-account referenties.
1. Nadat u zich hebt aangemeld, selecteert u uw weergave naam in de rechter bovenhoek.
1. Selecteer in de vervolg keuzelijst **编辑开发者信息** (ontwikkelaars gegevens bewerken).
1. Voer de vereiste gegevens in en selecteer **提交** (verzenden).
1. Voltooi het e-mail verificatie proces.
1. Ga naar de [pagina identiteits verificatie](https://open.weibo.com/developers/identity/edit).
1. Voer de vereiste gegevens in en selecteer **提交** (verzenden).

### <a name="register-a-weibo-application"></a>Een Weibo-toepassing registreren

1. Ga naar de [nieuwe pagina](https://open.weibo.com/apps/new)voor het registreren van Weibo-apps.
1. Voer de benodigde toepassings gegevens in.
1. Selecteer **创建** (maken).
1. Kopieer de waarden van de **app-sleutel** en het app- **geheim**. U hebt beide nodig om de ID-provider toe te voegen aan uw Tenant.
1. Upload de vereiste Foto's en voer de benodigde gegevens in.
1. Selecteer **保存以上信息** (opslaan).
1. Selecteer **高级信息** (geavanceerde informatie).
1. Selecteer **编辑** (bewerken) naast het veld voor OAuth 2.0 **授权设置** (omleidings-URL).
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in voor OAuth 2.0- **授权设置** (omleidings-URL). Als uw Tenant naam bijvoorbeeld contoso is, stelt u de URL in op `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Selecteer **提交** (verzenden).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Een Weibo-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **Weibo (preview)**.
1. Voer een **naam** in. Bijvoorbeeld *Weibo*.
1. Voor de **client-id**voert u de app-sleutel in van de Weibo-toepassing die u eerder hebt gemaakt.
1. Voer voor **Clientgeheim** het app-geheim in dat u hebt genoteerd.
1. Selecteer **Opslaan**.
