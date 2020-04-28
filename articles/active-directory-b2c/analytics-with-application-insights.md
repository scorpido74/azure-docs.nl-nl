---
title: Gebruikers gedrag bijhouden met Application Insights
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van gebeurtenis Logboeken in Application Insights van Azure AD B2C gebruikers trajecten door aangepaste beleids regels te gebruiken.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80672520"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Gebruikers gedrag bijhouden in Azure Active Directory B2C met behulp van Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) ondersteunt het rechtstreeks verzenden van gebeurtenis gegevens naar [Application Insights](../azure-monitor/app/app-insights-overview.md) door gebruik te maken van de instrumentatie sleutel die aan Azure AD B2C is gegeven.  Met een Application Insights technisch profiel kunt u gedetailleerde en aangepaste gebeurtenis logboeken ophalen voor uw gebruikers trajecten:

* Krijg inzichten op het gedrag van gebruikers.
* Los uw eigen beleid in ontwikkeling of productie op.
* Prestaties meten.
* Meldingen van Application Insights maken.

## <a name="how-it-works"></a>Hoe werkt het?

Het technische profiel van [Application Insights](application-insights-technical-profile.md) definieert een gebeurtenis van Azure AD B2C. Het profiel bevat de naam van de gebeurtenis, de claims die worden vastgelegd en de instrumentatie sleutel. Als u een evenement wilt plaatsen, wordt het technische profiel vervolgens toegevoegd als een indelings stap in een [gebruikers traject](userjourneys.md).

Application Insights kunt de gebeurtenissen samen voegen met behulp van een correlatie-ID om een gebruikers sessie vast te leggen. Application Insights maakt de gebeurtenis en sessie binnen enkele seconden beschikbaar en biedt veel visualisatie-, export-en analytische hulpprogram ma's.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid](custom-policy-get-started.md). U moet beschikken over een werkend aangepast beleid voor het aanmelden en aanmelden met lokale accounts.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Wanneer u Application Insights met Azure AD B2C gebruikt, hoeft u alleen maar een resource te maken en de instrumentatie sleutel op te halen. Zie [een Application Insights resource maken](../azure-monitor/app/create-new-resource.md) voor meer informatie

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure-abonnement gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw abonnement bevat. Deze Tenant is niet uw Azure AD B2C-Tenant.
3. Kies **een resource maken** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **Application Insights**.
4. Klik op **maken**.
5. Voer een **naam** in voor de resource.
6. Selecteer voor **toepassings Type** **ASP.NET-webtoepassing**.
7. Voor **resource groep**selecteert u een bestaande groep of voert u een naam in voor een nieuwe groep.
8. Klik op **maken**.
4. Nadat u de Application Insights resource hebt gemaakt, opent u deze, vouwt u de **essentiële**elementen uit en kopieert u de instrumentatie sleutel.

![Application Insights overzicht en instrumentatie sleutel](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Claims definiëren

Een claim biedt een tijdelijke opslag van gegevens tijdens het uitvoeren van een Azure AD B2C beleid. Het [claim schema](claimsschema.md) is de plaats waar u uw claims declareert.

1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em> `SocialAndLocalAccounts/` </em>.
1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claims toe aan het **ClaimsSchema** -element. 

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

Technische profielen kunnen worden beschouwd als functies in het Framework voor identiteits ervaring van Azure AD B2C. In deze tabel worden de technische profielen gedefinieerd die worden gebruikt voor het openen van een sessie en het plaatsen van gebeurtenissen.

| Technisch profiel | Taak |
| ----------------- | -----|
| AppInsights-algemeen | De algemene set para meters die moeten worden opgenomen in alle technische profielen van Azure Insights. |
| AppInsights-SignInRequest | Registreert `SignInRequest` een gebeurtenis met een set claims wanneer een aanmeldings aanvraag is ontvangen. |
| AppInsights-UserSignUp | Registreert `UserSignUp` een gebeurtenis wanneer de gebruiker de registratie optie activeert in een traject voor registreren/aanmelden. |
| AppInsights-SignInComplete | Registreert `SignInComplete` een gebeurtenis wanneer een verificatie is voltooid, wanneer er een token naar de Relying Party-toepassing is verzonden. |

Voeg de profielen toe aan het bestand *TrustFrameworkExtensions. XML* van het Starter Pack. Deze elementen toevoegen aan het **ClaimsProviders** -element:

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
> Wijzig de instrumentatie sleutel in het `AppInsights-Common` technische profiel in de GUID die uw Application Insights resource levert.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Voeg de technische profielen toe als Orchestration-stappen

Aanroep `AppInsights-SignInRequest` als Orchestration-stap 2 om bij te houden dat er een aanvraag voor aanmelden/registreren is ontvangen:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Voeg *before* onmiddellijk vóór `SendClaims` de Orchestration-stap een nieuwe stap toe die aanroept `AppInsights-UserSignup`. Het wordt geactiveerd wanneer de gebruiker de registratie knop selecteert in een traject voor registreren/aanmelden.

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

Bel `AppInsights-SignInComplete`direct na `SendClaims` de Orchestration-stap. In deze stap ziet u dat de reis is voltooid.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Nadat u de nieuwe indelings stappen hebt toegevoegd, moet u de stappen sequentieel opnieuw nummeren zonder een geheel getal tussen 1 en N over te slaan.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Upload uw bestand, voer het beleid uit en Bekijk gebeurtenissen

Sla het bestand *TrustFrameworkExtensions. XML* op en upload het. Roep vervolgens het Relying Party-beleid aan vanuit uw toepassing of gebruik **nu uitvoeren** in de Azure Portal. In enkele seconden zijn uw gebeurtenissen beschikbaar in Application Insights.

1. Open de **Application Insights** -resource in uw Azure Active Directory-Tenant.
2. Selecteer **gebruiks** > **gebeurtenissen**.
3. Stel **in** op **vorig uur** en **met** **3 minuten**.  Mogelijk moet u **vernieuwen** selecteren om de resultaten weer te geven.

![Application Insights gebruik: gebeurtenissen blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>Beschrijving Meer gegevens verzamelen

Voeg claim typen en gebeurtenissen toe aan uw gebruikers traject, zodat deze aan uw behoeften voldoen. U kunt [claim resolvers](claim-resolver-overview.md) of een teken reeks claim type gebruiken, de claims toevoegen door een **invoer claim** element toe te voegen aan de Application Insights gebeurtenis of aan het AppInsights-algemeen technische profiel.

- **ClaimTypeReferenceId** is de verwijzing naar een claim type.
- **PartnerClaimType** is de naam van de eigenschap die wordt weer gegeven in azure Insights. Gebruik de syntaxis van `{property:NAME}`, waarbij `NAME` de eigenschap wordt toegevoegd aan de gebeurtenis.
- **DefaultValue** gebruik een wille keurige teken reeks waarde of de claim resolver.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Application Insights](application-insights-technical-profile.md) technisch profiel vindt u in de IEF-referentie. 
