---
title: Problemen met aangepaste beleid oplossen met Application Insights
titleSuffix: Azure AD B2C
description: Application Insights instellen om de uitvoering van uw aangepaste beleids regels te traceren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3aeef1614f22563e0fd348c5bc6ae7ff1e7b0b03
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848146"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Azure Active Directory B2C-logboeken met Application Insights verzamelen

In dit artikel worden de stappen beschreven voor het verzamelen van logboeken van Active Directory B2C (Azure AD B2C), zodat u problemen met uw aangepaste beleids regels kunt vaststellen. Application Insights biedt een manier om uitzonde ringen te diagnosticeren en prestatie problemen van toepassingen te visualiseren. Azure AD B2C bevat een functie voor het verzenden van gegevens naar Application Insights.

De gedetailleerde activiteiten logboeken die hier worden beschreven, moeten **alleen** worden ingeschakeld tijdens de ontwikkeling van uw aangepaste beleids regels.

> [!WARNING]
> Schakel de ontwikkelings modus niet in voor productie. In Logboeken worden alle claims verzameld die worden verzonden naar en van id-providers. U bent als ontwikkelaar verantwoordelijk voor alle persoons gegevens die in uw Application Insights-logboeken worden verzameld. Deze gedetailleerde logboeken worden alleen verzameld wanneer het beleid in de **ontwikkelaars modus**wordt geplaatst.

## <a name="set-up-application-insights"></a>Application Insights instellen

Als u er nog geen hebt, maakt u een instantie van Application Insights in uw abonnement.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure-abonnement bevat (niet uw Azure AD B2C Directory).
1. Selecteer **een resource maken** in het navigatie menu aan de linkerkant.
1. Zoek en selecteer **Application Insights**en selecteer vervolgens **maken**.
1. Vul het formulier in, selecteer **controleren + maken**en selecteer vervolgens **maken**.
1. Zodra de implementatie is voltooid, selecteert **u naar resource**.
1. Onder **configureren** in Application Insights menu, selecteert u **Eigenschappen**.
1. Noteer de **instrumentatie sleutel** voor gebruik in een latere stap.

## <a name="configure-the-custom-policy"></a>Het aangepaste beleid configureren

1. Open het Relying Party (RP)-bestand, bijvoorbeeld *SignUpOrSignin. XML*.
1. Voeg de volgende kenmerken toe aan het `<TrustFrameworkPolicy>`-element:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Als deze nog niet bestaat, voegt u een onder`<UserJourneyBehaviors>` onderliggend knoop punt toe aan het knoop punt `<RelyingParty>`. Deze moet direct na `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`worden geplaatst.
1. Voeg het volgende knoop punt toe als onderliggend item van het `<UserJourneyBehaviors>`-element. Zorg ervoor dat u `{Your Application Insights Key}` vervangt door de Application Insights **instrumentatie sleutel** die u eerder hebt vastgelegd.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` vertelt ApplicationInsights de telemetrie te versnellen door de verwerkings pijplijn. Goed voor ontwikkeling, maar beperkt op hoge volumes.
    * `ClientEnabled="true"` verzendt het ApplicationInsights script aan de client zijde voor het bijhouden van de pagina weergave en fouten aan de client zijde. U kunt deze weer geven in de tabel **browserTimings** in de Application Insights Portal. Door `ClientEnabled= "true"`in te stellen, voegt u Application Insights toe aan het script van uw pagina en krijgt u een tijds duur van het laden van pagina's en AJAX-aanroepen, tellingen, Details van browser uitzonderingen en AJAX-fouten, en het aantal gebruikers en sessies. Dit veld is **optioneel**en wordt standaard ingesteld op `false`.
    * `ServerEnabled="true"` stuurt de bestaande UserJourneyRecorder-JSON als een aangepaste gebeurtenis naar Application Insights.

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

## <a name="see-the-logs-in-application-insights"></a>Raadpleeg de logboeken in Application Insights

Er is een korte vertraging, meestal minder dan vijf minuten, voordat u nieuwe logboeken in Application Insights kunt zien.

1. Open de Application Insights resource die u hebt gemaakt in de [Azure Portal](https://portal.azure.com).
1. Selecteer in het menu **overzicht** de optie **analyse**.
1. Open een nieuw tabblad in Application Insights.

Hier volgt een lijst met query's die u kunt gebruiken om de logboeken weer te geven:

| Query | Beschrijving |
|---------------------|--------------------|
`traces` | Alle logboeken weer geven die zijn gegenereerd door Azure AD B2C |
`traces | where timestamp > ago(1d)` | Alle logboeken weer geven die zijn gegenereerd door Azure AD B2C voor de afgelopen dag

De invoer kan lang zijn. Exporteren naar CSV voor een betere blik.

Zie [overzicht van logboek query's in azure monitor](../azure-monitor/log-query/log-query-overview.md)voor meer informatie over het uitvoeren van query's.

## <a name="next-steps"></a>Volgende stappen

De community heeft een reis viewer voor gebruikers ontwikkeld om identiteits ontwikkelaars te helpen. Het leest van uw Application Insights-exemplaar en biedt een goed gestructureerde weer gave van de reis gebeurtenissen van de gebruiker. U verkrijgt de bron code en implementeert deze in uw eigen oplossing.

De gebruikers reis speler wordt niet ondersteund door micro soft en wordt strikt beschikbaar gesteld.

U kunt de versie van de viewer vinden die gebeurtenissen van Application Insights op GitHub leest:

[Azure-samples/Active-Directory-B2C-Advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
