---
title: 'Permutatie functie urgentie: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de permutatie functie urgentie module in Azure Machine Learning service voor het berekenen van de urgentie van de permutatie functie van functie variabelen op basis van een getraind model en een test gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517941"
---
# <a name="permutation-feature-importance"></a>Permutatie functie urgentie

In dit artikel wordt beschreven hoe u de [permutatie functie urgentie](permutation-feature-importance.md) module in azure machine learning Designer (preview) gebruikt om een aantal score punten van de functie voor uw gegevensset te berekenen. U kunt deze scores gebruiken om u te helpen bij het bepalen van de beste functies die in een model moeten worden gebruikt.

In deze module worden functie waarden wille keurig in de wille keurige volg orde, één kolom per keer en de prestaties van het model gemeten vóór en na. U kunt een van de standaard metrische gegevens opgeven om prestaties te meten.

De scores die de module retourneert, vertegenwoordigen de **wijziging** van de prestaties van een getraind model, na permutatie. Belang rijke functies zijn doorgaans gevoeliger voor het proces waarin de procedure wordt uitgevoerd en resulteert daardoor in hogere urgentie scores. 

Dit artikel biedt een goed overzicht van het belang van permutatie functies, theoretische basis en de bijbehorende toepassingen in machine learning: [permutatie functie urgentie](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>Het belang van de permutatie-functie gebruiken

Als u een set functie scores wilt genereren, moet u een al getraind model en een test-gegevensset hebben.  

1.  Voeg de **permutatie functie urgentie** module toe aan uw pijp lijn. U kunt deze module vinden in de categorie **functie selectie** . 

2.  Verbind een getraind model met de linkerkant invoer. Het model moet een regressie model of classificatie model zijn.  

3.  Verbind aan de rechter invoer een gegevensset, bij voor keur één die afwijkt van de gegevensset die wordt gebruikt voor het trainen van het model. Deze gegevensset wordt gebruikt voor het scoren op basis van het getrainde model en voor het evalueren van het model nadat de waarden van de onderdelen zijn gewijzigd.  

4.  Typ voor **wille keurig zaad**een waarde die moet worden gebruikt als seed voor wille keurige. Als u 0 (de standaard waarde) opgeeft, wordt er een nummer gegenereerd op basis van de systeem klok.

     Een Seed-waarde is optioneel, maar u moet een waarde opgeven als u de reproduceer baarheid van dezelfde pijp lijn wilt uitvoeren.  

5.  Selecteer voor **metrische gegevens voor het meten van de prestaties**één metrische waarde die moet worden gebruikt bij het berekenen van de model kwaliteit na permutatie.  

     Azure Machine Learning Designer ondersteunt de volgende metrische gegevens, afhankelijk van of u een classificatie of regressie model evalueert:  

    -   **Classificatie**

        Nauw keurigheid, precisie, intrekken, gemiddeld logboek verlies  

    -   **Regressie**

        Precisie, intrekken, gemiddelde absolute fout, wortel gemiddelde fout, relatieve absolute fout, relatieve kwadraat fout, coëfficiënt van bepaling  

     Zie voor een gedetailleerde beschrijving van deze metrische gegevens van de evaluatie en hoe deze worden berekend [model evalueren](evaluate-model.md).  

6.  Voer de pijplijn uit.  

7.  De module voert een lijst van de functie kolommen en de bijbehorende scores uit, gerangschikt op volg orde van de scores, aflopend.  


##  <a name="technical-notes"></a>Technische opmerkingen

In deze sectie vindt u informatie over de implementatie, tips en antwoorden op veelgestelde vragen.

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>Hoe vergelijkt dit met andere functie selectie methoden?

Het belang van de permutatie functie werkt door wille keurig de waarden van elke functie kolom te wijzigen, één kolom tegelijk en vervolgens het model te evalueren. 

De classificatie van de permutatie functie is vaak anders dan die van de [functie selectie op basis van filters](filter-based-feature-selection.md), waarmee de scores worden berekend **voordat** een model wordt gemaakt. 

Dit komt doordat de betekenis van de permutatie functie de koppeling tussen een functie en een doel waarde niet meet, maar in plaats daarvan wordt vastgelegd hoeveel invloed heeft op elke functie op voor spellingen van het model.
  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
