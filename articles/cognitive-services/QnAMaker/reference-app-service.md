---
title: Service configuratie-QnA Maker
description: Meer informatie over hoe en waar u resources kunt configureren.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: eac930971cab041fbf398da1ac5f8a055412832d
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376856"
---
# <a name="service-configuration"></a>Serviceconfiguratie

Voor elke versie van QnA Maker wordt een andere set met Azure-resources (Services) gebruikt. In dit artikel worden de ondersteunde aanpassingen voor deze services beschreven. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele versie)](#tab/v1)

QnA Maker gebruikt de App Service voor het leveren van de query runtime die wordt gebruikt door de [generateAnswer-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).

Deze instellingen zijn beschikbaar in de Azure Portal voor de App Service. De instellingen zijn beschikbaar door **instellingen** en vervolgens **configuratie** te selecteren.

U kunt een afzonderlijke instelling instellen via de lijst toepassings instellingen of verschillende instellingen wijzigen door **Geavanceerd bewerken** te selecteren.

|Resource|Instelling|
|--|--|
|AzureSearchAdminKey|Cognitive Search: wordt gebruikt voor QnA Storage en Rank #1|
|AzureSearchName|Cognitive Search: wordt gebruikt voor QnA Storage en Rank #1|
|DefaultAnswer|Tekst van het antwoord wanneer er geen overeenkomst wordt gevonden|
|UserAppInsightsAppId|Chat logboek en telemetrie|
|UserAppInsightsKey|Chat logboek en telemetrie|
|UserAppInsightsName|Chat logboek en telemetrie|

U moet de service **opnieuw starten** op de pagina **overzicht** van de Azure Portal, wanneer u klaar bent met het aanbrengen van wijzigingen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-versie)](#tab/v2)

App Service aanpassingen zijn niet van toepassing op QnA Maker beheerd (preview).

---

## <a name="qna-maker-service"></a>QnA Maker-service

De QnA Maker-service biedt de configuratie voor de volgende gebruikers om samen te werken aan één QnA Maker-service en alle kennissen daarvan.

Meer informatie [over het toevoegen van samen](./how-to/collaborate-knowledge-base.md) werkers aan uw service.

## <a name="change-azure-cognitive-search"></a>Azure Cognitive Search wijzigen

Meer informatie [over het wijzigen van de Cognitive Search-service](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) die aan uw QnA Maker service is gekoppeld.

## <a name="change-default-answer"></a>Standaardantwoord wijzigen

Meer informatie [over hoe u de tekst van uw standaard antwoorden kunt wijzigen](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Telemetrie

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele versie)](#tab/v1)

Application Insights wordt gebruikt voor het bewaken van telemetrie met QnA Maker GA. Er zijn geen configuratie-instellingen specifiek voor QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-versie)](#tab/v2)

Meer informatie [over hoe u telemetrie kunt toevoegen aan uw QnA Maker Managed-Service (preview)](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>App Service-plan

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (stabiele versie)](#tab/v1)

App Service plan heeft geen specifieke configuratie-instellingen voor QnA Maker.

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker beheerd (preview-versie)](#tab/v2)

App Service plan wordt niet gebruikt met QnA Maker beheerd (preview-versie).

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [indelingen](reference-document-format-guidelines.md) voor documenten en url's die u wilt importeren in een Knowledge Base.