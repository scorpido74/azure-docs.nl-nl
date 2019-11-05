---
title: 'Aanbeveling evalueren: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module beoordeling aanbevelen in Azure Machine Learning service om de nauw keurigheid van de voor spellingen van het aanbevolen model te evalueren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517993"
---
# <a name="evaluate-recommender"></a>Aanbeveling evalueren

In dit artikel wordt beschreven hoe u de module **Aanbevolen procedures evalueren** in azure machine learning Designer (preview) gebruikt om de nauw keurigheid van de voor spellingen van een aanbevelings model te meten. Met deze module kunt u vier verschillende soorten aanbevelingen evalueren:  
  
-   Classificaties voor speld voor een bepaalde gebruiker en een bepaald item  
  
-   Aanbevolen items voor een bepaalde gebruiker  
  
Wanneer u voor spellingen maakt met behulp van een aanbevelings model, worden er enigszins verschillende resultaten geretourneerd voor elk van deze ondersteunde Voorspellings typen. In de module **beoordeling aanbevelen** wordt het type voor spelling afgeleid van de kolom indeling van de Score gegevensset. De **gescoorde gegevensset** kan bijvoorbeeld het volgende bevatten:

- gebruikers-item-beoordeling drieën
- gebruikers en de aanbevolen items

De module past ook de juiste prestatie gegevens toe, op basis van het type voor spellingen dat wordt gemaakt. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Aanbevolen evaluatie configureren

De module voor het **evalueren** van een aanbeveling vergelijkt de voor spellingen die worden uitgevoerd door een aanbevolen model met de bijbehorende gegevens op ' grond waarheid '. De module [Score SVD aanbevelen](score-svd-recommender.md) produceert bijvoorbeeld gescoorde gegevens sets die kunnen worden geanalyseerd met de **aanbeveling evalueren**.

### <a name="requirements"></a>Vereisten

**Aanbevolen wordt om** de volgende gegevens sets als invoer te evalueren. 
  
#### <a name="test-dataset"></a>Gegevensset testen

De **test gegevensset** bevat de gegevens van de ' grond waarheid ' in de vorm van **triples van gebruikers items-beoordeling**.  

#### <a name="scored-dataset"></a>Score gegevensset

De **gescoorde gegevensset** bevat de voor spellingen die zijn gegenereerd door het aanbevelings model.  
  
De kolommen in deze tweede gegevensset zijn afhankelijk van het soort voor spelling dat u tijdens de score hebt uitgevoerd. De gescoorde gegevensset kan bijvoorbeeld een van de volgende bevatten:

- Gebruikers, items en de classificatie die de gebruiker voor het item zou kunnen geven
- Een lijst met gebruikers en items die hiervoor worden aanbevolen 

### <a name="metrics"></a>Metrische gegevens

Prestatie gegevens voor het model worden gegenereerd op basis van het type invoer. Zie de volgende secties voor meer informatie:

+ [Voorspelde classificaties evalueren](#evaluate-predicted-ratings)
+ [Aanbevelingen voor item evalueren](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>Voorspelde classificaties evalueren  

Bij het evalueren van voorspelde beoordelingen moet de Score van de gescoorde gegevensset (de tweede invoer voor het evalueren van de **Aanbevolen**functie) een beoordeling van drie punten van **gebruikers items**bevatten, aan deze vereisten voldoen:
  
-   De eerste kolom van de gegevensset bevat gebruikers-id's.  
  
-   De tweede kolom bevat de item-id's.  
  
-   De derde kolom bevat de bijbehorende beoordelingen van gebruikers items.  
  
> [!IMPORTANT] 
> Voor een geslaagde evaluatie moeten de kolom namen respectievelijk `User`, `Item`en `Rating`zijn.  
  
De **Aanbevolen functie evalueren** vergelijkt de classificaties in de gegevensset van de grond waarheid met de voorspelde classificaties van de gescoorde gegevensset en berekent de **gemiddelde absolute fout** (Mae) en de **wortel gemiddelde fout** (RMSE).



## <a name="evaluate-item-recommendations"></a>Aanbevelingen voor item evalueren

Bij het evalueren van de aanbeveling van een item gebruikt u een gescoorde gegevensset die de aanbevolen items voor elke gebruiker bevat:
  
-   De eerste kolom van de gegevensset moet de gebruikers-id bevatten.  
  
-   Alle volgende kolommen moeten de bijbehorende aanbevolen item-id's bevatten, geordend op basis van de relevantie van een item voor de gebruiker. 

    Voordat u deze gegevensset verbindt, wordt u aangeraden de gegevensset te sorteren zodat de meest relevante items het eerst worden geleverd.  



> [!IMPORTANT] 
> Als u wilt evalueren, kunt u het **beste** de kolom namen `User`, `Item 1`, `Item 2``Item 3` enzovoort gebruiken.  
  
Bij **evaluatie van evalueren** wordt de gemiddelde genormaliseerde cumulatieve winst (**NDCG**) berekend en geretourneerd in de uitvoer gegevensset.  
  
Omdat het niet mogelijk is om de werkelijke "vloer waarheid" te weten voor de aanbevolen items, moet u **Aanbevolen** worden de beoordelingen van gebruikers items in de gegevensset test gebruiken als toename in de berekening van de NDCG. Om te kunnen evalueren, moet de Score module voor aanbevolen beoordelingen alleen aanbevelingen voor items met een grondige waarheids classificatie (in de test gegevensset) produceren.  
  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
