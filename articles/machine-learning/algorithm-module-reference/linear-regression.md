---
title: 'Lineaire regressie: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Lineaire regressie in Azure Machine Learning om een lineair regressiemodel te maken voor gebruik in een pijplijn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 602553637e21b17aa4f9bc7402753af024c697c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477558"
---
# <a name="linear-regression-module"></a>Module Lineaire regressie
In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een lineair regressiemodel te maken voor gebruik in een pijplijn.  Lineaire regressie probeert een lineaire relatie tot stand te brengen tussen een of meer onafhankelijke variabelen en een numerieke uitkomst of afhankelijke variabele. 

U gebruikt deze module om een lineaire regressiemethode te definiëren en vervolgens een model te trainen met behulp van een gelabelde gegevensset. Het getrainde model kan vervolgens worden gebruikt om voorspellingen te doen.

## <a name="about-linear-regression"></a>Over lineaire regressie

Lineaire regressie is een veel voorkomende statistische methode, die is overgenomen in machine learning en verbeterd met veel nieuwe methoden voor het monteren van de lijn en het meten van fouten. In de meest elementaire zin verwijst regressie naar het voorspellen van een numeriek doel. Lineaire regressie is nog steeds een goede keuze wanneer u een eenvoudig model wilt voor een basisvoorspellende taak. Lineaire regressie heeft ook de neiging om goed te werken op hoog-dimensionale, schaarse datasets zonder complexiteit.

Azure Machine Learning ondersteunt een verscheidenheid aan regressiemodellen, naast lineaire regressie. De term "regressie" kan echter losjes worden geïnterpreteerd en sommige typen regressie in andere hulpmiddelen worden niet ondersteund.

+ Het klassieke regressieprobleem omvat één onafhankelijke variabele en een afhankelijke variabele. Dit wordt *eenvoudige regressie*genoemd.  Deze module ondersteunt eenvoudige regressie.

+ *Meerdere lineaire regressie* omvat twee of meer onafhankelijke variabelen die bijdragen aan één afhankelijke variabele. Problemen waarbij meerdere ingangen worden gebruikt om een enkele numerieke uitkomst te voorspellen, worden ook *wel multivariate lineaire regressie*genoemd.

    De **Module Lineaire Regressie** kan deze problemen oplossen, net als de meeste andere regressiemodules.

+ *Multi-label regressie* is de taak van het voorspellen van meerdere afhankelijke variabelen binnen een enkel model. In multi-label logistieke regressie kan bijvoorbeeld een voorbeeld worden toegewezen aan meerdere verschillende labels. (Dit is anders dan de taak van het voorspellen van meerdere niveaus binnen een variabele met één klasse.)

    Dit type regressie wordt niet ondersteund in Azure Machine Learning. Als u meerdere variabelen wilt voorspellen, maakt u een afzonderlijke leerling voor elke uitvoer die u wilt voorspellen.

Statistici ontwikkelen al jaren steeds geavanceerdere methoden voor regressie. Dit geldt zelfs voor lineaire regressie. Deze module ondersteunt twee methoden om fouten te meten en de regressielijn te passen: de gewone methode voor de kleinste vierkanten en gradiëntafdaling.

- **Verloopafdaling** is een methode die de hoeveelheid fouten bij elke stap van het modeltrainingsproces minimaliseert. Er zijn veel variaties op gradiënt afdaling en de optimalisatie ervan voor verschillende leerproblemen is uitgebreid bestudeerd. Als u deze optie kiest voor **de methode Oplossing,** u verschillende parameters instellen om de stapgrootte, leersnelheid enzovoort te bepalen. Deze optie ondersteunt ook het gebruik van een geïntegreerde parametersweep.

- **Gewone kleinste vierkanten** is een van de meest gebruikte technieken in lineaire regressie. De kleinste vierkanten zijn bijvoorbeeld de methode die wordt gebruikt in de Analysis Toolpak voor Microsoft Excel.

    Gewone kleinste vierkanten verwijst naar de verliesfunctie, die fout berekent als de som van het kwadraat van de afstand van de werkelijke waarde tot de voorspelde lijn, en past bij het model door de kwadraatfout te minimaliseren. Deze methode gaat uit van een sterke lineaire relatie tussen de ingangen en de afhankelijke variabele.

## <a name="configure-linear-regression"></a>Lineaire regressie configureren

Deze module ondersteunt twee methoden voor het aanpassen van een regressiemodel, met verschillende opties:

+ [Een regressiemodel aanpassen met gewone kleinste vierkantjes](#create-a-regression-model-using-ordinary-least-squares)

    Voor kleine gegevenssets is het het beste om gewone kleinste vierkanten te selecteren. Dit moet vergelijkbare resultaten opleveren als Excel.
    
+ [Een regressiemodel maken met behulp van online verloopafdaling](#create-a-regression-model-using-online-gradient-descent)

    Gradiëntafdaling is een betere verliesfunctie voor modellen die complexer zijn of die te weinig trainingsgegevens hebben gezien het aantal variabelen.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Een regressiemodel maken met gewone kleinste vierkanten

1. Voeg de module **Lineair regressiemodel** toe aan uw pijplijn in de ontwerper.

    U vindt deze module in de categorie **Machine Learning.** Vouw **initialiserenmodel**uit, vouw **Regressie**uit en sleep vervolgens de module **Lineair regressiemodel** naar de pijplijn.

2. Selecteer in het deelvenster **Eigenschappen** in de vervolgkeuzelijst **Oplossingsmethode** de optie **Gewoonminste vierkanten**. Met deze optie geeft u de berekeningsmethode op die wordt gebruikt om de regressielijn te vinden.

3. Typ in **L2-regularisatiegewicht**de te gebruiken waarde als het gewicht voor L2-regularisatie. We raden u aan een niet-nulwaarde te gebruiken om overfitting te voorkomen.

     Zie dit artikel: [L1 en L2 Regularisatie voor Machine Learning voor](https://msdn.microsoft.com/magazine/dn904675.aspx) meer informatie over de invloed van regularisatie op modelfittingen

4. Selecteer de **optie,Onderscheppingsterm opnemen**, als u de term voor de onderschepping wilt weergeven.

    Schakel deze optie uit als u de regressieformule niet hoeft te controleren.

5. Voor **Random number seed**u optioneel een waarde typen om de random number generator te zaaien die door het model wordt gebruikt.

    Het gebruik van een zaadwaarde is handig als u dezelfde resultaten wilt behouden voor verschillende runs van dezelfde pijplijn. Anders is het standaard om een waarde van de systeemklok te gebruiken.


7. Voeg de module [Treinmodel](./train-model.md) toe aan uw pijplijn en sluit een gelabelde gegevensset aan.

8. Verzend de pijplijn.

### <a name="results-for-ordinary-least-squares-model"></a>Resultaten voor gewone kleinste vierkanten model

Na de training is voltooid:


+ Als u voorspellingen wilt doen, sluit u het getrainde model aan op de module [Scoremodel,](./score-model.md) samen met een gegevensset met nieuwe waarden. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Een regressiemodel maken met behulp van online verloopafdaling

1. Voeg de module **Lineair regressiemodel** toe aan uw pijplijn in de ontwerper.

    U vindt deze module in de categorie **Machine Learning.** **Initialiserenmodel**uitvouwen, **Regressie**uitbreiden en de module **Lineair regressiemodel** naar uw pijplijn slepen

2. Kies in het deelvenster **Eigenschappen** in de vervolgkeuzelijst **Oplossingsmethode** de optie **Online verloopafdaling** als de berekeningsmethode die wordt gebruikt om de regressielijn te vinden.

3. Geef **bij De trainermodus Maken**aan of u het model wilt trainen met een vooraf gedefinieerde set parameters of dat u het model wilt optimaliseren met behulp van een parametersweep.

    + **Eén parameter:** Als u weet hoe u het lineaire regressienetwerk wilt configureren, u een specifieke set waarden als argumenten opgeven.
    
    + **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren.  

   
4. Voor **de leersnelheid**geeft u de initiële leersnelheid voor de stochastische gradatie-dalingsoptimizer op.

5. Typ **bij Aantal trainingstijdperken**een waarde die aangeeft hoe vaak het algoritme door voorbeelden moet worden gewijzigd. Voor gegevenssets met een klein aantal voorbeelden moet dit aantal groot zijn om convergentie te bereiken.

6. **Functies normaliseren:** Als u de numerieke gegevens die worden gebruikt om het model te trainen al hebt genormaliseerd, u deze optie deselecteren. Standaard normaliseert de module alle numerieke ingangen tot een bereik tussen 0 en 1.

    > [!NOTE]
    > 
    > Vergeet niet om dezelfde normalisatiemethode toe te passen op nieuwe gegevens die worden gebruikt voor het scoren.

7. Typ in **L2-regularisatiegewicht**de te gebruiken waarde als het gewicht voor L2-regularisatie. We raden u aan een niet-nulwaarde te gebruiken om overfitting te voorkomen.

    Zie dit artikel: [L1 en L2 Regularisatie voor Machine Learning voor](https://msdn.microsoft.com/magazine/dn904675.aspx) meer informatie over de invloed van regularisatie op modelfittingen


9. Selecteer de **optie, Verlaag de leersnelheid**, als u wilt dat de leersnelheid afneemt naarmate de iteraties vorderen.  

10. Voor **Random number seed**u optioneel een waarde typen om de random number generator te zaaien die door het model wordt gebruikt. Het gebruik van een zaadwaarde is handig als u dezelfde resultaten wilt behouden voor verschillende runs van dezelfde pijplijn.


12. Voeg een gelabelde gegevensset en een van de trainingsmodules toe.

    Als u geen parametersweep gebruikt, gebruikt u de module [Treinmodel.](train-model.md)

13. Verzend de pijplijn.

### <a name="results-for-online-gradient-descent"></a>Resultaten voor online gradiëntafdaling

Na de training is voltooid:

+ Als u voorspellingen wilt doen, sluit u het getrainde model aan op de module [Scoremodel,](./score-model.md) samen met nieuwe invoergegevens.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 