---
title: X12 berichten voor B2B-integratie
description: Exchange X12-berichten in EDI-indeling voor B2B Enter prise integration in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: cbf0a1f033ddafc68debab8de26dff29d73cc98e
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651471"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-berichten voor B2B Enter prise integration in Azure Logic Apps met Enterprise Integration Pack

Voordat u X12-berichten kunt uitwisselen voor Azure Logic Apps, moet u een X12-overeenkomst maken en die overeenkomst opslaan in uw integratie account. Hier volgen de stappen voor het maken van een X12-overeenkomst.

> [!NOTE]
> Op deze pagina worden de X12-functies voor Azure Logic Apps beschreven. Zie [EDIFACT](logic-apps-enterprise-integration-edifact.md)voor meer informatie.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die u nodig hebt:

* Een [integratie account](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en gekoppeld aan uw Azure-abonnement
* Ten minste twee [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) die zijn gedefinieerd in uw integratie account en zijn geconfigureerd met de X12-id onder **Business Identities**    
* Een vereist [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dat u kunt uploaden naar uw integratie account

Nadat u [een integratie account hebt gemaakt](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [partners hebt toegevoegd](logic-apps-enterprise-integration-partners.md)en een [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) hebt dat u wilt gebruiken, kunt u een X12-overeenkomst maken door de volgende stappen uit te voeren.

## <a name="create-an-x12-agreement"></a>Een X12-overeenkomst maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com "Azure-portal"). 

2. Selecteer in het hoofd menu van Azure **alle services**. 
   Voer in het zoekvak ' Integration ' in en selecteer vervolgens **integratie accounts**.  

   ![Uw integratie account zoeken](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Als **alle services** niet worden weer gegeven, moet u het menu mogelijk eerst uitbreiden. Selecteer boven in het samengevouwen menu de optie **menu weer geven**.

3. Onder **integratie accounts**selecteert u het integratie account waaraan u de overeenkomst wilt toevoegen.

   ![Integratie account selecteren waar de overeenkomst moet worden gemaakt](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Selecteer **overzicht**en selecteer vervolgens de tegel **overeenkomsten** . 
   Als u geen tegel overeenkomsten hebt, voegt u eerst de tegel toe. 

   ![Kies de tegel ' overeenkomsten '](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Klik onder **overeenkomsten**op **toevoegen**.

   ![Kies toevoegen](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Voer onder **toevoegen**een **naam** in voor uw overeenkomst. 
   Selecteer **X12**voor het type overeenkomst. 
   Selecteer de **host-partner**, de **identiteit**van de host, de **gast partner**en de **gast identiteit** voor uw overeenkomst. 
   Zie de tabel in deze stap voor meer informatie over de eigenschap.

    ![Details van overeenkomst opgeven](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Eigenschap | Beschrijving |
    | --- | --- |
    | Name |Naam van de overeenkomst |
    | Type overeenkomst | Moet X12 zijn |
    | Host-partner |Een overeenkomst heeft zowel een host-als een gast partner nodig. De host-partner vertegenwoordigt de organisatie die de overeenkomst configureert. |
    | Host-id |Een id voor de host-partner |
    | Gast partner |Een overeenkomst heeft zowel een host-als een gast partner nodig. De gast partner vertegenwoordigt de organisatie die zakendoet met de host-partner. |
    | Gast identiteit |Een id voor de gast partner |
    | Instellingen voor ontvangen |Deze eigenschappen zijn van toepassing op alle berichten die door een overeenkomst worden ontvangen. |
    | Instellingen verzenden |Deze eigenschappen zijn van toepassing op alle berichten die door een overeenkomst worden verzonden. |  

   > [!NOTE]
   > De oplossing van de X12-overeenkomst is afhankelijk van de kwalificatie en id van de afzender en de ontvanger en id die zijn gedefinieerd in de partner en het inkomende bericht. Als deze waarden worden gewijzigd voor uw partner, werkt u de overeenkomst ook bij.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configureren hoe uw overeenkomst ontvangen berichten verwerkt

Nu u de eigenschappen van de overeenkomst hebt ingesteld, kunt u configureren hoe deze overeenkomst inkomende berichten die van uw partner worden ontvangen via deze overeenkomst identificeren en verwerken.

1.  Onder **toevoegen**selecteert u **instellingen voor ontvangen**.
Configureer deze eigenschappen op basis van uw overeenkomst met de partner die berichten met u uitwisselt. Zie voor beschrijvingen van eigenschappen de tabellen in deze sectie.

    **Ontvangst-instellingen** zijn ingedeeld in de volgende secties: Id's, bevestiging, Schema's, enveloppen, controle nummers, validaties en interne instellingen.

2. Wanneer u klaar bent, slaat u de instellingen op door **OK**te kiezen.

U bent nu klaar om inkomende berichten af te handelen die overeenkomen met de geselecteerde instellingen.

### <a name="identifiers"></a>Identifiers

![Id-eigenschappen instellen](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Eigenschap | Beschrijving |
| --- | --- |
| ISA1 (autorisatie kwalificatie) |Selecteer de waarde voor autorisatie kwalificatie in de vervolg keuzelijst. |
| ISA2 |Optioneel. Voer de waarde voor autorisatie gegevens in. Als de waarde die u hebt ingevoerd voor ISA1 andere dan 00, voert u Mini maal één alfanumeriek teken en Maxi maal 10 in. |
| ISA3 (beveiligings kwalificatie) |Selecteer de waarde voor de beveiligings kwalificatie in de vervolg keuzelijst. |
| ISA4 |Optioneel. Geef de waarde voor de beveiligings gegevens op. Als de waarde die u hebt ingevoerd voor ISA3 andere dan 00, voert u Mini maal één alfanumeriek teken en Maxi maal 10 in. |

### <a name="acknowledgment"></a>Bevestiging

![Eigenschappen voor bevestiging instellen](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| TA1 verwacht |Retourneert een technische bevestiging van de verzender van het uitwisselings certificaat |
| FA verwacht |Retourneert een functionele bevestiging van de verzender van het uitwisselings certificaat. Selecteer vervolgens of u de 997-of 999-bevestigingen wilt, op basis van de schema versie |
| AK2/IK2-lus toevoegen |Hiermee kunnen AK2-lussen worden gegenereerd in functionele bevestigingen voor geaccepteerde transactie sets |

### <a name="schemas"></a>Schema's

Selecteer een schema voor elk transactie type (ST1) en Sender Application (GS2). De receive-pijp lijn ontleedt het inkomende bericht door te voldoen aan de waarden voor ST1 en GS2 in het inkomende bericht met de waarden die u hier instelt, en het schema van het inkomende bericht met het schema dat u hier hebt ingesteld.

![Schema selecteren](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Version |De X12-versie selecteren |
| Transactie type (ST01) |Het transactie type selecteren |
| Sender Application (GS02) |De toepassing voor de afzender selecteren |
| Schema |Selecteer het schema bestand dat u wilt gebruiken. Schema's worden toegevoegd aan uw integratie account. |

> [!NOTE]
> Configureer het vereiste [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dat is geüpload naar uw [integratie account](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Enveloppen

![Geef het scheidings teken in een transactieset op: Kies een standaard-id of herhalings scheidings teken](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Eigenschap | Beschrijving |
| --- | --- |
| ISA11 gebruik |Hiermee geeft u het scheidings teken moet worden gebruikt in een transactieset: <p>Selecteer **standaard-id** voor het gebruik van een punt (.) voor de decimale notatie, in plaats van de decimale notatie van het inkomende document in de EDI-ontvangst pijplijn. <p>Selecteer **herhalings scheidings teken** om het scheidings teken voor herhalingen van een eenvoudig gegevens element of een herhaalde gegevens structuur op te geven. Meestal wordt de dakje (^) gebruikt als scheidings teken voor herhalingen. Voor HIPAA-schema's kunt u alleen de dakje gebruiken. |

### <a name="control-numbers"></a>Controle nummers

![Selecteren hoe dubbele controle nummers moeten worden verwerkt](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Dubbele uitwisselings controle nummers niet toestaan |Dubbele interwijzigingen blok keren. Controleert het uitwisselings controle nummer (ISA13) voor het ontvangen Interchange Control-nummer. Als er een overeenkomst wordt gedetecteerd, wordt de uitwisseling niet verwerkt door de receiver-pijp lijn. U kunt het aantal dagen opgeven voor het uitvoeren van de controle door een waarde op te geven voor het *controleren op dubbele ISA13 elke (dagen)* . |
| Dubbele items van groeps controle nummers niet toestaan |Interwijzigingen met dubbele groeps controle nummers blok keren. |
| Geen duplicaten voor het instellen van transactie sets toestaan |Interacties blok keren met dubbele trans acties set control numbers. |

### <a name="validation"></a>Validatie

![Validatie-eigenschappen voor ontvangen berichten instellen](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Wanneer u elke validatie rij voltooit, wordt er automatisch een andere toegevoegd. Als u geen regels opgeeft, wordt met de validatie de rij ' default ' gebruikt.

| Eigenschap | Beschrijving |
| --- | --- |
| Berichttype |Selecteer het type EDI-bericht. |
| EDI-validatie |Voer EDI-validatie uit op gegevens typen zoals gedefinieerd door de EDI-eigenschappen, lengte beperkingen, lege gegevens elementen en navolgende scheidings tekens van het schema. |
| Uitgebreide validatie |Als het gegevens type niet EDI is, is validatie van de vereiste voor het gegevens element en de toegestane herhaling, opsommingen en gegevens element lengte validatie (min/max). |
| Voor loop-en volg nullen toestaan |Bewaar eventuele extra voor loop-of volg spaties. Verwijder deze tekens niet. |
| Voor loop-en volg nullen bijsnijden |Voor loop-of volg spaties verwijderen. |
| Volg scheidings beleid |Navolgende scheidings tekens genereren. <p>Selecteer **niet toegestaan** om navolgende scheidings tekens en schei in de ontvangen uitwisseling te verbieden. Als de uitwisseling navolgende scheidings tekens en schei heeft, wordt de uitwisseling gedeclareerd als ongeldig. <p>Selecteer **optioneel** om wissels met of zonder navolgende scheidings tekens en schei te accepteren. <p>Selecteer **verplicht** wanneer het uitwisselings-en scheidings tekens moeten worden gevolgd. |

### <a name="internal-settings"></a>Interne instellingen

![Interne instellingen selecteren](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| De geïmpliceerde decimale notatie "nn" converteren naar een numerieke waarde met grondtal 10 |Converteert een EDI-nummer dat is opgegeven met de notatie "nn" in een numerieke waarde van grondtal 10 |
| Lege XML-tags maken als navolgende scheidings tekens zijn toegestaan |Schakel dit selectie vakje in als u wilt dat de verzender van de uitwisseling lege XML-codes bevat voor afsluitende scheidings tekens. |
| Gesplitste uitwisseling als transactie sets-transactie sets uitstellen bij fout|Parseert elke transactie set in een uitwisseling in een afzonderlijk XML-document door de juiste envelop toe te passen op de transactieset. Hiermee worden alleen de trans acties onderbroken waarbij de validatie mislukt. |
| Gesplitste uitwisseling als transactie sets: uitwisseling onderbreken bij fout|Parseert elke transactie set in een uitwisseling in een afzonderlijk XML-document door de juiste envelop toe te passen. Hiermee wordt het hele uitwisselings proces onderbroken wanneer een of meer transactie sets in de uitwisselings fout worden gevalideerd. | 
| Trans actie sets met uitwisselingen behouden bij fout |Laat de uitwisseling intact, maakt een XML-document voor de gehele gebatcheerde uitwisseling. Suspendeert alleen de transactie sets die niet zijn gevalideerd, terwijl alle andere transactie sets worden verwerkt. |
| Uitwisseling van uitwisseling met fouten behouden |Laat de uitwisseling intact, maakt een XML-document voor de gehele gebatcheerde uitwisseling. Hiermee wordt het hele uitwisselings proces onderbroken wanneer een of meer transactie sets in de uitwisseling mislukt. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configureren hoe uw overeenkomst berichten verzendt

U kunt configureren hoe deze overeenkomst uitgaande berichten identificeert en verwerkt die u via deze overeenkomst naar uw partner verzendt.

1.  Onder **toevoegen**selecteert u **instellingen verzenden**.
Configureer deze eigenschappen op basis van uw overeenkomst met uw partner die berichten met u uitwisselt. Zie voor beschrijvingen van eigenschappen de tabellen in deze sectie.

    De **instellingen voor verzenden** zijn onderverdeeld in de volgende secties: Id's, bevestiging, Schema's, enveloppen, teken sets en scheidings tekens, controle nummers en validatie.

2. Wanneer u klaar bent, slaat u de instellingen op door **OK**te kiezen.

Uw overeenkomst is nu klaar voor het afhandelen van uitgaande berichten die voldoen aan de geselecteerde instellingen.

### <a name="identifiers"></a>Identifiers

![Id-eigenschappen instellen](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Eigenschap | Beschrijving |
| --- | --- |
| Autorisatie kwalificatie (ISA1) |Selecteer de waarde voor autorisatie kwalificatie in de vervolg keuzelijst. |
| ISA2 |Voer de waarde voor autorisatie gegevens in. Als deze waarde niet gelijk is aan 00, voert u Mini maal één alfanumeriek teken en Maxi maal 10 in. |
| Beveiligings kwalificatie (ISA3) |Selecteer de waarde voor de beveiligings kwalificatie in de vervolg keuzelijst. |
| ISA4 |Geef de waarde voor de beveiligings gegevens op. Als deze waarde niet gelijk is aan 00, voor het tekstvak value (ISA4), voert u Mini maal één alfanumerieke waarde en Maxi maal 10 in. |

### <a name="acknowledgment"></a>Bevestiging

![Eigenschappen voor bevestiging instellen](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Eigenschap | Beschrijving |
| --- | --- |
| TA1 verwacht |Een technische bevestiging (TA1) retour neren aan de verzender van het uitwisselings certificaat. Met deze instelling geeft u op dat de host-partner die het bericht verzendt, een bevestiging vraagt van de gast partner in de overeenkomst. Deze bevestigingen worden verwacht door de host-partner op basis van de ontvangst instellingen van de overeenkomst. |
| FA verwacht |Een functie-bevestigingen (FA) retour neren naar de verzender van het uitwisselings certificaat. Selecteer of u de 997-of 999-bevestigingen wilt, op basis van de schema versies waarmee u werkt. Deze bevestigingen worden verwacht door de host-partner op basis van de ontvangst instellingen van de overeenkomst. |
| VA-versie |De VA-versie selecteren |

### <a name="schemas"></a>Schema's

![Te gebruiken schema selecteren](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Eigenschap | Beschrijving |
| --- | --- |
| Version |De X12-versie selecteren |
| Transactie type (ST01) |Het transactie type selecteren |
| SCHEMA |Selecteer het schema dat u wilt gebruiken. Schema's bevinden zich in uw integratie account. Als u eerst schema selecteert, worden de versie en het transactie type automatisch geconfigureerd  |

> [!NOTE]
> Configureer het vereiste [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dat is geüpload naar uw [integratie account](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Enveloppen

![Geef het scheidings teken in een transactieset op: Kies een standaard-id of herhalings scheidings teken](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| ISA11 gebruik |Hiermee geeft u het scheidings teken moet worden gebruikt in een transactieset: <p>Selecteer **standaard-id** voor het gebruik van een punt (.) voor de decimale notatie, in plaats van de decimale notatie van het inkomende document in de EDI-ontvangst pijplijn. <p>Selecteer **herhalings scheidings teken** om het scheidings teken voor herhalingen van een eenvoudig gegevens element of een herhaalde gegevens structuur op te geven. Meestal wordt de dakje (^) gebruikt als scheidings teken voor herhalingen. Voor HIPAA-schema's kunt u alleen de dakje gebruiken. |

### <a name="control-numbers"></a>Controle nummers

![Eigenschappen van controle nummers opgeven](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Controle versie nummer (ISA12) |Selecteer de versie van de X12-standaard |
| Gebruiks indicator (ISA15) |Selecteer de context van een uitwisseling.  De waarden zijn informatie, productie gegevens of test gegevens |
| Schema |Genereert de GS-en ST-segmenten voor een met X12 gecodeerde uitwisseling die wordt verzonden naar de verzendende pijp lijn |
| GS1 |Optioneel, selecteer een waarde voor de functie code in de vervolg keuzelijst |
| GS2 |Optioneel, afzender van de toepassing |
| GS3 |Optioneel, ontvanger van toepassing |
| GS4 |Optioneel, selecteer CCYYMMDD of JJMMDD |
| GS5 |Optioneel, selecteer HHMM, HHMMSS of HHMMSSdd |
| GS7 |Optioneel, selecteer een waarde voor de verantwoordelijke instantie in de vervolg keuzelijst |
| GS8 |Optionele versie van het document |
| Uitwisselings controle nummer (ISA13) |Vereist, voer een bereik van waarden in voor het uitwisselings controle nummer. Voer een numerieke waarde in met mini maal 1 en Maxi maal 999999999 |
| Groeps controle nummer (GS06) |Vereist, voer een bereik van getallen in voor het groeps controle nummer. Voer een numerieke waarde in met mini maal 1 en Maxi maal 999999999 |
| ST02 (trans actie set Control Number) |Vereist, voer een bereik van getallen in voor het besturings nummer van de Transactieset. Voer een bereik met numerieke waarden in met mini maal 1 en Maxi maal 999999999 |
| Beleids |Optioneel, opgegeven voor het bereik van de trans actie besturings nummers die worden gebruikt bij bevestiging. Voer een numerieke waarde in voor de middelste twee velden en een alfanumerieke waarde (indien gewenst) voor het voor voegsel en de achtervoegsel velden. De middelste velden zijn vereist en bevatten de minimum-en maximum waarden voor het controle nummer |
| Achtervoegsel |Optioneel, opgegeven voor het bereik van de besturings getallen voor trans actie sets die worden gebruikt in een bevestiging. Voer een numerieke waarde in voor de middelste twee velden en een alfanumerieke waarde (indien gewenst) voor het voor voegsel en de achtervoegsel velden. De middelste velden zijn vereist en bevatten de minimum-en maximum waarden voor het controle nummer |

### <a name="character-sets-and-separators"></a>Teken sets en scheidings tekens

Met uitzonde ring van de tekenset, kunt u een andere set scheidings tekens voor elk bericht type opgeven. Als er geen tekenset is opgegeven voor een bepaald bericht schema, wordt de standaard tekenset gebruikt.

![Scheidings tekens voor bericht typen opgeven](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Eigenschap | Beschrijving |
| --- | --- |
| Tekenset die moet worden gebruikt |Als u de eigenschappen wilt valideren, selecteert u de X12 teken reeks. De opties zijn Basic, Extended en UTF8. |
| Schema |Selecteer een schema in de vervolg keuzelijst. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe rij toegevoegd. Voor het geselecteerde schema selecteert u de scheidings tekens die u wilt gebruiken, op basis van de onderstaande scheidings tekens. |
| Invoer type |Selecteer een invoer type in de vervolg keuzelijst. |
| Onderdeel scheidings teken |Voer één teken in om samengestelde gegevens elementen van elkaar te scheiden. |
| Scheidings teken voor gegevens elementen |Als u eenvoudige gegevens elementen in samengestelde gegevens elementen wilt scheiden, voert u één teken in. |
| Vervangings teken |Voer een vervangings teken in dat wordt gebruikt om alle scheidings tekens in de payloadgegevens te vervangen wanneer het uitgaande X12-bericht wordt gegenereerd. |
| Segment Terminator |Voer één teken in om het einde van een EDI-segment aan te geven. |
| Achtervoegsel |Selecteer het teken dat wordt gebruikt met de segment-id. Als u een achtervoegsel aanwijst, kan het gegevens element van het segment Terminator leeg zijn. Als de eind afsluit van het segment leeg blijft, moet u een achtervoegsel aanwijzen. |

> [!TIP]
> Als u speciale teken waarden wilt opgeven, bewerkt u de overeenkomst als JSON en geeft u de ASCII-waarde voor het speciale teken op.

### <a name="validation"></a>Validatie

![Validatie-eigenschappen instellen voor het verzenden van berichten](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Wanneer u elke validatie rij voltooit, wordt er automatisch een andere toegevoegd. Als u geen regels opgeeft, wordt met de validatie de rij ' default ' gebruikt.

| Eigenschap | Beschrijving |
| --- | --- |
| Berichttype |Selecteer het type EDI-bericht. |
| EDI-validatie |Voer EDI-validatie uit op gegevens typen zoals gedefinieerd door de EDI-eigenschappen, lengte beperkingen, lege gegevens elementen en navolgende scheidings tekens van het schema. |
| Uitgebreide validatie |Als het gegevens type niet EDI is, is validatie van de vereiste voor het gegevens element en de toegestane herhaling, opsommingen en gegevens element lengte validatie (min/max). |
| Voor loop-en volg nullen toestaan |Bewaar eventuele extra voor loop-of volg spaties. Verwijder deze tekens niet. |
| Voor loop-en volg nullen bijsnijden |Voor loop-of volg spaties verwijderen. |
| Volg scheidings beleid |Navolgende scheidings tekens genereren. <p>Selecteer **niet toegestaan** om het afsluiten van de scheidings tekens en schei ding van de gezonden uitwisseling te verhinderen. Als de uitwisseling navolgende scheidings tekens en schei heeft, wordt de uitwisseling gedeclareerd als ongeldig. <p>Selecteer **optioneel** om wissels met of zonder navolgende scheidings tekens en schei dingen te verzenden. <p>Selecteer **verplicht** als het gestuurde uitwisseling moet navolgende scheidings tekens en schei ding. |

## <a name="find-your-created-agreement"></a>Uw gemaakte overeenkomst zoeken

1.  Nadat u alle eigenschappen van de overeenkomst hebt ingesteld, kiest u **OK** op de pagina **toevoegen** om uw overeenkomst te maken en terug te keren naar uw integratie account.

    De zojuist toegevoegde overeenkomst wordt nu weer gegeven in de lijst met **overeenkomsten** .

2.  U kunt uw overeenkomsten ook bekijken in het overzicht van het integratie account. Kies **overzicht**in het menu integratie account en selecteer vervolgens de tegel **overeenkomsten** .

    ![Kies de tegel ' overeenkomsten '](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals acties en limieten zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](https://docs.microsoft.com/connectors/x12/). 

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)