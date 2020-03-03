---
title: Aangemeld blijven in Azure Active Directory B2C
description: Meer informatie over het instellen van KMSI (keep me aangemeld) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a27487fa69888b02883c3d9a2151887f41afc45
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189375"
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

Als u KMSI wilt inschakelen, stelt u de inhouds definitie `DataUri` element in op [pagina-id](contentdefinitions.md#datauri) `unifiedssp` en [pagina versie](page-layout.md) *1.1.0* of hoger.

1. Open het extensie bestand van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>  . Dit extensie bestand is een van de beleids bestanden in het aangepaste beleids Starter Pack, die u in de vereiste moet hebben verkregen, aan de [slag met aangepast beleid](custom-policy-get-started.md).
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

1. Sla het bestand met extensies op.



## <a name="configure-a-relying-party-file"></a>Een Relying Party-bestand configureren

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Open het bestand aangepast beleid. Bijvoorbeeld *SignUpOrSignin. XML*.
1. Als deze nog niet bestaat, voegt u een onder`<UserJourneyBehaviors>` onderliggend knoop punt toe aan het knoop punt `<RelyingParty>`. Deze moet direct na `<DefaultUserJourney ReferenceId="User journey Id" />`worden geplaatst, bijvoorbeeld: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Voeg het volgende knoop punt toe als onderliggend item van het `<UserJourneyBehaviors>`-element.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** : geeft aan hoe de sessie wordt uitgebreid met de tijd die is opgegeven in `SessionExpiryInSeconds` en `KeepAliveInDays`. De `Rolling`-waarde (standaard) geeft aan dat de sessie wordt uitgebreid telkens wanneer de gebruiker verificatie uitvoert. De waarde `Absolute` geeft aan dat de gebruiker na de opgegeven tijds periode opnieuw moet worden geverifieerd.

    - **SessionExpiryInSeconds** : de levens duur van sessie cookies wanneer *keep me aangemeld* is niet ingeschakeld, of als een gebruiker niet is geselecteerd als *aangemeld blijven*. De sessie verloopt nadat `SessionExpiryInSeconds` is voltooid of de browser is gesloten.

    - **KeepAliveInDays** : de levens duur van sessie cookies wanneer *aangemeld blijven* is ingeschakeld en de gebruiker selecteert *aangemeld blijven*.  De waarde van `KeepAliveInDays` heeft voor rang op de `SessionExpiryInSeconds` waarde en dicteert de verloop tijd van de sessie. Als een gebruiker de browser sluit en later opnieuw opent, kan hij of zij zich nog steeds op de achtergrond aanmelden zolang deze zich binnen de KeepAliveInDays-periode bevindt.

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

4. Sla de wijzigingen op en upload het bestand.
5. Als u het aangepaste beleid dat u hebt geüpload, wilt testen, gaat u in het Azure Portal naar de pagina beleid en selecteert u **nu uitvoeren**.

U kunt [hier](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)het voorbeeld beleid vinden.
