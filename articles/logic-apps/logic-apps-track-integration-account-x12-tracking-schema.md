---
title: X12-trackingschema's voor B2B-berichten
description: Trackingschema's maken om X12-berichten voor Azure Logic Apps te controleren
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905298"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Schema's maken voor het bijhouden van X12-berichten in Azure Logic Apps

Om u te helpen succes, fouten en berichteigenschappen voor B2B-transacties (business-to-business) te controleren, u deze X12-trackingschema's gebruiken in uw integratieaccount:

* X12-schema voor het bijhouden van x12-transacties
* X12-schema voor het bijhouden van bevestigingen instellen
* X12-trackingschema voor knooppunten
* X12-volgschema voor bevestigingen
* X12 functioneel groepstrackingschema
* X12-schema voor het bijhouden van bevestigingvan functionele groepsbevestigingen

## <a name="x12-transaction-set-tracking-schema"></a>X12-schema voor het bijhouden van x12-transacties

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

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | De partnernaam van de afzender van x12-berichten |
| receiverPartnerName | Nee | Tekenreeks | De partnernaam van de X12-berichtontvanger |
| senderQualifier | Ja | Tekenreeks | Partnerkwalificatie verzenden |
| afzenderId | Ja | Tekenreeks | Partner-id verzenden |
| ontvangerQualifier | Ja | Tekenreeks | Partnerkwalificatie ontvangen |
| ontvangerId | Ja | Tekenreeks | Partner-id ontvangen |
| overeenkomstNaam | Nee | Tekenreeks | Naam van de X12-overeenkomst waaraan de berichten zijn opgelost |
| richting | Ja | Enum | Richting van de berichtstroom, `receive` die is of`send` |
| interchangeControlNumber | Nee | Tekenreeks | Interchange-controlenummer |
| functionalGroupControlNumber | Nee | Tekenreeks | Functioneel controlenummer |
| transactionSetControlNumber | Nee | Tekenreeks | Regelnummer transactieset |
| CorrelatieMessageId | Nee | Tekenreeks | Correlatiebericht-id, een combinatie van {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| messageType (messageType) | Nee | Tekenreeks | Transactieset of documenttype |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| isTechnicalAcknowledgementExpected | Ja | Booleaans | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst |
| isFunctionalAcknowledgementExpected | Ja | Booleaans | Of de functionele bevestiging is geconfigureerd in de X12-overeenkomst |
| needAk2LoopForValidMessages | Ja | Booleaans | Of de AK2-lus vereist is voor een geldig bericht |
| segmentenTellen | Nee | Geheel getal | Aantal segmenten in de X12-transactieset |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12-schema voor het bijhouden van bevestigingen instellen

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

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | De partnernaam van de afzender van x12-berichten |
| receiverPartnerName | Nee | Tekenreeks | De partnernaam van de X12-berichtontvanger |
| senderQualifier | Ja | Tekenreeks | Partnerkwalificatie verzenden |
| afzenderId | Ja | Tekenreeks | Partner-id verzenden |
| ontvangerQualifier | Ja | Tekenreeks | Partnerkwalificatie ontvangen |
| ontvangerId | Ja | Tekenreeks | Partner-id ontvangen |
| overeenkomstNaam | Nee | Tekenreeks | Naam van de X12-overeenkomst waaraan de berichten zijn opgelost |
| richting | Ja | Enum | Richting van de berichtstroom, `receive` die is of`send` |
| interchangeControlNumber | Nee | Tekenreeks | Het controlenummer van de uitwisseling van de functionele erkenning. De waarde wordt alleen ingevuld voor de verzendkant waar functionele bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden. |
| functionalGroupControlNumber | Nee | Tekenreeks | Functioneel groepscontrolenummer van de functionele bevestiging. De waarde wordt alleen ingevuld voor de verzendzijde waar functionele bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden |
| isaSegment isaSegment | Nee | Tekenreeks | ISA-segment van het bericht. De waarde wordt alleen ingevuld voor de verzendzijde waar functionele bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden |
| gsSegment | Nee | Tekenreeks | GS-segment van het bericht. De waarde wordt alleen ingevuld voor de verzendzijde waar functionele bevestiging wordt ontvangen voor de berichten die naar de partner worden verzonden |
| reagerenfunctionalGroupControlNumber | Nee | Tekenreeks | Het antwoordknooppuntcontrolenummer |
| reagerenFunctionalGroupId | Nee | Tekenreeks | De antwoordende functionele groepsIDENTITEITSKAART, die aan AK101 in de erkenning in kaart brengt |
| reagerentransactionSetControlNumber | Nee | Tekenreeks | Het antwoordtransactiesetregelnummer |
| reagerenTransactionsetid | Nee | Tekenreeks | De antwoordende transactiereeksIDENTITEITSKAART, die aan AK201 in de erkenning toebrengt |
| statusCode | Ja | Booleaans | Statuscode voor bevestiging van transactieset |
| segmentenTellen | Ja | Enum | Bevestigingsstatuscode met deze toegestane `Accepted` `Rejected`waarden: , , en`AcceptedWithErrors` |
| verwerkingStatus | Ja | Enum | Verwerkingsstatus van de bevestiging met `Received`deze `Generated`toegestane waarden: , en`Sent` |
| CorrelatieMessageId | Nee | Tekenreeks | Correlatiebericht-id, een combinatie van {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| ak2Segment | Nee | Tekenreeks | Bevestiging voor een transactie die is ingesteld binnen de ontvangen functionele groep |
| ak3Segment | Nee | Tekenreeks | Fouten in een gegevenssegment melden |
| ak5Segment | Nee | Tekenreeks | Rapporteert of de transactieset die in het AK2-segment is geïdentificeerd, wordt geaccepteerd of afgewezen en waarom |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12-trackingschema voor knooppunten

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

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | De partnernaam van de afzender van x12-berichten |
| receiverPartnerName | Nee | Tekenreeks | De partnernaam van de X12-berichtontvanger |
| senderQualifier | Ja | Tekenreeks | Partnerkwalificatie verzenden |
| afzenderId | Ja | Tekenreeks | Partner-id verzenden |
| ontvangerQualifier | Ja | Tekenreeks | Partnerkwalificatie ontvangen |
| ontvangerId | Ja | Tekenreeks | Partner-id ontvangen |
| overeenkomstNaam | Nee | Tekenreeks | Naam van de X12-overeenkomst waaraan de berichten zijn opgelost |
| richting | Ja | Enum | Richting van de berichtstroom, `receive` die is of`send` |
| interchangeControlNumber | Nee | Tekenreeks | Interchange-controlenummer |
| isaSegment isaSegment | Nee | Tekenreeks | Bericht ISA-segment |
| isTechnicalAcknowledgementExpected | Booleaans | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst  |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| isa09 | Nee | Tekenreeks | X12-documentuitwisselingsdatum |
| isa10 | Nee | Tekenreeks | X12-documentuitwisselingstijd |
| isa11 | Nee | Tekenreeks | X12-identificatievoor interbancaire controlenormen |
| isa12 | Nee | Tekenreeks | X12-versienummer voor interbancaire controle |
| isa14 | Nee | Tekenreeks | X12-bevestiging wordt aangevraagd |
| isa15 | Nee | Tekenreeks | Indicator voor test of productie |
| isa16 | Nee | Tekenreeks | Elementscheidingsteken |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12-volgschema voor bevestigingen

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

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | De partnernaam van de afzender van x12-berichten |
| receiverPartnerName | Nee | Tekenreeks | De partnernaam van de X12-berichtontvanger |
| senderQualifier | Ja | Tekenreeks | Partnerkwalificatie verzenden |
| afzenderId | Ja | Tekenreeks | Partner-id verzenden |
| ontvangerQualifier | Ja | Tekenreeks | Partnerkwalificatie ontvangen |
| ontvangerId | Ja | Tekenreeks | Partner-id ontvangen |
| overeenkomstNaam | Nee | Tekenreeks | Naam van de X12-overeenkomst waaraan de berichten zijn opgelost |
| richting | Ja | Enum | Richting van de berichtstroom, `receive` die is of`send` |
| interchangeControlNumber | Nee | Tekenreeks | Interchange control nummer van de technische bevestiging die is ontvangen van partners |
| isaSegment isaSegment | Nee | Tekenreeks | ISA-segment voor de technische bevestiging die van partners is ontvangen |
| reagerenInterchangeControlNumber | Nee | Tekenreeks | Interchange control number for the technical acknowledgement that's received from partners Interchange control number for the technical acknowledgement that's received from partners Interchange control number for the technical acknowledgement that's received from partners |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| statusCode | Ja | Enum | Statuscode voor de uitwisseling van `Accepted` `Rejected`bevestiging met deze toegestane waarden: , en`AcceptedWithErrors` |
| verwerkingStatus | Ja | Enum | Erkenningsstatus met deze `Received`toegestane `Generated`waarden: , , en`Sent` |
| ta102 | Nee | Tekenreeks | Interbancaire datum |
| ta103 | Nee | Tekenreeks | Interbancaire tijd |
| ta105 | Nee | Tekenreeks | Interchange note code |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 functioneel groepstrackingschema

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

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | De partnernaam van de afzender van x12-berichten |
| receiverPartnerName | Nee | Tekenreeks | De partnernaam van de X12-berichtontvanger |
| senderQualifier | Ja | Tekenreeks | Partnerkwalificatie verzenden |
| afzenderId | Ja | Tekenreeks | Partner-id verzenden |
| ontvangerQualifier | Ja | Tekenreeks | Partnerkwalificatie ontvangen |
| ontvangerId | Ja | Tekenreeks | Partner-id ontvangen |
| overeenkomstNaam | Nee | Tekenreeks | De naam van de X12-overeenkomst waarop de berichten zijn opgelost |
| richting | Ja | Enum | Richting van de berichtstroom, ontvangen of verzenden |
| interchangeControlNumber | Nee | Tekenreeks | Interchange-controlenummer |
| functionalGroupControlNumber | Nee | Tekenreeks | Functioneel controlenummer |
| gsSegment | Nee | Tekenreeks | Gs-segment bericht |
| isTechnicalAcknowledgementExpected | Ja | Booleaans | Of de technische bevestiging is geconfigureerd in de X12-overeenkomst |
| isFunctionalAcknowledgementExpected | Ja | Booleaans | Of de functionele bevestiging is geconfigureerd in de X12-overeenkomst |
| isMessageFailed | Ja | Booleaans | Of het X12-bericht is mislukt |
| gs01 | Nee | Tekenreeks | Functionele id-code |
| gs02 | Nee | Tekenreeks | Code van de afzender van de toepassing |
| gs03 | Nee | Tekenreeks | Code van de toepassingsontvanger |
| gs04 | Nee | Tekenreeks | Datum van functionele groep |
| gs05 | Nee | Tekenreeks | Functionele groepstijd |
| gs07 | Nee | Tekenreeks | Verantwoordelijke bureaucode |
| gs08 | Nee | Tekenreeks | Id-code voor de versie, release of industrie |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12-schema voor het bijhouden van bevestigingvan functionele groepsbevestigingen

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

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | De partnernaam van de afzender van x12-berichten |
| receiverPartnerName | Nee | Tekenreeks | De partnernaam van de X12-berichtontvanger |
| senderQualifier | Ja | Tekenreeks | Partnerkwalificatie verzenden |
| afzenderId | Ja | Tekenreeks | Partner-id verzenden |
| ontvangerQualifier | Ja | Tekenreeks | Partnerkwalificatie ontvangen |
| ontvangerId | Ja | Tekenreeks | Partner-id ontvangen |
| overeenkomstNaam | Nee | Tekenreeks | Naam van de X12-overeenkomst waaraan de berichten zijn opgelost |
| richting | Ja | Enum | Richting van de berichtstroom, `receive` die is of`send` |
| interchangeControlNumber | Nee | Tekenreeks | Het controlenummer van de uitwisseling, dat voor de verzonden kant vult wanneer een technische erkenning van partners wordt ontvangen |
| functionalGroupControlNumber | Nee | Tekenreeks | Functioneel groepscontrolenummer van de technische erkenning, dat voor de verzonden kant vult wanneer een technische erkenning van partners wordt ontvangen |
| isaSegment isaSegment | Nee | Tekenreeks | Hetzelfde als interchange control nummer, maar alleen bevolkt in specifieke gevallen |
| gsSegment | Nee | Tekenreeks | Hetzelfde als functioneel groepscontrolenummer, maar alleen ingevuld in specifieke gevallen |
| reagerenfunctionalGroupControlNumber | Nee | Tekenreeks | Controlenummer van de oorspronkelijke functionele groep |
| reagerenFunctionalGroupId | Nee | Tekenreeks | Kaarten aan AK101 in de bevestigings functionele groepsIDENTITEITSKAART |
| isMessageFailed | Booleaans | Of het X12-bericht is mislukt |
| statusCode | Ja | Enum | Bevestigingsstatuscode met deze toegestane `Accepted` `Rejected`waarden: , , en`AcceptedWithErrors` |
| verwerkingStatus | Ja | Enum | Verwerkingsstatus van de bevestiging met `Received`deze `Generated`toegestane waarden: , en`Sent` |
| ak903 | Nee | Tekenreeks | Aantal ontvangen transactiesets |
| ak904 | Nee | Tekenreeks | Aantal transactiesets dat is geaccepteerd in de geïdentificeerde functionele groep |
| ak9Segment | Nee | Tekenreeks | Of de functionele groep die in het AK1-segment is geïdentificeerd, wordt geaccepteerd of afgewezen, en waarom |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protocoltrackingschema's

Zie voor informatie over b2b-protocoltrackingschema's:

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B aangepaste trackingschema's](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [B2B-berichten bewaken met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)