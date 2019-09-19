---
title: Aangepaste volgschema's voor B2B-berichten - Azure Logic Apps | Microsoft Docs
description: Aangepaste volgschema's voor die B2B-berichten in integratieaccounts voor Azure Logic Apps met Enterprise Integration Pack bewaken maken
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: 76a9ece9e925543e856136a798a60038316caad9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203046"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Maken van aangepaste volgschema's voor die end-to-end-werkstromen in Azure Logic Apps bewaken

Is er ingebouwde bijhouden dat u voor verschillende onderdelen van uw business-to-business-werkstroom, zoals het bijhouden van de AS2- of X12 berichten inschakelen kunt. Wanneer u werkstromen maakt die bevat een logische app, BizTalk Server, SQL Server of een andere laag en vervolgens kunt u aangepaste volgschema die gebeurtenissen vanaf het begin aan het einde van de werkstroom vastlegt inschakelen. 

Dit artikel bevat aangepaste code die u in de lagen buiten uw logische app gebruiken kunt. 

## <a name="custom-tracking-schema"></a>Aangepaste volgschema's

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": "",
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {                
         }
      }
   ]
}
```

| Eigenschap | Vereist | Type | Description |
| --- | --- | --- | --- |
| sourceType | Ja |   | Het type van de bron-uitvoeren. Toegestane waarden zijn **Microsoft.Logic/workflows** en **aangepaste**. |
| source | Ja |   | Als het brontype is **Microsoft.Logic/workflows**, de informatie van de gegevensbron moet volgen dit schema. Als het brontype is **aangepaste**, het schema is een JToken. |
| systemId | Ja | String | Logische app systeem-ID. |
| runId | Ja | String | Logische app-id. |
| operationName | Ja | String | Naam van de bewerking (bijvoorbeeld: actie of trigger). |
| repeatItemScopeName | Ja | String | Herhaal de itemnaam als de actie binnen een `foreach` / `until` lus. |
| repeatItemIndex | Ja | Integer | Of de actie is binnen een `foreach` / `until` lus. Geeft aan dat de itemindex herhaalde. |
| trackingId | Nee | String | Tracerings-ID, de berichten correleren. |
| correlationId | Nee | String | Correlatie-ID, de berichten correleren. |
| clientRequestId | Nee | String | Client kunt invullen om berichten te correleren. |
| eventLevel | Ja |   | Niveau van de gebeurtenis. |
| eventTime | Ja |   | Tijd van de gebeurtenis, in UTC-notatie jjjj-MM-DDTHH:MM:SS.00000Z. |
| recordType | Ja |   | Het type van de record bijhouden. Toegestane waarde is **aangepaste**. |
| record | Ja |   | Aangepaste recordtype. Toegestane indeling is JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Volgschema's voor B2B-protocol

Zie voor informatie over B2B-protocol volgschema's:

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Volgschema’s voor X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [B2B-berichten controleren](logic-apps-monitor-b2b-message.md)
* Meer informatie over [B2B-berichten in Azure Monitor-logboeken bijhouden](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
