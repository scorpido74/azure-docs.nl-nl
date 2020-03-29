---
title: Aangepaste trackingschema's voor B2B-berichten
description: Aangepaste trackingschema's maken om B2B-berichten in Azure Logic Apps te controleren
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903063"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Aangepaste trackingschema's maken die end-to-end-werkstromen in Azure Logic A bewaken

Azure Logic Apps heeft ingebouwde tracking die u inschakelen voor delen van uw werkstroom. U echter aangepaste tracking instellen die gebeurtenissen van het begin tot het einde van werkstromen registreert, bijvoorbeeld werkstromen met een logische app, BizTalk Server, SQL Server of een andere laag. In dit artikel vindt u aangepaste code die u gebruiken in de lagen buiten uw logische app.

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
         "repeatItemIndex": ,
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
         "record": {}
      }
   ]
}
```

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| Sourcetype | Ja | Tekenreeks | Type van de run bron met `Microsoft.Logic/workflows`deze toegestane waarden: ,`custom` |
| source | Ja | Tekenreeks of JToken | Als het brontype is, `Microsoft.Logic/workflows`moet de broninformatie dit schema volgen. Als het brontype is, `custom`is het schema een JToken. |
| systemId systemId | Ja | Tekenreeks | Logische app-systeem-id |
| runId | Ja | Tekenreeks | Id voor logische app-run |
| operationName | Ja | Tekenreeks | Naam van de bewerking, bijvoorbeeld actie of trigger |
| repeatItemScopeName | Ja | Tekenreeks | Itemnaam herhalen als de `foreach`actie `until` zich binnen een of lus bevindt |
| repeatItemIndex | Ja | Geheel getal | Geeft aan dat de `foreach` `until` actie zich binnen een of lus bevindt en het herhaalde itemindexnummer is. |
| trackingId | Nee | Tekenreeks | Tracking ID om de berichten te correleren |
| correlationId | Nee | Tekenreeks | Correlatie-ID om de berichten te correleren |
| clientRequestId | Nee | Tekenreeks | Client kan deze eigenschap vullen om berichten te correleren |
| eventLevel | Ja | Tekenreeks | Niveau van het evenement |
| eventTime | Ja | DateTime | Tijd van de gebeurtenis in *UTC-formaat: YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType | Ja | Tekenreeks | Type van de track record met deze toegestane waarde alleen:`custom` |
| opnemen | Ja | JToken JToken | Aangepast recordtype met alleen JToken-indeling |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protocoltrackingschema's

Zie voor informatie over b2b-protocoltrackingschema's:

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Volgschema’s voor X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het bewaken van B2B-berichten met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)