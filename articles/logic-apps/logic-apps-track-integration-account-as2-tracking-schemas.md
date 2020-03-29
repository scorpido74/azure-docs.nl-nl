---
title: AS2-trackingschema's voor B2B-berichten
description: Trackingschema's maken om AS2-berichten in Azure Logic Apps te controleren
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906969"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Schema's maken voor het bijhouden van AS2-berichten in Azure Logic Apps

Om u te helpen succes, fouten en berichteigenschappen voor B2B-transacties (business-to-business) te controleren, u deze AS2-trackingschema's gebruiken in uw integratieaccount:

* AS2-trackingschema voor berichten
* AS2 Message Disposition Notification (MDN) tracking schema

## <a name="as2-message-tracking-schema"></a>AS2-trackingschema voor berichten

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
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | De partnernaam van as2-berichtafzender |
| receiverPartnerName | Nee | Tekenreeks | De partnernaam van as2-berichtontvanger |
| as2To | Ja | Tekenreeks | De naam van de AS2-berichtontvanger uit de kopteksten van het AS2-bericht |
| as2Van | Ja | Tekenreeks | De naam van as2-afzender uit de kopteksten van het AS2-bericht |
| overeenkomstNaam | Nee | Tekenreeks | Naam van de AS2-overeenkomst waarop de berichten zijn opgelost |
| richting | Ja | Tekenreeks | Richting van de berichtstroom, `receive` die is of`send` |
| messageId | Nee | Tekenreeks | AS2-bericht-id van de kopteksten van het AS2-bericht |
| dispositieType | Nee | Tekenreeks | Typewaarde van de Dispositie (Message Disposition Notification) |
| fileName | Nee | Tekenreeks | Bestandsnaam van de koptekst van het AS2-bericht |
| isMessageFailed | Ja | Booleaans | Of het AS2-bericht is mislukt |
| isMessageSigned | Ja | Booleaans | Of het AS2-bericht is ondertekend |
| isMessageEncrypted | Ja | Booleaans | Of het AS2-bericht is versleuteld |
| isMessageGecomprimeerd | Ja | Booleaans | Of het AS2-bericht is gecomprimeerd |
| correlatieMessageId | Nee | Tekenreeks | AS2-bericht-id, om berichten te correleren met MDN's |
| inkomende headers | Nee | Woordenboek van JToken | Inkomende AS2-berichtkoptekstdetails |
| uitgaande headers | Nee | Woordenboek van JToken | Uitgaande AS2-berichtkopgegevens |
| isNrrEnabled | Ja | Booleaans | Standaardwaarde gebruiken als de waarde niet bekend is |
| isMdnVerwacht | Ja | Booleaans | De standaardwaarde gebruiken als de waarde niet bekend is |
| mdnType | Ja | Enum | Toegestane waarden: `NotConfigured` `Sync`, , en`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN-trackingschema

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

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | De partnernaam van as2-berichtafzender |
| receiverPartnerName | Nee | Tekenreeks | De partnernaam van as2-berichtontvanger |
| as2To | Ja | Tekenreeks | Partnernaam die het AS2-bericht ontvangt |
| as2Van | Ja | Tekenreeks | Partnernaam die het AS2-bericht verzendt |
| overeenkomstNaam | Nee | Tekenreeks | Naam van de AS2-overeenkomst waarop de berichten zijn opgelost |
| richting | Ja | Tekenreeks | Richting van de berichtstroom, `receive` die is of`send` |
| messageId | Nee | Tekenreeks | AS2-bericht-id |
| origineleMessageId | Nee | Tekenreeks | AS2 oorspronkelijke bericht-id |
| dispositieType | Nee | Tekenreeks | Waarde van mdn-dispositie |
| isMessageFailed | Ja | Booleaans | Of het AS2-bericht is mislukt |
| isMessageSigned | Ja | Booleaans | Of het AS2-bericht is ondertekend |
| isNrrEnabled | Ja | Booleaans | De standaardwaarde gebruiken als de waarde niet bekend is |
| statusCode | Ja | Enum | Toegestane waarden: `Accepted` `Rejected`, , en`AcceptedWithErrors` |
| micVerificationStatus | Ja | Enum | Toegestane waarden:`NotApplicable` `Succeeded`, , en`Failed` |
| correlatieMessageId | Nee | Tekenreeks | Correlatie-id, de id voor het oorspronkelijke bericht waarop de MDN is geconfigureerd |
| inkomende headers | Nee | Woordenboek van JToken | Details van de inkomende berichtkoptekst |
| uitgaande headers | Nee | Woordenboek van JToken | Details van de uitgaande berichtkoptekst |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protocoltrackingschema's

Zie voor informatie over b2b-protocoltrackingschema's:

* [Volgschema’s voor X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B aangepaste trackingschema's](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [B2B-berichten bewaken met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)