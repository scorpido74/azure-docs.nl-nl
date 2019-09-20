---
title: X12 volgschema's voor B2B-berichten - Azure Logic Apps | Microsoft Docs
description: Volgschema's die B2B-berichten in integratieaccounts voor Azure Logic Apps met Enterprise Integration Pack bewaken X12 maken
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845763"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Maken van schema's voor het bijhouden van X12 berichten in integratieaccounts voor Azure Logic Apps

Om u te helpen succes van de monitor, fouten en eigenschappen van berichten voor business-to-business (B2B)-transacties, kunt u deze X12 volgschema's in uw integratie-account:

* X12 volgschema's voor de transactieset
* X12 bevestiging bijhouden schema voor de transactieset
* X12 interchange volgschema 's
* X12 interchange bevestiging schema bijhouden
* X12 functionele groep volgschema 's
* X12 functionele groep bevestiging schema bijhouden

## <a name="x12-transaction-set-tracking-schema"></a>X12 volgschema's voor de transactieset

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | X12 Partnernaam van de afzender het bericht. (Optioneel) |
| receiverPartnerName | String | X12 Partnernaam van de ontvanger het bericht. (Optioneel) |
| senderQualifier | String | De kwalificatie van de partner verzenden. (Verplicht) |
| senderIdentifier | String | Partner-id verzenden. (Verplicht) |
| receiverQualifier | String | De kwalificatie van de partner ontvangt. (Verplicht) |
| receiverIdentifier | String | Partner-id ontvangen. (Verplicht) |
| agreementName | String | Naam van de X12 overeenkomst waarop de berichten opgelost zijn. (Optioneel) |
| direction | Enum | Richting van de berichtenstroom ontvangen of verzenden. (Verplicht) |
| interchangeControlNumber | String | Uitwisselingscontrolenummer. (Optioneel) |
| functionalGroupControlNumber | String | Functionele controlenummer. (Optioneel) |
| transactionSetControlNumber | String | Transactiereekscontrolenummer. (Optioneel) |
| CorrelationMessageId | String | Correlatie bericht-ID. A combination of {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Optioneel) |
| messageType | String | Transactie standaarddocumenttype of ingesteld. (Optioneel) |
| isMessageFailed | Boolean | Of de X12 bericht is mislukt. (Verplicht) |
| isTechnicalAcknowledgmentExpected | Boolean | Of de technische bevestiging is geconfigureerd in de X12 overeenkomst. (Verplicht) |
| isFunctionalAcknowledgmentExpected | Boolean | Of de functionele bevestiging is geconfigureerd in de X12 overeenkomst. (Verplicht) |
| needAk2LoopForValidMessages | Boolean | Of de AK2-lus is vereist voor een geldig bericht. (Verplicht) |
| segmentsCount | Integer | Aantal segmenten in de X12 transactieset. (Optioneel) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 bevestiging bijhouden schema voor de transactieset

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | X12 Partnernaam van de afzender het bericht. (Optioneel) |
| receiverPartnerName | String | X12 Partnernaam van de ontvanger het bericht. (Optioneel) |
| senderQualifier | String | De kwalificatie van de partner verzenden. (Verplicht) |
| senderIdentifier | String | Partner-id verzenden. (Verplicht) |
| receiverQualifier | String | De kwalificatie van de partner ontvangt. (Verplicht) |
| receiverIdentifier | String | Partner-id ontvangen. (Verplicht) |
| agreementName | String | Naam van de X12 overeenkomst waarop de berichten opgelost zijn. (Optioneel) |
| direction | Enum | Richting van de berichtenstroom ontvangen of verzenden. (Verplicht) |
| interchangeControlNumber | String | Uitwisselingscontrolenummer van de functionele bevestiging. Waarde vult alleen voor de verzendkant waar functionele bevestiging is ontvangen voor de berichten die worden verzonden naar partner. (Optioneel) |
| functionalGroupControlNumber | String | Functionele groepscontrolenummer van de functionele bevestiging. Waarde vult alleen voor de verzendkant waar functionele bevestiging is ontvangen voor de berichten die worden verzonden naar partner. (Optioneel) |
| isaSegment | String | Het ISA-segment van het bericht. Waarde vult alleen voor de verzendkant waar functionele bevestiging is ontvangen voor de berichten die worden verzonden naar partner. (Optioneel) |
| gsSegment | String | GS-segment van het bericht. Waarde vult alleen voor de verzendkant waar functionele bevestiging is ontvangen voor de berichten die worden verzonden naar partner. (Optioneel) |
| respondingfunctionalGroupControlNumber | String | Uitwisseling van het controlenummer reageert. (Optioneel) |
| respondingFunctionalGroupId | String | Functionele groep-ID, die in de ontvangstbevestiging wordt toegewezen aan AK101 reageert. (Optioneel) |
| respondingtransactionSetControlNumber | String | Reageert transactiereekscontrolenummer. (Optioneel) |
| respondingTransactionSetId | String | Reageert transactieset-ID, die wordt toegewezen aan AK201 in de bevestiging. (Optioneel) |
| statusCode | Boolean | Bevestiging statuscode voor de transactieset. (Verplicht) |
| segmentsCount | Enum | Bevestiging statuscode. Toegestane waarden zijn **geaccepteerde**, **afgewezen**, en **AcceptedWithErrors**. (Verplicht) |
| Verwerkingsstatusnaam | Enum | De verwerkingsstatus van de bevestiging. Toegestane waarden zijn **ontvangen**, **gegenereerde**, en **verzonden**. (Verplicht) |
| CorrelationMessageId | String | Correlatie bericht-ID. A combination of {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Optioneel) |
| isMessageFailed | Boolean | Of de X12 bericht is mislukt. (Verplicht) |
| ak2Segment | String | Bevestiging voor een transactie die is ingesteld in de functiegroep ontvangen. (Optioneel) |
| ak3Segment | String | Rapporten fouten in een gegevenssegment. (Optioneel) |
| ak5Segment | String | Rapporten of de transactie is ingesteld in het segment AK2 geïdentificeerde wordt geaccepteerd of geweigerd en waarom. (Optioneel) |
||||

## <a name="x12-interchange-tracking-schema"></a>X12 interchange volgschema 's

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | X12 Partnernaam van de afzender het bericht. (Optioneel) |
| receiverPartnerName | String | X12 Partnernaam van de ontvanger het bericht. (Optioneel) |
| senderQualifier | String | De kwalificatie van de partner verzenden. (Verplicht) |
| senderIdentifier | String | Partner-id verzenden. (Verplicht) |
| receiverQualifier | String | De kwalificatie van de partner ontvangt. (Verplicht) |
| receiverIdentifier | String | Partner-id ontvangen. (Verplicht) |
| agreementName | String | Naam van de X12 overeenkomst waarop de berichten opgelost zijn. (Optioneel) |
| direction | Enum | Richting van de berichtenstroom ontvangen of verzenden. (Verplicht) |
| interchangeControlNumber | String | Uitwisselingscontrolenummer. (Optioneel) |
| isaSegment | String | Bericht ISA-segment. (Optioneel) |
| isTechnicalAcknowledgmentExpected | Boolean | Of de technische bevestiging is geconfigureerd in de X12 overeenkomst. (Verplicht) |
| isMessageFailed | Boolean | Of de X12 bericht is mislukt. (Verplicht) |
| isa09 | String | X12 document uitwisseling datum. (Optioneel) |
| isa10 | String | X12 document uitwisseling tijd. (Optioneel) |
| isa11 | String | X12 interchange besturingselement standaarden-id. (Optioneel) |
| isa12 | String | X12 interchange besturingselement versienummer. (Optioneel) |
| isa14 | String | X12 bevestiging wordt gevraagd. (Optioneel) |
| isa15 | String | Indicator voor test- of productieomgeving. (Optioneel) |
| isa16 | String | Scheidingsteken voor gegevenselementen. (Optioneel) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 interchange bevestiging schema bijhouden

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | X12 Partnernaam van de afzender het bericht. (Optioneel) |
| receiverPartnerName | String | X12 Partnernaam van de ontvanger het bericht. (Optioneel) |
| senderQualifier | String | De kwalificatie van de partner verzenden. (Verplicht) |
| senderIdentifier | String | Partner-id verzenden. (Verplicht) |
| receiverQualifier | String | De kwalificatie van de partner ontvangt. (Verplicht) |
| receiverIdentifier | String | Partner-id ontvangen. (Verplicht) |
| agreementName | String | Naam van de X12 overeenkomst waarop de berichten opgelost zijn. (Optioneel) |
| direction | Enum | Richting van de berichtenstroom ontvangen of verzenden. (Verplicht) |
| interchangeControlNumber | String | Uitwisselingscontrolenummer van de technische ontvangstbevestiging die worden ontvangen van partners. (Optioneel) |
| isaSegment | String | Het ISA-segment voor de technische bevestiging dat ontvangen van partners. (Optioneel) |
| respondingInterchangeControlNumber |String | Uitwisselingscontrolenummer voor de technische bevestiging dat ontvangen van partners. (Optioneel) |
| isMessageFailed | Boolean | Of de X12 bericht is mislukt. (Verplicht) |
| statusCode | Enum | Uitwisseling statuscode bevestiging. Toegestane waarden zijn **geaccepteerde**, **afgewezen**, en **AcceptedWithErrors**. (Verplicht) |
| Verwerkingsstatusnaam | Enum | Bevestigingsstatus. Toegestane waarden zijn **ontvangen**, **gegenereerde**, en **verzonden**. (Verplicht) |
| ta102 | String | Uitwisseling datum. (Optioneel) |
| ta103 | String | Uitwisseling tijd. (Optioneel) |
| ta105 | String | Houd er rekening mee code uitwisselbaar. (Optioneel) |
||||

## <a name="x12-functional-group-tracking-schema"></a>X12 functionele groep volgschema 's

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | X12 Partnernaam van de afzender het bericht. (Optioneel) |
| receiverPartnerName | String | X12 Partnernaam van de ontvanger het bericht. (Optioneel) |
| senderQualifier | String | De kwalificatie van de partner verzenden. (Verplicht) |
| senderIdentifier | String | Partner-id verzenden. (Verplicht) |
| receiverQualifier | String | De kwalificatie van de partner ontvangt. (Verplicht) |
| receiverIdentifier | String | Partner-id ontvangen. (Verplicht) |
| agreementName | String | Naam van de X12 overeenkomst waarop de berichten opgelost zijn. (Optioneel) |
| direction | Enum | Richting van de berichtenstroom ontvangen of verzenden. (Verplicht) |
| interchangeControlNumber | String | Uitwisselingscontrolenummer. (Optioneel) |
| functionalGroupControlNumber | String | Functionele controlenummer. (Optioneel) |
| gsSegment | String | GS-berichtsegment. (Optioneel) |
| isTechnicalAcknowledgmentExpected | Boolean | Of de technische bevestiging is geconfigureerd in de X12 overeenkomst. (Verplicht) |
| isFunctionalAcknowledgmentExpected | Boolean | Of de functionele bevestiging is geconfigureerd in de X12 overeenkomst. (Verplicht) |
| isMessageFailed | Boolean | Of de X12 bericht is mislukt. (Verplicht)|
| gs01 | String | Functionele id-code. (Optioneel) |
| gs02 | String | De code van de afzender van de toepassing. (Optioneel) |
| gs03 | String | De code van de ontvanger van de toepassing. (Optioneel) |
| gs04 | String | Functionele groep datum. (Optioneel) |
| gs05 | String | Functionele groep tijd. (Optioneel) |
| gs07 | String | Code die verantwoordelijk zijn Bureau. (Optioneel) |
| gs08 | String | Release-versie/branche-id-code. (Optioneel) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 functionele groep bevestiging schema bijhouden

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | X12 Partnernaam van de afzender het bericht. (Optioneel) |
| receiverPartnerName | String | X12 Partnernaam van de ontvanger het bericht. (Optioneel) |
| senderQualifier | String | De kwalificatie van de partner verzenden. (Verplicht) |
| senderIdentifier | String | Partner-id verzenden. (Verplicht) |
| receiverQualifier | String | De kwalificatie van de partner ontvangt. (Verplicht) |
| receiverIdentifier | String | Partner-id ontvangen. (Verplicht) |
| agreementName | String | Naam van de X12 overeenkomst waarop de berichten opgelost zijn. (Optioneel) |
| direction | Enum | Richting van de berichtenstroom ontvangen of verzenden. (Verplicht) |
| interchangeControlNumber | String | De uitwisselingscontrolenummer gevuld voor de verzendkant wanneer een technische bevestiging is ontvangen van partners. (Optioneel) |
| functionalGroupControlNumber | String | Functionele groepscontrolenummer van de technische acceptatie gevuld voor de verzendkant wanneer een technische bevestiging is ontvangen van partners. (Optioneel) |
| isaSegment | String | Hetzelfde als uitwisseling aantal, maar alleen in bepaalde gevallen ingevuld bepalen. (Optioneel) |
| gsSegment | String | Hetzelfde als functiegroep aantal, maar alleen in bepaalde gevallen ingevuld bepalen. (Optioneel) |
| respondingfunctionalGroupControlNumber | String | Aantal van de oorspronkelijke functiegroep bepalen. (Optioneel) |
| respondingFunctionalGroupId | String | In de functiegroep bevestiging is toegewezen aan AK101-id. (Optioneel) |
| isMessageFailed | Boolean | Of de X12 bericht is mislukt. (Verplicht) |
| statusCode | Enum | Bevestiging statuscode. Toegestane waarden zijn **geaccepteerde**, **afgewezen**, en **AcceptedWithErrors**. (Verplicht) |
| Verwerkingsstatusnaam | Enum | De verwerkingsstatus van de bevestiging. Toegestane waarden zijn **ontvangen**, **gegenereerde**, en **verzonden**. (Verplicht) |
| ak903 | String | Het aantal transactiesets ontvangen. (Optioneel) |
| ak904 | String | Aantal transactiesets geaccepteerd in de geïdentificeerde functiegroep. (Optioneel) |
| ak9Segment | String | Of de functiegroep geïdentificeerd in het segment AK1 wordt geaccepteerd of geweigerd en waarom. (Optioneel) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Volgschema's voor B2B-protocol

Zie voor informatie over B2B-protocol volgschema's:

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Aangepaste volgschema's B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [B2B-berichten controleren](logic-apps-monitor-b2b-message.md).
* Meer informatie over [B2B-berichten in Azure Monitor-logboeken bijhouden](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
