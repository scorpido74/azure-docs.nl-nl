---
title: 'Aanbeveling van Score SVD: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module Score SVD recommending in Azure Machine Learning service om aanbevelingen voor voor spellingen te beoordelen voor een gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 25de69873857512a70d6417973d4a85883ac6455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517915"
---
# <a name="score-svd-recommender"></a>Aanbeveling van Score SVD

In dit artikel wordt beschreven hoe u de module **Score SVD aanbevelen** in azure machine learning Designer (preview) gebruikt. Gebruik deze module om voor spellingen te maken met behulp van een getraind aanbevelings model op basis van het algoritme SVD (decomposite van één waarde).

De SVD-aanbevolen functie kan twee verschillende soorten voor spellingen genereren:

- [Beoordelingen voor een bepaalde gebruiker en een bepaald item voors pellen](#predict-ratings)

- [Het aanbevelen van items aan een bepaalde gebruiker](#recommend)

Wanneer u het tweede type voor spellingen maakt, kunt u in de *productie modus* of de *evaluatie modus*uitvoeren.

- De **productie modus** houdt rekening met alle gebruikers of items en wordt doorgaans gebruikt in een webservice.

    U kunt scores maken voor nieuwe gebruikers, niet alleen voor gebruikers die tijdens de training zijn gezien. Zie [deze sectie](#technical-notes)voor meer informatie. 

- De **evaluatie modus** werkt op een beperkt aantal gebruikers of items die kunnen worden geëvalueerd en wordt doorgaans gebruikt tijdens pipelineation.

Voor meer informatie over de SVD-aanbevolen algoritme raadpleegt u het document Research: [matrix factorization technieken voor aanbevolen systemen](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

                                                                                                                                    


## <a name="how-to-configure-score-svd-recommender"></a>Advieser voor Score SVD configureren

Deze module ondersteunt verschillende soorten aanbevelingen, elk met verschillende vereisten. Klik op de koppeling voor het type gegevens dat u hebt en het type aanbeveling dat u wilt maken.

+ [Voor spel classificaties](#predict-ratings)
+ [Aanbevolen items](#recommend)

###  <a name="predict-ratings"></a>Voor spel classificaties

Wanneer u de classificaties voor spelt, wordt in het model berekend hoe een bepaalde gebruiker reageert op een bepaald item, op basis van de trainings gegevens. Daarom moeten de invoer gegevens voor de Score zowel een gebruiker als het te beoordelen item bevatten.

1. Voeg een getraind aanbevelings model aan uw pijp lijn toe en verbind het met de **getrainde SVD-aanbeveling**.  U moet het model maken met behulp van [Train SVD aanbevelen](train-SVD-recommender.md).

2. **Voorspellings soort aanbevolen**: Selecteer **beoordelings voorspelling**. Er zijn geen verdere para meters vereist.

3. Voeg de gegevens toe waarvoor u de voor spellingen wilt maken en verbind deze met de **gegevensset voor de Score**.

    Om beoordelingen te voors pellen, moet de invoer gegevensset gebruikers paren bevatten.

    De gegevensset kan een optionele derde kolom met beoordelingen bevatten voor het combi neren van gebruikers items in de kolommen eerste en tweede, maar de derde kolom wordt genegeerd tijdens de voor spelling.

4. Voer de pijplijn uit.

### <a name="results-for-rating-predictions"></a>Resultaten voor de beoordeling van voor spellingen 

De uitvoer gegevensset bevat drie kolommen met de gebruiker, het item en de voorspelde classificatie voor elke invoer gebruiker en elk item.

###  <a name="recommend"></a>Aanbevelen 

Als u items wilt aanbevelen voor gebruikers, geeft u een lijst met gebruikers en items op als invoer. Op basis van deze gegevens gebruikt het model de kennis over bestaande items en gebruikers om een lijst met items te genereren met een waarschijnlijk beroep op elke gebruiker. U kunt het aantal geretourneerde aanbevelingen aanpassen en een drempel instellen voor het aantal eerdere aanbevelingen dat vereist is om een aanbeveling te genereren.

1. Voeg een getraind aanbevelings model aan uw pijp lijn toe en verbind het met de **getrainde SVD-aanbeveling**.  U moet het model maken met behulp van [Train SVD aanbevelen](train-svd-recommender.md).

2. Als u items wilt aanbevelen voor een bepaalde lijst met gebruikers, stelt u de **Voorspellings soort voor aanbevolen** functies in op **item aanbeveling**.

3. **Aanbevolen item selecteren**: Geef aan of u de Score module in productie of voor model evaluatie gebruikt door een van deze waarden te kiezen:

    - **Van alle items**: Selecteer deze optie als u een pijp lijn instelt voor gebruik in een webservice of productie omgeving.  Met deze optie schakelt u de **productie modus**in. de module maakt aanbevelingen van alle items die tijdens de training worden weer gegeven.

    - **Van geclassificeerde items (voor model evaluatie)** : Selecteer deze optie als u een model ontwikkelt of test. Met deze optie wordt de **evaluatie modus**ingeschakeld. in de module worden alleen aanbevelingen gedaan van die items in de ingevoerde gegevensset die zijn geclassificeerd.
    
    - **Van niet-geclassificeerde items (om nieuwe items voor te stellen voor gebruikers)** : Selecteer deze optie en de module voert alleen aanbevelingen uit van die items in de trainings gegevensset die nog niet zijn geclassificeerd. 

4. Voeg de gegevensset toe waarvoor u voor spellingen wilt maken en verbind deze met de **gegevensset om**deze te beoordelen.

    - Voor **van alle items**moet de invoer gegevensset bestaan uit één kolom die de id's van gebruikers bevat waarvoor aanbevelingen moeten worden gedaan.

        De gegevensset kan extra twee kolommen met item-id's en classificaties bevatten, maar deze twee kolommen worden genegeerd. 

    - Voor **van geclassificeerde items (voor model evaluatie)** moet de invoer gegevensset bestaan uit **gebruikers paren**. De eerste kolom moet de **gebruikers** -id bevatten. De tweede kolom moet de bijbehorende **item** -id's bevatten.

        De gegevensset kan een derde kolom van classificaties van gebruikers items bevatten, maar deze kolom wordt genegeerd.

    - Voor **van niet-geclassificeerde items (om nieuwe items voor test Ellen voor gebruikers)** , moet de invoer gegevensset bestaan uit **gebruikers paren**. De eerste kolom moet de **gebruikers** -id bevatten. De tweede kolom moet de bijbehorende **item** -id's bevatten.

        De gegevensset kan een derde kolom van classificaties van gebruikers items bevatten, maar deze kolom wordt genegeerd.

5. **Maximum aantal items dat aan een gebruiker**kan worden aanbevolen: Typ het aantal items dat voor elke gebruiker moet worden geretourneerd. Standaard worden vijf items aanbevolen.

6. **Minimum grootte van de aanbevelings groep per gebruiker**: Typ een waarde die aangeeft hoeveel eerdere aanbevelingen zijn vereist.  Deze para meter is standaard ingesteld op 2, wat betekent dat het item moet worden aanbevolen door ten minste twee andere gebruikers.

    Deze optie moet alleen worden gebruikt als u een score in de evaluatie modus hebt. De optie is niet beschikbaar als u **van alle items** of **van niet-geclassificeerde items (om nieuwe items te suggereren voor gebruikers)** selecteert.

7.  Voor **van niet-geclassificeerde items (om nieuwe items te suggereren voor gebruikers)** , gebruikt u de derde invoer poort, met de naam **trainings gegevens**, om items te verwijderen die al zijn geclassificeerd op basis van de Voorspellings resultaten.

    Als u dit filter wilt Toep assen, koppelt u de oorspronkelijke trainings gegevensset aan de invoer poort.

8. Voer de pijplijn uit.

### <a name="results-of-item-recommendation"></a>Resultaten van aanbeveling van het item

De gescoorde gegevensset die door **Score SVD** wordt geretourneerd, vermeldt de aanbevolen items voor elke gebruiker.

- De eerste kolom bevat de gebruikers-id's.
- Er worden een aantal extra kolommen gegenereerd, afhankelijk van de waarde die u hebt ingesteld voor het **maximum aantal items dat aan een gebruiker**moet worden aanbevolen. Elke kolom bevat een aanbevolen item (per id). De aanbevelingen worden geordend op basis van de gebruikers-item affiniteit, waarbij het item met de hoogste affiniteit in kolom, **item 1**, is geplaatst.

> [!WARNING]
> Deze gescoorde gegevensset kan niet worden geëvalueerd met behulp van de module [aanbeveling evalueren](evaluate-recommender.md) .


##  <a name="technical-notes"></a>Technische opmerkingen

In deze sectie vindt u antwoorden op enkele veelgestelde vragen over het gebruik van de aanbevolen functie voor het maken van voor spellingen.  

###  <a name="production-use-of-the-svd-recommender"></a>Productie gebruik van de SVD-aanbeveling

Als u een pijp lijn hebt met de SVD-aanbeveling en het model naar productie wilt verplaatsen, moet u rekening houden met de volgende belang rijke verschillen bij het gebruik van de aanbeveling in de evaluatie modus en in de productie modus:

- Voor evaluatie, per definitie, zijn voor spellingen vereist die kunnen worden gecontroleerd tegen de achterliggende *waarheid* in een testset. Wanneer u de aanbevolen functie evalueert, moet u daarom alleen items voors pellen die zijn geclassificeerd in de testset. Dit kan nood zakelijk zijn om de mogelijke waarden te beperken die worden voor speld.

    Wanneer u echter het model operationeel maken, wijzigt u doorgaans de Voorspellings modus om aanbevelingen te doen op basis van alle mogelijke items, om de beste voor spellingen te krijgen. Voor veel van deze voor spellingen is er geen overeenkomende grond waarheid, waardoor de nauw keurigheid van de aanbeveling niet op dezelfde manier kan worden gecontroleerd als tijdens pipelineation.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
