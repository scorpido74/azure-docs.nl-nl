---
title: Service configuratie-QnA Maker
description: Meer informatie over hoe en waar u resources kunt configureren.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651859"
---
# <a name="service-configuration"></a>Service configuratie

QnA Maker maakt gebruik van verschillende Azure-resources (Services), waaronder Cognitive Search, App Service, App Service plan en Application Insights.

Alle aanpassingen aan deze instellingen die door QnA Maker worden ondersteund, worden hieronder weer gegeven.

## <a name="app-service"></a>App Service

QnA Maker gebruikt de App Service voor het leveren van de query runtime die wordt gebruikt door de [generateAnswer-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Deze instellingen zijn beschikbaar in de Azure Portal voor de App Service. De instellingen zijn beschikbaar door **instellingen**en vervolgens **configuratie**te selecteren.

U kunt een afzonderlijke instelling instellen via de lijst toepassings instellingen of verschillende instellingen wijzigen door **Geavanceerd bewerken**te selecteren.

|Resource|Instelling|
|--|--|
|AzureSearchAdminKey|Cognitive Search: wordt gebruikt voor QnA opslag en rangorde #1|
|AzureSearchName|Cognitive Search: wordt gebruikt voor QnA opslag en rangorde #1|
|DefaultAnswer|Tekst van het antwoord wanneer er geen overeenkomst wordt gevonden|
|UserAppInsightsAppId|Chat logboek en telemetrie|
|UserAppInsightsKey|Chat logboek en telemetrie|
|UserAppInsightsName|Chat logboek en telemetrie|

Meer informatie [over het toevoegen van wijzigingen](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) aan uw Cognitive Search-service aan uw service.

U moet de service **opnieuw starten** op de pagina **overzicht** van de Azure Portal, wanneer u klaar bent met het aanbrengen van wijzigingen.

## <a name="qna-maker-service"></a>QnA Maker-Service

De QnA Maker-service biedt de configuratie voor de volgende gebruikers om samen te werken aan één QnA Maker-service en alle kennissen daarvan.

Meer informatie [over het toevoegen van samen](./how-to/collaborate-knowledge-base.md) werkers aan uw service.

## <a name="application-insights"></a>Application Insights

Application Insights heeft geen configuratie-instellingen die specifiek zijn voor QnA Maker.

## <a name="app-service-plan"></a>App Service-plan

App Service plan heeft geen specifieke configuratie-instellingen voor QnA Maker.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [indelingen](reference-document-format-guidelines.md) voor documenten en url's die u wilt importeren in een Knowledge Base.