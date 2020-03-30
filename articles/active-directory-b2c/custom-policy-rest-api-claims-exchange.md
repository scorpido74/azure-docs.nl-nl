---
title: REST API-claims - Azure Active Directory B2C
description: Rest API-claims uitwisselingen toevoegen aan aangepast beleid in Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330723"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Walkthrough: Rest API-claims uitwisselingen toevoegen aan aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) stelt identiteitsontwikkelaars in staat om een interactie met een RESTful API te integreren in een gebruikersreis. Aan het einde van deze walkthrough u een Azure AD B2C-gebruikersreis maken die samenwerkt met [RESTful-services.](custom-policy-rest-api-intro.md)

In dit scenario verrijken we de tokengegevens van de gebruiker door te integreren met een zakelijke bedrijfslijn. Tijdens het aanmelden of aanmelden met een lokaal of federatief account, roept Azure AD B2C een REST API in om de uitgebreide profielgegevens van de gebruiker uit een externe gegevensbron te halen. In dit voorbeeld verzendt Azure AD B2C de unieke id van de gebruiker, de objectId. De REST API retourneert vervolgens het accountsaldo van de gebruiker (een willekeurig getal). Gebruik dit voorbeeld als uitgangspunt om te integreren met uw eigen CRM-systeem, marketingdatabase of een zakelijke werkstroom.

U de interactie ook ontwerpen als een validatietechnisch profiel. Dit is geschikt wanneer de REST API gegevens op het scherm validert en claims retourt. Zie [Walkthrough: Rest API-claims uitwisselingen integreren in uw Azure AD B2C-gebruikersreis om gebruikersinvoer te valideren](custom-policy-rest-api-claims-validation.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Voer de stappen uit in [Aan de slag met aangepast beleid](custom-policy-get-started.md). U moet een werkend aangepast beleid hebben voor aanmelden en aanmelden met lokale accounts.
- Meer informatie over het [integreren van REST API-claimsuitwisselingen in uw aangepaste Azure AD B2C-beleid](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Een REST API-eindpunt voorbereiden

Voor deze walkthrough moet u een REST-API hebben die valideert of de Azure AD B2C-objectId van een gebruiker is geregistreerd in uw back-endsysteem. Als de REST API is geregistreerd, retourneert het saldo van het gebruikersaccount. Anders registreert de REST-API het nieuwe account in `50.00`de map en retourneert het beginsaldo .

De volgende JSON-code illustreert de gegevens die Azure AD B2C naar uw REST API-eindpunt verzendt. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

Zodra uw REST API de gegevens valideert, moet deze een HTTP 200 (Ok) retourneren met de volgende JSON-gegevens:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Het technische profiel van de RESTful API configureren 

Een [rustgevend technisch profiel](restful-technical-profile.md) biedt ondersteuning voor interfacing met uw eigen RESTful service. Azure AD B2C verzendt gegevens naar de `InputClaims` RESTful-service `OutputClaims` in een verzameling en ontvangt gegevens terug in een verzameling. Zoek het element **ClaimsProviders** in uw <em>**`TrustFrameworkExtensions.xml`**</em> bestand en voeg als volgt een nieuwe claimprovider toe:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

In dit voorbeeld `userLanguage` wordt deze naar de `lang` REST-service gestuurd zoals binnen de JSON-payload. De waarde `userLanguage` van de claim bevat de huidige gebruikersnaam. Zie [claimresolver](claim-resolver-overview.md)voor meer informatie .

De bovenstaande `AuthenticationType` `AllowInsecureAuthInProduction` opmerkingen en geef wijzigingen op die u moet aanbrengen wanneer u naar een productieomgeving gaat. Zie [Secure RESTful API](secure-rest-api.md)voor meer informatie over het beveiligen van uw RESTful API's voor productie.

## <a name="add-an-orchestration-step"></a>Een orchestration-stap toevoegen

[Gebruikersreizen](userjourneys.md) specificeren expliciete paden waardoor een toepassing van een relying party door een beleid de gewenste claims voor een gebruiker kan verkrijgen. Een gebruikersreis wordt weergegeven als een orkestratiereeks die moet worden gevolgd voor een succesvolle transactie. U orkestratiestappen optellen of aftrekken. In dit geval voegt u een nieuwe orchestration-stap toe die wordt gebruikt om de informatie die aan de toepassing wordt verstrekt nadat de gebruiker zich aanmeldde of zich aanmeldt via de REST API-aanroep uit te breiden.

1. Open het basisbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>
1. Zoek naar `<UserJourneys>` het element. Kopieer het hele element en verwijder het.
1. Open het extensiesbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>
1. Plak `<UserJourneys>` het in het extensiesbestand, `<ClaimsProviders>` na het sluiten van het element.
1. Zoek `<UserJourney Id="SignUpOrSignIn">`de en voeg de volgende orkestratiestap toe voor de laatste.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refactor de laatste orkestratie `Order` stap `8`door het veranderen van de . Uw laatste twee orkestratiestappen moeten er als volgt uitzien:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Herhaal de laatste twee stappen voor de gebruikersreizen **ProfileEdit** en **PasswordReset.**


## <a name="include-a-claim-in-the-token"></a>Een claim opnemen in het token 

Als u `balance` de claim wilt terugsturen naar de aanvraag <em> `SocialAndLocalAccounts/` </em> van de relying party, voegt u een uitvoerclaim toe aan het bestand. Als u een uitvoerclaim toevoegt, wordt de claim na een geslaagde gebruikersreis in het token verzonden en wordt deze naar de toepassing verzonden. Wijzig het technische profielelement binnen de `balance` sectie relying party om toe te voegen als uitvoerclaim.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Herhaal deze stap voor de gebruikersritten **ProfileEdit.xml**en **PasswordReset.xml.**

Sla de bestanden op die u hebt gewijzigd: *TrustFrameworkBase.xml*en *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*en *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD-tenant bevat.
1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
1. Selecteer **Identity Experience Framework**.
1. Selecteer **Aangepast beleid uploaden**en upload vervolgens de beleidsbestanden die u hebt gewijzigd: *TrustFrameworkBase.xml*en *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*en *PasswordReset.xml*. 
1. Selecteer het aanmeldings- of aanmeldingsbeleid dat u hebt geüpload en klik op **nu uitvoeren.**
1. Je moet je kunnen aanmelden via een e-mailadres of een Facebook-account.
1. Het token dat naar uw `balance` aanvraag wordt teruggestuurd, bevat de claim.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Volgende stappen


## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het beveiligen van uw API's:

- [Walkthrough: Integratie van REST API-claims uitwisselingen in uw Azure AD B2C-gebruikersreis als een orchestration-stap](custom-policy-rest-api-claims-exchange.md)
- [Beveilig uw RESTful API](secure-rest-api.md)
- [Referentie: RESTful technisch profiel](restful-technical-profile.md)
