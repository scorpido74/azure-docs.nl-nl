---
title: 'Permutatie functie urgentie: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de permutatie functie urgentie module in Azure Machine Learning voor het berekenen van de urgentie van de permutatie functie van functie variabelen, op basis van een getraind model en een test gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 9ef859cce68e13f5207d77d8396bc69ddbbce339
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314943"
---
# <a name="permutation-feature-importance"></a>Belang van permutatiefunctie

In dit artikel wordt beschreven hoe u de permutatie functie urgentie module in Azure Machine Learning Designer gebruikt voor het berekenen van een aantal belang rijke scores voor de functie van uw gegevensset. U kunt deze scores gebruiken om u te helpen bij het bepalen van de beste functies die in een model moeten worden gebruikt.

In deze module worden functie waarden wille keurig in de andere volg orde, één kolom per keer. De prestaties van het model worden vóór en na gemeten. U kunt een van de standaard metrische gegevens kiezen om prestaties te meten.

De scores die de module retourneert, vertegenwoordigen de *wijziging* van de prestaties van een getraind model, na permutatie. Belang rijke functies zijn doorgaans gevoeliger voor het proces waarin de procedure wordt uitgevoerd, zodat ze resulteren in hogere urgentie scores. 

Dit artikel bevat een overzicht van de permutatie functie, de theoretische basis en de bijbehorende toepassingen in machine learning: het belang van de [permutatie functie](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Het belang van de permutatie-functie gebruiken

Als u een set functie scores wilt genereren, moet u een al getraind model en een test gegevensset hebben.  

1.  Voeg de permutatie functie urgentie module toe aan uw pijp lijn. U kunt deze module vinden in de categorie **functie selectie** . 

2.  Verbind een getraind model met de linkerkant invoer. Het model moet een regressie model of classificatie model zijn.  

3.  Verbind een gegevensset aan de rechter invoer. Kies bij voor keur een ander type dan de gegevensset die u hebt gebruikt voor het trainen van het model. Deze gegevensset wordt gebruikt voor het scoren op basis van het getrainde model. Het wordt ook gebruikt voor het evalueren van het model nadat de waarden van de onderdelen zijn gewijzigd.  

4.  Voer voor **wille keurig zaad**een waarde in die moet worden gebruikt als seed voor wille keurige invoer. Als u 0 (de standaard waarde) opgeeft, wordt er een nummer gegenereerd op basis van de systeem klok.

     Een Seed-waarde is optioneel, maar u moet een waarde opgeven als u de reproduceer baarheid van dezelfde pijp lijn wilt uitvoeren.  

5.  Selecteer voor **metrische gegevens voor het meten van de prestaties**één metrische waarde voor het gebruik van de model kwaliteit na permutatie.  

     Azure Machine Learning Designer ondersteunt de volgende metrische gegevens, afhankelijk van of u een classificatie of regressie model wilt evalueren:  

    -   **Classificatie**

        Nauw keurigheid, precisie, intrekken, gemiddeld logboek verlies  

    -   **Regressie**

        Precisie, intrekken, gemiddelde absolute fout, wortel gemiddelde fout, relatieve absolute fout, relatieve kwadraat fout, coëfficiënt van bepaling  

     Zie voor een gedetailleerde beschrijving van deze metrische gegevens over de evaluatie en hoe deze worden berekend [model evalueren](evaluate-model.md).  

6.  Voer de pijplijn uit.  

7.  De module levert een lijst met functie kolommen en de bijbehorende scores. De lijst wordt gerangschikt in aflopende volg orde van de scores.  


##  <a name="technical-notes"></a>Technische opmerkingen

Het belang van de permutatie functie werkt door wille keurig de waarden van elke functie kolom, één kolom per keer te wijzigen. Vervolgens wordt het model geëvalueerd. 

De classificaties die de module biedt, zijn vaak verschillend van de functies die u ophaalt uit [functie selectie op basis van filters](filter-based-feature-selection.md). De functie selectie op basis van filters berekent de scores *voordat* een model wordt gemaakt. 

De reden voor het verschil is dat het belang van permutatie functies de koppeling tussen een functie en een doel waarde niet meet. In plaats daarvan wordt vastgelegd hoeveel invloed heeft op elke functie op voor spellingen van het model.
  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
