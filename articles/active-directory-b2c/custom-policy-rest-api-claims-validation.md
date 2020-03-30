---
title: REST API-claims worden uitgewisseld als validatie
titleSuffix: Azure AD B2C
description: Een walkthrough voor het maken van een Azure AD B2C-gebruikersreis die samenwerkt met RESTful-services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330826"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Walkthrough: Integratie van REST API-claims uitwisselingen in uw Azure AD B2C-gebruikersreis om gebruikersinvoer te valideren

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met het Identity Experience Framework (IEF) dat ten grondslag ligt aan Azure Active Directory B2C (Azure AD B2C), kunnen identiteitsontwikkelaars een interactie met een RESTful API integreren in een gebruikersreis.  Aan het einde van deze walkthrough u een Azure AD B2C-gebruikersreis maken die samenwerkt met [RESTful-services](custom-policy-rest-api-intro.md) om gebruikersinvoer te valideren.

In dit scenario voegen we gebruikers de mogelijkheid toe om een loyaliteitsnummer in te voeren in de aanmeldingspagina van Azure AD B2C. We valideren of deze combinatie van e-mail en loyaliteitsnummer is toegewezen aan een promotiecode door deze gegevens naar een REST API te sturen. Als de REST API een promotiecode voor deze gebruiker vindt, wordt deze teruggestuurd naar Azure AD B2C. Ten slotte wordt de promotiecode ingevoegd in de tokenclaims voor de toepassing om te consumeren.

U de interactie ook ontwerpen als een orkestratiestap. Dit is geschikt wanneer de REST API geen gegevens op het scherm validert en altijd claims retourneert. Zie [Walkthrough: Rest API-claims uitwisselingen integreren in uw Azure AD B2C-gebruikersreis als een orchestration-stap.](custom-policy-rest-api-claims-exchange.md)

## <a name="prerequisites"></a>Vereisten

- Voer de stappen uit in [Aan de slag met aangepast beleid](custom-policy-get-started.md). U moet een werkend aangepast beleid hebben voor aanmelden en aanmelden met lokale accounts.
- Meer informatie over het [integreren van REST API-claimsuitwisselingen in uw aangepaste Azure AD B2C-beleid](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Een REST API-eindpunt voorbereiden

Voor deze walkthrough moet u een REST-API hebben die valideert of een e-mailadres is geregistreerd in uw back-endsysteem met een loyaliteits-ID. Indien geregistreerd, moet de REST API een registratiepromotiecode retourneren, die de klant kan gebruiken om goederen binnen uw toepassing te kopen. Anders moet de REST-API een HTTP 409-foutbericht retourneren: 'Loyalty ID '{loyalty ID}' is niet gekoppeld aan '{email}' e-mailadres".

De volgende JSON-code illustreert de gegevens die Azure AD B2C naar uw REST API-eindpunt verzendt. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Zodra uw REST API de gegevens valideert, moet deze een HTTP 200 (Ok) retourneren met de volgende JSON-gegevens:

```json
{
    "promoCode": "24534"
}
```

Als de validatie is mislukt, moet de REST-API een `userMessage` HTTP 409 (Conflict) retourneren met het JSON-element. Het IEF `userMessage` verwacht de claim dat de REST API terugkeert. Deze claim wordt als een tekenreeks aan de gebruiker gepresenteerd als de validatie mislukt.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

De instelling van het REST API-eindpunt valt buiten het bereik van dit artikel. We hebben een voorbeeld van [Azure-functies](https://docs.microsoft.com/azure/azure-functions/functions-reference) gemaakt. U hebt toegang tot de volledige Azure-functiecode op [GitHub.](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)

## <a name="define-claims"></a>Claims definiëren

Een claim biedt tijdelijke opslag van gegevens tijdens een Azure AD B2C-beleidsuitvoering. U claims declareren binnen de sectie [claimschema.](claimsschema.md) 

1. Open het extensiesbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>
1. Zoek naar het element [Bouwstenen.](buildingblocks.md) Als het element niet bestaat, voeg je het toe.
1. Zoek het element [ClaimSchema.](claimsschema.md) Als het element niet bestaat, voeg je het toe.
1. Voeg de volgende claims toe aan het element **ClaimsSchema.**  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Het technische profiel van de RESTful API configureren 

Een [rustgevend technisch profiel](restful-technical-profile.md) biedt ondersteuning voor interfacing met uw eigen RESTful service. Azure AD B2C verzendt gegevens naar de `InputClaims` RESTful-service `OutputClaims` in een verzameling en ontvangt gegevens terug in een verzameling. Zoek het element **ClaimsProviders** en voeg als volgt een nieuwe claimprovider toe:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

In dit voorbeeld `userLanguage` wordt deze naar de `lang` REST-service gestuurd zoals binnen de JSON-payload. De waarde `userLanguage` van de claim bevat de huidige gebruikersnaam. Zie [claimresolver](claim-resolver-overview.md)voor meer informatie .

De bovenstaande `AuthenticationType` `AllowInsecureAuthInProduction` opmerkingen en geef wijzigingen op die u moet aanbrengen wanneer u naar een productieomgeving gaat. Zie [Secure RESTful API](secure-rest-api.md)voor meer informatie over het beveiligen van uw RESTful API's voor productie.

## <a name="validate-the-user-input"></a>De gebruikersinvoer valideren

Om het loyaliteitsnummer van de gebruiker te verkrijgen tijdens het aanmelden, moet u de gebruiker toestaan deze gegevens op het scherm in te voeren. Voeg de **loyaliteitsclaim** toe aan de aanmeldingspagina door deze toe te `OutputClaims` voegen aan het element van de bestaande aanmeldingssectie voor technische profielen. Geef de volledige lijst met uitvoerclaims op om de volgorde te bepalen waarin de claims op het scherm worden weergegeven.  

Voeg de referentie van het validatietechnische profiel toe `REST-ValidateProfile`aan het technische profiel van de aanmelding, dat de aanroep de . Het nieuwe validatietechnische profiel wordt toegevoegd `<ValidationTechnicalProfiles>` aan de bovenkant van de verzameling die is gedefinieerd in het basisbeleid. Dit gedrag betekent dat Azure AD B2C pas na succesvolle validatie verder gaat met het maken van het account in de map.   

1. Zoek het element **Claimproviders.** Voeg als volgt een nieuwe claimprovider toe:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Een claim opnemen in het token 

Als u de claim van de promotiecode wilt terugsturen <em> `SocialAndLocalAccounts/` </em> naar de toepassing van de relying party, voegt u een uitvoerclaim toe aan het bestand. Met de uitvoerclaim kan de claim na een succesvolle gebruikersreis aan het token worden toegevoegd en wordt deze naar de toepassing verzonden. Wijzig het technische profielelement binnen de sectie `promoCode` relying party om de claim als uitvoer claim toe te voegen.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD-tenant bevat.
1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
1. Selecteer **Identity Experience Framework**.
1. Selecteer **Aangepast beleid uploaden**en upload vervolgens de beleidsbestanden die u hebt gewijzigd: *TrustFrameworkExtensions.xml*en *SignUpOrSignin.xml*. 
1. Selecteer het aanmeldings- of aanmeldingsbeleid dat u hebt geüpload en klik op **nu uitvoeren.**
1. Je moet je kunnen aanmelden met een e-mailadres.
1. Klik op de link **Aanmelden nu.**
1. Typ 1234 in **de uw loyaliteits-id**en klik op **Doorgaan**. Op dit punt moet u een validatiefoutbericht ontvangen.
1. Wijzig in een andere waarde en klik op **Doorgaan**.
1. Het token dat naar uw `promoCode` aanvraag wordt teruggestuurd, bevat de claim.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het beveiligen van uw API's:

- [Walkthrough: Integratie van REST API-claims uitwisselingen in uw Azure AD B2C-gebruikersreis als een orchestration-stap](custom-policy-rest-api-claims-exchange.md)
- [Beveilig uw RESTful API](secure-rest-api.md)
- [Referentie: RESTful technisch profiel](restful-technical-profile.md)
