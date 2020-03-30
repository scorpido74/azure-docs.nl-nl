---
title: 'Permutatie functie belang: Module referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Belangrijkheid van permutatiefuncties in Azure Machine Learning om de scores van het aantal functies permutatie van functievariabelen te berekenen, met een getraind model en een testgegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456060"
---
# <a name="permutation-feature-importance"></a>Belang van permutatiefunctie

In dit artikel wordt beschreven hoe u de module Belangrijk heid van permutatiefunctie gebruiken in Azure Machine Learning-ontwerper (voorbeeld), om een reeks scores voor functiebelang voor uw gegevensset te berekenen. U gebruikt deze scores om u te helpen bij het bepalen van de beste functies die u in een model gebruiken.

In deze module worden functiewaarden willekeurig geschud, één kolom tegelijk. De prestaties van het model worden voor en na gemeten. U een van de standaardstatistieken kiezen om de prestaties te meten.

De scores die de module retourneert vertegenwoordigen de *verandering* in de prestaties van een getraind model, na permutatie. Belangrijke functies zijn meestal gevoeliger voor het schuifproces, zodat ze resulteren in hogere belang scores. 

Dit artikel geeft een overzicht van de permutatiefunctie, de theoretische basis en de toepassingen ervan in machine learning: [Permutatie Functie Belang](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Hoe permutatie functie belang te gebruiken

Het genereren van een set functiescores vereist dat u een al getraind model hebt, evenals een testgegevensset.  

1.  Voeg de module Permutatiefunctiebelang toe aan uw pijplijn. U vindt deze module in de categorie **Functieselectie.** 

2.  Sluit een getraind model aan op de linkerinvoer. Het model moet een regressiemodel of een classificatiemodel zijn.  

3.  Sluit op de juiste invoer een gegevensset aan. Kies bij voorkeur een andere dan de gegevensset die u hebt gebruikt voor het trainen van het model. Deze gegevensset wordt gebruikt voor het scoren op basis van het getrainde model. Het wordt ook gebruikt voor het evalueren van het model nadat de functiewaarden zijn gewijzigd.  

4.  Voer **voor willekeurig zaad**een waarde in die moet worden gebruikt als een zaad voor randomisatie. Als u 0 opgeeft (de standaardinstelling), wordt een getal gegenereerd op basis van de systeemklok.

     Een zaadwaarde is optioneel, maar u moet een waarde bieden als u reproduceerbaarheid wilt voor runs van dezelfde pijplijn.  

5.  Selecteer **voor Metric voor het meten van prestaties**één statistiek die u gebruiken wanneer u de modelkwaliteit na permutatie computert.  

     Azure Machine Learning-ontwerper ondersteunt de volgende statistieken, afhankelijk van of u een classificatie- of regressiemodel evalueert:  

    -   **Classificatie**

        Nauwkeurigheid, precisie, terugroeping  

    -   **Regressie**

        Precisie, Terugroepen, Gemiddelde Absolute Fout, Root Mean Kwadraatfout, relatieve absolute fout, relatieve kwadraatfout, bepalingscoëfficiënt  

     Zie [Model evalueren](evaluate-model.md)voor een meer gedetailleerde beschrijving van deze evaluatiestatistieken en hoe ze worden berekend.  

6.  Verzend de pijplijn.  

7.  De module geeft een lijst met functiekolommen en de scores die eraan zijn gekoppeld. De lijst wordt gerangschikt in aflopende volgorde van de scores.  


##  <a name="technical-notes"></a>Technische notities

Permutatiefunctie Belangrijkheid werkt door de waarden van elke functiekolom willekeurig te wijzigen, één kolom tegelijk. Vervolgens evalueert het model. 

De ranglijsten die de module biedt zijn vaak anders dan die je krijgt van [Filter Gebaseerde Feature Selection](filter-based-feature-selection.md). Filtergebaseerde functieselectie berekent scores *voordat* een model wordt gemaakt. 

De reden voor het verschil is dat Permutatie Functie Belang niet de associatie tussen een functie en een doelwaarde meet. In plaats daarvan legt het vast hoeveel invloed elke functie heeft op voorspellingen van het model.
  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
