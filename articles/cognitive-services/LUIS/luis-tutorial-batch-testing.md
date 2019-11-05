---
title: 'Zelf studie: batch test-LUIS'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie wordt gedemonstreerd hoe u batch tests kunt gebruiken om utterance-Voorspellings problemen in uw app te vinden en deze op te lossen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: ac88931d79df6c2527a2a5fd72b440baeb463115
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499048"
---
# <a name="tutorial-batch-test-data-sets"></a>Zelf studie: gegevens sets batch testen

In deze zelf studie wordt gedemonstreerd hoe u batch tests kunt gebruiken om utterance-Voorspellings problemen in uw app te vinden en deze op te lossen.  

Met batch tests kunt u de actieve, getrainde model status valideren met een bekende set gelabelde uitingen en entiteiten. Voeg in het batch bestand met JSON-indeling de uitingen toe en stel de entiteits labels in die u in de utterance wilt voordicteerd. 

Vereisten voor batch tests:

* Maxi maal 1000 uitingen per test. 
* Geen dubbele waarden. 
* Toegestane entiteits typen: alleen door machines geleerde entiteiten van eenvoudig en samengesteld. Batch tests zijn alleen nuttig voor door machines geleerde intenties en entiteiten.

Wanneer u een andere app dan deze zelf *studie gebruikt, mag u* het voor beeld uitingen al aan een intentie toevoegen. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Een batch-test bestand maken 
> * Een batch test uitvoeren
> * Test resultaten controleren
> * Fouten oplossen 
> * De batch opnieuw testen

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Voorbeeld-app importeren

Ga door met de in de laatste zelfstudie gemaakt app, **Human Resources**. 

Voer de volgende stappen uit:

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json) en sla het op.

2. Importeer de JSON in een nieuwe app.

3. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `batchtest`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL. 

4. Train de app.

## <a name="batch-file"></a>Batch bestand

1. Maak `HumanResources-jobs-batch.json` in een tekst editor of [down load](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) het bestand. 

2. Voeg in het batch bestand met JSON-indeling uitingen toe met de **intentie** die u in de test wilt voors pellen. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>De batch uitvoeren

1. Selecteer **test** in de bovenste navigatie balk. 

2. Selecteer **batch test paneel** in het deel venster aan de rechter kant. 

    [scherm opname van de LUIS-app ![met batch test panel gemarkeerd](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Selecteer **gegevensset importeren**.

    [Scherm opname van de LUIS-app ![met de import gegevensset gemarkeerd](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Kies de bestands locatie van het `HumanResources-jobs-batch.json` bestand.

5. Geef de gegevensset een naam `intents only` en selecteer **gereed**.

    ![Bestand selecteren](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Selecteer de knop **Run**. 

7. Selecteer **resultaten weer geven**.

8. Bekijk de resultaten in de grafiek en legenda.

    [Scherm opname van de LUIS-app ![met batch test resultaten](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Batch resultaten controleren

In de batch grafiek worden vier kwadranten met resultaten weer gegeven. Rechts van de grafiek is een filter. Het filter bevat intents en entiteiten. Wanneer u een [sectie van de grafiek](luis-concept-batch-test.md#batch-test-results) of een punt in de grafiek selecteert, worden de gekoppelde utterance (s) weer gegeven onder de grafiek. 

Bij het aanwijzen van de grafiek kan een muis wiel de weer gave in de grafiek verg Roten of verkleinen. Dit is handig wanneer er veel punten in het diagram zijn geclusterd met elkaar. 

De grafiek is in vier kwadranten, waarbij twee van de secties rood worden weer gegeven. **Dit zijn de secties waarop u zich kunt richten**. 

### <a name="getjobinformation-test-results"></a>Resultaten van GetJobInformation-test

De resultaten van de **GetJobInformation** -test worden weer gegeven in het filter, waarbij twee van de vier voor spellingen zijn geslaagd. Selecteer de naam **Onwaar** in de Kwadrant linksonder om de uitingen onder de grafiek weer te geven. 

Gebruik het toetsen bord, CTRL + E, om over te scha kelen naar de weer gave label om de exacte tekst van de utterance van de gebruiker te zien. 

De utterance-`Is there a database position open in Los Colinas?` wordt aangeduid als _GetJobInformation_ , maar het huidige model heeft de Utterance als _ApplyForJob_gedicteerd. 

Er zijn bijna drie keer zoveel voor beelden voor **ApplyForJob** dan **GetJobInformation**. Deze ongelijkheid van voor beeld uitingen weegt in **ApplyForJob** intentie voor deel, waardoor de onjuiste voor spelling wordt veroorzaakt. 

U ziet dat beide intenties hetzelfde aantal fouten hebben. Een onjuiste voor spelling in één intentie is ook van invloed op de andere intentie. Ze hebben beide fouten omdat de uitingen onjuist zijn voor speld voor één intentie en ook onjuist niet worden voor speld voor een ander doel. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>De app herstellen

Het doel van deze sectie is om alle uitingen correct voor te spelden voor **GetJobInformation** door de app te corrigeren. 

U kunt het beste deze batch bestand uitingen toevoegen aan de juiste oplossing. Dat is niet wat u wilt doen. U wilt dat LUIS deze uitingen op de juiste wijze voors pellen zonder ze als voor beeld toe te voegen. 

U kunt zich ook afvragen over het verwijderen van uitingen van **ApplyForJob** totdat de hoeveelheid utterance hetzelfde is als **GetJobInformation**. Dit kan leiden tot een oplossing van de test resultaten, maar zou LUIS voor komen dat de volgende keer dat intentie wordt gedicteerd. 

De oplossing is om meer uitingen toe te voegen aan **GetJobInformation**. Vergeet niet om de utterance lengte, de woord keuze en de indeling van het woord te variëren terwijl u de bedoeling van het vinden van de informatie over de taak _niet_ kunt Toep assen op de taak.

### <a name="add-more-utterances"></a>Meer uitingen toevoegen

1. Sluit het deel venster voor batch testen door de knop **testen** te selecteren in het bovenste navigatie paneel. 

2. Selecteer **GetJobInformation** in de lijst intenties. 

3. Voeg meer uitingen toe die variëren voor lengte, woord keuze en woord ordening, zodat u de termen `resume`, `c.v.`en `apply`op te nemen:

    |Voor beeld van uitingen voor **GetJobInformation** intentie|
    |--|
    |Moet de nieuwe taak in het magazijn voor een aandeel worden toegepast met een hervatting?|
    |Waar bevinden de plafond taken zich vandaag?|
    |Ik heb gehoord dat er een medische-code ring taak vereist is die moet worden hervat.|
    |Ik wil graag een baan hebben om hun c.v.s. te schrijven |
    |Dit is mijn CV, die op zoek is naar een nieuw bericht op het onderwijs van de community met behulp van computers.|
    |Welke functies zijn er beschikbaar in onderliggend en thuis zorg?|
    |Is er een thuis bureau in de krant?|
    |Mijn C.v. toont ik goed bij het analyseren van aankopen, budgetten en verloren geld. Is er iets voor dit type werk?|
    |Waar zijn de taken voor het boren van de aarde nu?|
    |Ik heb 8 jaar gewerkt als EMS-stuur programma. Nieuwe taken?|
    |Voor nieuwe taken voor levensmiddelen verwerking is toepassing vereist?|
    |Hoeveel nieuwe Yard-werk taken zijn beschikbaar?|
    |Is er een nieuw HR-bericht voor arbeids relaties en-onderhandelingen?|
    |Ik heb een model in bibliotheek-en archief beheer. Nieuwe posities?|
    |Zijn er babysitten-taken voor 13 jaar olds in de stad vandaag?|

    Voorzie de **taak** entiteit niet in de uitingen. Deze sectie van de zelf studie is alleen gericht op intentie voorspellingen.

4. Train de app door **Train** te selecteren in de rechter bovenhoek.

## <a name="verify-the-new-model"></a>Het nieuwe model controleren

Als u wilt controleren of de uitingen in de batch test correct wordt voor speld, voert u de batch-test opnieuw uit.

1. Selecteer **test** in de bovenste navigatie balk. Als de batch resultaten nog zijn geopend, selecteert **u terug naar lijst**.  

1. Selecteer de knop met weglatings tekens (***...***) rechts van de batch naam en selecteer **uitvoeren**. Wacht tot de batch test is voltooid. U ziet dat de knop **resultaten weer geven** nu groen is. Dit betekent dat de volledige batch is uitgevoerd.

1. Selecteer **resultaten weer geven**. De doel stellingen moeten alle groene pictogrammen links van de doel namen hebben. 

## <a name="create-batch-file-with-entities"></a>Batch bestand met entiteiten maken 

Als u entiteiten in een batch test wilt controleren, moeten de entiteiten worden gelabeld in het batch-JSON-bestand. 

De variatie van entiteiten voor het totale aantal woorden ([tokens](luis-glossary.md#token)) kan van invloed zijn op de Voorspellings kwaliteit. Zorg ervoor dat de trainings gegevens die zijn verstrekt aan het doel van de bedoeling van het uitingen een aantal verschillende lengten van de entiteit bevat. 

Wanneer u batch bestanden voor het eerst schrijft en test, kunt u het beste beginnen met een aantal uitingen en entiteiten waarvan u weet dat ze werken, evenals een paar van de voor spelling van een paar. Zo kunt u zich snel richten op de probleem gebieden. Nadat u de **GetJobInformation** -en **ApplyForJob** -intentie hebt getest met verschillende taak namen, die niet zijn voor speld, is dit batch test bestand ontwikkeld om te zien of er sprake is van een Voorspellings probleem met bepaalde waarden voor **taak** entiteit. 

De waarde van een **taak** entiteit, in de test uitingen, is doorgaans een of twee woorden, met enkele voor beelden van meer woorden. Als _uw eigen_ HRM-app doorgaans taak namen van veel woorden heeft, zou het voor beeld uitingen met de **taak** entiteit in deze app niet goed werken.

1. Maak `HumanResources-entities-batch.json` in een tekst editor zoals [VSCode](https://code.visualstudio.com/) of [down load](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) deze.

2. Voeg in het batch bestand met JSON-indeling een matrix met objecten toe die uitingen bevatten met de **intentie** die u wilt voor speld in de test, evenals locaties van entiteiten in de utterance. Omdat een entiteit is gebaseerd op tokens, moet u ervoor zorgen dat elke entiteit wordt gestart en gestopt in een teken. De utterance mag niet beginnen of eindigen op een spatie. Hierdoor treedt er een fout op tijdens het importeren van batch bestanden.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>De batch uitvoeren met entiteiten

1. Selecteer **test** in de bovenste navigatie balk. 

2. Selecteer **batch test paneel** in het deel venster aan de rechter kant. 

3. Selecteer **gegevensset importeren**.

4. Kies de locatie van het bestands systeem van het `HumanResources-entities-batch.json` bestand.

5. Geef de gegevensset een naam `entities` en selecteer **gereed**.

6. Selecteer de knop **Run**. Wacht tot de test is voltooid.

7. Selecteer **resultaten weer geven**.

## <a name="review-entity-batch-results"></a>Entiteit batch resultaten controleren

De grafiek wordt geopend met alle intentiesen die correct worden voor speld. Schuif omlaag in het filter aan de rechter kant om de voor spellingen van de entiteit te vinden met fouten. 

1. Selecteer de **taak** entiteit in het filter.

    ![Voor spellingen van de entiteit fout in het filter](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    De grafiek wordt gewijzigd om de voor spellingen van de entiteit weer te geven. 

2. Selecteer **Onwaar negatief** in de onderste, linkerkolom van de grafiek. Gebruik vervolgens het besturings element toetscombinatie + E om over te scha kelen naar de token weergave. 

    [![token weergave van entiteits voorspellingen](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Door de uitingen onder de grafiek te controleren, wordt een consistente fout weer gegeven wanneer de taak naam `SQL`bevat. Als u het voor beeld-uitingen en de lijst met taak woordgroepen bekijkt, wordt SQL slechts eenmaal gebruikt en alleen als onderdeel van een grotere taak naam `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>De app op basis van entiteits batch resultaten herstellen

Voor het herstellen van de app moeten LUIS de variaties van SQL-taken correct bepalen. Er zijn verschillende opties voor die oplossing. 

* Voeg expliciet meer voorbeeld uitingen toe die SQL gebruiken en voorzie deze woorden als een taak entiteit. 
* Expliciet meer SQL-taken toevoegen aan de woordgroepen lijst

Deze taken zijn nog niet voor u te doen.

Het toevoegen van een [patroon](luis-concept-patterns.md) voordat de entiteit correct wordt voor speld, wordt niet het probleem opgelost. Dit komt doordat het patroon niet overeenkomt totdat alle entiteiten in het patroon zijn gedetecteerd. 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

De zelf studie heeft een batch test gebruikt voor het vinden van problemen met het huidige model. Het model is verholpen en opnieuw getest met het batch-bestand om te controleren of de wijziging juist is.

> [!div class="nextstepaction"]
> [Meer informatie over patronen](luis-tutorial-pattern.md)

