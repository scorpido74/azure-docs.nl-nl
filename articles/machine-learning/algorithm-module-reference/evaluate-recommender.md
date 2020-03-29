---
title: 'Recommender evalueren: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Recommender evalueren in Azure Machine Learning om de nauwkeurigheid van voorspellingsmodelvoorspellingen te evalueren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76312257"
---
# <a name="evaluate-recommender"></a>Aanbevelingsfunctie voor evaluatie

In dit artikel wordt beschreven hoe u de module Recommender evalueren gebruiken in Azure Machine Learning-ontwerper (voorbeeld). Het doel is om de nauwkeurigheid van voorspellingen te meten die een aanbevelingsmodel heeft gemaakt. Met deze module u verschillende soorten aanbevelingen evalueren:  
  
-   Beoordelingen voorspeld voor een gebruiker en een item    
-   Aanbevolen objecten voor een gebruiker  
  
Wanneer u voorspellingen maakt met behulp van een aanbevelingsmodel, worden voor elk van deze ondersteunde voorspellingstypen iets andere resultaten geretourneerd. De module Recommender evalueren leidt het soort voorspelling af uit de kolomindeling van de gescoorde gegevensset. De gegevensset met punten kan bijvoorbeeld het:

- Triples van gebruikers-item-rating
- Gebruikers en hun aanbevolen items

De module past ook de juiste prestatiestatistieken toe, gebaseerd op het type voorspelling dat wordt uitgevoerd. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Recommender evalueren configureren

De module Recommender evalueren vergelijkt de voorspellingsuitvoer met behulp van een aanbevelingsmodel met de bijbehorende "grondwaarheid"-gegevens. De module [Score SVD Recommender](score-svd-recommender.md) produceert bijvoorbeeld gescoorde gegevenssets die u analyseren met evalueren Recommender.

### <a name="requirements"></a>Vereisten

Voor het evalueren van Recommender zijn de volgende gegevenssets vereist als invoer. 
  
#### <a name="test-dataset"></a>Testgegevensset

De testdataset bevat de "ground truth" gegevens in de vorm van triples met user-item rating.  

#### <a name="scored-dataset"></a>Gegevensset Gescoord

De gescoorde gegevensset bevat de voorspellingen die het aanbevelingsmodel heeft gegenereerd.  
  
De kolommen in deze tweede gegevensset zijn afhankelijk van het soort voorspelling dat u tijdens het scoringsproces hebt uitgevoerd. De gegevensset met punten kan bijvoorbeeld een van de volgende gegevens bevatten:

- Gebruikers, items en de beoordelingen die de gebruiker waarschijnlijk zou geven voor het item
- Een lijst met gebruikers en items die voor hen worden aanbevolen 

### <a name="metrics"></a>Metrische gegevens

Prestatiestatistieken voor het model worden gegenereerd op basis van het type invoer. De volgende secties geven details.

## <a name="evaluate-predicted-ratings"></a>Voorspelde beoordelingen evalueren  

Wanneer u voorspelde beoordelingen evalueert, moet de gegevensset voor de score (de tweede invoer voor Recommender evalueren) triples van gebruikers-items bevatten die aan deze vereisten voldoen:
  
-   De eerste kolom van de gegevensset bevat de gebruikers-id's.    
-   De tweede kolom bevat de item-id's.  
-   De derde kolom bevat de bijbehorende gebruikersitemclassificaties.  
  
> [!IMPORTANT] 
> Om de evaluatie te laten `User`slagen, `Rating`moeten de kolomnamen respectievelijk , en , zijn. `Item`  
  
Evalueren Recommender vergelijkt de beoordelingen in de "ground truth" dataset met de voorspelde beoordelingen van de gescoorde dataset. Vervolgens berekent het de gemiddelde absolute fout (MAE) en de root-gemiddelde kwadraatfout (RMSE).



## <a name="evaluate-item-recommendations"></a>Aanbevelingen voor items evalueren

Wanneer u aanbevelingen voor items evalueert, gebruikt u een gescoorde gegevensset met de aanbevolen items voor elke gebruiker:
  
-   De eerste kolom van de gegevensset moet de gebruikers-id bevatten.    
-   Alle volgende kolommen moeten de bijbehorende aanbevolen artikel-id's bevatten, geordend op basis van hoe relevant een item is voor de gebruiker. 

Voordat u deze gegevensset verbindt, raden we u aan de gegevensset te sorteren, zodat de meest relevante items op de eerste plaats komen.  

> [!IMPORTANT] 
> Als u Recommender evalueren wilt laten `User` `Item 1`werken, moeten de kolomnamen , , `Item 2`enzovoort, `Item 3` zijn.  
  
Evalueren Recommender berekent de gemiddelde genormaliseerde verdisconteerde cumulatieve winst (NDCG) en retourneert deze in de uitvoergegevensset.  
  
Omdat het onmogelijk is om de werkelijke "grondwaarheid" voor de aanbevolen items te kennen, gebruikt Evaluate Recommender de gebruikersitemclassificaties in de testgegevensset als winst in de berekening van de NDCG. Om te evalueren, moet de scoremodule voor recommenderalleen aanbevelingen produceren voor items met 'ground truth'-classificaties (in de testgegevensset).  
  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
