---
title: Aangepaste tracking schema's voor B2B-berichten
description: Aangepaste tracking schema's maken voor het controleren van B2B-berichten in integratie accounts voor Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792796"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Aangepaste tracking schema's maken waarmee end-to-end werk stromen in Azure Logic Apps worden bewaakt

Er is een ingebouwde tracering die u kunt inschakelen voor verschillende onderdelen van uw Business-to-Business-werk stroom, zoals het volgen van AS2-of X12-berichten. Wanneer u werk stromen maakt die een logische app, BizTalk Server, SQL Server of een andere laag bevat, kunt u aangepaste tracking inschakelen waarmee gebeurtenissen van het begin tot het einde van uw werk stroom worden vastgelegd. 

Dit artikel bevat aangepaste code die u kunt gebruiken in de lagen buiten uw logische app. 

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

| Eigenschap | Verplicht | Type | Beschrijving |
| --- | --- | --- | --- |
| Type | Ja |   | Het type van de uitvoerings bron. Toegestane waarden zijn **micro soft. Logic/werk stromen** en **aangepast**. |
| source | Ja |   | Als het bron type **micro soft. Logic/workflows**is, moeten de bron gegevens dit schema volgen. Als het bron type **aangepast**is, is het schema een JToken. |
| systemId | Ja | Tekenreeks | ID van Logic-app-systeem. |
| RunId | Ja | Tekenreeks | Run-ID van logische app. |
| operationName | Ja | Tekenreeks | De naam van de bewerking (bijvoorbeeld actie of trigger). |
| repeatItemScopeName | Ja | Tekenreeks | De naam van het item herhalen als de actie zich in een `foreach`/`until` lus bevindt. |
| repeatItemIndex | Ja | Geheel getal | Hiermee wordt aangegeven of de actie zich in een `foreach`/`until` lus bevindt. Hiermee wordt de herhaalde item index aangegeven. |
| trackingId | Nee | Tekenreeks | Tracerings-ID voor het correleren van de berichten. |
| correlationId | Nee | Tekenreeks | Correlatie-ID voor het correleren van de berichten. |
| clientRequestId | Nee | Tekenreeks | De client kan deze vullen voor het correleren van berichten. |
| eventLevel | Ja |   | Het niveau van de gebeurtenis. |
| eventTime | Ja |   | Tijd van de gebeurtenis, in UTC-notatie JJJJ-MM-DDTUU: MM: SS. 00000Z. |
| Record type | Ja |   | Het type van de track record. De toegestane waarde is **aangepast**. |
| teken | Ja |   | Aangepast record type. De toegestane indeling is JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schema's voor het bijhouden van B2B-protocollen

Voor informatie over schema's voor het bijhouden van B2B-protocollen raadpleegt u:

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Volgschema’s voor X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [controleren van B2B-berichten](logic-apps-monitor-b2b-message.md)
* Meer informatie over het [bijhouden van B2B-berichten in azure monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
