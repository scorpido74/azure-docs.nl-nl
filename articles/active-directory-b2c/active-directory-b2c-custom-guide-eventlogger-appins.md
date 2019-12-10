---
title: Gebruikers gedrag bijhouden met Application Insights
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van gebeurtenis Logboeken in Application Insights van Azure AD B2C gebruikers trajecten met aangepaste beleids regels (preview).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6643759688817811890fd022c7aa061607270b9e
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948943"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Gebruikers gedrag bijhouden in Azure Active Directory B2C met behulp van Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Wanneer u Azure Active Directory B2C (Azure AD B2C) gebruikt in combi natie met Azure-toepassing Insights, kunt u gedetailleerde en aangepaste gebeurtenis logboeken voor uw gebruikers trajecten ophalen. In dit artikel leert u het volgende:

* Krijg inzichten op het gedrag van gebruikers.
* Los uw eigen beleid in ontwikkeling of productie op.
* Prestaties meten.
* Meldingen van Application Insights maken.

## <a name="how-it-works"></a>Het werkt als volgt

Het Framework voor identiteits ervaring in Azure AD B2C bevat de provider `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Er worden gebeurtenis gegevens rechtstreeks naar Application Insights verzonden met behulp van de instrumentatie sleutel die aan Azure AD B2C wordt gegeven.

Met een technisch profiel wordt deze provider gebruikt voor het definiëren van een gebeurtenis van Azure AD B2C. Het profiel bevat de naam van de gebeurtenis, de claims die worden vastgelegd en de instrumentatie sleutel. Als u een evenement wilt plaatsen, wordt het technische profiel toegevoegd als `orchestration step`of als een `validation technical profile` in een aangepaste gebruikers traject.

Application Insights kunt de gebeurtenissen samen voegen met behulp van een correlatie-ID om een gebruikers sessie vast te leggen. Application Insights maakt de gebeurtenis en sessie binnen enkele seconden beschikbaar en biedt veel visualisatie-, export-en analytische hulpprogram ma's.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid](active-directory-b2c-get-started-custom.md). In dit artikel wordt ervan uitgegaan dat u het aangepaste beleids Starter Pack gebruikt. Maar het Starter Pack is niet vereist.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Wanneer u Application Insights met Azure AD B2C gebruikt, hoeft u alleen maar een resource te maken en de instrumentatie sleutel op te halen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure-abonnement gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw abonnement bevat. Deze Tenant is niet uw Azure AD B2C-Tenant.
3. Kies **een resource maken** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **Application Insights**.
4. Klik op **Maken**.
5. Voer een **naam** in voor de resource.
6. Selecteer voor **toepassings Type** **ASP.NET-webtoepassing**.
7. Voor **resource groep**selecteert u een bestaande groep of voert u een naam in voor een nieuwe groep.
8. Klik op **Maken**.
4. Nadat u de Application Insights resource hebt gemaakt, opent u deze, vouwt u de **essentiële**elementen uit en kopieert u de instrumentatie sleutel.

![Application Insights overzicht en instrumentatie sleutel](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Nieuwe claim type definities toevoegen

Open het bestand *TrustFrameworkExtensions. XML* uit het Start pakket en voeg de volgende elementen toe aan het [BuildingBlocks](buildingblocks.md) -element:

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>Nieuwe technische profielen toevoegen

Technische profielen kunnen worden beschouwd als functies in het Framework voor identiteits ervaring van Azure AD B2C. In deze tabel worden de technische profielen gedefinieerd die worden gebruikt voor het openen van een sessie en het plaatsen van gebeurtenissen.

| Technisch profiel | Taak |
| ----------------- | -----|
| AzureInsights-Common | Hiermee maakt u een algemene set para meters die moeten worden opgenomen in alle AzureInsights Technical-profielen. |
| AzureInsights-SignInRequest | Maakt een gebeurtenis aanmelden met een set claims wanneer een aanmeldings aanvraag is ontvangen. |
| AzureInsights-UserSignup | Hiermee maakt u een UserSignup-gebeurtenis wanneer de gebruiker de registratie optie activeert in een traject voor registreren/aanmelden. |
| AzureInsights-SignInComplete | Registreert de geslaagde voltooiing van een verificatie wanneer een token naar de Relying Party-toepassing is verzonden. |

Voeg de profielen toe aan het bestand *TrustFrameworkExtensions. XML* van het Starter Pack. Deze elementen toevoegen aan het **ClaimsProviders** -element:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Wijzig de instrumentatie sleutel in het technische profiel van `AzureInsights-Common` in de GUID die uw Application Insights resource levert.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Voeg de technische profielen toe als Orchestration-stappen

Roep `Azure-Insights-SignInRequest` aan als indelings stap 2 om te controleren of er een aanvraag voor aanmelden/registreren is ontvangen:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Voeg direct *vóór* de `SendClaims` Orchestration-stap een nieuwe stap toe die `Azure-Insights-UserSignup`aanroept. Het wordt geactiveerd wanneer de gebruiker de registratie knop selecteert in een traject voor registreren/aanmelden.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Bel direct na de `SendClaims` Orchestration-stap `Azure-Insights-SignInComplete`. In deze stap ziet u dat de reis is voltooid.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
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

![Application Insights gebruik: gebeurtenissen blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Volgende stappen

Voeg claim typen en gebeurtenissen toe aan uw gebruikers traject, zodat deze aan uw behoeften voldoen. U kunt [claim resolvers](claim-resolver-overview.md) of een teken reeks claim type gebruiken, de claims toevoegen door een **invoer claim** element toe te voegen aan de Application Insights gebeurtenis of aan het AzureInsights-algemeen technische profiel.

- **ClaimTypeReferenceId** is de verwijzing naar een claim type.
- **PartnerClaimType** is de naam van de eigenschap die wordt weer gegeven in azure Insights. Gebruik de syntaxis van `{property:NAME}`, waarbij `NAME` een eigenschap is die wordt toegevoegd aan de gebeurtenis.
- **DefaultValue** gebruik een wille keurige teken reeks waarde of de claim resolver.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

