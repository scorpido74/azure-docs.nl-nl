---
title: Direct aanmelden instellen met Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het vooraf invullen van de aanmeldingsnaam of het doorverwijzen naar een aanbieder van sociale identiteit.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a02ad3ea43ae9d91489417bc314e3c23d54a958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188763"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Direct aanmelden instellen met Azure Active Directory B2C

Wanneer u aanmelding voor uw toepassing instelt met Azure Active Directory (AD) B2C, u de aanmeldingsnaam of directe aanmelding vooraf invullen bij een specifieke aanbieder van sociale identiteit, zoals Facebook, LinkedIn of een Microsoft-account.

## <a name="prepopulate-the-sign-in-name"></a>De aanmeldingsnaam vooraf invullen

Tijdens een gebruikersreis met een aanmelding kan een toepassing van een relying party zich richten op een specifieke gebruiker of domeinnaam. Wanneer u een gebruiker target, kan een toepassing `login_hint` in de autorisatieaanvraag de queryparameter met de aanmeldingsnaam van de gebruiker opgeven. Azure AD B2C vult automatisch de aanmeldingsnaam in, terwijl de gebruiker alleen het wachtwoord hoeft op te geven.

![Aanmeldingspagina met login_hint queryparam gemarkeerd in URL](./media/direct-signin/login-hint.png)

De gebruiker kan de waarde in het aanmeldingstekstvak wijzigen.

Als u een aangepast beleid `SelfAsserted-LocalAccountSignin-Email` gebruikt, overschrijft u het technische profiel. Stel `<InputClaims>` in de sectie de standaardwaarde van `{OIDC:LoginHint}`de claim signInName in op . De `{OIDC:LoginHint}` variabele bevat de `login_hint` waarde van de parameter. Azure AD B2C leest de waarde van de signInName-claim en vult het tekstvak signInName vooraf in.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Aanmelding doorverwijzen naar een sociale provider

Als je de aanmeldingsreis voor je toepassing hebt geconfigureerd om sociale accounts op te `domain_hint` nemen, zoals Facebook, LinkedIn of Google, kun je de parameter opgeven. Deze queryparameter geeft een hint naar Azure AD B2C over de sociale identiteitsprovider die moet worden gebruikt voor aanmelding. Als de toepassing bijvoorbeeld `domain_hint=facebook.com`aangeeft, gaat aanmelden rechtstreeks naar de aanmeldingspagina van Facebook.

![Aanmeldingspagina met domain_hint queryparam gemarkeerd in URL](./media/direct-signin/domain-hint.png)

Als u een aangepast beleid gebruikt, u `<Domain>domain name</Domain>` de domeinnaam `<ClaimsProvider>`configureren met behulp van het XML-element van een.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


