---
title: 'Train SVD-aanbeveling: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de Train SVD Aanbevelener-module in Azure Machine Learning service om een Bayesiaanse-aanbeveling te trainen met het SVD-algoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3b86d77470a4f3d4fe5b005e562a8adae21f8bc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515666"
---
# <a name="train-svd-recommender"></a>Train SVD-aanbeveling

In dit artikel wordt beschreven hoe u de module **Train SVD adviseering** gebruikt in azure machine learning Designer (preview). Met deze module kunt u een aanbevelings model trainen op basis van het algoritme voor **SVD** (ontleding met één waarde).  

De **Train SVD aanbevelener** -module leest een gegevensset van de beoordeling van de gebruikers items. Er wordt een getrainde SVD-aanbeveling geretourneerd. U kunt het getrainde model vervolgens gebruiken om beoordelingen te voors pellen of aanbevelingen te genereren met behulp van de module [Score SVD aanbevelen](score-svd-recommender.md) .  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Meer informatie over aanbevelings modellen en de SVD-aanbeveling  

Het belangrijkste doel van een aanbevelings systeem is het aanbevelen van een of meer *items* aan *gebruikers* van het systeem. Voor beelden van een item kunnen een film, een restaurant, een boek of een nummer zijn. Een gebruiker kan een persoon, een groep personen of een andere entiteit met voorkeurs instellingen voor item zijn.  

Er zijn twee belang rijke benaderingen voor aanbevolen systemen. 

+ De eerste is de **op inhoud gebaseerde** benadering, waarmee u functies voor zowel gebruikers als items kunt gebruiken. Gebruikers kunnen worden beschreven door eigenschappen zoals leeftijd en geslacht, en items kunnen worden beschreven door eigenschappen als auteur en fabrikant. Typische voor beelden van systemen met aanbeveling op basis van inhoud zijn te vinden op de sociale matchmaking-sites. 
+ De tweede benadering is het **samen werken met filters**, waarbij alleen id's van de gebruikers en de items worden gebruikt en impliciete informatie over deze entiteiten wordt opgehaald uit een (sparse) matrix met beoordelingen van de gebruikers aan de items. We hebben meer informatie over een gebruiker van de items die ze hebben geclassificeerd en van andere gebruikers die dezelfde items hebben geclassificeerd.  

De SVD-aanbevolen code gebruikt id's van de gebruikers en de items, en een matrix met beoordelingen van de gebruikers aan de items. Het is een **samen werkende aanbeveling**. 

Voor meer informatie over de SVD-aanbeveling raadpleegt u de relevante Research Paper: [matrix factorization technieken voor aanbevolen systemen](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Advies van Train SVD configureren  

+ [De trainings gegevens voorbereiden](#prepare-data)
+ [Het model trainen](#train-the-model)

### <a name="prepare-data"></a>Gegevens voorbereiden

Voordat u probeert de module te gebruiken, is het essentieel dat uw gegevens de indeling hebben die wordt verwacht door het aanbevelings model. Er is een opleidings gegevensverzameling van de beoordeling van de **gebruiker-item classificatie** vereist.

#### <a name="required-dataset-of-user-item-ratings"></a>Vereiste gegevensset van gebruiker-item-classificaties

Het is belang rijk dat de invoer gegevens die worden gebruikt voor de training het juiste type gegevens in de juiste indeling bevatten: 

+ De eerste kolom moet gebruikers-id's bevatten.
+ De tweede kolom moet item-id's bevatten.
+ De derde kolom bevat de classificatie voor het combi neren van gebruikers items. Classificatie waarden moeten numeriek type zijn.  

                                                                                                                                                                                                           

De gegevensset van de **restaurant classificaties** in azure machine learning Designer (Klik op **opgeslagen gegevens sets** en vervolgens voor **beelden**) toont de verwachte indeling:

|Naam|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

In dit voor beeld kunt u zien dat één gebruiker twee afzonderlijke restaurants heeft beoordeeld. 

### <a name="train-the-model"></a>Het model trainen

1.  Voeg de module **Train SVD adviseering** toe aan uw pijp lijn in de ontwerp functie en verbind deze met de trainings gegevens.  
   
2.  Voor het **aantal factoren**typt u het nummer waarmee het aantal factoren wordt aangegeven dat moet worden gebruikt met aanbevolen.  
    
    Elke factor meet de hoeveelheid die de gebruiker aan het item heeft gekoppeld. Het aantal factoren is ook de dimensionaliteit van de latente factor ruimte. Met het aantal gebruikers en de toename van het item is het beter om een groter aantal factoren in te stellen. Als het aantal echter te groot is, kunnen de prestaties verminderen.
    
3.  **Aantal iteraties van aanbevelings algoritmen**geeft aan hoe vaak de invoer gegevens moeten worden verwerkt door de algoritme. Hoe hoger dit aantal, des te nauw keuriger de voor spellingen; de training gaat echter trager. De standaard waarde is 30.

4.  Voor het **leer tempo**typt u een getal tussen 0,0 en 2,0 dat de stap grootte tijdens het leren definieert.

    Het leer tempo bepaalt de grootte van de stap die bij elke herhaling moet worden uitgevoerd. Als de grootte van de stap te groot is, is het mogelijk dat u de optimale oplossing kunt overschrijden. Als de grootte van de stap te klein is, neemt de training meer tijd in beslag op de beste oplossing. 
  
5.  Voer de pijplijn uit.  


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
