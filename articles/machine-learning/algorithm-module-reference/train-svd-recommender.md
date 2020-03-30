---
title: 'Trein SVD Recommender: Module Referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Train SVD Recommender-module in Azure Machine Learning om een Bayesiaanse recommender te trainen met behulp van het SVD-algoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477405"
---
# <a name="train-svd-recommender"></a>Aanbevelingsfunctie van SVD-training

In dit artikel wordt beschreven hoe u de module TreinSVD Recommender gebruiken in Azure Machine Learning-ontwerper (voorbeeld). Gebruik deze module om een aanbevelingsmodel te trainen op basis van het SVD-algoritme (Single Value Decomposition).  

De Train SVD Recommender module leest een dataset van user-item-rating triples. Het retourneert een getrainde SVD-aanstenaar. U het getrainde model vervolgens gebruiken om beoordelingen te voorspellen of aanbevelingen te genereren met behulp van de module [Score SVD Recommender.](score-svd-recommender.md)  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Meer informatie over aanbevelingsmodellen en de SVD-aanbeveling  

Het belangrijkste doel van een aanbevelingssysteem is om een of meer *items* aan te bevelen aan *gebruikers* van het systeem. Voorbeelden van een item zijn mogelijk een film, restaurant, boek of nummer. Een gebruiker kan een persoon, een groep personen of een andere entiteit zijn met artikelvoorkeuren.  

Er zijn twee belangrijke benaderingen van recommender systemen: 

+ Een **op inhoud gebaseerde** benadering maakt gebruik van functies voor zowel gebruikers als items. Gebruikers kunnen worden beschreven op eigenschappen zoals leeftijd en geslacht. Items kunnen worden beschreven door eigenschappen zoals auteur en fabrikant. U vindt typische voorbeelden van op inhoud gebaseerde aanbevelingssystemen op sociale matchmaking-sites. 
+ **Collaboratief filteren** gebruikt alleen id's van de gebruikers en de items. Het krijgt impliciete informatie over deze entiteiten uit een (schaarse) matrix van ratings gegeven door de gebruikers aan de items. We kunnen meer te weten komen over een gebruiker van de items die ze hebben beoordeeld en van andere gebruikers die dezelfde items hebben beoordeeld.  

De SVD-recommender gebruikt id's van de gebruikers en de items en een matrix van beoordelingen die door de gebruikers aan de items worden gegeven. Het is een *collaboratieve aanbeveling.* 

Zie voor meer informatie over de SVD recommender het desbetreffende onderzoeksdocument: [Matrix factorization techniques for recommender systems](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>TreinSVD-recommender configureren  

### <a name="prepare-data"></a>Gegevens voorbereiden

Voordat u de module gebruikt, moeten uw invoergegevens in de indeling zijn die het aanbevelingsmodel verwacht. Er is een trainingsgegevensset van triples voor gebruikers-items vereist.

+ De eerste kolom bevat gebruikers-id's.
+ De tweede kolom bevat item-id's.
+ De derde kolom bevat de beoordeling voor het gebruikersitempaar. Waarderingswaarden moeten een numeriek type zijn.  

De gegevensset **Filmclassificaties** in Azure Machine Learning designer (selecteer **Gegevenssets** en vervolgens **Voorbeelden)** toont de verwachte indeling:

![Filmbeoordelingen](media/module/movie-ratings-dataset.png)

In dit voorbeeld u zien dat één gebruiker meerdere films heeft beoordeeld. 

### <a name="train-the-model"></a>Het model trainen

1.  Voeg de Module Trein SVD Recommender toe aan uw pijplijn in de ontwerper en sluit deze aan op de trainingsgegevens.  
   
2.  Geef **bij Aantal factoren**het aantal factoren op dat u met de recommender moet gebruiken.  
    
    Elke factor meet hoeveel de gebruiker met betrekking is tot het item. Het aantal factoren is ook de dimensionaliteit van latente factorruimte. Nu het aantal gebruikers en items toeneemt, is het beter om een groter aantal factoren in te stellen. Maar als het aantal te groot is, kunnen de prestaties dalen.
    
3.  **Aantal iteraties van aanbevelingsalgoritmen** geeft aan hoe vaak het algoritme de invoergegevens moet verwerken. Hoe hoger dit getal is, hoe nauwkeuriger de voorspellingen zijn. Een hoger aantal betekent echter een tragere training. De standaardwaarde is 30.

4.  Voer **voor leersnelheid**een getal in tussen 0,0 en 2,0 dat de stapgrootte voor leren definieert.

    Het leerpercentage bepaalt de grootte van de stap bij elke iteratie. Als de stapgrootte te groot is, u de optimale oplossing overshooten. Als de stapgrootte te klein is, duurt de training langer om de beste oplossing te vinden. 
  
5.  Verzend de pijplijn.  


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
