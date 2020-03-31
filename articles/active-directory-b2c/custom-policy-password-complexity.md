---
title: Complexiteit van wachtwoorden configureren met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Vereisten voor de complexiteit van wachtwoorden configureren met behulp van een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138431"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Wachtwoordcomplexiteit configureren met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C) u de complexiteitsvereisten configureren voor wachtwoorden die door een gebruiker worden verstrekt bij het maken van een account. Azure AD B2C gebruikt standaard **Sterke** wachtwoorden. In dit artikel ziet u hoe u de complexiteit van wachtwoorden configureert in [aangepast beleid.](custom-policy-overview.md) Het is ook mogelijk om wachtwoordcomplexiteit in [gebruikersstromen](user-flow-password-complexity.md)te configureren.

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Aan de slag met aangepast beleid](custom-policy-get-started.md). U moet een werkend aangepast beleid hebben voor aanmelden en aanmelden met lokale accounts.


## <a name="add-the-elements"></a>De elementen toevoegen

Als u de complexiteit van `newPassword` `reenterPassword` het wachtwoord wilt configureren, overschrijft u de typen en [claimtypen](claimsschema.md) met een verwijzing naar [predicaatvalidaties.](predicates.md#predicatevalidations) Het element PredicateValidations groepeert een set predicaten om een gebruikersinvoervalidatie te vormen die kan worden toegepast op een claimtype. Open het extensiesbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>

1. Zoek naar het element [Bouwstenen.](buildingblocks.md) Als het element niet bestaat, voeg je het toe.
1. Zoek het element [ClaimSchema.](claimsschema.md) Als het element niet bestaat, voeg je het toe.
1. Voeg `newPassword` de `reenterPassword` claims en claims toe aan het **element ClaimsSchema.**

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predicaten](predicates.md) definieert een basisvalidatie om de waarde van een claimtype te controleren en retourneert waar of onwaar. De validatie wordt uitgevoerd met behulp van een opgegeven methodeelement en een set parameters die relevant zijn voor de methode. Voeg de volgende predicaten toe aan het element `</ClaimsSchema>` **Bouwstenen,** direct na het sluiten van het element:

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

1. Voeg de volgende predicaatvalidaties toe aan het element `</Predicates>` **Bouwstenen,** onmiddellijk na het sluiten van het element:

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

1. De volgende technische profielen zijn [technische profielen van Active Directory,](active-directory-technical-profile.md)die gegevens lezen en schrijven naar Azure Active Directory. Overschrijf deze technische profielen in het extensiebestand. Gebruik `PersistedClaims` om het sterke wachtwoordbeleid uit te schakelen. Zoek het element **Claimproviders.**  Voeg als volgt de volgende claimproviders toe:

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

1. Sla het beleidsbestand op.

## <a name="test-your-policy"></a>Uw beleid testen

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Identity Experience Framework**.
5. Klik op de pagina Aangepast beleid op **Beleid uploaden**.
6. Selecteer **Het beleid overschrijven als het bestaat**en zoek en selecteer het bestand *TrustFrameworkExtensions.xml.*
7. Klik op **Uploaden**.

### <a name="run-the-policy"></a>Voer het beleid uit

1. Open het aanmeldings- of aanmeldingsbeleid. Bijvoorbeeld, *B2C_1A_signup_signin*.
2. Selecteer **voor Toepassing**uw toepassing die u eerder hebt geregistreerd. Als u het token wilt `https://jwt.ms`zien, moet de URL van het **antwoord** worden weergegeven .
3. Klik op **Nu uitvoeren**.
4. Selecteer **Nu aanmelden,** voer een e-mailadres in en voer een nieuw wachtwoord in. Richtlijnen worden gepresenteerd over wachtwoordbeperkingen. Voltooi het invoeren van de gebruikersgegevens en klik op **Maken**. U moet de inhoud zien van het token dat is geretourneerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van wachtwoordwijzigingen met behulp van aangepast beleid in Azure Active Directory B2C](custom-policy-password-change.md).
- Lees meer over de elementen [Predicaten](predicates.md) en [Predicatevaliden](predicates.md#predicatevalidations) in de IEF-verwijzing.
