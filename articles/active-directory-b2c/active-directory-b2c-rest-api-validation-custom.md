---
title: Claim uitwisseling REST API als validatie
titleSuffix: Azure AD B2C
description: Een overzicht van het maken van een Azure AD B2C gebruikers traject dat communiceert met de REST-services.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8730870bfae9f704ee43594497f79942b70a6181
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949369"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Walkthrough: REST API claims-uitwisselingen integreren in uw Azure AD B2C gebruikers door voeren als validatie op gebruikers invoer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met de Identity experience Framework (IEF) die Azure Active Directory B2C (Azure AD B2C), kan de identiteits ontwikkelaar een interactie met een REST-API in een gebruikers traject integreren.

Aan het einde van dit overzicht kunt u een Azure AD B2C gebruikers traject maken dat samenwerkt met de REST-services.

De IEF verzendt gegevens in claims en ontvangt gegevens terug in claims. De interactie met de API:

- Kan worden ontworpen als een REST API claim uitwisseling of als een validatie profiel, dat binnen een Orchestration-stap plaatsvindt.
- Valideert doorgaans de invoer van de gebruiker. Als de waarde van de gebruiker wordt geweigerd, kan de gebruiker opnieuw proberen een geldige waarde in te voeren met de kans om een fout bericht te retour neren.

U kunt de interactie ook als een Orchestration-stap ontwerpen. Zie voor meer informatie [Walkthrough: integreer rest API claim uitwisselingen in uw Azure AD B2C gebruikers traject als een](active-directory-b2c-rest-api-step-custom.md)indelings stap.

Voor het voor beeld van een validatie profiel gebruiken we het profiel gebruikers traject voor het bewerken van profielen in het Starter Pack-bestand ProfileEdit. XML.

We kunnen controleren of de naam van de gebruiker in het profiel bewerken geen deel uitmaakt van een uitsluitings lijst.

## <a name="prerequisites"></a>Vereisten

- Een Azure AD B2C-Tenant die is geconfigureerd voor het volt ooien van een lokaal account, aanmelden/aanmelden, zoals beschreven in [aan de slag](active-directory-b2c-get-started-custom.md).
- Een REST API-eind punt om te communiceren. Voor dit scenario hebben we een demo site met de naam [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) ingesteld met een rest API-service.

## <a name="step-1-prepare-the-rest-api-function"></a>Stap 1: de REST API-functie voorbereiden

> [!NOTE]
> Het instellen van REST API-functies valt buiten het bereik van dit artikel. [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) biedt een uitstekende Toolkit voor het maken van rest-Services in de Cloud.

We hebben een Azure-functie gemaakt die een claim ontvangt die wordt verwacht als `playerTag`. De functie valideert of deze claim bestaat. U kunt toegang krijgen tot de volledige Azure-functie code in [github](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

De IEF verwacht de `userMessage` claim die de Azure-functie retourneert. Deze claim wordt weer gegeven als een teken reeks voor de gebruiker als de validatie mislukt, bijvoorbeeld wanneer de status van een 409-conflict wordt geretourneerd in het vorige voor beeld.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Stap 2: Configureer de REST API-claim uitwisseling als technisch profiel in uw TrustFrameworkExtensions. XML-bestand

Een technisch profiel is de volledige configuratie van de uitwisseling die nodig is voor de REST-service. Open het bestand TrustFrameworkExtensions. XML en voeg in het element `<ClaimsProviders>` het volgende XML-fragment toe.

> [!NOTE]
> In de volgende XML wordt de `Version=1.0.0.0` van de REST-provider als het protocol beschreven. Beschouw het als de functie die met de externe service communiceert. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

Het `InputClaims`-element definieert de claims die van de IEF naar de REST-service worden verzonden. In dit voor beeld wordt de inhoud van de claim `givenName` als `playerTag`naar de REST-service verzonden. In dit voor beeld verwacht de IEF geen claims terug. In plaats daarvan wordt gewacht op een reactie van de REST-service en reageert op basis van de status codes die worden ontvangen.

De opmerkingen hierboven `AuthenticationType` en `AllowInsecureAuthInProduction` u de wijzigingen opgeeft die u moet aanbrengen wanneer u overstapt naar een productie omgeving. Voor meer informatie over het beveiligen van uw REST-Api's voor productie raadpleegt u [beveiligde rest api's met basis verificatie](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) en [beveiligde rest api's met certificaat verificatie](active-directory-b2c-custom-rest-api-netfw-secure-cert.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Stap 3: Neem de REST-service claims uitwisseling op in een zelf-bevestigd technisch profiel waar u de gebruikers invoer wilt valideren

Het meest voorkomende gebruik van de validatie stap is in de interactie met een gebruiker. Alle interacties waarbij de gebruiker wordt verwacht om invoer te bieden, zijn *zelfondertekende technische profielen*. In dit voor beeld voegen we de validatie toe aan het zelf-bevestigingen-ProfileUpdate technische profiel. Dit is het technische profiel dat wordt gebruikt door het Relying Party (RP)-beleids bestand `Profile Edit`.

De claim uitwisseling toevoegen aan het zelf-beweringen technische profiel:

1. Open het bestand TrustFrameworkBase. XML en zoek naar `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Bekijk de configuratie van dit technische profiel. Houd er rekening mee dat de uitwisseling met de gebruiker is gedefinieerd als claims die worden gevraagd door de gebruiker (invoer claims) en claims die terug worden verwacht van de zelfbevestigende provider (uitvoer claims).
3. Zoeken naar `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`en u ziet dat dit profiel wordt aangeroepen als Orchestration-stap 5 van `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Stap 4: Upload en test het profiel bestand bewerken RP-beleid

1. Upload de nieuwe versie van het bestand TrustFrameworkExtensions. XML.
2. Gebruik **nu uitvoeren** om het profiel bestand bewerken RP-beleid te testen.
3. Test de validatie door een van de bestaande namen op te geven (bijvoorbeeld mcvinny) in het veld voor de **opgegeven naam** . Als alles goed is ingesteld, ontvangt u een bericht dat de gebruiker wordt gewaarschuwd dat de Player-code al wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

[De profiel wijziging en gebruikers registratie wijzigen om aanvullende informatie van uw gebruikers te verzamelen](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Walkthrough: Integreer REST API claim uitwisselingen in uw Azure AD B2C gebruikers traject als een indelings stap](active-directory-b2c-rest-api-step-custom.md)

[Referentie: technisch profiel (REST)](restful-technical-profile.md)

Raadpleeg de volgende artikelen voor meer informatie over het beveiligen van uw Api's:

* [Beveilig uw REST API met basis verificatie (gebruikers naam en wacht woord)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [De REST-API beveiligen met client certificaten](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
