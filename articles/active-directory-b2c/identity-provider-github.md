---
title: Aanmelden en aanmelden met een GitHub-account instellen
titleSuffix: Azure AD B2C
description: Geef aanmelden en aanmelden aan klanten met GitHub-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188202"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een GitHub-account instellen met Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Een GitHub OAuth-toepassing maken

Als u een GitHub-account wilt gebruiken als [identiteitsprovider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw tenant die deze vertegenwoordigt. Als je nog geen GitHub-account hebt, kun [https://www.github.com/](https://www.github.com/)je je aanmelden bij .

1. Meld u aan bij de [GitHub Developer-website](https://github.com/settings/developers) met uw GitHub-referenties.
1. Selecteer **OAuth-apps** en selecteer **Vervolgens Nieuwe OAuth-app**.
1. Voer een **toepassingsnaam** en uw url van de **homepage in.**
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` de **URL van de terugroepterugroeping van autorisatie**in . Vervang `your-tenant-name` de naam van uw Azure AD B2C-tenant. Gebruik alle kleine letters bij het invoeren van de tenantnaam, zelfs als de tenant is gedefinieerd met hoofdletters in Azure AD B2C.
1. Klik **op Toepassing registreren**.
1. Kopieer de waarden van **client-id** en **clientgeheim**. U hebt beide nodig om de identiteitsprovider aan uw tenant toe te voegen.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Een GitHub-account configureren als identiteitsprovider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **Vervolgens GitHub (Voorbeeld)**.
1. Voer een **naam**in . Bijvoorbeeld, *GitHub*.
1. Voer voor de **client-id**de client-id in van de GitHub-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client het clientgeheim in dat u hebt opgenomen.
1. Selecteer **Opslaan**.
