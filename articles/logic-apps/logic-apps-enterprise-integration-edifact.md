---
title: EDIFACT-berichten voor B2B-integratie
description: Exchange EDIFACT-berichten in EDI-indeling voor B2B-bedrijfsintegratie in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/26/2016
ms.openlocfilehash: 3ada12a0cde122fb78815a1d3241d8acb9da2580
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651454"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>EdIFACT-berichten uitwisselen voor B2B-bedrijfsintegratie in Azure Logic Apps met Enterprise Integration Pack

Voordat u EDIFACT-berichten uitwisselen voor Azure Logic Apps, moet u een EDIFACT-overeenkomst maken en die overeenkomst opslaan in uw integratieaccount. Hier volgen de stappen voor het maken van een EDIFACT-overeenkomst.

> [!NOTE]
> Deze pagina bevat de EDIFACT-functies voor Azure Logic Apps. Zie [X12](logic-apps-enterprise-integration-x12.md)voor meer informatie.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die je nodig hebt:

* Een [integratieaccount](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en is gekoppeld aan uw Azure-abonnement  
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratieaccount

> [!NOTE]
> Wanneer u een overeenkomst maakt, moet de inhoud in de berichten die u ontvangt of verzendt van en naar de partner overeenkomen met het type overeenkomst.

Nadat u [een integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) hebt gemaakt en [partners hebt toegevoegd,](logic-apps-enterprise-integration-partners.md)u een EDIFACT-overeenkomst maken door deze stappen te volgen.

## <a name="create-an-edifact-agreement"></a>Een EDIFACT-overeenkomst maken 

1. Meld u aan bij [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. Selecteer **alle services**in het hoofdmenu van Azure . Voer in het zoekvak 'integratie' in en selecteer **Integratieaccounts**.

   ![Uw integratieaccount zoeken](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Als **Alle services** niet worden weergegeven, moet u mogelijk eerst het menu uitbreiden. Selecteer boven aan het samengevouwen menu de optie **Tekstlabels weergeven**.

3. Selecteer **onder Integratieaccounts**het integratieaccount waar u de overeenkomst wilt maken.

   ![Integratieaccount selecteren waar u de overeenkomst maken](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Kies **Overeenkomsten**. Als u geen tegel Overeenkomsten hebt, voegt u eerst de tegel toe.   

   ![De tegel 'Overeenkomsten' kiezen](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Kies op de pagina Overeenkomsten de optie **Toevoegen**.

   ![Kies 'Toevoegen'](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Voer **onder Toevoegen**een **naam** in voor uw overeenkomst. Selecteer **EDIFACT**voor **het type Overeenkomst**. Selecteer de **hostpartner,** **hostidentiteit,** **gastpartner**en **gastidentiteit** voor uw overeenkomst.

   ![Details van de overeenkomst verstrekken](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Eigenschap | Beschrijving |
   | --- | --- |
   | Name |Naam van de overeenkomst |
   | Type overeenkomst | Moet EDIFACT zijn |
   | Gastpartner |Een overeenkomst heeft zowel een gast- als gastpartner nodig. De hostpartner vertegenwoordigt de organisatie die de overeenkomst configureert. |
   | Host identiteit |Een id voor de hostpartner |
   | Gastpartner |Een overeenkomst heeft zowel een gast- als gastpartner nodig. De gastpartner vertegenwoordigt de organisatie die zaken doet met de gastpartner. |
   | Gastidentiteit |Een id voor de gastpartner |
   | Instellingen voor ontvangen |Deze eigenschappen zijn van toepassing op alle berichten die door een overeenkomst zijn ontvangen. |
   | Verzendinstellingen |Deze eigenschappen zijn van toepassing op alle berichten die door een overeenkomst worden verzonden. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configureren hoe uw overeenkomst met ontvangen berichten omgaat

Nu u de overeenkomsteigenschappen hebt ingesteld, u configureren hoe deze overeenkomst binnenkomende berichten identificeert en verwerkt die via deze overeenkomst van uw partner zijn ontvangen.

1. Selecteer **Onder Toevoegen**de optie Instellingen **ontvangen**.
Configureer deze eigenschappen op basis van uw overeenkomst met de partner die berichten met u uitwisselt. Zie de tabellen in deze sectie voor eigenschapbeschrijvingen.

   **Instellingen voor ontvangen** is ingedeeld in deze secties: Id's, Bevestiging, Schema's, Controlenummers, Validatie en Interne instellingen.

   !['Instellingen voor ontvangen' configureren](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Nadat u klaar bent, moet u uw instellingen opslaan door **OK**te kiezen.

Nu is uw overeenkomst klaar om binnenkomende berichten te verwerken die voldoen aan de geselecteerde instellingen.

### <a name="identifiers"></a>Identificatiemiddelen

| Eigenschap | Beschrijving |
| --- | --- |
| UNB6.1 (wachtwoord voor geadresseerden) |Voer een alfanumerieke waarde in die varieert van 1 tot 14 tekens. |
| UNB6.2 (Referentiekwalificatie voor ontvangers) |Voer een alfanumerieke waarde in met minimaal één teken en maximaal twee tekens. |

### <a name="acknowledgments"></a>Erkenningen

| Eigenschap | Beschrijving |
| --- | --- |
| Ontvangst van bericht (CONTRL) |Schakel dit selectievakje in om een technische bevestiging (CONTRL) terug te sturen naar de afzender van de uitwisseling. De bevestiging wordt verzonden naar de afzender van de uitwisseling op basis van de verzendinstellingen verzenden voor de overeenkomst. |
| Erkenning (CONTRL) |Schakel dit selectievakje in om een functionele (CONTRL)-bevestiging terug te sturen naar de afzender van de uitwisseling De bevestiging wordt naar de afzender van de uitwisseling verzonden op basis van de verzendinstellingen voor de overeenkomst. |

### <a name="schemas"></a>Schema 's

| Eigenschap | Beschrijving |
| --- | --- |
| UNH2.1 (TYPE) |Selecteer een type transactieset. |
| UNH2.2 (VERSIE) |Voer het versienummer van het bericht in. (Minimum, één teken; maximaal, drie tekens). |
| UNH2.3 (RELEASE) |Voer het releasenummer van het bericht in. (Minimum, één teken; maximaal, drie tekens). |
| UNH2.5 (BIJBEHORENDE TOEGEWEZEN CODE) |Voer de toegewezen code in. (Maximaal, zes tekens. Moet alfanumeriek zijn). |
| UNG2.1 (APP SENDER ID) |Voer een alfanumerieke waarde in met minimaal één teken en maximaal 35 tekens. |
| UNG2.2 (KWALIFICATIE VOOR DE AFZENDER VAN DE APP) |Voer een alfanumerieke waarde in, met een maximum van vier tekens. |
| SCHEMA |Selecteer het eerder geüploade schema dat u wilt gebruiken in uw gekoppelde integratieaccount. |

### <a name="control-numbers"></a>Controlenummers

| Eigenschap | Beschrijving |
| --- | --- |
| Duplicaten van interchange control number weigeren |Als u dubbele knooppunten wilt blokkeren, selecteert u deze eigenschap. Indien geselecteerd, controleert de EDIFACT Decode Action of het interchange control number (UNB5) voor de ontvangen uitwisseling niet overeenkomt met een eerder verwerkt interchange control nummer. Als een overeenkomst wordt gedetecteerd, wordt het knooppunt niet verwerkt. |
| Controleer elke (dag) op dubbele UNB5 |Als u ervoor hebt gekozen dubbele uitwisselingscontrolenummers te verbieden, u het aantal dagen opgeven waarop u de controle moet uitvoeren door de juiste waarde voor deze instelling op te geven. |
| Groepcontrolenummerduplicaten weigeren |Als u knooppunten wilt blokkeren met dubbele groepscontrolenummers (UNG5), selecteert u deze eigenschap. |
| Dubbele gegevens van transactiesetregelnummer weigeren |Als u knooppunten wilt blokkeren met dubbele transactiesetcontrolenummers (UNH1), selecteert u deze eigenschap. |
| EDIFACT Bevestigingscontrolenummer |Als u de referentienummers van de transactieset wilt aanwijzen voor gebruik in een bevestiging, voert u een waarde in voor het voorvoegsel, een bereik van referentienummers en een achtervoegsel. |

### <a name="validation"></a>Validatie

Wanneer u elke validatierij voltooit, wordt er automatisch een andere toegevoegd. Als u geen regels opgeeft, gebruikt validatie de rij Standaard.

| Eigenschap | Beschrijving |
| --- | --- |
| Berichttype |Selecteer het type EDI-bericht. |
| EDI-validatie |Voer EDI-validatie uit op gegevenstypen zoals gedefinieerd door de EDI-eigenschappen van het schema, lengtebeperkingen, lege gegevenselementen en trailing-scheidingstekens. |
| Uitgebreide validatie |Als het gegevenstype geen EDI is, bevindt validatie zich op de vereiste gegevenselement en is herhaling, opsommingen en validatie van gegevenselementen (min/max) toegestaan. |
| Toestaan Dat nullen voor/nalopen |Behoudt eventuele extra voor- of achterlooptekens en spatietekens. Verwijder deze tekens niet. |
| Voorloop-/naloopnullen bijsnijden |Verwijder voorloop- of achterlooptekens en spatietekens. |
| Scheidingsbeleid slepen |Trailing separators genereren. <p>Selecteer **Niet toegestaan** om trailing-scheidingstekens en scheidingstekens in de ontvangen uitwisseling te verbieden. Als de uitwisseling trailing limiters en separatoren heeft, wordt de uitwisseling niet geldig verklaard. <p>Selecteer **Optioneel** om knooppunten te accepteren met of zonder trailing-scheidingstekens en scheidingstekens. <p>Selecteer **Verplicht** wanneer de ontvangen uitwisseling achterliggende scheidingstekens en scheidingstekens moet hebben. |

### <a name="internal-settings"></a>Interne instellingen

| Eigenschap | Beschrijving |
| --- | --- |
| Lege XML-tags maken als trailing separators zijn toegestaan |Schakel dit selectievakje in om de afzender van de uitwisseling lege XML-tags voor slepende scheidingstekens op te laten staan. |
| Split interchange als transactiesets - transactiesets op fout opschorten|Parseert elke transactie die is ingesteld in een uitwisseling in een afzonderlijk XML-document door de juiste envelop toe te passen op de transactieset. Alleen de transactiesets opschorten die niet-validatie uitvoeren. |
| Split Interchange als transactiesets - knooppunt opfout opschorten|Parseert elke transactie die in een uitwisseling is ingesteld in een afzonderlijk XML-document door de juiste envelop toe te passen. De volledige uitwisseling opschorten wanneer een of meer transacties in de validatie van de uitwisselingsfail. | 
| Knooppunt behouden - transactiesets op fout opschorten |Hiermee blijft de uitwisseling intact, maakt u een XML-document voor de volledige batch-uitwisseling. Alleen de transactiesets die niet-validatie uitvoeren, onderbreken terwijl alle andere transactiesets worden verwerkt. |
| Interchange behouden - knooppunt op fout opschorten |Hiermee blijft de uitwisseling intact, maakt u een XML-document voor de volledige batch-uitwisseling. De volledige uitwisseling opschorten wanneer een of meer transacties in de validatie van de uitwisselingsfail. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configureren hoe uw overeenkomst berichten verzendt

U configureren hoe deze overeenkomst uitgaande berichten identificeert en verwerkt die u via deze overeenkomst naar uw partners verzendt.

1.  Selecteer **Onder Toevoegen**de optie Instellingen **verzenden**.
Configureer deze eigenschappen op basis van uw overeenkomst met uw partner die berichten met u uitwisselt. Zie de tabellen in deze sectie voor eigenschapbeschrijvingen.

    **Verzendinstellingen** zijn ingedeeld in deze secties: Id's, Bevestiging, Schema's, Enveloppen, Tekensets en Scheidingstekens, Controlenummers en Validaties.

    !['Instellingen verzenden' configureren](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Nadat u klaar bent, moet u uw instellingen opslaan door **OK**te kiezen.

Nu is uw overeenkomst klaar om uitgaande berichten te verwerken die voldoen aan de geselecteerde instellingen.

### <a name="identifiers"></a>Identificatiemiddelen

| Eigenschap | Beschrijving |
| --- | --- |
| UNB1.2 (Syntaxisversie) |Selecteer een waarde tussen **1** en **4**. |
| UNB2.3 (Afzender Reverse Routing Address) |Voer een alfanumerieke waarde in met minimaal één teken en maximaal 14 tekens. |
| UNB3.3 (Reverse Routing Address Recipient Reverse Routing) |Voer een alfanumerieke waarde in met minimaal één teken en maximaal 14 tekens. |
| UNB6.1 (wachtwoord voor geadresseerden) |Voer een alfanumerieke waarde in met een minimum van één en maximaal 14 tekens. |
| UNB6.2 (Referentiekwalificatie voor ontvangers) |Voer een alfanumerieke waarde in met minimaal één teken en maximaal twee tekens. |
| UNB7 (referentie-id voor toepassingen) |Voer een alfanumerieke waarde in met minimaal één teken en maximaal 14 tekens |

### <a name="acknowledgment"></a>Erkenning

| Eigenschap | Beschrijving |
| --- | --- |
| Ontvangst van bericht (CONTRL) |Schakel dit selectievakje in als de gehoste partner een technische bevestiging (CONTRL) verwacht te ontvangen. Deze instelling geeft aan dat de gehoste partner, die het bericht verzendt, een bevestiging van de gastpartner aanvraagt. |
| Erkenning (CONTRL) |Schakel dit selectievakje in als de gehoste partner een functionele (CONTRL)-bevestiging verwacht. Deze instelling geeft aan dat de gehoste partner, die het bericht verzendt, een bevestiging van de gastpartner aanvraagt. |
| SG1/SG4-lus genereren voor geaccepteerde transactiesets |Als u ervoor kiest om een functionele bevestiging aan te vragen, schakelt u dit selectievakje in om het genereren van SG1/SG4-lussen te forceren in functionele CONTRL-bevestigingen voor geaccepteerde transactiesets. |

### <a name="schemas"></a>Schema 's

| Eigenschap | Beschrijving |
| --- | --- |
| UNH2.1 (TYPE) |Selecteer een type transactieset. |
| UNH2.2 (VERSIE) |Voer het versienummer van het bericht in. |
| UNH2.3 (RELEASE) |Voer het releasenummer van het bericht in. |
| SCHEMA |Selecteer het te gebruiken schema. Schema's bevinden zich in uw integratieaccount. Als u toegang wilt krijgen tot uw schema's, koppelt u eerst uw integratieaccount aan uw Logic-app. |

### <a name="envelopes"></a>Enveloppen

| Eigenschap | Beschrijving |
| --- | --- |
| UNB8 (Priority Code verwerken) |Voer een alfabetische waarde in die niet meer dan één teken lang is. |
| UNB10 (communicatieovereenkomst) |Voer een alfanumerieke waarde in met minimaal één teken en maximaal 40 tekens. |
| UNB11 (testindicator) |Schakel dit selectievakje in om aan te geven dat de gegenereerde uitwisseling testgegevens zijn |
| UNA-segment toepassen (servicetekenreeksadvies) |Schakel dit selectievakje in om een UNA-segment te genereren voor de uitwisseling die moet worden verzonden. |
| Ung-segmenten toepassen (koptekst van functiegroep) |Schakel dit selectievakje in om groeperingssegmenten te maken in de functionele groepskop in de berichten die naar de gastpartner worden verzonden. De volgende waarden worden gebruikt om de UNG-segmenten te maken: <p>Voer voor **UNG1**een alfanumerieke waarde in met minimaal één teken en maximaal zes tekens. <p>Voer voor **UNG2.1**een alfanumerieke waarde in met een minimum van één teken en maximaal 35 tekens. <p>Voer **voor UNG2.2**een alfanumerieke waarde in met een maximum van vier tekens. <p>Voer **voor UNG3.1**een alfanumerieke waarde in met een minimum van één teken en maximaal 35 tekens. <p>Voer **voor UNG3.2**een alfanumerieke waarde in met een maximum van vier tekens. <p>Voer voor **UNG6**een alfanumerieke waarde in met een minimum van één en maximaal drie tekens. <p>Voer **voor UNG7.1**een alfanumerieke waarde in met minimaal één teken en maximaal drie tekens. <p>Voer **voor UNG7.2**een alfanumerieke waarde in met minimaal één teken en maximaal drie tekens. <p>Voer **voor UNG7.3**een alfanumerieke waarde in met een minimum van 1 teken en maximaal 6 tekens. <p>Voer voor **UNG8**een alfanumerieke waarde in met minimaal één teken en maximaal 14 tekens. |

### <a name="character-sets-and-separators"></a>Tekensets en scheidingstekens

Anders dan de tekenset u een andere set scheidingstekens invoeren die voor elk berichttype moeten worden gebruikt. Als een tekenset niet is opgegeven voor een bepaald berichtschema, wordt de standaardtekenset gebruikt.

| Eigenschap | Beschrijving |
| --- | --- |
| UNB1.1 (systeem-id) |Selecteer de EDIFACT-tekenset die moet worden toegepast op de uitgaande uitwisseling. |
| Schema |Selecteer een schema in de vervolgkeuzelijst. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe rij toegevoegd. Selecteer voor het geselecteerde schema de scheidingstekens die u wilt gebruiken op basis van de onderstaande scheidingsbeschrijvingen. |
| Invoertype |Selecteer een invoertype in de vervolgkeuzelijst. |
| Componentscheidingsteken |Als u samengestelde gegevenselementen wilt scheiden, voert u één teken in. |
| Scheidingsteken van gegevenselement |Als u eenvoudige gegevenselementen wilt scheiden in samengestelde gegevenselementen, voert u één teken in. |
| Segment Terminator |Als u het einde van een EDI-segment wilt aangeven, voert u één teken in. |
| Achtervoegsel |Selecteer het teken dat wordt gebruikt met de segment-id. Als u een achtervoegsel aanwijst, kan het segmentterminator-gegevenselement leeg zijn. Als de segmentterminator leeg blijft, moet u een achtervoegsel aanwijzen. |

### <a name="control-numbers"></a>Controlenummers

| Eigenschap | Beschrijving |
| --- | --- |
| UNB5 (Interchange Control Number) |Voer een voorvoegsel, een bereik van waarden voor het knooppuntcontrolenummer en een achtervoegsel in. Deze waarden worden gebruikt om een uitgaande uitwisseling te genereren. Het voorvoegsel en het achtervoegsel zijn optioneel, terwijl het controlenummer vereist is. Het besturingselementnummer wordt verhoogd voor elk nieuw bericht; het voorvoegsel en het achtervoegsel blijven hetzelfde. |
| UNG5 (groepscontrolenummer) |Voer een voorvoegsel, een bereik van waarden voor het knooppuntcontrolenummer en een achtervoegsel in. Deze waarden worden gebruikt om het groepsbesturingselementnummer te genereren. Het voorvoegsel en het achtervoegsel zijn optioneel, terwijl het controlenummer vereist is. Het besturingselementnummer wordt voor elk nieuw bericht verhoogd totdat de maximale waarde is bereikt; het voorvoegsel en het achtervoegsel blijven hetzelfde. |
| UNH1 (referentienummer van de berichtkop) |Voer een voorvoegsel, een bereik van waarden voor het knooppuntcontrolenummer en een achtervoegsel in. Deze waarden worden gebruikt om het referentienummer van de berichtkop te genereren. Het voorvoegsel en het achtervoegsel zijn optioneel, terwijl het referentienummer vereist is. Het referentienummer wordt verhoogd voor elk nieuw bericht; het voorvoegsel en het achtervoegsel blijven hetzelfde. |

### <a name="validation"></a>Validatie

Wanneer u elke validatierij voltooit, wordt er automatisch een andere toegevoegd. Als u geen regels opgeeft, gebruikt validatie de rij Standaard.

| Eigenschap | Beschrijving |
| --- | --- |
| Berichttype |Selecteer het type EDI-bericht. |
| EDI-validatie |Voer EDI-validatie uit op gegevenstypen zoals gedefinieerd door de EDI-eigenschappen van het schema, lengtebeperkingen, lege gegevenselementen en trailing-scheidingstekens. |
| Uitgebreide validatie |Als het gegevenstype geen EDI is, bevindt validatie zich op de vereiste gegevenselement en is herhaling, opsommingen en validatie van gegevenselementen (min/max) toegestaan. |
| Toestaan Dat nullen voor/nalopen |Behoudt eventuele extra voor- of achterlooptekens en spatietekens. Verwijder deze tekens niet. |
| Voorloop-/naloopnullen bijsnijden |Verwijder voorloop- of achterloopnultekens. |
| Scheidingsbeleid slepen |Trailing separators genereren. <p>Selecteer **Niet toegestaan** om trailing-limiters en scheidingstekens in de verzonden uitwisseling te verbieden. Als de uitwisseling trailing limiters en separatoren heeft, wordt de uitwisseling niet geldig verklaard. <p>Selecteer **Optioneel** om knooppunten te verzenden met of zonder trailing-scheidingstekens en scheidingstekens. <p>Selecteer **Verplicht** als het verzonden knooppunt achterliggende scheidingstekens en scheidingstekens moet hebben. |

## <a name="find-your-created-agreement"></a>Uw gemaakte overeenkomst zoeken

1.  Nadat u al uw overeenkomsteigenschappen hebt ingesteld, kiest u op de pagina **Toevoegen** **ok** om uw overeenkomst te voltooien en terug te keren naar uw integratieaccount.

    Uw nieuw toegevoegde overeenkomst wordt nu weergegeven in uw **lijst met overeenkomsten.**

2.  U uw overeenkomsten ook bekijken in het overzicht van uw integratieaccount. Kies in het menu Van uw integratieaccount **overzicht**en selecteer vervolgens de tegel **Overeenkomsten.** 

    ![De tegel 'Overeenkomsten' kiezen](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/edifact/)de connector voor meer technische details over deze connector, zoals acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)