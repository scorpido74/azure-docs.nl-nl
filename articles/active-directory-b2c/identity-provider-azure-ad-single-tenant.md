---
title: Aanmelden instellen voor een Azure AD-organisatie
titleSuffix: Azure AD B2C
description: Aanmelden instellen voor een specifieke Azure Active Directory-organisatie in Azure Active Directory B2C.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678040"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Aanmelden instellen voor een specifieke Azure Active Directory-organisatie in Azure Active Directory B2C

Als u een Azure Active Directory (Azure AD) wilt gebruiken als [identiteitsprovider](authorization-code-flow.md) in Azure AD B2C, moet u een toepassing maken die deze vertegenwoordigt. In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers uit een specifieke Azure AD-organisatie met behulp van een gebruikersstroom in Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD configureren als identiteitsprovider

1. Zorg ervoor dat u de map gebruikt die Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw Azure AD B2C-tenant.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **vervolgens Nieuwe OpenID Connect-provider**.
1. Voer een **naam**in . Typ bijvoorbeeld *Contoso Azure AD*.
1. Voer voor de url met `{tenant}` **aagegevens**de volgende URL in die wordt vervangen door de domeinnaam van uw Azure AD-tenant:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. Voer **voor client-id**de toepassings-id in die u eerder hebt opgenomen.
1. Voer **voor clientgeheim**het clientgeheim in dat u eerder hebt opgenomen.
1. Voer **voor**de `openid profile`scope de .
1. Laat de standaardwaarden voor **antwoordtype**en **reactiemodus .**
1. (Optioneel) Voer voor de `contoso.com` **domeinhint**. Zie [Directe aanmelding instellen met Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider)voor meer informatie.
1. Selecteer **onder Het in kaart brengen van claims voor identiteitsopties**de volgende claims:

    * **Gebruikersnaam**: *oid*
    * **Weergavenaam**: *naam*
    * **Voornaam**: *given_name*
    * **Achternaam**: *family_name*
    * **E-mail**: *unique_name*

1. Selecteer **Opslaan**.
