---
title: X12-berichten voor B2B-integratie
description: Exchange X12-berichten in EDI-indeling voor B2B-bedrijfsintegratie in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: cbf0a1f033ddafc68debab8de26dff29d73cc98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651471"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-berichten voor B2B-bedrijfsintegratie in Azure Logic Apps met Enterprise Integration Pack

Voordat u X12-berichten uitwisselen voor Azure Logic Apps, moet u een X12-overeenkomst maken en die overeenkomst opslaan in uw integratieaccount. Hier volgen de stappen voor het maken van een X12-overeenkomst.

> [!NOTE]
> Deze pagina bevat de X12-functies voor Azure Logic Apps. Zie [EDIFACT](logic-apps-enterprise-integration-edifact.md)voor meer informatie.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die je nodig hebt:

* Een [integratieaccount](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en is gekoppeld aan uw Azure-abonnement
* Ten minste twee [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) die zijn gedefinieerd in uw integratieaccount en zijn geconfigureerd met de X12-id onder **Bedrijfsidentiteiten**    
* Een vereist [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dat u uploaden naar uw integratieaccount

Nadat u [een integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)hebt gemaakt, [partners hebt toegevoegd](logic-apps-enterprise-integration-partners.md)en een [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) hebt dat u wilt gebruiken, u een X12-overeenkomst maken door deze stappen te volgen.

## <a name="create-an-x12-agreement"></a>Een X12-overeenkomst maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. Selecteer **alle services**in het hoofdmenu van Azure. 
   Voer in het zoekvak 'integratie' in en selecteer **Integratieaccounts**.  

   ![Uw integratieaccount zoeken](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Als **Alle services** niet worden weergegeven, moet u mogelijk eerst het menu uitbreiden. Selecteer boven aan het samengevouwen menu het **menu Weergeven**.

3. Selecteer **onder Integratieaccounts**het integratieaccount waar u de overeenkomst wilt toevoegen.

   ![Integratieaccount selecteren waar u de overeenkomst maken](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Selecteer **Overzicht**en selecteer vervolgens de tegel **Overeenkomsten.** 
   Als u geen tegel Overeenkomsten hebt, voegt u eerst de tegel toe. 

   ![De tegel 'Overeenkomsten' kiezen](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Kies Onder **Overeenkomsten**de optie **Toevoegen**.

   ![Kies 'Toevoegen'](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Voer **onder Toevoegen**een **naam** in voor uw overeenkomst. 
   Selecteer **X12**voor het type overeenkomst . 
   Selecteer de **hostpartner,** **hostidentiteit,** **gastpartner**en **gastidentiteit** voor uw overeenkomst. 
   Zie de tabel in deze stap voor meer eigendomsdetails.

    ![Details van de overeenkomst verstrekken](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Eigenschap | Beschrijving |
    | --- | --- |
    | Name |Naam van de overeenkomst |
    | Type overeenkomst | Moet X12 zijn |
    | Gastpartner |Een overeenkomst heeft zowel een gast- als gastpartner nodig. De hostpartner vertegenwoordigt de organisatie die de overeenkomst configureert. |
    | Host identiteit |Een id voor de hostpartner |
    | Gastpartner |Een overeenkomst heeft zowel een gast- als gastpartner nodig. De gastpartner vertegenwoordigt de organisatie die zaken doet met de gastpartner. |
    | Gastidentiteit |Een id voor de gastpartner |
    | Instellingen voor ontvangen |Deze eigenschappen zijn van toepassing op alle berichten die door een overeenkomst zijn ontvangen. |
    | Verzendinstellingen |Deze eigenschappen zijn van toepassing op alle berichten die door een overeenkomst worden verzonden. |  

   > [!NOTE]
   > De resolutie van de X12-overeenkomst is afhankelijk van het afstemmen van de kwalificatie en id van de afzender en de kwalificatie en id voor de ontvanger die is gedefinieerd in de partner en het binnenkomende bericht. Als deze waarden voor uw partner veranderen, werkt u de overeenkomst ook bij.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configureren hoe uw overeenkomst met ontvangen berichten omgaat

Nu u de overeenkomsteigenschappen hebt ingesteld, u configureren hoe deze overeenkomst binnenkomende berichten identificeert en verwerkt die via deze overeenkomst van uw partner zijn ontvangen.

1.  Selecteer **Onder Toevoegen**de optie Instellingen **ontvangen**.
Configureer deze eigenschappen op basis van uw overeenkomst met de partner die berichten met u uitwisselt. Zie de tabellen in deze sectie voor eigenschapbeschrijvingen.

    **Instellingen voor ontvangen** is ingedeeld in deze secties: Id's, Bevestiging, Schema's, Enveloppen, Controlenummers, Validaties en Interne instellingen.

2. Nadat u klaar bent, moet u uw instellingen opslaan door **OK**te kiezen.

Nu is uw overeenkomst klaar om binnenkomende berichten te verwerken die voldoen aan de geselecteerde instellingen.

### <a name="identifiers"></a>Identificatiemiddelen

![Eigenschappen van id instellen](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Eigenschap | Beschrijving |
| --- | --- |
| ISA1 (Kwalificatie Kwalificatie Voor autorisatie) |Selecteer de kwalificatiewaarde voor autorisatie in de vervolgkeuzelijst. |
| ISA2 ISA2 |Optioneel. Voer de waarde van autorisatiegegevens in. Als de waarde die u voor ISA1 hebt ingevoerd, anders is dan 00, voert u minimaal één alfanumeriek teken en een maximum van 10 in. |
| ISA3 (Beveiligingskwalificatie) |Selecteer de waarde van de kwalificatievoor beveiligingsingroep in de vervolgkeuzelijst. |
| ISA4 ISA4 |Optioneel. Voer de waarde Beveiligingsgegevens in. Als de waarde die u voor ISA3 hebt ingevoerd, anders is dan 00, voert u minimaal één alfanumeriek teken en een maximum van 10 in. |

### <a name="acknowledgment"></a>Erkenning

![Bevestigingseigenschappen instellen](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| TA1 verwacht |Retourneert een technische bevestiging aan de afzender van de uitwisseling |
| FA verwacht |Retourneert een functionele bevestiging aan de afzender van de uitwisseling. Selecteer vervolgens of u de 997- of 999-bevestigingen wilt, op basis van de schemaversie |
| AK2/IK2-lus opnemen |Maakt het genereren van AK2-lussen mogelijk in functionele bevestigingen voor geaccepteerde transactiesets |

### <a name="schemas"></a>Schema 's

Selecteer een schema voor elk transactietype (ST1) en Afzendertoepassing (GS2). De binnenkomende pijplijn demonteert het binnenkomende bericht door de waarden voor ST1 en GS2 in het binnenkomende bericht te matchen met de waarden die u hier instelt, en het schema van het binnenkomende bericht met het schema dat u hier instelt.

![Schema selecteren](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Versie |De X12-versie selecteren |
| Transactietype (ST01) |Het transactietype selecteren |
| Afzendertoepassing (GS02) |De afzendertoepassing selecteren |
| Schema |Selecteer het schemabestand dat u wilt gebruiken. Schema's worden toegevoegd aan uw integratieaccount. |

> [!NOTE]
> Configureer het vereiste [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dat is geüpload naar uw [integratieaccount.](../logic-apps/logic-apps-enterprise-integration-accounts.md)

### <a name="envelopes"></a>Enveloppen

![De scheidingsteken opgeven in een transactieset: kies Standaard-id of Herhalingsscheidingsscheiding](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Eigenschap | Beschrijving |
| --- | --- |
| ISA11-gebruik |Hiermee geeft u de scheidingsscheiding op die u in een transactieset moet gebruiken: <p>Selecteer **Standaard-id** om een punt (.) te gebruiken voor decimale notatie, in plaats van de decimale notatie van het binnenkomende document in de EDI-ontvangstpijplijn. <p>Selecteer **Herhalingsscheidingsscheiding** om de scheidingsteken op te geven voor herhaalde gebeurtenissen van een eenvoudig gegevenselement of een herhaalde gegevensstructuur. Bijvoorbeeld, meestal het karaat (^) wordt gebruikt als de herhaling separator. Voor HIPAA-schema's u alleen het karaat gebruiken. |

### <a name="control-numbers"></a>Controlenummers

![Selecteer hoe u de dubbele gegevens van het controlenummer verwerken](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Duplicaten van interchange control number weigeren |Dubbele knooppunten blokkeren. Hiermee wordt het interchange control number (ISA13) gecontroleerd op het ontvangen controlenummer. Als een overeenkomst wordt gedetecteerd, wordt de uitwisseling niet verwerkt door de pijplijn ontvangen. U het aantal dagen voor het uitvoeren van de controle opgeven door elke (dagen) een waarde *voor Controleren op dubbele ISA13 te*geven. |
| Groepcontrolenummerduplicaten weigeren |Uitwisselingen blokkeren met dubbele groepscontrolenummers. |
| Dubbele gegevens van transactiesetregelnummer weigeren |Blokkeer knooppunten met dubbele transactiesetcontrolenummers. |

### <a name="validation"></a>Validatie

![Validatie-eigenschappen instellen voor ontvangen berichten](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Wanneer u elke validatierij voltooit, wordt er automatisch een andere toegevoegd. Als u geen regels opgeeft, gebruikt validatie de rij Standaard.

| Eigenschap | Beschrijving |
| --- | --- |
| Berichttype |Selecteer het type EDI-bericht. |
| EDI-validatie |Voer EDI-validatie uit op gegevenstypen zoals gedefinieerd door de EDI-eigenschappen van het schema, lengtebeperkingen, lege gegevenselementen en trailing-scheidingstekens. |
| Uitgebreide validatie |Als het gegevenstype geen EDI is, bevindt validatie zich op de vereiste gegevenselement en is herhaling, opsommingen en validatie van gegevenselementen (min/max) toegestaan. |
| Toestaan Dat nullen voor/nalopen |Behoudt eventuele extra voor- of achterlooptekens en spatietekens. Verwijder deze tekens niet. |
| Voorloop-/naloopnullen bijsnijden |Verwijder voorloop- of achterlooptekens en spatietekens. |
| Scheidingsbeleid slepen |Trailing separators genereren. <p>Selecteer **Niet toegestaan** om trailing-scheidingstekens en scheidingstekens in de ontvangen uitwisseling te verbieden. Als de uitwisseling trailing limiters en separatoren heeft, wordt de uitwisseling niet geldig verklaard. <p>Selecteer **Optioneel** om knooppunten te accepteren met of zonder trailing-scheidingstekens en scheidingstekens. <p>Selecteer **Verplicht** wanneer de uitwisseling achterliggende scheidingstekens en scheidingstekens moet hebben. |

### <a name="internal-settings"></a>Interne instellingen

![Interne instellingen selecteren](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Impliciete decimale notatie Nn converteren naar een basiswaarde van 10. |Converteert een EDI-nummer dat is opgegeven met de opmaak "Nn" in een numerieke waarde basis-10 |
| Lege XML-tags maken als trailing separators zijn toegestaan |Schakel dit selectievakje in om de afzender van de uitwisseling lege XML-tags voor slepende scheidingstekens op te laten staan. |
| Split interchange als transactiesets - transactiesets op fout opschorten|Parseert elke transactie die is ingesteld in een uitwisseling in een afzonderlijk XML-document door de juiste envelop toe te passen op de transactieset. Hiermee worden alleen de transacties opgeschort wanneer de validatie mislukt. |
| Split Interchange als transactiesets - knooppunt opfout opschorten|Parseert elke transactie die in een uitwisseling is ingesteld in een afzonderlijk XML-document door de juiste envelop toe te passen. Hiermee wordt de volledige uitwisseling opgeschort wanneer een of meer transacties worden ingesteld in de validatie van de uitwisselingsfail. | 
| Knooppunt behouden - transactiesets op fout opschorten |Hiermee blijft de uitwisseling intact, maakt u een XML-document voor de volledige batch-uitwisseling. Hiermee worden alleen de transactiesets opschorten die niet-validatie uitvoeren, terwijl alle andere transactiesets worden voortgezet. |
| Interchange behouden - knooppunt op fout opschorten |Hiermee blijft de uitwisseling intact, maakt u een XML-document voor de volledige batch-uitwisseling. Hiermee wordt de volledige uitwisseling opgeschort wanneer een of meer transacties worden ingesteld in de validatie van de uitwisselingsfail. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configureren hoe uw overeenkomst berichten verzendt

U configureren hoe deze overeenkomst uitgaande berichten identificeert en verwerkt die u via deze overeenkomst naar uw partner verzendt.

1.  Selecteer **Onder Toevoegen**de optie Instellingen **verzenden**.
Configureer deze eigenschappen op basis van uw overeenkomst met uw partner die berichten met u uitwisselt. Zie de tabellen in deze sectie voor eigenschapbeschrijvingen.

    **Verzendinstellingen** zijn ingedeeld in deze secties: Id's, Bevestiging, Schema's, Enveloppen, Tekensets en Scheidingstekens, Controlenummers en Validatie.

2. Nadat u klaar bent, moet u uw instellingen opslaan door **OK**te kiezen.

Nu is uw overeenkomst klaar om uitgaande berichten te verwerken die voldoen aan de geselecteerde instellingen.

### <a name="identifiers"></a>Identificatiemiddelen

![Eigenschappen van id instellen](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Eigenschap | Beschrijving |
| --- | --- |
| Kwalificatie voor autorisatie (ISA1) |Selecteer de kwalificatiewaarde voor autorisatie in de vervolgkeuzelijst. |
| ISA2 ISA2 |Voer de waarde van autorisatiegegevens in. Als deze waarde anders is dan 00, voert u ten minste één alfanumeriek teken en een maximum van 10 in. |
| Beveiligingskwalificatie (ISA3) |Selecteer de waarde van de kwalificatievoor beveiligingsingroep in de vervolgkeuzelijst. |
| ISA4 ISA4 |Voer de waarde Beveiligingsgegevens in. Als deze waarde anders is dan 00, voert u voor het tekstvak Waarde (ISA4) een minimum van één alfanumerieke waarde en een maximum van 10 in. |

### <a name="acknowledgment"></a>Erkenning

![Bevestigingseigenschappen instellen](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Eigenschap | Beschrijving |
| --- | --- |
| TA1 verwacht |Geef een technische bevestiging (TA1) terug naar de afzender van de uitwisseling. Met deze instelling geeft u aan dat de hostpartner die het bericht verzendt, een bevestiging aanvraagt van de gastpartner in de overeenkomst. Deze bevestigingen worden verwacht door de hostpartner op basis van de instellingen voor ontvangen van de overeenkomst. |
| FA verwacht |Een functionele bevestiging (FA) terugsturen naar de afzender van de uitwisseling. Selecteer of u de 997- of 999-bevestigingen wilt, op basis van de schemaversies waarmee u werkt. Deze bevestigingen worden verwacht door de hostpartner op basis van de instellingen voor ontvangen van de overeenkomst. |
| FA-versie |De FA-versie selecteren |

### <a name="schemas"></a>Schema 's

![Schema selecteren dat u wilt gebruiken](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Eigenschap | Beschrijving |
| --- | --- |
| Versie |De X12-versie selecteren |
| Transactietype (ST01) |Het transactietype selecteren |
| SCHEMA |Selecteer het te gebruiken schema. Schema's bevinden zich in uw integratieaccount. Als u eerst schema selecteert, configureert het automatisch versie- en transactietype  |

> [!NOTE]
> Configureer het vereiste [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dat is geüpload naar uw [integratieaccount.](../logic-apps/logic-apps-enterprise-integration-accounts.md)

### <a name="envelopes"></a>Enveloppen

![De scheidingsteken opgeven in een transactieset: kies Standaard-id of Herhalingsscheidingsscheiding](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| ISA11-gebruik |Hiermee geeft u de scheidingsscheiding op die u in een transactieset moet gebruiken: <p>Selecteer **Standaard-id** om een punt (.) te gebruiken voor decimale notatie, in plaats van de decimale notatie van het binnenkomende document in de EDI-ontvangstpijplijn. <p>Selecteer **Herhalingsscheidingsscheiding** om de scheidingsteken op te geven voor herhaalde gebeurtenissen van een eenvoudig gegevenselement of een herhaalde gegevensstructuur. Bijvoorbeeld, meestal het karaat (^) wordt gebruikt als de herhaling separator. Voor HIPAA-schema's u alleen het karaat gebruiken. |

### <a name="control-numbers"></a>Controlenummers

![Eigenschappen van besturingselementnummer opgeven](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Versienummer van de besturing (ISA12) |Selecteer de versie van de X12-standaard |
| Gebruiksindicator (ISA15) |Selecteer de context van een uitwisseling.  De waarden zijn informatie, productiegegevens of testgegevens |
| Schema |Hiermee worden de GS- en ST-segmenten gegenereerd voor een X12-gecodeerde uitwisseling die naar de pijplijn verzenden wordt verzonden |
| GS1 |Selecteer optioneel een waarde voor de functionele code in de vervolgkeuzelijst |
| GS2 |Optioneel, afzender van de toepassing |
| GS3 |Optioneel, toepassingsontvanger |
| GS4 |Selecteer optioneel CCYYMMDD of YYMMDD |
| GS5 |Selecteer Optioneel HHMM, HHMMSS of HHMMSSdd |
| GS7 |Selecteer optioneel een waarde voor het verantwoordelijke bureau in de vervolgkeuzelijst |
| GS8 |Optioneel, versie van het document |
| Interchange Control Number (ISA13) |Voer een bereik van waarden in voor het schakelregelnummer. Voer een numerieke waarde in met een minimum van 1 en maximaal 99999999999 |
| Groepscontrolenummer (GS06) |Voer een reeks getallen in voor het groepscontrolenummer. Voer een numerieke waarde in met een minimum van 1 en maximaal 99999999999 |
| Besturingselementnummer transactieset (ST02) |Voer een reeks nummers in voor het besturingselementnummer transactieset. Voer een bereik van numerieke waarden in met een minimum van 1 en maximaal 9999999999 |
| Voorvoegsel |Optioneel, aangewezen voor het bereik van transactiesetcontrolenummers die in bevestiging worden gebruikt. Voer een numerieke waarde in voor de middelste twee velden en een alfanumerieke waarde (indien gewenst) voor de velden voor voorvoegsel en achtervoegsel. De middelste velden zijn vereist en bevatten de minimum- en maximumwaarden voor het besturingselementnummer |
| Achtervoegsel |Optioneel, aangewezen voor het bereik van transactiesetcontrolenummers die in een bevestiging worden gebruikt. Voer een numerieke waarde in voor de middelste twee velden en een alfanumerieke waarde (indien gewenst) voor de velden voor voorvoegsel en achtervoegsel. De middelste velden zijn vereist en bevatten de minimum- en maximumwaarden voor het besturingselementnummer |

### <a name="character-sets-and-separators"></a>Tekensets en scheidingstekens

Anders dan de tekenset u voor elk berichttype een andere set scheidingstekens invoeren. Als een tekenset niet is opgegeven voor een bepaald berichtschema, wordt de standaardtekenset gebruikt.

![Scheidingstekens opgeven voor berichttypen](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Tekenset die moet worden gebruikt |Als u de eigenschappen wilt valideren, selecteert u de tekenset X12. De opties zijn Basic, Extended en UTF8. |
| Schema |Selecteer een schema in de vervolgkeuzelijst. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe rij toegevoegd. Selecteer voor het geselecteerde schema de scheidingstekens die u wilt gebruiken op basis van de onderstaande scheidingsbeschrijvingen. |
| Invoertype |Selecteer een invoertype in de vervolgkeuzelijst. |
| Componentscheidingsteken |Als u samengestelde gegevenselementen wilt scheiden, voert u één teken in. |
| Scheidingsteken van gegevenselement |Als u eenvoudige gegevenselementen wilt scheiden in samengestelde gegevenselementen, voert u één teken in. |
| Vervangend teken |Voer een vervangend teken in dat wordt gebruikt voor het vervangen van alle scheidingstekens in de payloadgegevens bij het genereren van het uitgaande X12-bericht. |
| Segment Terminator |Als u het einde van een EDI-segment wilt aangeven, voert u één teken in. |
| Achtervoegsel |Selecteer het teken dat wordt gebruikt met de segment-id. Als u een achtervoegsel aanwijst, kan het segmentterminator-gegevenselement leeg zijn. Als de segmentterminator leeg blijft, moet u een achtervoegsel aanwijzen. |

> [!TIP]
> Als u speciale tekenwaarden wilt opgeven, bewerkt u de overeenkomst als JSON en geeft u de ASCII-waarde voor het speciale teken.

### <a name="validation"></a>Validatie

![Validatie-eigenschappen instellen voor het verzenden van berichten](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

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

    ![De tegel 'Overeenkomsten' kiezen](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/x12/)de connector voor meer technische details over deze connector, zoals acties en limieten zoals beschreven in het Swagger-bestand van de connector. 

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)