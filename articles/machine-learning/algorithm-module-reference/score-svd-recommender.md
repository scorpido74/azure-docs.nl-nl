---
title: 'Score SVD Recommender: Module referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module SVD Recommender score in Azure Machine Learning om aanbevelingsvoorspellingen voor een gegevensset te scoren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/10/2020
ms.openlocfilehash: 82c3454ad4c8db3a9b19084f5b6ece988cc86b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455975"
---
# <a name="score-svd-recommender"></a>Aanbevelingsfunctie voor SVD-score 

In dit artikel wordt beschreven hoe u de module Score SVD Recommender gebruiken in Azure Machine Learning designer (preview). Gebruik deze module om voorspellingen te maken met behulp van een getraind aanbevelingsmodel op basis van het SVD-algoritme (Single Value Decomposition).

De SVD-recommender kan twee verschillende soorten voorspellingen genereren:

- [Beoordelingen voor een bepaalde gebruiker en item voorspellen](#prediction-of-ratings)
- [Items aanbevelen aan een gebruiker](#recommendations-for-users)

Wanneer u het tweede type voorspellingen maakt, u in een van de volgende modi werken:

- **De productiemodus** houdt rekening met alle gebruikers of artikelen. Het wordt meestal gebruikt in een webservice.

  U scores maken voor nieuwe gebruikers, niet alleen gebruikers die tijdens de training worden gezien. Zie voor meer informatie de [technische notities](#technical-notes). 

- **De evaluatiemodus** werkt op een beperkte set gebruikers of items die kunnen worden geëvalueerd. Het wordt meestal gebruikt tijdens pijplijnbewerkingen.

Voor meer informatie over de SVD recommender algoritme, zie het onderzoek papier [Matrix factorization technieken voor recommender systemen](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Score SVD-recommender configureren

Deze module ondersteunt twee soorten voorspellingen, elk met verschillende vereisten. 

###  <a name="prediction-of-ratings"></a>Voorspelling van ratings

Wanneer u beoordelingen voorspelt, berekent het model hoe een gebruiker op een bepaald item reageert, gezien de trainingsgegevens. De invoergegevens voor het scoren moeten zowel een gebruiker als het te beoordelen item opgeven.

1. Voeg een getraind aanbevelingsmodel toe aan uw pijplijn en sluit deze aan **op getrainde SVD-aanbeveling**. U moet het model maken met behulp van de [Train SVD Recommender-module.](train-SVD-recommender.md)

2. Selecteer **Voorspelling stegen voor** **recommender-voorspelling**. Er zijn geen andere parameters vereist.

3. Voeg de gegevens waarvoor u voorspellingen wilt doen toe en sluit deze aan **op Gegevensset om te scoren.**

   Om classificaties te voorspellen, moet de invoergegevensset gebruikersitemparen bevatten.

   De gegevensset kan een optionele derde kolom met classificaties voor het gebruikersitempaar in de eerste en tweede kolom bevatten. Maar de derde kolom zal worden genegeerd tijdens de voorspelling.

4. Verzend de pijplijn.

### <a name="results-for-rating-predictions"></a>Resultaten voor waarderingsvoorspellingen 

De uitvoergegevensset bevat drie kolommen: gebruikers, items en de voorspelde beoordeling voor elke invoergebruiker en -item.

###  <a name="recommendations-for-users"></a>Aanbevelingen voor gebruikers 

Als u items voor gebruikers wilt aanbevelen, geeft u een lijst met gebruikers en items als invoer. Uit deze gegevens gebruikt het model zijn kennis over bestaande items en gebruikers om een lijst met items te genereren met een waarschijnlijke aantrekkingskracht op elke gebruiker. U het aantal geretourneerde aanbevelingen aanpassen. En u een drempel instellen voor het aantal eerdere aanbevelingen dat nodig is om een aanbeveling te genereren.

1. Voeg een getraind aanbevelingsmodel toe aan uw pijplijn en sluit deze aan **op getrainde SVD-aanbeveling**.  U moet het model maken met behulp van de [Train SVD Recommender-module.](train-svd-recommender.md)

2. Als u items voor een lijst met gebruikers wilt aanbevelen, stelt u **de voorspellingstype recommender in** **op Aanbeveling voor objecten**.

3. Geef **voor aanbevolen artikelselectie**aan of u de scoremodule in productie of voor modelevaluatie gebruikt. Kies een van de volgende waarden:

    - **Uit alle items:** selecteer deze optie als u een pijplijn instelt die u wilt gebruiken in een webservice of in productie.  Met deze optie u *de productiemodus inschakelen.* De module doet aanbevelingen van alle items die tijdens de training worden gezien.

    - **Van Beoordeelde items (voor modelevaluatie):** Selecteer deze optie als u een model ontwikkelt of test. Met deze optie *u de evaluatiemodus inschakelen*. De module doet alleen aanbevelingen van die items in de invoergegevensset die zijn beoordeeld.
    
    - **Van Niet-beoordeelde items (om nieuwe items voor te stellen aan gebruikers)**: Selecteer deze optie als u wilt dat de module alleen aanbevelingen doet van items in de trainingsgegevensset die niet zijn beoordeeld. 

4. Voeg de gegevensset toe waarvoor u voorspellingen wilt doen en sluit deze aan **op Gegevensset om te scoren.**

    - Voor **Van alle items**moet de invoergegevensset uit één kolom bestaan. Het bevat de id's van gebruikers waarvoor aanbevelingen te doen.

      De gegevensset kan twee extra kolommen met item-id's en classificaties bevatten, maar deze twee kolommen worden genegeerd. 

    - Voor **Van beoordeelde items (voor modelevaluatie)** moet de invoergegevensset bestaan uit gebruikersitemparen. De eerste kolom moet de gebruikers-id bevatten. De tweede kolom moet de bijbehorende item-id's bevatten.

      De gegevensset kan een derde kolom met gebruikersitemclassificaties bevatten, maar deze kolom wordt genegeerd.

    - Voor **Van niet-beoordeelde items (om nieuwe items voor te stellen aan gebruikers)** moet de invoergegevensset bestaan uit gebruikersitemparen. De eerste kolom moet de gebruikers-id bevatten. De tweede kolom moet de bijbehorende item-id's bevatten.

     De gegevensset kan een derde kolom met gebruikersitemclassificaties bevatten, maar deze kolom wordt genegeerd.

5. **Maximaal aantal items dat een gebruiker moet aanbevelen:** Voer het aantal items in dat voor elke gebruiker moet worden geretourneerd. Standaard raadt de module vijf items aan.

6. **Minimumgrootte van de aanbevelingsgroep per gebruiker**: Voer een waarde in die aangeeft hoeveel eerdere aanbevelingen vereist zijn. Standaard is deze parameter ingesteld op 2, wat betekent dat ten minste twee andere gebruikers het item hebben aanbevolen.

   Gebruik deze optie alleen als u scoort in de evaluatiemodus. De optie is niet beschikbaar als u **Uit alle items** of van niet-beoordeelde items selecteert **(om nieuwe items voor te stellen aan gebruikers)**.

7.  Voor **Van niet-beoordeelde items (om nieuwe items voor te stellen aan gebruikers)** gebruikt u de derde invoerpoort, genaamd **Trainingsgegevens,** om items te verwijderen die al zijn beoordeeld uit de voorspellingsresultaten.

    Als u dit filter wilt toepassen, verbindt u de oorspronkelijke trainingsgegevensset met de invoerpoort.

8. Verzend de pijplijn.

### <a name="results-of-item-recommendation"></a>Resultaten van artikelaanbeveling

De scoreset die wordt geretourneerd door Score SVD Recommender bevat de aanbevolen items voor elke gebruiker:

- De eerste kolom bevat de gebruikers-id's.
- Er worden een aantal extra kolommen gegenereerd, afhankelijk van de waarde die u hebt ingesteld voor **maximaal aantal items dat een gebruiker moet aanbevelen.** Elke kolom bevat een aanbevolen item (per id). De aanbevelingen worden besteld op basis van affiniteit met gebruikersitems. Het item met de hoogste affiniteit wordt in kolom **item 1**geplaatst .

> [!WARNING]
> U deze gegevensset niet evalueren met behulp van de module [Recommender evalueren.](evaluate-recommender.md)


##  <a name="technical-notes"></a>Technische notities

Als u een pijplijn hebt met de SVD-aanbeveling en u het model naar productie verplaatst, moet u zich ervan bewust zijn dat er belangrijke verschillen zijn tussen het gebruik van de aanbeveling in de evaluatiemodus en het gebruik ervan in de productiemodus.

Evaluatie, per definitie, vereist voorspellingen die kunnen worden geverifieerd tegen de *grond waarheid* in een test set. Wanneer u de aanbevelingstoer evalueert, moet deze alleen items voorspellen die zijn beoordeeld in de testset. Dit beperkt de mogelijke waarden die worden voorspeld.

Wanneer u het model operationeel maakt, wijzigt u meestal de voorspellingsmodus om aanbevelingen te doen op basis van alle mogelijke items, om de beste voorspellingen te krijgen. Voor veel van deze voorspellingen, is er geen overeenkomstige grondwaarheid. De nauwkeurigheid van de aanbeveling kan dus niet op dezelfde manier worden geverifieerd als tijdens pijplijnbewerkingen.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
