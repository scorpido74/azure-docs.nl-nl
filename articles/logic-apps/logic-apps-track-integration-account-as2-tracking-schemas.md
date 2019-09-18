---
title: Volgschema's voor AS2 voor B2B-berichten - Azure Logic Apps | Microsoft Docs
description: Volgschema's voor AS2 die B2B-berichten in integratieaccounts voor Azure Logic Apps met Enterprise Integration Pack bewaken maken
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845780"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Schema's voor het bijhouden van het AS2-berichten en MDN's integratieaccounts voor Azure Logic Apps maken

Om te controleren van geslaagd, fouten en eigenschappen van berichten voor business-to-business (B2B)-transacties, kunt u deze volgschema's voor AS2 in uw integratie-account:

* Volgschema voor AS2-bericht
* Volgschema voor AS2 MDN

## <a name="as2-message-tracking-schema"></a>Volgschema voor AS2-bericht

```json
{
   "agreementProperties": {  
      "senderPartnerName": "",  
      "receiverPartnerName": "",  
      "as2To": "",  
      "as2From": "",  
      "agreementName": ""  
   },  
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Naam van de partner van afzender AS2-bericht. (Optioneel) |
| receiverPartnerName | String | Naam van de partner van Ontvanger AS2-bericht. (Optioneel) |
| as2To | String | AS2-bericht ontvanger naam van de headers van het AS2-bericht. (Verplicht) |
| as2From | String | Naam van de afzender AS2-bericht, van de headers van het AS2-bericht. (Verplicht) |
| agreementName | String | De naam van de AS2-overeenkomst waarop de berichten opgelost zijn. (Optioneel) |
| direction | String | Richting van de berichtenstroom ontvangen of verzenden. (Verplicht) |
| messageId | String | AS2-bericht-ID, uit de headers van de AS2-bericht (optioneel) |
| dispositionType |String | Waarde voor brontype Message Disposition-Notification (MDN) toestand. (Optioneel) |
| fileName | String | Bestandsnaam van de koptekst van het AS2-bericht. (Optioneel) |
| isMessageFailed |Boolean | Of het AS2-bericht is mislukt. (Verplicht) |
| isMessageSigned | Boolean | Of het AS2-bericht is ondertekend. (Verplicht) |
| isMessageEncrypted | Boolean | Of het AS2-bericht is versleuteld. (Verplicht) |
| isMessageCompressed |Boolean | Of het AS2-bericht is gecomprimeerd. (Verplicht) |
| correlationMessageId | String | AS2-bericht-ID, berichten correleren met MDN's. (Optioneel) |
| incomingHeaders |Woordenlijst met JToken | Binnenkomende AS2-bericht-header details. (Optioneel) |
| outgoingHeaders |Woordenlijst met JToken | Uitgaande AS2-bericht-header details. (Optioneel) |
| isNrrEnabled | Boolean | Gebruik de standaardwaarde als de waarde is niet bekend. (Verplicht) |
| isMdnExpected | Boolean | Gebruik de standaardwaarde als de waarde is niet bekend. (Verplicht) |
| mdnType | Enum | Toegestane waarden zijn **NotConfigured**, **Sync**, en **Async**. (Verplicht) |
||||

## <a name="as2-mdn-tracking-schema"></a>Volgschema voor AS2 MDN

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Naam van de partner van afzender AS2-bericht. (Optioneel) |
| receiverPartnerName | String | Naam van de partner van Ontvanger AS2-bericht. (Optioneel) |
| as2To | String | Partnernaam die het AS2-bericht ontvangt. (Verplicht) |
| as2From | String | Partnernaam die het AS2-bericht verzendt. (Verplicht) |
| agreementName | String | De naam van de AS2-overeenkomst waarop de berichten opgelost zijn. (Optioneel) |
| direction |String | Richting van de berichtenstroom ontvangen of verzenden. (Verplicht) |
| messageId | String | AS2-bericht-ID. (Optioneel) |
| originalMessageId |String | AS2 oorspronkelijke bericht-ID. (Optioneel) |
| dispositionType | String | MDN toestand typewaarde. (Optioneel) |
| isMessageFailed |Boolean | Of het AS2-bericht is mislukt. (Verplicht) |
| isMessageSigned |Boolean | Of het AS2-bericht is ondertekend. (Verplicht) |
| isNrrEnabled | Boolean | Gebruik de standaardwaarde als de waarde is niet bekend. (Verplicht) |
| statusCode | Enum | Toegestane waarden zijn **geaccepteerde**, **afgewezen**, en **AcceptedWithErrors**. (Verplicht) |
| micVerificationStatus | Enum | Toegestane waarden zijn **niet van toepassing**, **geslaagd**, en **mislukt**. (Verplicht) |
| correlationMessageId | String | Correlatie-ID. De oorspronkelijke ID mailberichten (de bericht-ID van het bericht waarvoor MDN is geconfigureerd). (Optioneel) |
| incomingHeaders | Woordenlijst met JToken | Geeft aan dat binnenkomende berichtgegevens-header. (Optioneel) |
| outgoingHeaders |Woordenlijst met JToken | Geeft aan uitgaande berichtgegevens-header. (Optioneel) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Volgschema's voor B2B-protocol

Zie voor informatie over B2B-protocol volgschema's:

* [Volgschema’s voor X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Aangepaste volgschema's B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [B2B-berichten controleren](logic-apps-monitor-b2b-message.md)
* Meer informatie over [B2B-berichten in Azure Monitor-logboeken bijhouden](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
