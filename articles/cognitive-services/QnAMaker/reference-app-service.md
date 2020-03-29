---
title: Serviceconfiguratie - QnA Maker
description: Begrijp hoe en waar u resources configureren.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651859"
---
# <a name="service-configuration"></a>Serviceconfiguratie

QnA Maker maakt gebruik van verschillende Azure-bronnen (services), waaronder Cognitief zoeken, App Service, App Service Plan en Application Insights.

Alle aanpassingen aan deze instellingen die door QnA Maker worden ondersteund, worden hieronder weergegeven.

## <a name="app-service"></a>App Service

QnA Maker gebruikt de App-service om de queryruntijd te bieden die wordt gebruikt door de [generateAnswer API.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)


Deze instellingen zijn beschikbaar in de Azure-portal voor de App-service. De instellingen zijn beschikbaar door **Instellingen**te selecteren , vervolgens **Configuratie**.

U een afzonderlijke instelling instellen via de lijst Toepassingsinstellingen of verschillende instellingen wijzigen door **Geavanceerd bewerken te selecteren.**

|Resource|Instelling|
|--|--|
|AzureSearchAdminKey|Cognitief zoeken - gebruikt voor QnA set storage en Ranker #1|
|AzureSearchName|Cognitief zoeken - gebruikt voor QnA set storage en Ranker #1|
|Standaardantwoord|Tekst van het antwoord wanneer er geen overeenkomst wordt gevonden|
|UserAppInsightsAppId|Chatlogboek en telemetrie|
|UserAppInsightsKey|Chatlogboek en telemetrie|
|UserAppInsightsName|Chatlogboek en telemetrie|

Meer informatie over het toevoegen van wijzigingen in uw service voor [Cognitive Search.](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)

U moet de service **opnieuw starten** vanaf de **overzichtspagina** van de Azure-portal, zodra u klaar bent met het aanbrengen van wijzigingen.

## <a name="qna-maker-service"></a>QnA Maker Service

De QnA Maker-service biedt configuratie voor de volgende gebruikers om samen te werken aan één QnA Maker-service en al zijn kennisbases.

Meer informatie over [het toevoegen van bijdragers](./how-to/collaborate-knowledge-base.md) aan uw service.

## <a name="application-insights"></a>Application Insights

Application Insights heeft geen configuratie-instellingen die specifiek zijn voor QnA Maker.

## <a name="app-service-plan"></a>App Service-plan

App Service Plan heeft geen configuratie-instellingen die specifiek zijn voor QnA Maker.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [indelingen](reference-document-format-guidelines.md) voor documenten en URL's die u wilt importeren in een kennisbank.