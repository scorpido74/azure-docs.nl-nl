---
title: Wachtwoord complexiteit configureren met aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Vereisten voor wachtwoord complexiteit configureren met behulp van een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2de1130e28b5071913e4cf3632c3fe4407597a98
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189137"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Wachtwoord complexiteit configureren met aangepaste beleids regels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C) kunt u de complexiteits vereisten configureren voor wacht woorden die door een gebruiker worden verschaft bij het maken van een account. Azure AD B2C maakt standaard gebruik van **sterke** wacht woorden. In dit artikel leest u hoe u wachtwoord complexiteit kunt configureren in [aangepaste beleids regels](custom-policy-overview.md). Het is ook mogelijk om wachtwoord complexiteit te configureren in [gebruikers stromen](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid in Active Directory B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>De elementen toevoegen

1. Kopieer het *SignUpOrSignIn. XML-* bestand dat u met het eerste pakket hebt gedownload en noem dit *SingUpOrSignInPasswordComplexity. XML*.
2. Open het bestand *SingUpOrSignInPasswordComplexity. XML* en wijzig de **PolicyId** en de **PublicPolicyUri** in een nieuwe beleids naam. Bijvoorbeeld *B2C_1A_signup_signin_password_complexity*.
3. Voeg de volgende **claim** type-elementen met de id's van `newPassword` en `reenterPassword`toe:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Predikaten](predicates.md) hebben methoden van het type `IsLengthRange` of `MatchesRegex`. Het `MatchesRegex` type wordt gebruikt om een reguliere expressie te vinden. Het `IsLengthRange` type neemt de minimale en maximale teken reeks lengte. Voeg een **predikaten** -element toe aan het **BuildingBlocks** -element als dit niet bestaat met de volgende **predicaat** elementen:

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Elk **InputValidation** -element wordt samengesteld met behulp van de gedefinieerde **predicaat** elementen. Met dit element kunt u Booleaanse aggregaties uitvoeren die vergelijkbaar zijn met `and` en `or`. Voeg een **InputValidations** -element toe aan het **BuildingBlocks** -element als dit niet bestaat met het volgende **InputValidation** -element:

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Zorg ervoor dat het technische profiel **PolicyProfile** de volgende elementen bevat:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Sla het beleids bestand op.

## <a name="test-your-policy"></a>Uw beleid testen

Bij het testen van uw toepassingen in Azure AD B2C kan het nuttig zijn om het Azure AD B2C-token terug te krijgen naar `https://jwt.ms` om de claims in de toepassing te kunnen bekijken.

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Klik op het tabblad Aangepaste beleids regels op **beleid uploaden**.
6. Selecteer **het beleid overschrijven als dit bestaat**, en zoek en selecteer het bestand *SingUpOrSignInPasswordComplexity. XML* .
7. Klik op **Uploaden**.

### <a name="run-the-policy"></a>Het beleid uitvoeren

1. Open het beleid dat u hebt gewijzigd. Bijvoorbeeld *B2C_1A_signup_signin_password_complexity*.
2. Selecteer voor **toepassing**de toepassing die u eerder hebt geregistreerd. Om het token weer te geven, moet de **antwoord-URL** `https://jwt.ms`weer geven.
3. Klik op **Nu uitvoeren**.
4. Selecteer **nu aanmelden**, voer een e-mail adres in en voer een nieuw wacht woord in. Richt lijnen worden weer gegeven voor wachtwoord beperkingen. Voer de gebruikers gegevens in en klik vervolgens op **maken**. De inhoud van het geretourneerde token moet worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van wachtwoord wijzigingen met aangepaste beleids regels in azure Active Directory B2C](custom-policy-password-change.md).


