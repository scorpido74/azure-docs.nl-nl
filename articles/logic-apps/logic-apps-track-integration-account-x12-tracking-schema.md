---
title: X12 tracking-schema's voor B2B-berichten
description: Tracking schema's maken voor het bewaken van X12-berichten voor Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905298"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Schema's maken voor het bijhouden van X12-berichten in Azure Logic Apps

Als hulp bij het bewaken van geslaagde, fout-en bericht eigenschappen voor Business-to-Business (B2B)-trans acties, kunt u deze X12-tracking schema's gebruiken in uw integratie account:

* Tracking schema voor X12-transactie sets
* Tracking schema voor bevestiging van X12-transactie sets
* X12 Interchange tracking-schema
* Tracking schema voor X12 uitwisselings bevestiging
* Tracking schema van X12-functionele groep
* Tracking schema voor bevestiging van X12-functie groep

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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Eigenschap | Vereist | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Tekenreeks | Partner naam van de afzender van het X12-bericht |
| receiverPartnerName | No | Tekenreeks | Partner naam van X12-bericht ontvanger |
| senderQualifier | Yes | Tekenreeks | Partner kwalificatie verzenden |
| senderIdentifier | Yes | Tekenreeks | Partner-id verzenden |
| receiverQualifier | Yes | Tekenreeks | Partner kwalificatie ontvangen |
| receiverIdentifier | Yes | Tekenreeks | Partner-id ontvangen |
| overeenkomst | No | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet |
| richting | Yes | Enum | Richting van de berichten stroom, ofwel `receive` of`send` |
| interchangeControlNumber | No | Tekenreeks | Uitwisselings controle nummer |
| functionalGroupControlNumber | No | Tekenreeks | Functioneel controle nummer |
| transactionSetControlNumber | No | Tekenreeks | Controle nummer van transactie set |
| CorrelationMessageId | No | Tekenreeks | ID van correlatie bericht, een combi natie van {*GroupControlNumber*} {TransactionSetControlNumber} |
| Message type | No | Tekenreeks | Transactieset of document type |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| isTechnicalAcknowledgmentExpected | Ja | Booleaans | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst |
| isFunctionalAcknowledgmentExpected | Ja | Booleaans | Of de functie bevestiging is geconfigureerd in de X12-overeenkomst |
| needAk2LoopForValidMessages | Ja | Booleaans | Of de AK2-lus vereist is voor een geldig bericht |
| segmentsCount | No | Geheel getal | Aantal segmenten in de X12-transactieset |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Tracking schema voor bevestiging van X12-transactie sets

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

| Eigenschap | Vereist | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Tekenreeks | Partner naam van de afzender van het X12-bericht |
| receiverPartnerName | No | Tekenreeks | Partner naam van X12-bericht ontvanger |
| senderQualifier | Yes | Tekenreeks | Partner kwalificatie verzenden |
| senderIdentifier | Yes | Tekenreeks | Partner-id verzenden |
| receiverQualifier | Yes | Tekenreeks | Partner kwalificatie ontvangen |
| receiverIdentifier | Yes | Tekenreeks | Partner-id ontvangen |
| overeenkomst | No | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet |
| richting | Yes | Enum | Richting van de berichten stroom, ofwel `receive` of`send` |
| interchangeControlNumber | No | Tekenreeks | Uitwisselings controle nummer van de functie bevestiging. De waarde wordt alleen ingevuld voor de verzend kant waar de functie bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden. |
| functionalGroupControlNumber | No | Tekenreeks | Controle nummer van functionele groep van de functie bevestiging. De waarde wordt alleen ingevuld voor de verzend kant waar de functie bevestiging wordt ontvangen voor de berichten die worden verzonden naar de partner |
| isaSegment | No | Tekenreeks | Het ISA-segment van het bericht. De waarde wordt alleen ingevuld voor de verzend kant waar de functie bevestiging wordt ontvangen voor de berichten die worden verzonden naar de partner |
| gsSegment | No | Tekenreeks | GS-segment van het bericht. De waarde wordt alleen ingevuld voor de verzend kant waar de functie bevestiging wordt ontvangen voor de berichten die worden verzonden naar de partner |
| respondingfunctionalGroupControlNumber | No | Tekenreeks | Het aflopende uitwisselings controle nummer |
| respondingFunctionalGroupId | No | Tekenreeks | De groeps-ID van de reactie die wordt toegewezen aan AK101 in de bevestiging |
| respondingtransactionSetControlNumber | No | Tekenreeks | Het controle nummer van de transactieset die reageert |
| respondingTransactionSetId | No | Tekenreeks | De trans actie set-ID die wordt toegewezen aan AK201 in de bevestiging |
| Status code | Ja | Booleaans | Status code van bevestigings transactie set |
| segmentsCount | Yes | Enum | Status code van de bevestiging met de volgende toegestane waarden: `Accepted` , `Rejected` en`AcceptedWithErrors` |
| processingStatus | Yes | Enum | Verwerkings status van de bevestiging met de volgende toegestane waarden: `Received` , `Generated` en`Sent` |
| CorrelationMessageId | No | Tekenreeks | ID van correlatie bericht, een combi natie van {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| ak2Segment | No | Tekenreeks | Bevestiging voor een transactieset binnen de ontvangen functionele groep |
| ak3Segment | No | Tekenreeks | Hiermee worden fouten in een gegevens segment gerapporteerd |
| ak5Segment | No | Tekenreeks | Hiermee wordt aangegeven of de trans actie die in het AK2-segment is geïdentificeerd, wordt geaccepteerd of afgewezen en waarom |
|||||

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

| Eigenschap | Vereist | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Tekenreeks | Partner naam van de afzender van het X12-bericht |
| receiverPartnerName | No | Tekenreeks | Partner naam van X12-bericht ontvanger |
| senderQualifier | Yes | Tekenreeks | Partner kwalificatie verzenden |
| senderIdentifier | Yes | Tekenreeks | Partner-id verzenden |
| receiverQualifier | Yes | Tekenreeks | Partner kwalificatie ontvangen |
| receiverIdentifier | Yes | Tekenreeks | Partner-id ontvangen |
| overeenkomst | No | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet |
| richting | Yes | Enum | Richting van de berichten stroom, ofwel `receive` of`send` |
| interchangeControlNumber | No | Tekenreeks | Uitwisselings controle nummer |
| isaSegment | No | Tekenreeks | Message ISA-segment |
| isTechnicalAcknowledgmentExpected | Boolean-waarde | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst  |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| isa09 | No | Tekenreeks | X12-document uitwisselings datum |
| isa10 | No | Tekenreeks | X12-document-uitwisselings tijd |
| isa11 | No | Tekenreeks | X12 Interchange Control-standaard-id |
| isa12 | No | Tekenreeks | Versie nummer van X12 Interchange Control |
| isa14 | No | Tekenreeks | Bevestiging van X12 is aangevraagd |
| isa15 | No | Tekenreeks | Indicator voor test of productie |
| isa16 | No | Tekenreeks | Element scheidings teken |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Tracking schema voor X12 uitwisselings bevestiging

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

| Eigenschap | Vereist | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Tekenreeks | Partner naam van de afzender van het X12-bericht |
| receiverPartnerName | No | Tekenreeks | Partner naam van X12-bericht ontvanger |
| senderQualifier | Yes | Tekenreeks | Partner kwalificatie verzenden |
| senderIdentifier | Yes | Tekenreeks | Partner-id verzenden |
| receiverQualifier | Yes | Tekenreeks | Partner kwalificatie ontvangen |
| receiverIdentifier | Yes | Tekenreeks | Partner-id ontvangen |
| overeenkomst | No | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet |
| richting | Yes | Enum | Richting van de berichten stroom, ofwel `receive` of`send` |
| interchangeControlNumber | No | Tekenreeks | Uitwisselings controle nummer van de technische bevestigingen die van partners worden ontvangen |
| isaSegment | No | Tekenreeks | ISA-segment voor de technische bevestigingen die van partners worden ontvangen |
| respondingInterchangeControlNumber | No | Tekenreeks | Uitwisselings controle nummer voor de technische bevestigingen die van partners worden ontvangen |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| Status code | Yes | Enum | Status code van uitwisselings bevestiging met de volgende toegestane waarden: `Accepted` , `Rejected` en`AcceptedWithErrors` |
| processingStatus | Yes | Enum | Bevestigings status met de volgende toegestane waarden: `Received` , `Generated` en`Sent` |
| ta102 | No | Tekenreeks | Uitwisselings datum |
| ta103 | No | Tekenreeks | Uitwisselings tijd |
| ta105 | No | Tekenreeks | Code voor uitwisselings notities |
|||||

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

| Eigenschap | Vereist | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Tekenreeks | Partner naam van de afzender van het X12-bericht |
| receiverPartnerName | No | Tekenreeks | Partner naam van X12-bericht ontvanger |
| senderQualifier | Yes | Tekenreeks | Partner kwalificatie verzenden |
| senderIdentifier | Yes | Tekenreeks | Partner-id verzenden |
| receiverQualifier | Yes | Tekenreeks | Partner kwalificatie ontvangen |
| receiverIdentifier | Yes | Tekenreeks | Partner-id ontvangen |
| overeenkomst | No | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet |
| richting | Yes | Enum | De richting van de berichten stroom, ofwel ontvangen of verzenden |
| interchangeControlNumber | No | Tekenreeks | Uitwisselings controle nummer |
| functionalGroupControlNumber | No | Tekenreeks | Functioneel controle nummer |
| gsSegment | No | Tekenreeks | Bericht GS-segment |
| isTechnicalAcknowledgmentExpected | Ja | Booleaans | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst |
| isFunctionalAcknowledgmentExpected | Ja | Booleaans | Of de functie bevestiging is geconfigureerd in de X12-overeenkomst |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| gs01 | No | Tekenreeks | Functionele id-code |
| gs02 | No | Tekenreeks | Code van de afzender van de toepassing |
| gs03 | No | Tekenreeks | Code van de ontvanger van de toepassing |
| gs04 | No | Tekenreeks | Datum van functionele groep |
| gs05 | No | Tekenreeks | Tijd van functionele groep |
| gs07 | No | Tekenreeks | Verantwoordelijke instantie code |
| gs08 | No | Tekenreeks | ID-code voor de versie, versie of branche |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Tracking schema voor bevestiging van X12-functie groep

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

| Eigenschap | Vereist | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Tekenreeks | Partner naam van de afzender van het X12-bericht |
| receiverPartnerName | No | Tekenreeks | Partner naam van X12-bericht ontvanger |
| senderQualifier | Yes | Tekenreeks | Partner kwalificatie verzenden |
| senderIdentifier | Yes | Tekenreeks | Partner-id verzenden |
| receiverQualifier | Yes | Tekenreeks | Partner kwalificatie ontvangen |
| receiverIdentifier | Yes | Tekenreeks | Partner-id ontvangen |
| overeenkomst | No | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten worden omgezet |
| richting | Yes | Enum | Richting van de berichten stroom, ofwel `receive` of`send` |
| interchangeControlNumber | No | Tekenreeks | Het uitwisselings controle nummer, dat voor de verzend zijde gevuld wordt wanneer er een technische bevestiging van partners wordt ontvangen |
| functionalGroupControlNumber | No | Tekenreeks | Controle nummer van functionele groep van de technische bevestigingen, die voor de verzend zijde gevuld wordt wanneer er een technische bevestiging van partners wordt ontvangen |
| isaSegment | No | Tekenreeks | Hetzelfde als uitwisselings controle nummer, maar alleen in bepaalde gevallen ingevuld |
| gsSegment | No | Tekenreeks | Hetzelfde als het beheer nummer van de functionele groep, maar alleen in bepaalde gevallen ingevuld |
| respondingfunctionalGroupControlNumber | No | Tekenreeks | Controle nummer van de oorspronkelijke functionele groep |
| respondingFunctionalGroupId | No | Tekenreeks | Verwijst naar AK101 in de ID van de bevestigings functionele groep |
| isMessageFailed | Boolean-waarde | Of het X12-bericht is mislukt |
| Status code | Yes | Enum | Status code van de bevestiging met de volgende toegestane waarden: `Accepted` , `Rejected` en`AcceptedWithErrors` |
| processingStatus | Yes | Enum | Verwerkings status van de bevestiging met de volgende toegestane waarden: `Received` , `Generated` en`Sent` |
| ak903 | No | Tekenreeks | Aantal ontvangen transactie sets |
| ak904 | No | Tekenreeks | Aantal transactie sets dat in de geïdentificeerde functionele groep is geaccepteerd |
| ak9Segment | No | Tekenreeks | Of de in het AK1-segment geïdentificeerde functionele groep wordt geaccepteerd of afgewezen en waarom |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schema's voor het bijhouden van B2B-protocollen

Voor informatie over schema's voor het bijhouden van B2B-protocollen raadpleegt u:

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Aangepaste B2B-tracking schema's](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [B2B-berichten bewaken met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)