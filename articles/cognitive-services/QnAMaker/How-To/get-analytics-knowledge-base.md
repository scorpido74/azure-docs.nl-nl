---
title: Analytics op Knowledge Base-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker alle chat logboeken en andere telemetrie opslaat, als u app Insights hebt ingeschakeld tijdens het maken van uw QnA Maker-service. Voer de voorbeeld query's uit om uw chat logboeken van app Insights te ontvangen.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: 58bd765b5f240f56cad0eafc4952918c9cd52c36
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193582"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Analytische gegevens verkrijgen voor uw knowledge base

QnA Maker alle chat logboeken en andere telemetrie opslaat, als u app Insights hebt ingeschakeld tijdens het [maken van uw QnA Maker-service](./set-up-qnamaker-service-azure.md). Voer de voorbeeld query's uit om uw chat logboeken van app Insights te ontvangen.

1. Ga naar uw app Insights-resource.

    ![Uw Application Insights-resource selecteren](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Selecteer **Analytics**. Er wordt een nieuw venster geopend waarin u QnA Maker telemetrie kunt opvragen.

    ![Analyse selecteren](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Plak de volgende query en voer deze uit.

    ```query
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration, performanceBucket
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId 
    ```

    Selecteer **uitvoeren** om de query uit te voeren.

    ![Query uitvoeren](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Query's uitvoeren voor andere analyses op uw QnA Maker Knowledge Base

### <a name="total-90-day-traffic"></a>Totaal 90-dagen verkeer

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Totaal vraag verkeer binnen een bepaalde tijds periode

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Gebruikers verkeer

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Latentie distributie van vragen

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Capactiy kiezen](../tutorials/choosing-capacity-qnamaker-deployment.md)
