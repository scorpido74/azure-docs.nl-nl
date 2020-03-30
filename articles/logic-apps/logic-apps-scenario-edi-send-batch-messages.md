---
title: Edi-berichten als groep batchverwerken
description: EDI-berichten verzenden en ontvangen als batches, groepen of verzamelingen met batchverwerking in Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666666"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>EDI-berichten uitwisselen als batches of groepen tussen handelspartners in Azure Logic Apps

In business to business (B2B) scenario's wisselen partners vaak berichten uit in groepen of *batches.* Wanneer u een batchingoplossing bouwt met Logic Apps, u berichten verzenden naar handelspartners en deze berichten samen in batches verwerken. In dit artikel ziet u hoe u EDI-berichten batchverwerken, waarbij X12 als voorbeeld wordt gebruikt, door een logische app voor batchafzender en een logische app voor batch-ontvanger te maken. 

X12-berichten batcheren werkt als het batchn van andere berichten; u gebruikt een batchtrigger die berichten verzamelt in een batch en een batchactie die berichten naar de batch verzendt. X12-batching bevat ook een X12-coderingsstap voordat de berichten naar de handelspartner of andere bestemming gaan. Zie [Batchprocesberichten voor](../logic-apps/logic-apps-batch-process-send-receive-messages.md)meer informatie over de trigger en actie van de batch.

In dit artikel bouwt u een batchingoplossing door twee logische apps te maken binnen hetzelfde Azure-abonnement, Azure-gebied en deze specifieke volgorde te volgen:

* Een [logica-app voor batch-ontvanger,](#receiver) die berichten accepteert en verzamelt in een batch totdat aan uw opgegeven criteria is voldaan voor het vrijgeven en verwerken van die berichten. In dit scenario codeert de batchontvanger ook de berichten in de batch met behulp van de opgegeven X12-overeenkomst of partneridentiteiten.

  Zorg ervoor dat u eerst de batch-ontvanger maakt, zodat u later de batchbestemming selecteren wanneer u de batchafzender maakt.

* Een logica-app [voor batchafzender,](#sender) die de berichten naar de eerder gemaakte batch-ontvanger verzendt. 

Zorg ervoor dat uw batch-ontvanger en batchafzender hetzelfde Azure-abonnement *en* azure-gebied delen. Als dit niet het zo is, u de batch-ontvanger niet selecteren wanneer u de afzender van de batch maakt omdat deze niet voor elkaar zichtbaar zijn.

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt volgen, hebt u deze items nodig:

* Een Azure-abonnement. Als u geen abonnement hebt, u [beginnen met een gratis Azure-account.](https://azure.microsoft.com/free/) Of [meld u aan voor een abonnement op basis van betalen per gebruik.](https://azure.microsoft.com/pricing/purchase-options/)

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Een bestaand [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement en is gekoppeld aan uw logische apps

* Ten minste twee bestaande [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) in uw integratieaccount. Elke partner moet de X12 -kwalificatie (Standard Carrier Alpha Code) gebruiken als bedrijfsidentiteit in de eigenschappen van de partner.

* Een bestaande [X12-overeenkomst](../logic-apps/logic-apps-enterprise-integration-x12.md) in uw integratieaccount

* Als u Visual Studio wilt gebruiken in plaats van de Azure-portal, controleert u of u [Visual Studio instelt voor het werken met Logische Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>X12-batch-ontvanger maken

Voordat u berichten naar een batch verzenden, moet die batch eerst bestaan als de bestemming waar u deze berichten verzendt. Dus eerst moet u de logica-app 'batch-ontvanger', die begint met de **batchtrigger,** maken. Op die manier u bij het maken van de logische app 'batchafzender' de logica-app voor batch-ontvanger selecteren. De batch-ontvanger blijft berichten verzamelen totdat aan de opgegeven criteria is voldaan voor het vrijgeven en verwerken van deze berichten. Hoewel batchontvangers niets hoeven te weten over batchafzenders, moeten batchafzenders de bestemming weten waar ze de berichten verzenden. 

Voor deze batch-ontvanger geeft u de batchmodus, naam, releasecriteria, X12-overeenkomst en andere instellingen op. 

1. Maak in de [Azure-portal](https://portal.azure.com) of Visual Studio een logische app met deze naam: "BatchX12Messages"

2. [Koppel uw logica-app aan uw integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. Voeg in Logic Apps Designer de **trigger batch** toe, waarmee de werkstroom van uw logische app wordt gestart. Voer in het zoekvak 'batch' in als filter. Deze trigger selecteren: **Batchberichten**

   ![Batch-trigger toevoegen](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Stel de eigenschappen van de batch-ontvanger in: 

   | Eigenschap | Waarde | Opmerkingen | 
   |----------|-------|-------|
   | **Batchmodus** | Inline |  |  
   | **Batchnaam** | TestBatch | Alleen beschikbaar met **inline-batchmodus** | 
   | **Releasecriteria** | Aantal berichten op basis van planning op basis van | Alleen beschikbaar met **inline-batchmodus** | 
   | **Aantal berichten** | 10 | Alleen beschikbaar met **op berichten tellen gebaseerde** releasecriteria | 
   | **Interval** | 10 | Alleen beschikbaar met **op planning gebaseerde** releasecriteria | 
   | **Frequentie** | minuut | Alleen beschikbaar met **op planning gebaseerde** releasecriteria | 
   ||| 

   ![Details van batchtrigger verstrekken](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > In dit voorbeeld wordt geen partitie voor de batch ingesteld, zodat elke batch dezelfde partitiesleutel gebruikt. Zie [Batchprocesberichten voor](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender)meer informatie over partities.

5. Voeg nu een actie toe die elke batch codeert: 

   1. Kies Onder de batchtrigger de optie **Nieuwe stap**.

   2. Typ in het zoekvak 'X12 batch' als filter en selecteer deze actie (elke versie): **Batch codeer <*versie*> - X12** 

      ![X12 Batch Encode selecteren: actie](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Als u nog niet eerder verbinding hebt gemaakt met uw integratieaccount, maakt u nu de verbinding. Geef een naam op voor uw verbinding, selecteer het gewenste integratieaccount en kies **Vervolgens Maken**.

      ![Verbinding maken tussen batch-encoder en integratieaccount](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Stel deze eigenschappen in voor uw batch-encoderactie:

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Naam van X12-overeenkomst** | Open de lijst en selecteer uw bestaande overeenkomst. <p>Als uw lijst leeg is, moet u ervoor zorgen dat u [uw logica-app koppelt aan het integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) dat de gewenste overeenkomst heeft. | 
      | **BatchName** | Klik in dit vak en selecteer het token **Batchnaam** nadat de lijst met dynamische inhoud is weergegeven. | 
      | **PartitionName** | Klik in dit vak en selecteer het token **Partitienaam** nadat de lijst met dynamische inhoud is weergegeven. | 
      | **Items** | Sluit het vak met itemdetails en klik in dit vak. Nadat de lijst met dynamische inhoud is weergegeven, selecteert u het token **Batched Items.** | 
      ||| 

      ![Actiegegevens voor batchcoderen](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Voor het vak **Objecten:**

      ![Actieitems voor batchcoderen](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Sla uw logische app op. 

7. Als u Visual Studio gebruikt, controleert u of u [de app voor batch-ontvangerlogica implementeert in Azure.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) Anders u de batch-ontvanger niet selecteren wanneer u de afzender van de batch maakt.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of uw batch-ontvanger werkt zoals verwacht, u een HTTP-actie toevoegen voor testdoeleinden en een batched-bericht verzenden naar de [service Opslaglocatie](https://requestbin.com/). 

1. Kies onder de x12-codeeractie De optie **Nieuwe stap**. 

2. Voer in het zoekvak 'http' in als filter. Selecteer deze actie: **HTTP - HTTP**
    
   ![HTTP-actie selecteren](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Stel de eigenschappen in voor de HTTP-actie:

   | Eigenschap | Beschrijving | 
   |----------|-------------|
   | **Methode** | Selecteer **POST in**deze lijst . | 
   | **Uri** | Genereer een URI voor uw aanvraagopslagen en voer die URI in dit vak in. | 
   | **Hoofdtekst** | Klik in dit vak en nadat de lijst met dynamische inhoud is geopend, selecteert u het teken **van hoofd,** dat wordt weergegeven in de sectie **Batch codeer op de naam van de overeenkomst**. <p>Als u **het** bodytoken niet ziet, selecteert u naast **Batch-code met de naam**van de overeenkomst de optie Meer **weergeven**. | 
   ||| 

   ![Http-actiegegevens opgeven](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Sla uw logische app op. 

   De logica-app voor batch-ontvanger ziet er als volgt uit: 

   ![De logica-app voor batch-ontvanger opslaan](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>X12-batchafzender maken

Maak nu een of meer logische apps die berichten verzenden naar de logica-app voor batch-ontvanger. In elke batchafzender geeft u de logica-app en batchnaam van de batch-ontvanger, berichtinhoud en andere instellingen op. U optioneel een unieke partitiesleutel verstrekken om de batch in subsets te verdelen om berichten met die sleutel te verzamelen. 

* Zorg ervoor dat u [uw batch-ontvanger](#receiver) al hebt gemaakt, zodat u bij het maken van de batchafzender de bestaande batch-ontvanger selecteren als de doelbatch. Hoewel batchontvangers niets hoeven te weten over batchafzenders, moeten batchafzenders weten waar ze berichten moeten verzenden. 

* Zorg ervoor dat uw batch-ontvanger en batchafzender dezelfde Azure-regio *en* Azure-abonnement delen. Als dit niet het zo is, u de batch-ontvanger niet selecteren wanneer u de afzender van de batch maakt omdat deze niet voor elkaar zichtbaar zijn.

1. Een andere logische app maken met deze naam: "SendX12MessagesToBatch" 

2. Typ in het zoekvak 'wanneer een http-verzoek' als filter. Deze trigger selecteren: **wanneer een HTTP-aanvraag wordt ontvangen** 
   
   ![De trigger van Aanvraag toevoegen](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Voeg een actie toe voor het verzenden van berichten naar een batch.

   1. Kies onder de actie HTTP-aanvraag de optie **Nieuwe stap**.

   2. Voer in het zoekvak 'batch' in als filter. 
   Selecteer de lijst **Acties** en selecteer deze actie: **Kies een werkstroom van Logische Apps met batchtrigger - Berichten verzenden naar batch**

      ![Selecteer 'Een werkstroom van Logische apps kiezen met batchtrigger'](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Selecteer nu de logische app 'BatchX12Messages' die u eerder hebt gemaakt.

      ![De logische app 'batch-ontvanger' selecteren](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Selecteer deze actie: **Batch_messages - <uw *batch-ontvanger* > **

      ![De actie 'Batch_messages' selecteren](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Stel de eigenschappen van de afzender van de batch in.

   | Eigenschap | Beschrijving | 
   |----------|-------------| 
   | **Batchnaam** | De batchnaam gedefinieerd door de app voor de logica van de ontvanger, die in dit voorbeeld 'TestBatch' is <p>**Belangrijk:** de batchnaam wordt gevalideerd tijdens runtime en moet overeenkomen met de naam die is opgegeven door de app voor de logica van de ontvanger. Als u de batchnaam wijzigt, mislukt de afzender van de batch. | 
   | **Berichtinhoud** | De inhoud voor het bericht dat u wilt verzenden, dat is het teken van de **tekst in** dit voorbeeld | 
   ||| 
   
   ![Batch-eigenschappen instellen](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Sla uw logische app op. 

   De logische app voor batchafzenders ziet er als volgt uit:

   ![De logica-app voor batchafzenderopslaan](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Uw logische apps testen

Als u uw batchoplossing wilt testen, plaatst u X12-berichten op de logische app voor batchafzenders van [Postman](https://www.getpostman.com/postman) of een soortgelijk hulpmiddel. Binnenkort krijgt u X12-berichten in uw aanvraagopslaglocatie, elke 10 minuten of in batches van 10, allemaal met dezelfde partitiesleutel.

## <a name="next-steps"></a>Volgende stappen

* [Berichten verwerken als batches](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
