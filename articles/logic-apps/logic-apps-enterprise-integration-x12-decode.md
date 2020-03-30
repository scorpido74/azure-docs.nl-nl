---
title: X12-berichten decoderen
description: EDI valideren en bevestigingen genereren met X12-berichtdecoder in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792368"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12-berichten decoderen in Azure Logic Apps met enterprise-integratiepakket

Met de Decode X12-berichtenconnector u de envelop valideren op basis van een partnerovereenkomst, EDI- en partnerspecifieke eigenschappen valideren, uitwisselingen splitsen in transactiesets of volledige knooppunten behouden en bevestigingen genereren voor verwerkte transacties. Als u deze connector wilt gebruiken, moet u de connector toevoegen aan een bestaande trigger in uw logische app.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die je nodig hebt:

* Een Azure-account; u een [gratis account aanmaken](https://azure.microsoft.com/free)
* Een [integratieaccount](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en is gekoppeld aan uw Azure-abonnement. U moet een integratieaccount hebben om de X12-berichtenconnector decoderen te gebruiken.
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratieaccount
* Een [X12-overeenkomst](logic-apps-enterprise-integration-x12.md) die al is gedefinieerd in uw integratieaccount

## <a name="decode-x12-messages"></a>X12-berichten decoderen

1. [Een logische app maken](quickstart-create-first-logic-app-workflow.md).

2. De X12-berichtenconnector decoderen heeft geen triggers, dus u moet een trigger toevoegen voor het starten van uw logische app, zoals een trigger voor aanvragen. Voeg in de Logic App Designer een trigger toe en voeg vervolgens een actie toe aan uw logische app.

3.  Voer in het zoekvak 'x12' voor uw filter in. Selecteer **X12 - X12-bericht decoderen**.
   
    ![Zoek naar "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Als u nog geen verbindingen met uw integratieaccount hebt gemaakt, wordt u gevraagd die verbinding nu te maken. Geef uw verbinding een naam en selecteer het integratieaccount waarmee u verbinding wilt maken. 

    ![Verbindingsgegevens voor integratie-account opgeven](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Eigenschappen met een sterretje zijn vereist.

    | Eigenschap | Details |
    | --- | --- |
    | Verbindingsnaam * |Voer een naam in voor uw verbinding. |
    | Integratieaccount * |Voer een naam in voor uw integratieaccount. Zorg ervoor dat uw integratieaccount en logische app zich op dezelfde Azure-locatie bevinden. |

5.  Wanneer u klaar bent, moeten uw verbindingsgegevens op dit voorbeeld lijken. Als u het maken van uw verbinding wilt voltooien, kiest **u Maken**.
   
    ![verbindingsgegevens van integratie-account](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Nadat uw verbinding is gemaakt, zoals in dit voorbeeld wordt weergegeven, selecteert u het x12-platte bestandsbericht dat u wilt decoderen.

    ![integratie-accountverbinding gemaakt](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Bijvoorbeeld:

    ![X12-bericht met platte bestanden selecteren voor decodering](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > De werkelijke inhoud van het bericht of payload voor het bericht array, goed of slecht, is base64 gecodeerd. U moet dus een expressie opgeven die deze inhoud verwerkt.
   > Hier is een voorbeeld dat de inhoud verwerkt als XML die u invoeren in de codeweergave of met behulp van expressiebouwer in de ontwerper.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Voorbeeld van inhoud](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 Decoderen

De X12 Decode-connector voert deze taken uit:

* Valideert de envelop tegen de overeenkomst met handelspartners
* Valideert EDI en partnerspecifieke eigenschappen
  * EDI structurele validatie en uitgebreide schemavalidatie
  * Validatie van de structuur van de uitwisselingsenveloppe.
  * Schemavalidatie van de envelop tegen het besturingselementschema.
  * Schemavalidatie van de gegevenselementen met transactieset en het berichtschema.
  * EDI-validatie uitgevoerd op gegevenselementen met transactieset 
* Controleert of de controlenummers voor knooppunten, groep en transactieset geen duplicaten zijn
  * Hiermee controleert u het knooppuntcontrolenummer op eerder ontvangen knooppunten.
  * Hiermee controleert u het groepscontrolenummer op andere groepscontrolenummers in de uitwisseling.
  * Hiermee controleert u het controlenummer van de transactieset op andere transactiesetcontrolenummers in die groep.
* Splitst de uitwisseling op in transactiesets of behoudt de volledige uitwisseling:
  * Split interchange as transaction sets - suspend transaction sets on error: Splitst interchange in transactiesets en ontkadert elke transactieset. 
  De x12-decoderen-actie produceert alleen de `badMessages`transactiesets die de validatie `goodMessages`niet uitvoeren en worden uitgevoerd op de resterende transacties ingesteld op .
  * Split interchange as transaction sets - suspend interchange on error: Splitst interchange in transactiesets en parseert elke transactieset. 
  Als een of meer transactiesets in de validatie van de interchange fail worden ingesteld, `badMessages`worden in de actie X12 Decode alle transactiesets in die uitwisseling uitgevoerd op .
  * Interchange behouden - transactiesets op fout opschorten: behoud de uitwisseling en verwerk de volledige batched-uitwisseling. 
  De x12-decoderen-actie produceert alleen de `badMessages`transactiesets die de validatie `goodMessages`niet uitvoeren en worden uitgevoerd op de resterende transacties ingesteld op .
  * Behouden Uitwisseling - knooppunt op fout opschorten: Behoud de uitwisseling en verwerk de gehele batched interchange. 
  Als een of meer transactiesets in de validatie van de interchange fail worden ingesteld, `badMessages`worden in de actie X12 Decode alle transactiesets in die uitwisseling uitgevoerd op . 
* Hiermee genereert u een technische en/of functionele bevestiging (indien geconfigureerd).
  * Een technische bevestiging genereert als gevolg van headervalidatie. De technische erkenning rapporteert de status van de verwerking van een uitwisselingsheader en aanhangwagen door de adresontvanger.
  * Een functionele bevestiging genereert als gevolg van body validatie. De functionele bevestiging rapporteert elke fout die is opgetreden tijdens het verwerken van het ontvangen document

## <a name="view-the-swagger"></a>Bekijk de branie
Zie de [branie details](/connectors/x12/). 

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Meer informatie over enterprise integration pack") 

