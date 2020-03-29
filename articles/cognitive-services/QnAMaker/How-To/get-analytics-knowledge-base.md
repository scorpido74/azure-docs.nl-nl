---
title: Analytics op knowledgebase - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker slaat alle chatlogs en andere telemetrie op, als u App Insights hebt ingeschakeld tijdens het maken van uw QnA Maker-service. Voer de voorbeeldquery's uit om uw chatlogboeken uit App Insights te halen.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: e769bde39bc796b5b598109328b468b15385f38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650398"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Analytische gegevens verkrijgen voor uw knowledge base

QnA Maker slaat alle chatlogs en andere telemetrie op, als u App Insights hebt ingeschakeld tijdens het [maken van uw QnA Maker-service.](./set-up-qnamaker-service-azure.md) Voer de voorbeeldquery's uit om uw chatlogboeken uit App Insights te halen.

1. Ga naar uw App Insights-bron.

    ![Selecteer de bron voor toepassingsinzichten](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Selecteer **Log (Analytics)**. Er wordt een nieuw venster geopend waar u de telemetrie van QnA Maker opvragen.

3. Plak de volgende query en voer deze uit.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Selecteer **Uitvoeren** om de query uit te voeren.

    [![Query uitvoeren om vragen, antwoorden en score van gebruikers te bepalen](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Query's uitvoeren voor andere analyses op uw QnA Maker-kennisbank

### <a name="total-90-day-traffic"></a>Totaal 90-daags verkeer

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Totaal vragenverkeer in een bepaalde periode

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Gebruikersverkeer

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Latentieverdeling van vragen

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Onbeantwoorde vragen

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kies capactiy](./improve-knowledge-base.md)
