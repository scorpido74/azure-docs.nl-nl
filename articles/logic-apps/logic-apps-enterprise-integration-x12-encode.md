---
title: X12-berichten coderen
description: EDI valideren en XML-gecodeerde berichten converteren met X12-berichtencoder in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792346"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12-berichten coderen in Azure Logic Apps met Enterprise Integration Pack

Met de X12-berichtenconnector encode u EDI- en partnerspecifieke eigenschappen valideren, XML-gecodeerde berichten converteren naar EDI-transactiesets in de uitwisseling en een technische bevestiging, functionele bevestiging of beide aanvragen.
Als u deze connector wilt gebruiken, moet u de connector toevoegen aan een bestaande trigger in uw logische app.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die je nodig hebt:

* Een Azure-account; u een [gratis account aanmaken](https://azure.microsoft.com/free)
* Een [integratieaccount](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en is gekoppeld aan uw Azure-abonnement. U moet een integratieaccount hebben om de X12-berichtenconnector encode te gebruiken.
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratieaccount
* Een [X12-overeenkomst](logic-apps-enterprise-integration-x12.md) die al is gedefinieerd in uw integratieaccount

## <a name="encode-x12-messages"></a>X12-berichten coderen

1. [Een logische app maken](quickstart-create-first-logic-app-workflow.md).

2. De X12-berichtenconnector encoderen heeft geen triggers, dus u moet een trigger toevoegen voor het starten van uw logische app, zoals een trigger voor aanvragen. Voeg in de Logic App Designer een trigger toe en voeg vervolgens een actie toe aan uw logische app.

3.  Voer in het zoekvak 'x12' voor uw filter in. Selecteer **X12 - Coderen op X12-bericht onder de naam van de overeenkomst** of **X12 - Coderen op X12-bericht op identiteiten**.
   
    ![Zoek naar "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Als u nog geen verbindingen met uw integratieaccount hebt gemaakt, wordt u gevraagd die verbinding nu te maken. Geef uw verbinding een naam en selecteer het integratieaccount waarmee u verbinding wilt maken. 
   
    ![integratie-accountverbinding](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Eigenschappen met een sterretje zijn vereist.

    | Eigenschap | Details |
    | --- | --- |
    | Verbindingsnaam * |Voer een naam in voor uw verbinding. |
    | Integratieaccount * |Voer een naam in voor uw integratieaccount. Zorg ervoor dat uw integratieaccount en logische app zich op dezelfde Azure-locatie bevinden. |

5.  Wanneer u klaar bent, moeten uw verbindingsgegevens op dit voorbeeld lijken. Als u het maken van uw verbinding wilt voltooien, kiest **u Maken**.

    ![integratie-accountverbinding gemaakt](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Uw verbinding is nu gemaakt.

    ![verbindingsgegevens van integratie-account](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>X12-berichten coderen onder de naam van de overeenkomst

Als u ervoor kiest x12-berichten te coderen onder de naam van de overeenkomst, opent u de lijst **Naam van X12-overeenkomst,** voert u uw bestaande X12-overeenkomst in of selecteert u deze. Voer het XML-bericht in om te coderen.

![Voer de naam van x12-overeenkomsten en het XML-bericht in om te coderen](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>X12-berichten coderen op identiteiten

Als u ervoor kiest x12-berichten te coderen op identiteiten, voert u de afzender-id, de kwalificatie voor afzenders, de ontvanger-id en de kwalificatie voor de ontvanger in zoals geconfigureerd in uw X12-overeenkomst. Selecteer het XML-bericht dat u wilt coderen.
   
![Geef identiteiten op voor afzender en ontvanger, selecteer XML-bericht om te coderen](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 Details coderen

De X12 Encode-connector voert deze taken uit:

* Oplossing van de overeenkomst door de eigenschappen van de verzender en de context van de ontvanger te matchen.
* Serialiseert de EDI-uitwisseling en converteert XML-gecodeerde berichten naar EDI-transactiesets in de uitwisseling.
* Past header- en trailersegmenten voor transactieset toe
* Hiermee genereert u een interchange control-nummer, een groepscontrolenummer en een transactiesetregelnummer voor elke uitgaande uitwisseling
* Vervangt scheidingstekens in de payloadgegevens
* Valideert EDI en partnerspecifieke eigenschappen
  * Schemavalidatie van de gegevenselementen van de transactieset op basis van het berichtschema
  * EDI-validatie uitgevoerd op gegevenselementen met transactiesets.
  * Uitgebreide validatie uitgevoerd op gegevenselementen met transactieset
* Hiermee vraagt u een technische en/of functionele bevestiging aan (indien geconfigureerd).
  * Een technische bevestiging genereert als gevolg van headervalidatie. De technische erkenning rapporteert de status van de verwerking van een uitwisselingsheader en aanhangwagen door de adresontvanger
  * Een functionele bevestiging genereert als gevolg van body validatie. De functionele bevestiging rapporteert elke fout die is opgetreden tijdens het verwerken van het ontvangen document

## <a name="view-the-swagger"></a>Bekijk de branie
Zie de [branie details](/connectors/x12/). 

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Meer informatie over enterprise integration pack") 

