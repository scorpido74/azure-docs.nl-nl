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
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: af6a7611381cbf7a251e65969d156f4c40d71843
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126768"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Wachtwoord complexiteit configureren met aangepaste beleids regels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C) kunt u de complexiteits vereisten configureren voor wacht woorden die door een gebruiker worden verschaft bij het maken van een account. Azure AD B2C maakt standaard gebruik van **sterke** wacht woorden. In dit artikel leest u hoe u wachtwoord complexiteit kunt configureren in [aangepaste beleids regels](custom-policy-overview.md). Het is ook mogelijk om wachtwoord complexiteit te configureren in [gebruikers stromen](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid](custom-policy-get-started.md). U moet beschikken over een werkend aangepast beleid voor het aanmelden en aanmelden met lokale accounts.


## <a name="add-the-elements"></a>De elementen toevoegen

Als u de wachtwoord complexiteit wilt configureren, overschrijft u de `newPassword` en `reenterPassword` [claim typen](claimsschema.md) met een verwijzing naar [predikaten-validaties](predicates.md#predicatevalidations). Met het element PredicateValidations wordt een set predikaten gegroepeerd om een gebruikers invoer validatie te vormen die kan worden toegepast op een claim type. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>.

1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de `newPassword`-en `reenterPassword` claims toe aan het **ClaimsSchema** -element.

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predikaten](predicates.md) definieert een basis validatie om de waarde van een claim type te controleren en retourneert waar of onwaar. De validatie wordt uitgevoerd met behulp van een opgegeven methode-element en een set para meters die relevant zijn voor de methode. Voeg de volgende predikaten toe aan het element **BuildingBlocks** direct na het sluiten van het `</ClaimsSchema>` element:

    ```XML
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Voeg de volgende predikaten-validaties toe aan het element **BuildingBlocks** , direct na het sluiten van het `</Predicates>` element:

    ```XML
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. De volgende technische profielen zijn [Active Directory technische profielen](active-directory-technical-profile.md), waarmee gegevens worden gelezen en geschreven naar Azure Active Directory. Vervang deze technische profielen in het extensie bestand. Gebruik `PersistedClaims` om het beleid voor sterke wacht woorden uit te scha kelen. Zoek het element **ClaimsProviders** .  Voeg de volgende claim providers als volgt toe:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Sla het beleids bestand op.

## <a name="test-your-policy"></a>Uw beleid testen

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Klik op het tabblad Aangepaste beleids regels op **beleid uploaden**.
6. Selecteer **het beleid overschrijven als dit bestaat**, en zoek en selecteer het bestand *TrustFrameworkExtensions. XML* .
7. Klik op **Uploaden**.

### <a name="run-the-policy"></a>Het beleid uitvoeren

1. Open het registratie-of aanmeldings beleid. Bijvoorbeeld *B2C_1A_signup_signin*.
2. Selecteer voor **toepassing**de toepassing die u eerder hebt geregistreerd. Om het token weer te geven, moet de **antwoord-URL** `https://jwt.ms`weer geven.
3. Klik op **Nu uitvoeren**.
4. Selecteer **nu aanmelden**, voer een e-mail adres in en voer een nieuw wacht woord in. Richt lijnen worden weer gegeven voor wachtwoord beperkingen. Voer de gebruikers gegevens in en klik vervolgens op **maken**. De inhoud van het geretourneerde token moet worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van wachtwoord wijzigingen met aangepaste beleids regels in azure Active Directory B2C](custom-policy-password-change.md).
- - Meer informatie over de [predikaten](predicates.md) en [PredicateValidations](predicates.md#predicatevalidations) -elementen vindt u in de IEF-verwijzing.
