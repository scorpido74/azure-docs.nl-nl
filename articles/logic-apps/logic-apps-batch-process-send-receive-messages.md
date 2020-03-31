---
title: Batchprocesberichten als groep
description: Berichten verzenden en ontvangen in groepen tussen uw werkstromen met batchverwerking in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e48d2bb2ffce0dd4f9293417534165165d426784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666751"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Berichten verzenden, ontvangen en batchverwerken in Azure Logic Apps

Als u berichten op een specifieke manier als groepen wilt verzenden en verwerken, u een batchoplossing maken die berichten in een *batch* verzamelt totdat aan uw opgegeven criteria is voldaan voor het vrijgeven en verwerken van de batchberichten. Batching kan verminderen hoe vaak uw logische app berichten verwerkt. In dit artikel ziet u hoe u een batchingoplossing maken door twee logische apps te maken binnen hetzelfde Azure-abonnement, Azure-gebied en deze specifieke volgorde te volgen: 

* De [logica-app 'batch-ontvanger',](#batch-receiver) die berichten accepteert en verzamelt in een batch totdat aan de opgegeven criteria is voldaan voor het vrijgeven en verwerken van die berichten.

  Zorg ervoor dat u eerst de batch-ontvanger maakt, zodat u later de batchbestemming selecteren wanneer u de batchafzender maakt.

* Een of meer logische apps [voor 'batchafzender',](#batch-sender) die de berichten naar de eerder gemaakte batch-ontvanger sturen. 

   U ook een unieke sleutel opgeven, zoals een klantnummer, die de doelbatch *partities* of verdeelt in logische subsets op basis van die sleutel. Op die manier kan de ontvanger-app alle items met dezelfde sleutel verzamelen en samen verwerken.

Zorg ervoor dat uw batch-ontvanger en batchafzender hetzelfde Azure-abonnement *en* azure-gebied delen. Als dit niet het zo is, u de batch-ontvanger niet selecteren wanneer u de afzender van de batch maakt omdat deze niet voor elkaar zichtbaar zijn.

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt volgen, hebt u deze items nodig:

* Een Azure-abonnement. Als u geen abonnement hebt, u [beginnen met een gratis Azure-account.](https://azure.microsoft.com/free/) Of [meld u aan voor een abonnement op basis van betalen per gebruik.](https://azure.microsoft.com/pricing/purchase-options/)

* Een e-mailaccount bij elke [e-mailprovider die wordt ondersteund door Azure Logic Apps](../connectors/apis-list.md)

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Als u Visual Studio wilt gebruiken in plaats van de Azure-portal, controleert u of u [Visual Studio instelt voor het werken met Logische Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Batch-ontvanger maken

Voordat u berichten naar een batch verzenden, moet die batch eerst bestaan als de bestemming waar u deze berichten verzendt. Dus eerst moet u de logica-app 'batch-ontvanger', die begint met de **batchtrigger,** maken. Op die manier u bij het maken van de logische app 'batchafzender' de logica-app voor batch-ontvanger selecteren. De batch-ontvanger blijft berichten verzamelen totdat aan de opgegeven criteria is voldaan voor het vrijgeven en verwerken van deze berichten. Hoewel batchontvangers niets hoeven te weten over batchafzenders, moeten batchafzenders de bestemming weten waar ze de berichten verzenden. 

1. Maak in de [Azure-portal](https://portal.azure.com) of Visual Studio een logische app met deze naam: "BatchReceiver" 

2. Voeg in Logic Apps Designer de **trigger batch** toe, waarmee de werkstroom van uw logische app wordt gestart. Voer in het zoekvak 'batch' in als filter. Deze trigger selecteren: **Batchberichten**

   ![Trigger voor batchberichten toevoegen](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Stel deze eigenschappen in voor de batch-ontvanger: 

   | Eigenschap | Beschrijving | 
   |----------|-------------|
   | **Batchmodus** | - **Inline**: Voor het definiëren van releasecriteria in de batchtrigger <br>- **Integratieaccount:** voor het definiëren van meerdere configuratieconfiguraties van releasecriteria via een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Met een integratieaccount u deze configuraties allemaal op één plaats onderhouden in plaats van in afzonderlijke logische apps. | 
   | **Batchnaam** | De naam voor uw batch, die in dit voorbeeld 'TestBatch' is, is alleen van toepassing op **de batchmodus in line** |  
   | **Releasecriteria** | Geldt alleen voor **de inlinebatchmodus** en selecteert de criteria waaraan moet worden voldaan voordat elke batch wordt verwerkt: <p>- **Aantal berichten op basis van**: Laat de batch vrij op basis van het aantal berichten dat door de batch is verzameld. <br>- **Grootte gebaseerd**: Laat de batch vrij op basis van de totale grootte in bytes voor alle berichten die door die batch worden verzameld. <br>- **Planning:** Laat de batch vrij op basis van een herhalingsschema, waarin een interval en frequentie worden opgegeven. In de geavanceerde opties u ook een tijdzone selecteren en een begindatum en -tijd geven. <br>- **Alles selecteren**: Gebruik alle opgegeven criteria. | 
   | **Aantal berichten** | Het aantal berichten dat moet worden verzameld in de batch, bijvoorbeeld 10 berichten. De limiet van een batch is 8.000 berichten. | 
   | **Batchgrootte** | De totale grootte in bytes die moet worden verzameld in de batch, bijvoorbeeld 10 MB. De groottelimiet van een batch is 80 MB. | 
   | **Planning** | Het interval en de frequentie tussen batch releases, bijvoorbeeld, 10 minuten. De minimale herhaling is 60 seconden of 1 minuut. Fractionele minuten worden effectief afgerond op 1 minuut. Als u een tijdzone of een begindatum en -tijd wilt opgeven, kiest **u Geavanceerde opties weergeven**. | 
   ||| 

   > [!NOTE]
   > 
   > Als u de releasecriteria wijzigt terwijl de trigger nog steeds batched maar niet verzonden berichten heeft, gebruikt de trigger de bijgewerkte releasecriteria voor het verwerken van de niet-verzonden berichten. 

   In dit voorbeeld worden alle criteria weergegeven, maar probeer voor uw eigen testen slechts één criterium:

   ![Details van batchtrigger opgeven](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Voeg nu een of meer acties toe die elke batch verwerken. 

   Voeg in dit voorbeeld een actie toe die een e-mail verzendt wanneer de batch wordt geactiveerd. 
   De trigger wordt uitgevoerd en stuurt een e-mail wanneer de batch ofwel 10 berichten heeft, 10 MB bereikt of na 10 minuten voorbij is.

   1. Kies Onder de batchtrigger de optie **Nieuwe stap**.

   2. Voer 'e-mail verzenden' als filter in het zoekvak in.
   Selecteer op basis van uw e-mailprovider een e-mailconnector.

      Als u bijvoorbeeld een persoonlijk account @outlook.com hebt, zoals of @hotmail.com, selecteert u de Outlook.com-connector. 
      Als je een Gmail-account hebt, selecteer je de Gmail-connector. 
      In dit voorbeeld wordt Office 365 Outlook gebruikt. 

   3. Selecteer deze actie: **Stuur een e-mail - <*e-mailprovider* > **

      Bijvoorbeeld:

      ![Selecteer actie 'Een e-mail verzenden' voor uw e-mailprovider](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Meld u aan bij uw e-mailaccount als dat wordt gevraagd. 

6. Stel de eigenschappen in voor de actie die u hebt toegevoegd.

   * Voer het e-mailadres van de ontvanger in het vak **Aan** in. 
   Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken.

   * Selecteer in het vak **Onderwerp,** wanneer de lijst met dynamische inhoud wordt weergegeven, het veld **Partitienaam.**

     ![Selecteer in de lijst met dynamische inhoud de optie 'Partitienaam'](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Later, in de batchafzender, u een unieke partitiesleutel opgeven die de doelbatch verdeelt in logische subsets waar u berichten verzenden. 
     Elke set heeft een uniek nummer dat wordt gegenereerd door de logica-app voor batchafzenders. 
     Met deze mogelijkheid u één batch met meerdere subsets gebruiken en elke subset definiëren met de naam die u opgeeft.

     > [!IMPORTANT]
     > Een partitie heeft een limiet van 5.000 berichten of 80 MB. Als aan een van beide voorwaarden is voldaan, kan Logic Apps de batch vrijgeven, zelfs als niet aan de gedefinieerde releasevoorwaarde is voldaan.

   * Selecteer in het vak **Hoofdlichaam,** wanneer de lijst met dynamische inhoud wordt weergegeven, het veld **Bericht-id.** 

     De Logic Apps Designer voegt automatisch een lus 'Voor elke' lus rond de actie e-mail verzenden toe, omdat die actie de uitvoer van de vorige actie behandelt als een verzameling in plaats van als een batch. 

     ![Selecteer 'Bericht-id' voor 'Lichaam'](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Sla uw logische app op. U hebt nu een batch-ontvanger gemaakt.

    ![Uw logische app opslaan](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Als u Visual Studio gebruikt, controleert u of u [de app voor batch-ontvangerlogica implementeert in Azure.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) Anders u de batch-ontvanger niet selecteren wanneer u de afzender van de batch maakt.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Batchafzender maken

Maak nu een of meer logische apps voor batchafzenders die berichten verzenden naar de logica-app voor batch-ontvanger. In elke batchafzender geeft u de batch-ontvanger en batchnaam, berichtinhoud en andere instellingen op. U optioneel een unieke partitiesleutel bieden om de batch te verdelen in logische subsets voor het verzamelen van berichten met die sleutel. 

* Zorg ervoor dat u [uw batch-ontvanger](#batch-receiver) al hebt gemaakt, zodat u bij het maken van de batchafzender de bestaande batch-ontvanger selecteren als de doelbatch. Hoewel batchontvangers niets hoeven te weten over batchafzenders, moeten batchafzenders weten waar ze berichten moeten verzenden. 

* Zorg ervoor dat uw batch-ontvanger en batchafzender dezelfde Azure-regio *en* Azure-abonnement delen. Als dit niet het zo is, u de batch-ontvanger niet selecteren wanneer u de afzender van de batch maakt omdat deze niet voor elkaar zichtbaar zijn.

1. Een andere logische app maken met deze naam: "BatchSender"

   1. Voer in het zoekvak 'herhaling' in als filter. 
   Selecteer deze trigger: **Recidief - Schema**

      ![De trigger 'Herhaling - schema' toevoegen](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Stel de frequentie en het interval in om de logische app voor afzenders elke minuut uit te voeren.

      ![Frequentie en interval instellen voor recidieftrigger](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Voeg een nieuwe actie toe voor het verzenden van berichten naar een batch.

   1. Kies Onder de recidieftrigger de optie **Nieuwe stap**.

   2. Voer in het zoekvak 'batch' in als filter. 
   Selecteer de lijst **Acties** en selecteer deze actie: **Kies een werkstroom van Logische Apps met batchtrigger - Berichten verzenden naar batch**

      ![Selecteer 'Een werkstroom van Logische apps kiezen met batchtrigger'](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Selecteer de logische app voor batch-ontvanger die u eerder hebt gemaakt.

      ![De logische app 'batch-ontvanger' selecteren](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > De lijst toont ook alle andere logische apps met batchtriggers. 
      > 
      > Als u Visual Studio gebruikt en u geen batchontvangers ziet die u wilt selecteren, controleert u of u uw batch-ontvanger hebt geïmplementeerd in Azure. Als u dit nog niet hebt gedaan, leest u hoe [u de logica-app voor batch-ontvanger implementeert in Azure.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) 

   4. Selecteer deze actie: **Batch_messages - <uw *batch-ontvanger* > **

      ![Selecteer deze actie: "Batch_messages - <uw-logica-app>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Stel de eigenschappen van de afzender van de batch in:

   | Eigenschap | Beschrijving | 
   |----------|-------------| 
   | **Batchnaam** | De batchnaam gedefinieerd door de app voor de logica van de ontvanger, die in dit voorbeeld 'TestBatch' is <p>**Belangrijk:** de batchnaam wordt gevalideerd tijdens runtime en moet overeenkomen met de naam die is opgegeven door de app voor de logica van de ontvanger. Als u de batchnaam wijzigt, mislukt de afzender van de batch. | 
   | **Berichtinhoud** | De inhoud voor het bericht dat u wilt verzenden | 
   ||| 

   Voeg in dit voorbeeld deze expressie toe, waarin de huidige datum en tijd worden ingevoegd in de berichtinhoud die u naar de batch verzendt:

   1. Klik in het vak **Berichtinhoud.** 

   2. Wanneer de lijst met dynamische inhoud wordt weergegeven, kiest u **Expressie**. 

   3. Voer de `utcnow()`expressie in en kies **OK**. 

      ![Kies in 'Berichtinhoud' de optie 'Expressie', typ 'utcnow()' en kies 'OK'.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Stel nu een partitie in voor de batch. Kies in de actie BatchReceiver de optie **Geavanceerde opties weergeven** en stel deze eigenschappen in:

   | Eigenschap | Beschrijving | 
   |----------|-------------| 
   | **Partitienaam** | Een optionele unieke partitiesleutel die u wilt gebruiken om de doelbatch te verdelen in logische subsets en berichten te verzamelen op basis van die sleutel | 
   | **Bericht-id** | Een optionele bericht-id die een gegenereerde wereldwijde unieke id (GUID) is wanneer deze leeg is | 
   ||| 

   Voeg in dit voorbeeld in het vak **Partitienaam** een expressie toe die een willekeurig getal genereert tussen één en vijf. Laat het **vak Bericht-id** leeg.
   
   1. Klik in het vak **Partitienaam,** zodat de lijst met dynamische inhoud wordt weergegeven. 

   2. Kies in de lijst met dynamische inhoud voor **Expressie**.
   
   3. Voer de `rand(1,6)`expressie in en kies **OK**.

      ![Een partitie instellen voor uw doelbatch](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Deze **randfunctie** genereert een getal tussen één en vijf. 
      U verdeelt deze batch dus in vijf genummerde partities, die deze expressie dynamisch instelt.

5. Sla uw logische app op. De app voor afzenderlogica lijkt nu op dit voorbeeld:

   ![De logica-app voor afzenders opslaan](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Uw logische apps testen

Als u uw batchoplossing wilt testen, laat u uw logische apps enkele minuten draaien. Al snel krijg je e-mails in groepen van vijf, allemaal met dezelfde partitiesleutel.

Uw batchafzenderlogica-app wordt elke minuut uitgevoerd, genereert een willekeurig getal tussen één en vijf en gebruikt dit gegenereerde nummer als de partitiesleutel voor de doelbatch waar berichten worden verzonden. Elke keer dat de batch vijf items met dezelfde partitiesleutel heeft, wordt de logica-app batch-ontvanger geactiveerd en verzendt deze e-mail voor elk bericht.

> [!IMPORTANT]
> Wanneer u klaar bent met testen, moet u ervoor zorgen dat u de logica-app BatchSender uitschakelt om te stoppen met het verzenden van berichten en te voorkomen dat uw postvak IN overbelast raakt.

## <a name="next-steps"></a>Volgende stappen

* [Batches maken van EDI-berichten en deze verzenden](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Bouwen op logische app-definities met JSON](../logic-apps/logic-apps-author-definitions.md)
* [Een serverloze app bouwen in Visual Studio met Azure Logic Apps en -functies](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Verwerking van uitzonderingen en foutlogboekregistratie voor logische apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
