---
title: AS2 tracking-schema's voor B2B-berichten
description: AS2-tracking schema's maken waarmee B2B-berichten in integratie accounts voor Azure Logic Apps met Enterprise Integration Pack worden gecontroleerd
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792805"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Schema's maken voor het bijhouden van AS2-berichten en MDNs in integratie accounts voor Azure Logic Apps

Als hulp bij het bewaken van geslaagde, fout-en bericht eigenschappen voor Business-to-Business (B2B)-trans acties, kunt u deze AS2-tracking schema's gebruiken in uw integratie account:

* AS2-schema voor het bijhouden van berichten
* Tracking schema voor AS2-MDN

## <a name="as2-message-tracking-schema"></a>AS2-schema voor het bijhouden van berichten

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| senderPartnerName | Tekenreeks | De partner naam van de afzender van het AS2-bericht. Beschrijving |
| receiverPartnerName | Tekenreeks | Partner naam van AS2-bericht ontvanger. Beschrijving |
| as2To | Tekenreeks | De naam van de ontvanger van het AS2-bericht van de headers van het AS2-bericht. Ingevuld |
| as2From | Tekenreeks | De naam van de afzender van het AS2-bericht, van de headers van het AS2-bericht. Ingevuld |
| overeenkomst | Tekenreeks | De naam van de AS2-overeenkomst waarop de berichten worden omgezet. Beschrijving |
| richting | Tekenreeks | Richting van de berichten stroom, ontvangen of verzenden. Ingevuld |
| messageId | Tekenreeks | AS2 bericht-ID, van de headers van het AS2-bericht (optioneel) |
| dispositionType |Tekenreeks | Waarde van het type voor het verplaatsen van berichten (MDN). Beschrijving |
| fileName | Tekenreeks | Bestands naam van de header van het AS2-bericht. Beschrijving |
| isMessageFailed |Booleaans | Of het AS2-bericht is mislukt. Ingevuld |
| isMessageSigned | Booleaans | Hiermee wordt aangegeven of het AS2-bericht is ondertekend. Ingevuld |
| isMessageEncrypted | Booleaans | Hiermee wordt aangegeven of het AS2-bericht is versleuteld. Ingevuld |
| isMessageCompressed |Booleaans | Hiermee wordt aangegeven of het AS2-bericht is gecomprimeerd. Ingevuld |
| CorrelationMessageId | Tekenreeks | AS2 bericht-ID voor het correleren van berichten met MDNs. Beschrijving |
| incomingHeaders |Woorden lijst met JToken | Details van de inkomende AS2-bericht header. Beschrijving |
| outgoingHeaders |Woorden lijst met JToken | Details van de uitgaande AS2-bericht header. Beschrijving |
| isNrrEnabled | Booleaans | Gebruik de standaard waarde als de waarde niet bekend is. Ingevuld |
| isMdnExpected | Booleaans | Gebruik de standaard waarde als de waarde niet bekend is. Ingevuld |
| mdnType | Enum | Toegestane waarden zijn **NotConfigured**, **Sync**en **async**. Ingevuld |
||||

## <a name="as2-mdn-tracking-schema"></a>Tracking schema voor AS2-MDN

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| senderPartnerName | Tekenreeks | De partner naam van de afzender van het AS2-bericht. Beschrijving |
| receiverPartnerName | Tekenreeks | Partner naam van AS2-bericht ontvanger. Beschrijving |
| as2To | Tekenreeks | De naam van de partner die het AS2-bericht ontvangt. Ingevuld |
| as2From | Tekenreeks | De naam van de partner die het AS2-bericht verzendt. Ingevuld |
| overeenkomst | Tekenreeks | De naam van de AS2-overeenkomst waarop de berichten worden omgezet. Beschrijving |
| richting |Tekenreeks | Richting van de berichten stroom, ontvangen of verzenden. Ingevuld |
| messageId | Tekenreeks | AS2 bericht-ID. Beschrijving |
| originalMessageId |Tekenreeks | AS2 oorspronkelijke bericht-ID. Beschrijving |
| dispositionType | Tekenreeks | Waarde van MDN-indelings type. Beschrijving |
| isMessageFailed |Booleaans | Of het AS2-bericht is mislukt. Ingevuld |
| isMessageSigned |Booleaans | Hiermee wordt aangegeven of het AS2-bericht is ondertekend. Ingevuld |
| isNrrEnabled | Booleaans | Gebruik de standaard waarde als de waarde niet bekend is. Ingevuld |
| Status code | Enum | Toegestane waarden zijn **geaccepteerd**, **afgewezen**en **AcceptedWithErrors**. Ingevuld |
| micVerificationStatus | Enum | Toegestane waarden zijn **niet van toepassing**, **geslaagd**en **mislukt**. Ingevuld |
| CorrelationMessageId | Tekenreeks | Correlatie-ID. De oorspronkelijke bericht-ID (de bericht-ID van het bericht waarvoor MDN is geconfigureerd). Beschrijving |
| incomingHeaders | Woorden lijst met JToken | Geeft de details van de koptekst van het binnenkomende bericht aan. Beschrijving |
| outgoingHeaders |Woorden lijst met JToken | Geeft details van de koptekst van uitgaande berichten aan. Beschrijving |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schema's voor het bijhouden van B2B-protocollen

Voor informatie over schema's voor het bijhouden van B2B-protocollen raadpleegt u:

* [Volgschema’s voor X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Aangepaste B2B-tracking schema's](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [controleren van B2B-berichten](logic-apps-monitor-b2b-message.md)
* Meer informatie over het [bijhouden van B2B-berichten in azure monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)