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
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188304"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Aanmelden instellen voor een specifieke Azure Active Directory-organisatie in Azure Active Directory B2C

Als u een Azure Active Directory (Azure AD) wilt gebruiken als [identiteitsprovider](authorization-code-flow.md) in Azure AD B2C, moet u een toepassing maken die deze vertegenwoordigt. In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers uit een specifieke Azure AD-organisatie met behulp van een gebruikersstroom in Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Een Azure AD-app maken

Als u aanmelding wilt inschakelen voor gebruikers van een specifieke Azure AD-organisatie, moet u een toepassing registreren binnen de Azure AD-tenant van de organisatie Azure, die niet hetzelfde is als uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw Azure AD-tenant. Dit is niet dezelfde tenant als uw Azure AD B2C-tenant.
3. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
4. Selecteer **Nieuwe registratie**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld `Azure AD B2C App`.
6. Accepteer de selectie van **accounts in deze organisatiemap alleen** voor deze toepassing.
7. Accepteer voor de **URI-omleiding**de waarde van **web**en voer de `your-B2C-tenant-name` volgende URL in alle kleine letters in, waarbij de naam van uw Azure AD B2C-tenant wordt vervangen. Bijvoorbeeld: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Alle URL's moeten nu [b2clogin.com](b2clogin.md)gebruiken.

8. Klik **op Registreren**. Kopieer de **toepassings-id (client)** die later moet worden gebruikt.
9. Selecteer **Certificaten & geheimen** in het toepassingsmenu en selecteer Vervolgens Nieuw **clientgeheim**.
10. Voer een naam in voor het geheim van de klant. Bijvoorbeeld `Azure AD B2C App Secret`.
11. Selecteer de vervaldatum. Voor deze aanvraag, accepteer de selectie van **In 1 jaar**.
12. Selecteer De waarde van het nieuwe clientgeheim **toevoegen** en kopiëren dat later wordt weergegeven.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD configureren als identiteitsprovider

1. Zorg ervoor dat u de map gebruikt die Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw Azure AD B2C-tenant.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **vervolgens Nieuwe OpenID Connect-provider**.
1. Voer een **naam**in . Typ bijvoorbeeld *Contoso Azure AD*.
1. Voer voor de url met `your-AD-tenant-domain` **aagegevens**de volgende URL in die wordt vervangen door de domeinnaam van uw Azure AD-tenant:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Gebruik** bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`het eindpunt van het Azure AD v2.0-metagegevens niet . Als u dit doet, `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` ontstaat een fout die vergelijkbaar is met wanneer u probeert in te loggen.

1. Voer **voor client-id**de toepassings-id in die u eerder hebt opgenomen.
1. Voer **voor clientgeheim**het clientgeheim in dat u eerder hebt opgenomen.
1. Laat de standaardwaarden voor **bereik,** **antwoordtype**en **reactiemodus .**
1. (Optioneel) Voer een waarde in voor **Domain_hint**. Bijvoorbeeld *ContosoAD*. Dit is de waarde die u moet gebruiken wanneer u verwijst naar deze identiteitsprovider met behulp van *domain_hint* in de aanvraag.
1. Voer **onder Het toewijzen van claims voor identiteitsprovider**de volgende waarden voor het toewijzen van claims in:

    * **Gebruikersnaam**: *oid*
    * **Weergavenaam**: *naam*
    * **Voornaam**: *given_name*
    * **Achternaam**: *family_name*
    * **E-mail**: *unique_name*

1. Selecteer **Opslaan**.
