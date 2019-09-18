---
title: Registratie instellen en aanmelden met een GitHub-account-Azure Active Directory B2C
description: Meld u aan en meld u bieden aan klanten met een GitHub-account in uw toepassingen met behulp van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74c663d8847c2829a5d9466f8e601dd44593a6f8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065201"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Instellen van zich kunnen registreren en aanmelden met een GitHub-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Een GitHub-OAuth-toepassing maken

Als u een GitHub-account wilt gebruiken als een [ID-provider](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen GitHub-account hebt, kunt u zich aanmelden bij [https://www.github.com/](https://www.github.com/).

1. Aanmelden bij de [GitHub Developer](https://github.com/settings/developers) -website met uw GitHub-referenties.
1. Selecteer **OAuth Apps** en selecteer vervolgens **nieuwe OAuth-App**.
1. Voer een **toepassingsnaam** en uw **URL van de startpagina**.
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in **URL voor terugbellen voor autorisatie**. Vervang `your-tenant-name` met de naam van uw Azure AD B2C-tenant. Gebruik alleen kleine letters bij het invoeren van de tenantnaam van uw, zelfs als de tenant is gedefinieerd met behulp van hoofdletters in Azure AD B2C.
1. Klik op **toepassing registreren**.
1. Kopieer de waarden van **Client-ID** en **Clientgeheim**. U moet zowel de id-provider toevoegen aan uw tenant.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Een GitHub-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **github (preview)** .
1. Voer een **naam**in. Bijvoorbeeld *github*.
1. Voer voor de **client-id**de client-id in van de GitHub-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.
