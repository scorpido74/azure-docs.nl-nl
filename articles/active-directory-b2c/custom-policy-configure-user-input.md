---
title: Claims toevoegen en gebruikersinvoer aanpassen in aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van gebruikersinvoer en het toevoegen van claims aan de aanmeldings- of aanmeldingsreis in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473673"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Claims toevoegen en gebruikersinvoer aanpassen met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel verzamelt u een nieuw kenmerk tijdens uw aanmeldingstraject in Azure Active Directory B2C (Azure AD B2C). U verkrijgt de plaats van de gebruikers, configureert deze als een vervolgkeuzelijst en bepaalt of deze moet worden verstrekt.

> [!NOTE]
> Dit voorbeeld maakt gebruik van de ingebouwde claim 'stad'. In plaats daarvan u een van de ondersteunde [ingebouwde Azure AD B2C-ingebouwde kenmerken](user-profile-attributes.md) of een aangepast kenmerk kiezen. Als u een aangepast kenmerk wilt gebruiken, [schakelt u aangepaste kenmerken in uw beleid in.](custom-policy-custom-attributes.md) Als u een ander ingebouwd of aangepast kenmerk wilt gebruiken, vervangt u 'plaats' door het kenmerk van uw keuze, bijvoorbeeld de ingebouwde *attribuutfunctieTitle* of een aangepast kenmerk zoals *extension_loyaltyId*.  

U de eerste gegevens van uw gebruikers verzamelen met behulp van de aanmeldings- of aanmeldingsreis. Aanvullende claims kunnen later worden verzameld met behulp van een gebruikersreis voor profielbewerking. Wanneer Azure AD B2C informatie rechtstreeks van de gebruiker verzamelt, gebruikt het Identity Experience Framework zijn [zelfverklaarde technische profiel.](self-asserted-technical-profile.md) In dit voorbeeld ziet u het volgende:

1. Definieer een "stad" claim. 
1. Vraag de gebruiker naar hun stad.
1. Blijf vasthouden aan de plaats tot het gebruikersprofiel in de Azure AD B2C-map.
1. Lees de stadsclaim in de Azure AD B2C-map bij elke aanmelding.
1. Breng de stad terug naar uw aanvraag voor een relying party na aanmelding of aanmelding.  

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Aan de slag met aangepast beleid](custom-policy-get-started.md). U moet een werkend aangepast beleid hebben voor aanmelden en aanmelden met sociale en lokale accounts.

## <a name="define-a-claim"></a>Een claim definiëren

Een claim biedt een tijdelijke opslag van gegevens tijdens een Azure AD B2C-beleidsuitvoering. Het [schadeschema](claimsschema.md) is de plaats waar u uw claims declareert. De volgende elementen worden gebruikt om de claim te definiëren:

- **DisplayName** - Een tekenreeks die het label dat door de gebruiker wordt aangericht definieert.
- [DataType](claimsschema.md#datatype) - Het type claim.
- **UserHelpText** - Helpt de gebruiker te begrijpen wat er nodig is.
- [UserInputType](claimsschema.md#userinputtype) - Het type invoerbesturingselement, zoals tekstvak, radioselectie, vervolgkeuzelijst of meerdere selecties.

Open het extensiesbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>

1. Zoek naar het element [Bouwstenen.](buildingblocks.md) Als het element niet bestaat, voeg je het toe.
1. Zoek het element [ClaimSchema.](claimsschema.md) Als het element niet bestaat, voeg je het toe.
1. Voeg de claim van de plaats toe aan het element **Claimschema.**  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Een claim toevoegen aan de gebruikersinterface

De volgende technische profielen worden [zelf geclaimd,](self-asserted-technical-profile.md)ingeroepen wanneer van een gebruiker wordt verwacht dat hij invoer levert:

- **LocalAccountSignUpWithLogonEmail** - Lokale accountaanmeldingsstroom.
- **SelfAsserted-Social** - Federated account first-time user sign-in.
- **SelfAsserted-ProfileUpdate** - Profielstroom bewerken.

Om de claim van de stad tijdens het aanmelden te `LocalAccountSignUpWithLogonEmail` verzamelen, moet deze worden toegevoegd als uitvoerclaim aan het technische profiel. Overschrijf dit technische profiel in het extensiebestand. Geef de volledige lijst met uitvoerclaims op om de volgorde te bepalen waarin de claims op het scherm worden weergegeven. Zoek het element **Claimproviders.** Voeg als volgt een nieuwe ClaimProviders toe:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Om de claim van de stad te verzamelen na de eerste aanmelding met `SelfAsserted-Social` een federatieve account, moet deze worden toegevoegd als een uitvoerclaim voor het technische profiel. Als gebruikers van lokale en federatieve accounts hun profielgegevens later kunnen `SelfAsserted-ProfileUpdate` bewerken, voegt u de uitvoerclaim toe aan het technische profiel. Overschrijf deze technische profielen in het extensiebestand. Geef de volledige lijst op met de uitvoerclaims die de volgorde van de claims op het scherm beheren. Zoek het element **Claimproviders.** Voeg als volgt een nieuwe ClaimProviders toe:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Een claim lezen en schrijven

De volgende technische profielen zijn [technische profielen van Active Directory,](active-directory-technical-profile.md)die gegevens lezen en schrijven naar Azure Active Directory.  
Gebruiken `PersistedClaims` om gegevens naar het `OutputClaims` gebruikersprofiel te schrijven en gegevens uit het gebruikersprofiel te lezen binnen de respectievelijke technische profielen van Active Directory.

Overschrijf deze technische profielen in het extensiebestand. Zoek het element **Claimproviders.**  Voeg als volgt een nieuwe ClaimProviders toe:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Een claim opnemen in het token 

Als u de claim van de stad wilt terugsturen <em> `SocialAndLocalAccounts/` </em> naar de aanvraag van de relying party, voegt u een uitvoerclaim toe aan het bestand. De uitvoerclaim wordt na een geslaagde gebruikersreis aan het token toegevoegd en wordt naar de toepassing verzonden. Wijzig het technische profielelement binnen de sectie relying party om de stad toe te voegen als uitvoerclaim.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD-tenant bevat.
3. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
4. Selecteer **Identity Experience Framework**.
5. Selecteer **Aangepast beleid uploaden**en upload de twee beleidsbestanden die u hebt gewijzigd.
2. Selecteer het aanmeldings- of aanmeldingsbeleid dat u hebt geüpload en klik op **nu uitvoeren.**
3. Je moet je kunnen aanmelden met een e-mailadres.

Het aanmeldingsscherm moet er hetzelfde uitzien als de volgende schermafbeelding:

![Schermafbeelding van gewijzigde aanmeldingsoptie](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Het token dat naar uw `city` aanvraag wordt teruggestuurd, bevat de claim.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [element ClaimsSchema](claimsschema.md) vindt u in de IEF-verwijzing.
- Meer informatie over het gebruik van [aangepaste kenmerken in een aangepast profielbewerkingsbeleid](custom-policy-custom-attributes.md).
