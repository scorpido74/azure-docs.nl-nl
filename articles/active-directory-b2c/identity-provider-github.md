---
title: Registratie instellen en aanmelden met een GitHub-account
titleSuffix: Azure AD B2C
description: Gebruik Azure Active Directory B2C om u aan te melden en u aan te melden bij klanten met GitHub-accounts in uw toepassingen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba2441ae48c99d63ae637d2b80069058a04c5ef9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388184"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Stel registratie in en meld u aan met een GitHub-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Een GitHub OAuth-toepassing maken

Als u een GitHub-account wilt gebruiken als een [ID-provider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen GitHub-account hebt, kunt u zich aanmelden bij [https://www.github.com/](https://www.github.com/) .

1. Meld u aan bij de [github-ontwikkelaars](https://github.com/settings/developers) website met uw github-referenties.
1. Selecteer **OAuth-apps** en selecteer vervolgens **nieuwe OAuth-app**.
1. Voer een **toepassings naam** en de **URL van uw start pagina**in.
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` de **URL voor de autorisatie-call back**in. Vervang door `your-tenant-name` de naam van uw Azure AD B2C-Tenant. Gebruik alleen kleine letters wanneer u uw Tenant naam invoert, zelfs als de Tenant is gedefinieerd met hoofd letters in Azure AD B2C.
1. Klik op **toepassing registreren**.
1. Kopieer de waarden van de **client-id** en het **client geheim**. U hebt beide nodig om de ID-provider toe te voegen aan uw Tenant.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Een GitHub-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **github (preview)**.
1. Voer een **naam**in. Bijvoorbeeld *github*.
1. Voer voor de **client-id**de client-id in van de GitHub-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.
