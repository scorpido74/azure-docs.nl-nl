---
title: EDIFACT-berichten coderen
description: EDI valideren en XML genereren met EDIFACT-berichtencoder voor Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 257cbd0b1a68ddd2b16235e6f8dec5d5b0eb10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790659"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>EDIFACT-berichten coderen voor Azure Logic Apps met Enterprise Integration Pack

Met de Ecode EDIFACT-berichtconnector u EDI- en partnerspecifieke eigenschappen valideren, een XML-document voor elke transactieset genereren en een technische bevestiging, functionele bevestiging of beide aanvragen.
Als u deze connector wilt gebruiken, moet u de connector toevoegen aan een bestaande trigger in uw logische app.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die je nodig hebt:

* Een Azure-account; u een [gratis account aanmaken](https://azure.microsoft.com/free)
* Een [integratieaccount](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en is gekoppeld aan uw Azure-abonnement. U moet een integratieaccount hebben om de Ecode EDIFACT-berichtconnector te kunnen gebruiken. 
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratieaccount
* Een [EDIFACT-overeenkomst](logic-apps-enterprise-integration-edifact.md) die al is gedefinieerd in uw integratieaccount

## <a name="encode-edifact-messages"></a>EDIFACT-berichten coderen

1. [Een logische app maken](quickstart-create-first-logic-app-workflow.md).

2. De Ecode EDIFACT-berichtconnector heeft geen triggers, dus u moet een trigger toevoegen voor het starten van uw logische app, zoals een trigger voor aanvragen. Voeg in de Logic App Designer een trigger toe en voeg vervolgens een actie toe aan uw logische app.

3.  Voer in het zoekvak 'EDIFACT' in als filter. Selecteer **EDIFACT-bericht coderen op naam van de overeenkomst** of Coderen in **EDIFACT-bericht op identiteiten**.
   
    ![zoeken EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Als u nog geen verbindingen met uw integratieaccount hebt gemaakt, wordt u gevraagd die verbinding nu te maken. Geef uw verbinding een naam en selecteer het integratieaccount waarmee u verbinding wilt maken.

    ![integratieaccountverbinding maken](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Eigenschappen met een sterretje zijn vereist.

    | Eigenschap | Details |
    | --- | --- |
    | Verbindingsnaam * |Voer een naam in voor uw verbinding. |
    | Integratieaccount * |Voer een naam in voor uw integratieaccount. Zorg ervoor dat uw integratieaccount en logische app zich op dezelfde Azure-locatie bevinden. |

5.  Wanneer u klaar bent, moeten uw verbindingsgegevens op dit voorbeeld lijken. Als u het maken van uw verbinding wilt voltooien, kiest **u Maken**.

    ![verbindingsgegevens van integratie-account](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Uw verbinding is nu gemaakt.

    ![integratie-accountverbinding gemaakt](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>EDIFACT-bericht coderen met de naam van de overeenkomst

Als u ervoor kiest om EDIFACT-berichten te coderen onder de naam van de overeenkomst, opent u de lijst **Naam van EDIFACT-overeenkomst,** voert u de naam van uw EDIFACT-overeenkomst in of selecteert u deze. Voer het XML-bericht in om te coderen.

![Voer de naam van edifact-overeenkomst en het XML-bericht in om te coderen](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>EDIFACT-bericht coderen op identiteiten

Als u ervoor kiest om EDIFACT-berichten te coderen op identiteiten, voert u de afzender-id, de kwalificatie voor afzenders, de ontvanger-id en de kwalificatie voor ontvangers in zoals geconfigureerd in uw EDIFACT-overeenkomst. Selecteer het XML-bericht dat u wilt coderen.

![Geef identiteiten op voor afzender en ontvanger, selecteer XML-bericht om te coderen](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT Coderen details

De Ecode EDIFACT-connector voert deze taken uit: 

* De overeenkomst oplossen door de kwalificatie voor de afzender &-id en ontvanger kwalificatie en id te matchen
* Serialiseert de EDI-uitwisseling en converteert XML-gecodeerde berichten naar EDI-transactiesets in de uitwisseling.
* Past header- en trailersegmenten voor transactieset toe
* Hiermee genereert u een interchange control-nummer, een groepscontrolenummer en een transactiesetregelnummer voor elke uitgaande uitwisseling
* Vervangt scheidingstekens in de payloadgegevens
* Valideert EDI en partnerspecifieke eigenschappen
  * Schemavalidatie van de gegevenselementen met transactieset en het berichtschema.
  * EDI-validatie uitgevoerd op gegevenselementen met transactiesets.
  * Uitgebreide validatie uitgevoerd op gegevenselementen met transactieset
* Hiermee genereert u een XML-document voor elke transactieset.
* Hiermee vraagt u een technische en/of functionele bevestiging aan (indien geconfigureerd).
  * Als technische bevestiging geeft het CONTRL-bericht de ontvangst van een uitwisseling aan.
  * Als functionele bevestiging geeft het CONTRL-bericht de acceptatie of afwijzing van de ontvangen uitwisseling, groep of bericht aan, met een lijst met fouten of niet-ondersteunde functionaliteit

## <a name="view-swagger-file"></a>Swagger-bestand weergeven
Zie [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Meer informatie over enterprise integration pack") 

