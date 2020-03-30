---
title: Aangepaste beleidsregels oplossen met Toepassingsinzichten
titleSuffix: Azure AD B2C
description: Application Insights instellen om de uitvoering van uw aangepaste beleid te traceren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186264"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Azure Active Directory B2C-logboeken verzamelen met Toepassingsinzichten

In dit artikel vindt u stappen voor het verzamelen van logboeken uit Active Directory B2C (Azure AD B2C), zodat u problemen met uw aangepaste beleidsregels diagnosticeren. Application Insights biedt een manier om uitzonderingen te diagnosticeren en problemen met de prestaties van toepassingen te visualiseren. Azure AD B2C bevat een functie voor het verzenden van gegevens naar Application Insights.

De gedetailleerde activiteitslogboeken die hier worden beschreven, moeten **ALLEEN** worden ingeschakeld tijdens de ontwikkeling van uw aangepaste beleid.

> [!WARNING]
> Schakel de ontwikkelingsmodus in de productie niet in. Logs verzamelen alle claims die van en naar identiteitsproviders worden verzonden. U als ontwikkelaar neemt de verantwoordelijkheid op u voor alle persoonsgegevens die worden verzameld in uw Application Insights-logboeken. Deze gedetailleerde logboeken worden alleen verzameld wanneer het beleid in **DE ONTWIKKELAARSMODUS**wordt geplaatst.

## <a name="set-up-application-insights"></a>Toepassingsinzichten instellen

Als u er nog geen hebt, maakt u een exemplaar van Application Insights in uw abonnement.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure-abonnement (niet uw Azure AD B2C-map).
1. Selecteer **Een resource maken** in het navigatiemenu aan de linkerkant.
1. Zoeken naar en selecteer **Toepassingsinzichten**en selecteer vervolgens **Maken**.
1. Vul het formulier in, selecteer **Controleren + maken**en selecteer Vervolgens **Maken**.
1. Zodra de implementatie is voltooid, selecteert u **Ga naar resource**.
1. Selecteer **Eigenschappen**onder **Configureren** in het menu Toepassingsstatistieken .
1. Noteer de **INSTRUMENTATIE-SLEUTEL** voor gebruik in een latere stap.

## <a name="configure-the-custom-policy"></a>Het aangepaste beleid configureren

1. Open het RP-bestand (relying party), bijvoorbeeld *SignUpOrSignin.xml*.
1. Voeg de volgende kenmerken `<TrustFrameworkPolicy>` toe aan het element:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Als deze nog niet bestaat, `<UserJourneyBehaviors>` voegt u `<RelyingParty>` een onderliggend knooppunt toe aan het knooppunt. Het moet onmiddellijk `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`na .
1. Voeg als onderliggend element `<UserJourneyBehaviors>` het volgende knooppunt toe. Zorg ervoor `{Your Application Insights Key}` dat u de **instrumentatiesleutel van** Application Insights vervangt die u eerder hebt opgenomen.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`vertelt ApplicationInsights om de telemetrie via de verwerkingspijplijn te versnellen. Goed voor ontwikkeling, maar beperkt bij hoge volumes.
    * `ClientEnabled="true"`hiermee wordt het Script aan de clientzijde van ApplicationInsights uitgevoerd voor het bijhouden van paginaweergave en clientfouten. U deze bekijken in de tabel **browserTimings** in de Portal Application Insights. Door `ClientEnabled= "true"`toepassingsinformatie toe te voegen, voegt u Application Insights toe aan uw paginascript en krijgt u timings van paginabelastingen en AJAX-oproepen, tellingen, details van browseruitzonderingen en AJAX-fouten en gebruikers- en sessietellingen. Dit veld is **optioneel**en `false` is standaard ingesteld op.
    * `ServerEnabled="true"`stuurt de bestaande UserJourneyRecorder JSON als een aangepaste gebeurtenis naar Application Insights.

    Bijvoorbeeld:

    ```XML
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Upload het beleid.

## <a name="see-the-logs-in-application-insights"></a>Bekijk de logboeken in Application Insights

Er is een korte vertraging, meestal minder dan vijf minuten, voordat u nieuwe logboeken zien in Application Insights.

1. Open de Application Insights-bron die u hebt gemaakt in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Analytics**in het menu **Overzicht** .
1. Open een nieuw tabblad in Application Insights.

Hier is een lijst met query's die u gebruiken om de logboeken te bekijken:

| Query’s uitvoeren | Beschrijving |
|---------------------|--------------------|
`traces` | Bekijk alle logboeken die zijn gegenereerd door Azure AD B2C |
`traces | where timestamp > ago(1d)` | Bekijk alle logboeken die zijn gegenereerd door Azure AD B2C voor de laatste dag

De inzendingen kunnen lang zijn. Exporteer naar CSV voor een nadere blik.

Zie [Overzicht van logboekquery's in Azure Monitor](../azure-monitor/log-query/log-query-overview.md)voor meer informatie over query's.

## <a name="next-steps"></a>Volgende stappen

De community heeft een user journey viewer ontwikkeld om identiteitsontwikkelaars te helpen. Het leest uit uw instantie Application Insights en biedt een goed gestructureerd overzicht van de user journey-gebeurtenissen. U verkrijgt de broncode en implementeert deze in uw eigen oplossing.

De user journey speler wordt niet ondersteund door Microsoft, en wordt strikt beschikbaar gesteld zoals-is.

U vindt de versie van de viewer die gebeurtenissen leest uit Application Insights op GitHub, hier:

[Azure-Samples/active-directory-b2c-advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
