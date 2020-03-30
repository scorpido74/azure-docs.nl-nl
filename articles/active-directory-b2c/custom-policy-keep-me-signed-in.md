---
title: Houd me aangemeld in Azure Active Directory B2C
description: Meer informatie over het instellen van Houd me aangemeld (KMSI) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0de94cdce1d7f0e9da9d2844b300956ad6f6970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330842"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Houd me aangemeld (KMSI) inschakelen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

U kmsi-functionaliteit (Keep Me Signed In) inschakelen voor gebruikers van uw web- en native toepassingen met lokale accounts in uw Azure Active Directory B2C (Azure AD B2C) directory. Deze functie geeft gebruikers toegang tot uw toepassing zonder hen te vragen hun gebruikersnaam en wachtwoord opnieuw in te voeren. Deze toegang wordt ingetrokken wanneer een gebruiker zich afmeldt.

Gebruikers moeten deze optie niet inschakelen op openbare computers.

![Aanmeldingspagina voor een voorbeeld waarin een aanmeldingsvak Voor mij is ondertekend](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Vereisten

- Een Azure AD B2C-tenant die is geconfigureerd om aanmelding voor lokale account toe te staan. KMSI wordt niet ondersteund voor accounts van externe identiteitsprovideren.
- Voer de stappen uit in [Aan de slag met aangepast beleid](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>De pagina-id configureren

Als u KMSI wilt `DataUri` inschakelen, stelt u het element inhoudsdefinitie in op [pagina-id](contentdefinitions.md#datauri) `unifiedssp` en [paginaversie](page-layout.md) *1.1.0* of hoger.

1. Open het extensiebestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em> Dit extensiebestand is een van de beleidsbestanden die zijn opgenomen in het aangepaste startpakket voor beleid, dat u in de vereiste had moeten verkrijgen, [Aan de slag met aangepast beleid](custom-policy-get-started.md).
1. Zoek naar het element **Bouwstenen.** Als het element niet bestaat, voeg je het toe.
1. Voeg het element **ContentDefinities** toe aan het element **Bouwstenen** van het beleid.

    Uw aangepaste beleid moet er uitzien als het volgende codefragment:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Voeg de metadata toe aan het zelfverklaarde technische profiel

Als u het kmsi-selectievakje wilt toevoegen aan de aanmeldings- en aanmeldingspagina, stelt u de `setting.enableRememberMe` metagegevens in op false. Overschrijven van de technische profielen SelfAsserted-LocalAccountSignin-Email in het extensiebestand.

1. Zoek het element Claimproviders. Als het element niet bestaat, voeg je het toe.
1. Voeg de volgende claimprovider toe aan het element ClaimsProviders:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Sla het extensiesbestand op.

## <a name="configure-a-relying-party-file"></a>Een bestand van een relying party configureren

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u hebt gemaakt.

1. Open uw aangepaste beleidsbestand. U bijvoorbeeld *SignUpOrSignin.xml .*
1. Als deze nog niet bestaat, `<UserJourneyBehaviors>` voegt u `<RelyingParty>` een onderliggend knooppunt toe aan het knooppunt. Het moet onmiddellijk `<DefaultUserJourney ReferenceId="User journey Id" />`na , `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`bijvoorbeeld worden gevestigd: .
1. Voeg als onderliggend element `<UserJourneyBehaviors>` het volgende knooppunt toe.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Geeft aan hoe de sessie `SessionExpiryInSeconds` wordt `KeepAliveInDays`verlengd door de opgegeven tijd en . De `Rolling` waarde (standaard) geeft aan dat de sessie wordt verlengd telkens wanneer de gebruiker verificatie uitvoert. De `Absolute` waarde geeft aan dat de gebruiker na de opgegeven periode gedwongen wordt opnieuw te verifiëren.

    - **SessionExpiryInSeconds** - De levensduur van sessiecookies wanneer *u mij hebt aangemeld,* is niet ingeschakeld of als een gebruiker niet *selecteert, houdt u mij aangemeld.* De sessie `SessionExpiryInSeconds` verloopt nadat deze is verstreken of de browser is gesloten.

    - **KeepAliveInDays** - De levensduur van sessiecookies wanneer *u mij hebt aangemeld,* is ingeschakeld en de gebruiker selecteert mij ingelogd te *houden.*  De waarde `KeepAliveInDays` van de `SessionExpiryInSeconds` waarde heeft voorrang op de waarde en bepaalt de vervaldatum van de sessie. Als een gebruiker de browser sluit en later heropent, kan deze zich nog steeds in stilte aanmelden zolang deze binnen de KeepAliveInDays-periode valt.

    Zie het [gedrag van gebruikersreizen](relyingparty.md#userjourneybehaviors)voor meer informatie .

We raden u aan de waarde van SessionExpiryInSeconds in te stellen op een korte periode (1200 seconden), terwijl de waarde van KeepAliveInDays kan worden ingesteld op een relatief lange periode (30 dagen), zoals in het volgende voorbeeld wordt weergegeven:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>Uw beleid testen

1. Sla uw wijzigingen op en upload het bestand.
1. Als u het aangepaste beleid wilt testen dat u hebt geüpload, gaat u in de Azure-portal naar de beleidspagina en selecteert u **Nu uitvoeren**.
1. Typ uw **gebruikersnaam** en **wachtwoord,** selecteer **Houd mij aangemeld**en klik op **aanmelden**.
1. Ga terug naar Azure Portal. Ga naar de beleidspagina en selecteer **Kopiëren** om de aanmeldings-URL te kopiëren.
1. Verwijder in de adresbalk `&prompt=login` van de browser de parameter querytekenreeks, waardoor de gebruiker zijn referenties op dat verzoek moet invoeren.
1. Klik in de browser op **Ga**. Azure AD B2C geeft nu een toegangstoken uit zonder u te vragen zich opnieuw aan te melden. 

## <a name="next-steps"></a>Volgende stappen

Vind het voorbeeldbeleid [hier](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
