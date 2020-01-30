---
title: REST API claim uitwisselingen-Azure Active Directory B2C
description: REST API claims-uitwisselingen toevoegen aan aangepaste beleids regels in Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1caf0b3f6396cc008e77bb96e686fe78f462850b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849095"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>REST API claim uitwisselingen toevoegen aan aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

U kunt interactie met een REST-API toevoegen aan uw [aangepaste beleids regels](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). In dit artikel wordt beschreven hoe u een Azure AD B2C gebruikers traject maakt dat samenwerkt met de REST-services.

De interactie bevat een claim uitwisseling van informatie tussen de REST API claims en Azure AD B2C. Claim uitwisselingen hebben de volgende kenmerken:

- Kan worden ontworpen als een indelings stap.
- Kan een externe actie activeren. Het kan bijvoorbeeld een gebeurtenis registreren in een externe data base.
- Kan worden gebruikt om een waarde op te halen en op te slaan in de gebruikers database.
- Kan de stroom van de uitvoering wijzigen.

Het scenario dat in dit artikel wordt weer gegeven, omvat de volgende acties:

1. Zoek de gebruiker op in een extern systeem.
2. Haal de plaats op waar de gebruiker is geregistreerd.
3. Retourneert dat kenmerk als een claim voor de toepassing.

## <a name="prerequisites"></a>Vereisten

- Voer de stappen in aan de [slag met aangepast beleid](custom-policy-get-started.md).
- Een REST API-eind punt om te communiceren. In dit artikel wordt een eenvoudige Azure-functie als voor beeld gebruikt. Zie [uw eerste functie maken in de Azure Portal](../azure-functions/functions-create-first-azure-function.md)om de Azure-functie te maken.

## <a name="prepare-the-api"></a>De API voorbereiden

In deze sectie bereidt u de Azure-functie voor op het ontvangen van een waarde voor `email`en retourneert u vervolgens de waarde voor `city` die door Azure AD B2C als een claim kan worden gebruikt.

Wijzig het bestand run. CSX voor de Azure-functie die u hebt gemaakt voor het gebruik van de volgende code:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>De claim uitwisseling configureren

Een technisch profiel biedt de configuratie voor de claim uitwisseling.

Open het bestand *TrustFrameworkExtensions. XML* en voeg het volgende **ClaimsProvider** XML-element toe in het element **ClaimsProviders** .

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Het **InputClaims** -element definieert de claims die naar de rest-service worden verzonden. In dit voor beeld wordt de waarde van de claim `givenName` naar de REST-service verzonden als de claim `email`. Het **OutputClaims** -element definieert de claims die van de rest-service worden verwacht.

De opmerkingen hierboven `AuthenticationType` en `AllowInsecureAuthInProduction` u de wijzigingen opgeeft die u moet aanbrengen wanneer u overstapt naar een productie omgeving. Voor meer informatie over het beveiligen van uw REST-Api's voor productie raadpleegt u [beveiligde rest api's met basis verificatie](secure-rest-api-dotnet-basic-auth.md) en [beveiligde rest api's met certificaat verificatie](secure-rest-api-dotnet-certificate-auth.md).

## <a name="add-the-claim-definition"></a>De claim definitie toevoegen

Voeg een definitie toe voor `city` binnen het **BuildingBlocks** -element. U kunt dit element vinden aan het begin van het bestand TrustFrameworkExtensions. XML.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Een Orchestration-stap toevoegen

Er zijn veel use-cases waarbij de aanroep van REST API kan worden gebruikt als een indelings stap. Als een indelings stap kan deze worden gebruikt als een update voor een extern systeem nadat een gebruiker een taak heeft voltooid, zoals de eerste registratie, of als een profiel update om de informatie gesynchroniseerd te laten worden. In dit geval wordt deze gebruikt om de informatie die is verstrekt aan de toepassing te verbeteren nadat het profiel is bewerkt.

Een stap toevoegen aan het profiel gebruikers traject voor het bewerken van een gebruiker. Nadat de gebruiker is geverifieerd (indelings stappen 1-4 in de volgende XML) en de gebruiker de bijgewerkte profiel gegevens heeft verstrekt (stap 5). Kopieer de gebruikers traject XML-code van het profiel bewerken vanuit het bestand *TrustFrameworkBase. XML* naar uw *TrustFrameworkExtensions. XML-* bestand in het **UserJourneys** -element. Breng vervolgens de wijziging aan als stap 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Het uiteindelijke XML-bestand voor de gebruikers reis moet er als volgt uitzien:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>De claim toevoegen

Bewerk het bestand *ProfileEdit. XML* en voeg `<OutputClaim ClaimTypeReferenceId="city" />` toe aan het **OutputClaims** -element.

Nadat u de nieuwe claim hebt toegevoegd, ziet het technische profiel er als volgt uit:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Uw wijzigingen uploaden en testen

1. Beschrijving Sla de bestaande versie op (door te downloaden) van de bestanden voordat u doorgaat.
2. Upload *TrustFrameworkExtensions. XML* en *ProfileEdit. XML* en selecteer om het bestaande bestand te overschrijven.
3. Selecteer **B2C_1A_ProfileEdit**.
4. Selecteer voor **Select-toepassing** op de pagina overzicht van het aangepaste beleid de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. Zorg ervoor dat de **antwoord-URL** is `https://jwt.ms`.
4. Selecteer **nu uitvoeren**. Meld u aan met uw account referenties en klik op **door gaan**.

Als alles goed is ingesteld, bevat het token de nieuwe claim `city`met de waarde `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Volgende stappen

U kunt de interactie ook als een validatie profiel ontwerpen. Zie voor meer informatie [Walkthrough: integreer rest API claim uitwisselingen in uw Azure AD B2C gebruikers traject als validatie voor gebruikers invoer](custom-policy-rest-api-claims-validation.md).

[Wijzig de profiel bewerking om aanvullende informatie van uw gebruikers te verzamelen](custom-policy-custom-attributes.md)

[Referentie: technisch profiel (REST)](restful-technical-profile.md)

Raadpleeg de volgende artikelen voor meer informatie over het beveiligen van uw Api's:

* [Beveilig uw REST API met basis verificatie (gebruikers naam en wacht woord)](secure-rest-api-dotnet-basic-auth.md)
* [De REST-API beveiligen met client certificaten](secure-rest-api-dotnet-certificate-auth.md)
