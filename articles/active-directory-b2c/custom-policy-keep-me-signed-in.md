---
title: Aangemeld blijven in Azure Active Directory B2C
description: Meer informatie over het instellen van KMSI (keep me aangemeld) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041fb8d881307b52fb170a11618f930debc522a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80803157"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Aanmelden (KMSI) inschakelen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

U kunt de functie aangemeld blijven (KMSI) inschakelen voor gebruikers van uw web-en systeem eigen toepassingen die lokale accounts hebben in uw Azure Active Directory B2C (Azure AD B2C)-map. Deze functie verleent toegang aan gebruikers die terugkeren naar uw toepassing zonder dat ze de gebruikers naam en het wacht woord opnieuw moeten invoeren. Deze toegang wordt ingetrokken wanneer een gebruiker zich afmeldt.

Gebruikers moeten deze optie niet inschakelen op open bare computers.

![Voor beeld van aanmeldings pagina voor aanmelding met het selectie vakje aangemeld blijven](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Vereisten

- Een Azure AD B2C-Tenant die is geconfigureerd voor het toestaan van aanmelden via een lokaal account. KMSI wordt niet ondersteund voor accounts van externe ID-providers.
- Voer de stappen in aan de [slag met aangepast beleid](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>De pagina-id configureren

Als u KMSI wilt inschakelen, stelt u `DataUri` het inhouds definitie-element in op [pagina-id](contentdefinitions.md#datauri) `unifiedssp` en [pagina versie](page-layout.md) *1.1.0* of hoger.

1. Open het extensie bestand van uw beleid. Bijvoorbeeld <em> `SocialAndLocalAccounts/` </em>. Dit extensie bestand is een van de beleids bestanden in het aangepaste beleids Starter Pack, die u in de vereiste moet hebben verkregen, aan de [slag met aangepast beleid](custom-policy-get-started.md).
1. Zoek het element **BuildingBlocks** . Als het element niet bestaat, voegt u het toe.
1. Voeg het element **ContentDefinitions** toe aan het element **BuildingBlocks** van het beleid.

    Het aangepaste beleid moet eruitzien zoals in het volgende code fragment:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>De meta gegevens toevoegen aan het zelf-bebevestigde technische profiel

Als u het selectie vakje KMSI wilt toevoegen aan de registratie-en aanmeldings pagina, stelt u `setting.enableRememberMe` de meta gegevens in op waar. Overschrijf de technische profielen van de SelfAsserted-LocalAccountSignin-e-mail in het extensie bestand.

1. Zoek het element ClaimsProviders. Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claim provider toe aan het ClaimsProviders-element:

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

1. Sla het bestand met extensies op.

## <a name="configure-a-relying-party-file"></a>Een Relying Party-bestand configureren

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Open uw aangepaste beleids bestand. Bijvoorbeeld *SignUpOrSignin. XML*.
1. Als deze nog niet bestaat, voegt u `<UserJourneyBehaviors>` een onderliggend `<RelyingParty>` knoop punt toe aan het knoop punt. Het moet direct na `<DefaultUserJourney ReferenceId="User journey Id" />`worden geplaatst, bijvoorbeeld:. `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`
1. Voeg het volgende knoop punt toe als onderliggend item van het `<UserJourneyBehaviors>` element.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** : geeft aan hoe de sessie wordt uitgebreid met de tijd die `SessionExpiryInSeconds` is `KeepAliveInDays`opgegeven in en. De `Rolling` waarde (standaard) geeft aan dat de sessie wordt uitgebreid telkens wanneer de gebruiker verificatie uitvoert. De `Absolute` waarde geeft aan dat de gebruiker na de opgegeven tijds periode opnieuw moet worden geverifieerd.

    - **SessionExpiryInSeconds** : de levens duur van sessie cookies wanneer *keep me aangemeld* is niet ingeschakeld, of als een gebruiker niet is geselecteerd als *aangemeld blijven*. De sessie verloopt `SessionExpiryInSeconds` na het verlopen of de browser is gesloten.

    - **KeepAliveInDays** : de levens duur van sessie cookies wanneer *aangemeld blijven* is ingeschakeld en de gebruiker selecteert *aangemeld blijven*.  De waarde van `KeepAliveInDays` heeft voor rang op `SessionExpiryInSeconds` de waarde en dicteert de verloop tijd van de sessie. Als een gebruiker de browser sluit en later opnieuw opent, kan hij of zij zich nog steeds op de achtergrond aanmelden zolang deze zich binnen de KeepAliveInDays-periode bevindt.

    Zie [reis gedrag van gebruikers](relyingparty.md#userjourneybehaviors)voor meer informatie.

We raden u aan de waarde van SessionExpiryInSeconds in te stellen op een korte periode (1200 seconden), terwijl de waarde van KeepAliveInDays kan worden ingesteld op een relatief lange periode (30 dagen), zoals wordt weer gegeven in het volgende voor beeld:

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

1. Sla de wijzigingen op en upload het bestand.
1. Als u het aangepaste beleid wilt testen dat u hebt geüpload, gaat u in het Azure Portal naar de pagina beleid en selecteert u **nu uitvoeren**.
1. Typ uw **gebruikers naam** en **wacht woord**, selecteer **aangemeld blijven**en klik vervolgens op **Aanmelden**.
1. Ga terug naar Azure Portal. Ga naar de pagina beleid en selecteer **kopiëren** om de AANMELDINGS-URL te kopiëren.
1. Verwijder in de adres balk van de browser `&prompt=login` de query teken reeks parameter, waardoor de gebruiker de referenties voor deze aanvraag kan invoeren.
1. Klik in de browser op **Go**. Azure AD B2C wordt nu een toegangs token uitgegeven zonder dat u opnieuw wordt gevraagd om u aan te melden. 

## <a name="next-steps"></a>Volgende stappen

Zoek [hier](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)het voorbeeld beleid.
