---
title: EDIFACT-berichten decoderen
description: EDI valideren en bevestigingen genereren met de EDIFACT-berichtendecoder voor Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 78c0d20c0f32a6d63d134e958b30d38fe11fcc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790666"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>EDIFACT-berichten voor Azure Logic Apps decoderen met het Enterprise Integration Pack

Met de decode EDIFACT-berichtconnector u EDI- en partnerspecifieke eigenschappen valideren, uitwisselingen splitsen in transactiesets of volledige knooppunten behouden en bevestigingen genereren voor verwerkte transacties. Als u deze connector wilt gebruiken, moet u de connector toevoegen aan een bestaande trigger in uw logische app.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die je nodig hebt:

* Een Azure-account; u een [gratis account aanmaken](https://azure.microsoft.com/free)
* Een [integratieaccount](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en is gekoppeld aan uw Azure-abonnement. U moet een integratieaccount hebben om de decode EDIFACT-berichtconnector te gebruiken. 
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratieaccount
* Een [EDIFACT-overeenkomst](logic-apps-enterprise-integration-edifact.md) die al is gedefinieerd in uw integratieaccount

## <a name="decode-edifact-messages"></a>EDIFACT-berichten decoderen

1. [Een logische app maken](quickstart-create-first-logic-app-workflow.md).

2. De decode EDIFACT-berichtconnector heeft geen triggers, dus u moet een trigger toevoegen voor het starten van uw logische app, zoals een trigger voor aanvragen. Voeg in de Logic App Designer een trigger toe en voeg vervolgens een actie toe aan uw logische app.

3. Voer in het zoekvak 'EDIFACT' in als filter. Selecteer **EDIFACT-bericht decoderen**.
   
    ![zoeken EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Als u nog geen verbindingen met uw integratieaccount hebt gemaakt, wordt u gevraagd die verbinding nu te maken. Geef uw verbinding een naam en selecteer het integratieaccount waarmee u verbinding wilt maken.
   
    ![integratieaccount maken](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Eigenschappen met een sterretje zijn vereist.

    | Eigenschap | Details |
    | --- | --- |
    | Verbindingsnaam * |Voer een naam in voor uw verbinding. |
    | Integratieaccount * |Voer een naam in voor uw integratieaccount. Zorg ervoor dat uw integratieaccount en logische app zich op dezelfde Azure-locatie bevinden. |

4. Wanneer u klaar bent met het maken van uw verbinding, kiest u **Maken**. Uw verbindingsgegevens moeten op dit voorbeeld lijken:

    ![integratieaccountgegevens](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Nadat uw verbinding is gemaakt, zoals in dit voorbeeld wordt weergegeven, selecteert u het flatbestandbericht EDIFACT dat u wilt decoderen.

    ![integratie-accountverbinding gemaakt](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Bijvoorbeeld:

    ![EdIFACT-bericht met platte bestanden selecteren voor decodering](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT-decodergegevens

De Decode EDIFACT-connector voert deze taken uit: 

* Valideert de envelop tegen de overeenkomst met handelspartners.
* Hiermee wordt de overeenkomst opgelost door de kwalificatie voor afzenders &-id en de kwalificatie voor de ontvanger &-id te matchen.
* Splitst een uitwisseling op in meerdere transacties wanneer de uitwisseling meer dan één transactie heeft op basis van de configuratie van de ontvangstinstellingen van de overeenkomst.
* Demonteert het knooppunt.
* Valideert EDI en partnerspecifieke eigenschappen, waaronder:
  * Validatie van de structuur van de uitwisselingsenveloppe
  * Schemavalidatie van de envelop tegen het besturingselementschema
  * Schemavalidatie van de gegevenselementen met transactieset op basis van het berichtschema
  * EDI-validatie uitgevoerd op gegevenselementen met transactieset
* Controleert of de controlenummers voor knooppunten, groep en transactieset geen duplicaten zijn (indien geconfigureerd) 
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
* Hiermee genereert u een technische (besturingselement) en/of functionele bevestiging (indien geconfigureerd).
  * Een Technische Erkenning of CONTRL ACK rapporteert de resultaten van een syntactische controle van de volledige ontvangen uitwisseling.
  * Een functionele bevestiging erkent het accepteren of afwijzen van een ontvangen uitwisseling of een groep

## <a name="view-swagger-file"></a>Swagger-bestand weergeven
Zie [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Meer informatie over enterprise integration pack") 

