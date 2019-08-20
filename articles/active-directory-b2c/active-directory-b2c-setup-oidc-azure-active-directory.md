---
title: Aanmelding instellen voor een Azure Active Directory organisatie-Azure Active Directory B2C
description: Stel aanmelden in voor een specifieke Azure Active Directory organisatie in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 477b4e51c49a558aed0e5623a3821fa9b8d9eabd
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622370"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Aanmelden instellen voor een specifieke Azure Active Directory organisatie in Azure Active Directory B2C

Als u een Azure Active Directory (Azure AD) als een [ID-provider](active-directory-b2c-reference-oauth-code.md) in azure AD B2C wilt gebruiken, moet u een toepassing maken waarin deze wordt vertegenwoordigd. In dit artikel leest u hoe u aanmelden kunt inschakelen voor gebruikers van een specifieke Azure AD-organisatie met behulp van een gebruikers stroom in Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Een Azure AD-app maken

Als u het aanmelden voor gebruikers van een specifieke Azure AD-organisatie wilt inschakelen, moet u een toepassing registreren in de Azure AD-Tenant van de organisatie, die niet hetzelfde is als uw Azure AD B2C Tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Azure AD-Tenant bevat. Dit is niet dezelfde Tenant als uw Azure AD B2C-Tenant.
3. Kies **alle services** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **app-registraties**.
4. Selecteer **nieuwe registratie**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld `Azure AD B2C App`.
6. Accepteer de selectie van **accounts in deze organisatie Directory alleen** voor deze toepassing.
7. Voor de omleidings- **URI**accepteert u de waarde van **Web**en voert u de volgende URL in in kleine `your-B2C-tenant-name` letters, waar wordt vervangen door de naam van uw Azure AD B2C-Tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Alle Url's moeten nu gebruikmaken van [b2clogin.com](b2clogin.md).

8. Klik op **registreren**. Kopieer de **client-id** die u later wilt gebruiken.
9. Selecteer **certificaten & geheimen** in het menu toepassing en selecteer vervolgens **Nieuw client geheim**.
10. Voer een naam in voor het client geheim. Bijvoorbeeld `Azure AD B2C App Secret`.
11. Selecteer de verval periode. Accepteer voor deze toepassing de selectie van **in één jaar**.
12. Selecteer **toevoegen** en kopieer de waarde van het nieuwe client geheim dat wordt weer gegeven voor later gebruik.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD configureren als een id-provider

1. Zorg ervoor dat u de map gebruikt die Azure AD B2C Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **nieuwe OpenID Connect Connect-provider**.
1. Voer een **naam**in. Voer bijvoorbeeld *Contoso Azure AD*in.
1. Voor **meta gegevens-URL**voert u de volgende `your-AD-tenant-domain` URL in die wordt vervangen door de domein naam van uw Azure AD-Tenant:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Voer voor **client-id**de toepassings-id in die u eerder hebt vastgelegd.
1. Voer voor **client geheim**het client geheim in dat u eerder hebt vastgelegd.
1. Behoud de standaard waarden voor het **bereik**, het **antwoord type**en de **antwoord modus**.
1. Beschrijving Voer een waarde in voor **Domain_hint**. Bijvoorbeeld *ContosoAD*. Dit is de waarde die moet worden gebruikt om te verwijzen naar deze id-provider met behulp van *domain_hint* in de aanvraag.
1. Voer onder **claim toewijzing**van de identiteits provider de volgende claim toewijzings waarden in:

    * **Gebruikers-id**: *OID*
    * **Weergave naam**: *naam*
    * Voor **naam**: *given_name*
    * **Achternaam**: *family_name*
    * **E-mail**: *unique_name*

1. Selecteer **Opslaan**.
