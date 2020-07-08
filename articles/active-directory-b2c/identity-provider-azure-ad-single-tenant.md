---
title: Aanmelding instellen voor een Azure AD-organisatie
titleSuffix: Azure AD B2C
description: Stel aanmelden in voor een specifieke Azure Active Directory organisatie in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: ce5f373576f13a4b1bdb88b5ffb7869a2d7865cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388354"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Aanmelden instellen voor een specifieke Azure Active Directory organisatie in Azure Active Directory B2C

Als u een Azure Active Directory (Azure AD) als een [ID-provider](authorization-code-flow.md) in azure AD B2C wilt gebruiken, moet u een toepassing maken waarin deze wordt vertegenwoordigd. In dit artikel leest u hoe u aanmelden kunt inschakelen voor gebruikers van een specifieke Azure AD-organisatie met behulp van een gebruikers stroom in Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD configureren als een id-provider

1. Zorg ervoor dat u de map gebruikt die Azure AD B2C Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **nieuwe OpenID Connect Connect-provider**.
1. Voer een **naam**in. Voer bijvoorbeeld *Contoso Azure AD*in.
1. Voor **meta gegevens-URL**voert u de volgende URL `{tenant}` in die wordt vervangen door de domein naam van uw Azure AD-Tenant:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. Voer voor **client-id**de toepassings-id in die u eerder hebt vastgelegd.
1. Voer voor **client geheim**het client geheim in dat u eerder hebt vastgelegd.
1. Voer voor het **bereik**de in `openid profile` .
1. Behoud de standaard waarden voor het **antwoord type**en de **antwoord modus**.
1. Beschrijving Voer in voor de **domein Hint** `contoso.com` . Zie voor meer informatie [instellen van direct aanmelden met Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Selecteer onder **claim toewijzing van de identiteits provider**de volgende claims:

    * **Gebruikers-id**: *OID*
    * **Weergave naam**: *naam*
    * **Gegeven naam**: *given_name*
    * **Achternaam**: *family_name*
    * **E-mail**: *unique_name*

1. Selecteer **Opslaan**.
