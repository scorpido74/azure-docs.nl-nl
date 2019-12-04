---
title: X12 tracking-schema's voor B2B-berichten
description: X12-tracking schema's maken waarmee B2B-berichten in integratie accounts voor Azure Logic Apps met Enterprise Integration Pack worden gecontroleerd
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 6f2356600f5b6a637da731c650b26d968092e2f6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791722"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Schema's maken voor het bijhouden van X12-berichten in integratie accounts voor Azure Logic Apps

Als hulp bij het bewaken van geslaagde, fout-en bericht eigenschappen voor Business-to-Business (B2B)-trans acties, kunt u deze X12-tracking schema's gebruiken in uw integratie account:

* Tracking schema voor X12-transactie sets
* Tracking schema voor het bevestigen van X12-transactie sets
* X12 Interchange tracking-schema
* Tracking-schema voor X12 Interchange-bevestiging
* Tracking schema van X12-functionele groep
* Tracking schema voor het bevestigen van X12-functie groepen

## <a name="x12-transaction-set-tracking-schema"></a>Tracking schema voor X12-transactie sets

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| senderPartnerName | Tekenreeks | De partner naam van de afzender van het X12-bericht. Beschrijving |
| receiverPartnerName | Tekenreeks | Partner naam van X12-bericht ontvanger. Beschrijving |
| senderQualifier | Tekenreeks | Partner kwalificatie verzenden. Ingevuld |
| senderIdentifier | Tekenreeks | Verzend de partner-id. Ingevuld |
| receiverQualifier | Tekenreeks | Partner kwalificatie ontvangen. Ingevuld |
| receiverIdentifier | Tekenreeks | De partner-id ontvangen. Ingevuld |
| overeenkomst | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet. Beschrijving |
| richting | Enum | Richting van de berichten stroom, ontvangen of verzenden. Ingevuld |
| interchangeControlNumber | Tekenreeks | Uitwisselings controle nummer. Beschrijving |
| functionalGroupControlNumber | Tekenreeks | Het functionele controle nummer. Beschrijving |
| transactionSetControlNumber | Tekenreeks | Controle nummer van de transactie set. Beschrijving |
| CorrelationMessageId | Tekenreeks | Correlatie bericht-ID. Een combi natie van {licentieovereenkomst} {*GroupControlNumber*} {TransactionSetControlNumber}. Beschrijving |
| Message type | Tekenreeks | Transactieset of document type. Beschrijving |
| isMessageFailed | Booleaans | Of het X12-bericht is mislukt. Ingevuld |
| isTechnicalAcknowledgmentExpected | Booleaans | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst. Ingevuld |
| isFunctionalAcknowledgmentExpected | Booleaans | Of de functionaliteits bevestiging is geconfigureerd in de X12-overeenkomst. Ingevuld |
| needAk2LoopForValidMessages | Booleaans | Hiermee wordt aangegeven of de AK2-lus vereist is voor een geldig bericht. Ingevuld |
| segmentsCount | Geheel getal | Aantal segmenten in de X12-transactieset. Beschrijving |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Tracking schema voor het bevestigen van X12-transactie sets

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| senderPartnerName | Tekenreeks | De partner naam van de afzender van het X12-bericht. Beschrijving |
| receiverPartnerName | Tekenreeks | Partner naam van X12-bericht ontvanger. Beschrijving |
| senderQualifier | Tekenreeks | Partner kwalificatie verzenden. Ingevuld |
| senderIdentifier | Tekenreeks | Verzend de partner-id. Ingevuld |
| receiverQualifier | Tekenreeks | Partner kwalificatie ontvangen. Ingevuld |
| receiverIdentifier | Tekenreeks | De partner-id ontvangen. Ingevuld |
| overeenkomst | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet. Beschrijving |
| richting | Enum | Richting van de berichten stroom, ontvangen of verzenden. Ingevuld |
| interchangeControlNumber | Tekenreeks | Uitwisselings controle nummer van de functionele bevestiging. De waarde wordt alleen gevuld voor de verzend kant waar de functie bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden. Beschrijving |
| functionalGroupControlNumber | Tekenreeks | Het controle nummer van de functionele groep van de functionele bevestiging. De waarde wordt alleen gevuld voor de verzend kant waar de functie bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden. Beschrijving |
| isaSegment | Tekenreeks | Het ISA-segment van het bericht. De waarde wordt alleen gevuld voor de verzend kant waar de functie bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden. Beschrijving |
| gsSegment | Tekenreeks | GS-segment van het bericht. De waarde wordt alleen gevuld voor de verzend kant waar de functie bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden. Beschrijving |
| respondingfunctionalGroupControlNumber | Tekenreeks | Het uitwisselings controle nummer reageert. Beschrijving |
| respondingFunctionalGroupId | Tekenreeks | De functionele groep-ID die wordt toegewezen aan AK101 in de bevestiging. Beschrijving |
| respondingtransactionSetControlNumber | Tekenreeks | Er wordt een controle nummer voor de transactie set antwoord. Beschrijving |
| respondingTransactionSetId | Tekenreeks | De Trans Action Set-ID die wordt toegewezen aan AK201 in de bevestiging. Beschrijving |
| Status code | Booleaans | Status code voor bevestiging van transactie set. Ingevuld |
| segmentsCount | Enum | Status code van bevestiging. Toegestane waarden zijn **geaccepteerd**, **afgewezen**en **AcceptedWithErrors**. Ingevuld |
| processingStatus | Enum | Verwerkings status van de bevestiging. Toegestane waarden worden **ontvangen**, **gegenereerd**en **verzonden**. Ingevuld |
| CorrelationMessageId | Tekenreeks | Correlatie bericht-ID. Een combi natie van {licentieovereenkomst} {*GroupControlNumber*} {TransactionSetControlNumber}. Beschrijving |
| isMessageFailed | Booleaans | Of het X12-bericht is mislukt. Ingevuld |
| ak2Segment | Tekenreeks | Bevestiging voor een transactie groep binnen de ontvangen functionele groep. Beschrijving |
| ak3Segment | Tekenreeks | Hiermee worden fouten in een gegevens segment gerapporteerd. Beschrijving |
| ak5Segment | Tekenreeks | Hiermee wordt aangegeven of de trans actie die in het AK2-segment is geïdentificeerd, wordt geaccepteerd of afgewezen en waarom. Beschrijving |
||||

## <a name="x12-interchange-tracking-schema"></a>X12 Interchange tracking-schema

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| senderPartnerName | Tekenreeks | De partner naam van de afzender van het X12-bericht. Beschrijving |
| receiverPartnerName | Tekenreeks | Partner naam van X12-bericht ontvanger. Beschrijving |
| senderQualifier | Tekenreeks | Partner kwalificatie verzenden. Ingevuld |
| senderIdentifier | Tekenreeks | Verzend de partner-id. Ingevuld |
| receiverQualifier | Tekenreeks | Partner kwalificatie ontvangen. Ingevuld |
| receiverIdentifier | Tekenreeks | De partner-id ontvangen. Ingevuld |
| overeenkomst | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet. Beschrijving |
| richting | Enum | Richting van de berichten stroom, ontvangen of verzenden. Ingevuld |
| interchangeControlNumber | Tekenreeks | Uitwisselings controle nummer. Beschrijving |
| isaSegment | Tekenreeks | Message ISA-segment. Beschrijving |
| isTechnicalAcknowledgmentExpected | Booleaans | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst. Ingevuld |
| isMessageFailed | Booleaans | Of het X12-bericht is mislukt. Ingevuld |
| isa09 | Tekenreeks | X12-document uitwisselings datum. Beschrijving |
| isa10 | Tekenreeks | X12-document uitwisselings tijd. Beschrijving |
| isa11 | Tekenreeks | X12 Interchange Control-standaard-id. Beschrijving |
| isa12 | Tekenreeks | Versie nummer van X12 Interchange Control. Beschrijving |
| isa14 | Tekenreeks | Bevestiging van X12 is aangevraagd. Beschrijving |
| isa15 | Tekenreeks | Indicator voor test of productie. Beschrijving |
| isa16 | Tekenreeks | Element scheidings teken. Beschrijving |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Tracking-schema voor X12 Interchange-bevestiging

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| senderPartnerName | Tekenreeks | De partner naam van de afzender van het X12-bericht. Beschrijving |
| receiverPartnerName | Tekenreeks | Partner naam van X12-bericht ontvanger. Beschrijving |
| senderQualifier | Tekenreeks | Partner kwalificatie verzenden. Ingevuld |
| senderIdentifier | Tekenreeks | Verzend de partner-id. Ingevuld |
| receiverQualifier | Tekenreeks | Partner kwalificatie ontvangen. Ingevuld |
| receiverIdentifier | Tekenreeks | De partner-id ontvangen. Ingevuld |
| overeenkomst | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet. Beschrijving |
| richting | Enum | Richting van de berichten stroom, ontvangen of verzenden. Ingevuld |
| interchangeControlNumber | Tekenreeks | Uitwisselings controle nummer van de technische bevestiging die van partners wordt ontvangen. Beschrijving |
| isaSegment | Tekenreeks | ISA-segment voor de technische bevestiging die van partners wordt ontvangen. Beschrijving |
| respondingInterchangeControlNumber |Tekenreeks | Het uitwisselings controle nummer voor de technische bevestiging die van partners wordt ontvangen. Beschrijving |
| isMessageFailed | Booleaans | Of het X12-bericht is mislukt. Ingevuld |
| Status code | Enum | Status code van uitwisselings bevestiging. Toegestane waarden zijn **geaccepteerd**, **afgewezen**en **AcceptedWithErrors**. Ingevuld |
| processingStatus | Enum | Bevestigings status. Toegestane waarden worden **ontvangen**, **gegenereerd**en **verzonden**. Ingevuld |
| ta102 | Tekenreeks | Interchange date. Beschrijving |
| ta103 | Tekenreeks | Uitwisselings tijd. Beschrijving |
| ta105 | Tekenreeks | Code voor uitwisselings notities. Beschrijving |
||||

## <a name="x12-functional-group-tracking-schema"></a>Tracking schema van X12-functionele groep

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| senderPartnerName | Tekenreeks | De partner naam van de afzender van het X12-bericht. Beschrijving |
| receiverPartnerName | Tekenreeks | Partner naam van X12-bericht ontvanger. Beschrijving |
| senderQualifier | Tekenreeks | Partner kwalificatie verzenden. Ingevuld |
| senderIdentifier | Tekenreeks | Verzend de partner-id. Ingevuld |
| receiverQualifier | Tekenreeks | Partner kwalificatie ontvangen. Ingevuld |
| receiverIdentifier | Tekenreeks | De partner-id ontvangen. Ingevuld |
| overeenkomst | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet. Beschrijving |
| richting | Enum | Richting van de berichten stroom, ontvangen of verzenden. Ingevuld |
| interchangeControlNumber | Tekenreeks | Uitwisselings controle nummer. Beschrijving |
| functionalGroupControlNumber | Tekenreeks | Het functionele controle nummer. Beschrijving |
| gsSegment | Tekenreeks | Bericht GS-segment. Beschrijving |
| isTechnicalAcknowledgmentExpected | Booleaans | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst. Ingevuld |
| isFunctionalAcknowledgmentExpected | Booleaans | Of de functionaliteits bevestiging is geconfigureerd in de X12-overeenkomst. Ingevuld |
| isMessageFailed | Booleaans | Of het X12-bericht is mislukt. Ingevuld|
| gs01 | Tekenreeks | Functionele id-code. Beschrijving |
| gs02 | Tekenreeks | De code van de afzender van de toepassing. Beschrijving |
| gs03 | Tekenreeks | De code van de ontvanger van de toepassing. Beschrijving |
| gs04 | Tekenreeks | Datum van functionele groep. Beschrijving |
| gs05 | Tekenreeks | Tijd van functionele groep. Beschrijving |
| gs07 | Tekenreeks | Verantwoordelijke instantie code. Beschrijving |
| gs08 | Tekenreeks | Code van versie/release/branche-id. Beschrijving |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Tracking schema voor het bevestigen van X12-functie groepen

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| senderPartnerName | Tekenreeks | De partner naam van de afzender van het X12-bericht. Beschrijving |
| receiverPartnerName | Tekenreeks | Partner naam van X12-bericht ontvanger. Beschrijving |
| senderQualifier | Tekenreeks | Partner kwalificatie verzenden. Ingevuld |
| senderIdentifier | Tekenreeks | Verzend de partner-id. Ingevuld |
| receiverQualifier | Tekenreeks | Partner kwalificatie ontvangen. Ingevuld |
| receiverIdentifier | Tekenreeks | De partner-id ontvangen. Ingevuld |
| overeenkomst | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet. Beschrijving |
| richting | Enum | Richting van de berichten stroom, ontvangen of verzenden. Ingevuld |
| interchangeControlNumber | Tekenreeks | Uitwisselings controle nummer, dat voor de verzend zijde gevuld wanneer een technische bevestiging van partners wordt ontvangen. Beschrijving |
| functionalGroupControlNumber | Tekenreeks | Het controle nummer van de functionele groep van de technische bevestiging, dat voor de verzend kant gevuld wordt wanneer er een technische bevestiging van partners wordt ontvangen. Beschrijving |
| isaSegment | Tekenreeks | Hetzelfde als uitwisselings controle nummer, maar alleen in bepaalde gevallen ingevuld. Beschrijving |
| gsSegment | Tekenreeks | Hetzelfde als het beheer nummer van de functionele groep, maar alleen in bepaalde gevallen ingevuld. Beschrijving |
| respondingfunctionalGroupControlNumber | Tekenreeks | Controle nummer van de oorspronkelijke functionele groep. Beschrijving |
| respondingFunctionalGroupId | Tekenreeks | Verwijst naar AK101 in de groeps-ID van de bevestigings functie. Beschrijving |
| isMessageFailed | Booleaans | Of het X12-bericht is mislukt. Ingevuld |
| Status code | Enum | Status code van bevestiging. Toegestane waarden zijn **geaccepteerd**, **afgewezen**en **AcceptedWithErrors**. Ingevuld |
| processingStatus | Enum | Verwerkings status van de bevestiging. Toegestane waarden worden **ontvangen**, **gegenereerd**en **verzonden**. Ingevuld |
| ak903 | Tekenreeks | Aantal ontvangen transactie sets. Beschrijving |
| ak904 | Tekenreeks | Aantal transactie sets dat is geaccepteerd in de geïdentificeerde functionele groep. Beschrijving |
| ak9Segment | Tekenreeks | Of de functionele groep die in het AK1-segment is geïdentificeerd, wordt geaccepteerd of afgewezen en waarom. Beschrijving |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Schema's voor het bijhouden van B2B-protocollen

Voor informatie over schema's voor het bijhouden van B2B-protocollen raadpleegt u:

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Aangepaste B2B-tracking schema's](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [bewaken van B2B-berichten](logic-apps-monitor-b2b-message.md).
* Meer informatie over het [bijhouden van B2B-berichten in azure monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
