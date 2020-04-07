---
title: Gebruikersgedrag bijhouden met Application Insights
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van gebeurtenislogboeken in Toepassingsstatistieken van Azure AD B2C-gebruikersreizen met behulp van aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25e62e7c6865f91daa242a33a0f491f8015be41a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672520"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Gebruikersgedrag in Azure Active Directory B2C bijhouden met toepassingsinzichten

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) ondersteunt het rechtstreeks verzenden van gebeurtenisgegevens naar [Application Insights](../azure-monitor/app/app-insights-overview.md) met behulp van de instrumentatiesleutel die wordt verstrekt aan Azure AD B2C.  Met een technisch profiel van Application Insights u gedetailleerde en aangepaste gebeurtenislogboeken voor uw gebruikersreizen krijgen om:

* Krijg inzicht in het gedrag van gebruikers.
* Problemen met uw eigen beleid in ontwikkeling of productie oplossen.
* Meet de prestaties.
* Meldingen maken vanuit Application Insights.

## <a name="how-it-works"></a>Hoe werkt het?

Het technische profiel [Application Insights](application-insights-technical-profile.md) definieert een gebeurtenis vanuit Azure AD B2C. Het profiel geeft de naam van de gebeurtenis, de claims die worden geregistreerd en de instrumentatiesleutel. Als u een gebeurtenis wilt plaatsen, wordt het technische profiel vervolgens toegevoegd als een orkestratiestap in een [gebruikersreis.](userjourneys.md)

Application Insights kan de gebeurtenissen verenigen door een correlatie-id te gebruiken om een gebruikerssessie op te nemen. Application Insights maakt de gebeurtenis en sessie binnen enkele seconden beschikbaar en presenteert veel visualisatie-, export- en analytische tools.

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Aan de slag met aangepast beleid](custom-policy-get-started.md). U moet een werkend aangepast beleid hebben voor aanmelden en aanmelden met lokale accounts.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Wanneer u Toepassingsinzichten gebruikt met Azure AD B2C, hoeft u alleen maar een bron te maken en de instrumentatiesleutel op te halen. Zie Een [resource Application Insights maken](../azure-monitor/app/create-new-resource.md) voor informatie

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure-abonnement gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw abonnement bevat. Deze tenant is niet uw Azure AD B2C-tenant.
3. Kies **Een resource maken** in de linkerbovenhoek van de Azure-portal en zoek en selecteer Vervolgens **Toepassingsinzichten**.
4. Klik **op Maken**.
5. Voer een **naam** voor de resource in.
6. Selecteer **ASP.NET webtoepassing** **voor toepassingstype**.
7. Selecteer **voor Resourcegroep**een bestaande groep of voer een naam in voor een nieuwe groep.
8. Klik **op Maken**.
4. Nadat u de resource Application Insights hebt gemaakt, opent u deze, vouwt u **Essentials**uit en kopieert u de instrumentatiesleutel.

![Overzicht van toepassingsinzichten en instrumentatiesleutel](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Claims definiëren

Een claim biedt een tijdelijke opslag van gegevens tijdens een Azure AD B2C-beleidsuitvoering. Het [schadeschema](claimsschema.md) is de plaats waar u uw claims declareert.

1. Open het extensiesbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>
1. Zoek naar het element [Bouwstenen.](buildingblocks.md) Als het element niet bestaat, voeg je het toe.
1. Zoek het element [ClaimSchema.](claimsschema.md) Als het element niet bestaat, voeg je het toe.
1. Voeg de volgende claims toe aan het element **ClaimsSchema.** 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Nieuwe technische profielen toevoegen

Technische profielen kunnen worden beschouwd als functies in het Identity Experience Framework van Azure AD B2C. In deze tabel worden de technische profielen gedefinieerd die worden gebruikt om een sessie te openen en gebeurtenissen te plaatsen.

| Technisch profiel | Taak |
| ----------------- | -----|
| Algemeen appInsights | De algemene set parameters die in alle technische azure insights-profielen moeten worden opgenomen. |
| AppInsights-SignInRequest | Registreert `SignInRequest` een gebeurtenis met een reeks claims wanneer een aanmeldingsverzoek is ontvangen. |
| AppInsights-UserSignUp | Registreert `UserSignUp` een gebeurtenis wanneer de gebruiker de aanmeldingsoptie activeert in een aanmeldings-/aanmeldingsreis. |
| AppInsights-SignInComplete | Registreert `SignInComplete` een gebeurtenis op basis van het succesvol voltooien van een verificatie, wanneer een token is verzonden naar de toepassing van de relying party. |

Voeg de profielen toe aan het *bestand TrustFrameworkExtensions.xml* vanuit het startpakket. Voeg deze elementen toe aan het element **ClaimsProviders:**

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Wijzig de instrumentatiesleutel in het `AppInsights-Common` technische profiel in de GUID die uw Application Insights-bron biedt.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>De technische profielen toevoegen als orkestratiestappen

Bel `AppInsights-SignInRequest` als orkestratiestap 2 om bij te houden dat er een aanmeldings-/aanmeldingsverzoek is ontvangen:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Voeg *vlak voor* de `SendClaims` orkestratiestap een `AppInsights-UserSignup`nieuwe stap toe die aanroept. Deze wordt geactiveerd wanneer de gebruiker de aanmeldingsknop selecteert in een aanmeldings-/aanmeldingsreis.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Onmiddellijk na `SendClaims` de orkestratie `AppInsights-SignInComplete`stap, bel . Deze stap toont een succesvol voltooide reis.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Nadat u de nieuwe orkestratiestappen hebt toegevoegd, u de stappen opeenvolgend opnieuw nummeren zonder gehele getallen van 1 naar N over te slaan.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Uw bestand uploaden, het beleid uitvoeren en gebeurtenissen weergeven

Sla het *bestand TrustFrameworkExtensions.xml* op en upload deze. Bel vervolgens het beleid van de relying party vanuit uw toepassing of gebruik **Run Now** in de Azure-portal. Binnen enkele seconden zijn uw gebeurtenissen beschikbaar in Application Insights.

1. Open de **bron Application Insights** in uw Azure Active Directory-tenant.
2. Selecteer **Gebruiksgebeurtenissen** > **Events**.
3. Stel **tijdens** in op **het laatste uur** en **door** op **3 minuten**.  Mogelijk moet u **Vernieuwen** selecteren om resultaten weer te geven.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>[Optioneel] Meer gegevens verzamelen

Voeg claimtypen en gebeurtenissen toe aan uw gebruikersreis om aan uw behoeften te voldoen. U [claimresolvers](claim-resolver-overview.md) of een tekenreeksclaimtype gebruiken, de claims toevoegen door een **element Invoerclaim** toe te voegen aan de gebeurtenis Application Insights of aan het technische profiel AppInsights-Common.

- **ClaimTypeReferenceId** is de verwijzing naar een claimtype.
- **PartnerClaimType** is de naam van de eigenschap die wordt weergegeven in Azure Insights. Gebruik de `{property:NAME}`syntaxis `NAME` van , waar wordt de eigenschap toegevoegd aan de gebeurtenis.
- **DefaultValue** gebruikt een tekenreekswaarde of de claimresolver.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het technische profiel [van Application Insights](application-insights-technical-profile.md) vindt u in de IEF-referentie. 
