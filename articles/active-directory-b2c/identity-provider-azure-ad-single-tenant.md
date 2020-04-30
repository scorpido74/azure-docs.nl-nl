---
title: Aanmelding instellen voor een Azure AD-organisatie
titleSuffix: Azure AD B2C
description: Stel aanmelden in voor een specifieke Azure Active Directory organisatie in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678040"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Aanmelden instellen voor een specifieke Azure Active Directory organisatie in Azure Active Directory B2C

Als u een Azure Active Directory (Azure AD) als een [ID-provider](authorization-code-flow.md) in azure AD B2C wilt gebruiken, moet u een toepassing maken waarin deze wordt vertegenwoordigd. In dit artikel leest u hoe u aanmelden kunt inschakelen voor gebruikers van een specifieke Azure AD-organisatie met behulp van een gebruikers stroom in Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD configureren als een id-provider

1. Zorg ervoor dat u de map gebruikt die Azure AD B2C Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **nieuwe OpenID Connect Connect-provider**.
1. Voer een **naam**in. Voer bijvoorbeeld *Contoso Azure AD*in.
1. Voor **meta gegevens-URL**voert u de volgende `{tenant}` URL in die wordt vervangen door de domein naam van uw Azure AD-Tenant:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. Voer voor **client-id**de toepassings-id in die u eerder hebt vastgelegd.
1. Voer voor **client geheim**het client geheim in dat u eerder hebt vastgelegd.
1. Voer voor het **bereik**de `openid profile`in.
1. Behoud de standaard waarden voor het **antwoord type**en de **antwoord modus**.
1. Beschrijving Voer `contoso.com`in voor de **domein Hint**. Zie voor meer informatie [instellen van direct aanmelden met Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Selecteer onder **claim toewijzing van de identiteits provider**de volgende claims:

    * **Gebruikers-id**: *OID*
    * **Weergave naam**: *naam*
    * **Gegeven naam**: *given_name*
    * **Achternaam**: *family_name*
    * **E-mail**: *unique_name*

1. Selecteer **Opslaan**.
