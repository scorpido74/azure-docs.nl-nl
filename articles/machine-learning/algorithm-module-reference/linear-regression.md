---
title: 'Lineaire regressie: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de lineaire regressie module in Azure Machine Learning service om een lineair regressie model te maken voor gebruik in een experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 506f37a2e01f428ccadc0368bd2efb6b58c9106c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128678"
---
# <a name="linear-regression-module"></a>Lineaire regressie module
In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om een lineair regressie model te maken voor gebruik in een experiment.  Lineaire regressie pogingen om een lineaire relatie tot stand te brengen tussen een of meer onafhankelijke variabelen en een numeriek resultaat of een afhankelijke variabele. 

Met deze module kunt u een lineaire regressie methode definiëren en vervolgens een model trainen met een gegevensset met een label. Het getrainde model kan vervolgens worden gebruikt voor het maken van voor spellingen.

## <a name="about-linear-regression"></a>Over lineaire regressie

Lineaire regressie is een gemeen schappelijke statistische methode, die in machine learning is aangenomen en uitgebreid met veel nieuwe methoden voor het aanpassen van de lijn en het meten van de fout. In de meest eenvoudige zin verwijst regressie naar de voor spelling van een numeriek doel. Lineaire regressie is nog steeds een goede keuze als u een eenvoudig model voor een eenvoudige, voorspellende taak wilt. Lineaire regressie kan ook worden gebruikt voor zeer dimensionale, sparse gegevens sets die geen complexiteit hebben.

Azure Machine Learning ondersteunt diverse regressie modellen, naast lineaire regressie. De term ' regressie ' kan echter soepel worden geïnterpreteerd en bepaalde typen regressies die worden geboden in andere hulpprogram ma's, worden niet ondersteund.

+ Het klassieke regressie probleem omvat één onafhankelijke variabele en een afhankelijke variabele. Dit heet *eenvoudige regressie*.  Deze module biedt ondersteuning voor eenvoudige regressie.

+ *Meerdere lineaire regressie* omvat twee of meer onafhankelijke variabelen die bijdragen aan één afhankelijke variabele. Problemen waarbij meerdere invoer wordt gebruikt om een enkele numerieke uitkomst te voors pellen, ook wel *multidimensionale lineaire regressie*genoemd.

    Deze problemen kunnen worden opgelost met de module **Linear Regression** , evenals de meeste andere regressie modules.

+ *Regressie met meerdere labels* is de taak voor het voors pellen van meerdere afhankelijke variabelen binnen één model. Zo kan in een logistiek-regressie met meerdere labels een voor beeld worden toegewezen aan meerdere labels. (Dit wijkt af van de taak voor het voors pellen van meerdere niveaus binnen één klassen variabele.)

    Dit type regressie wordt niet ondersteund in Azure Machine Learning. Als u meerdere variabelen wilt voors pellen, maakt u een afzonderlijke cursist voor elke uitvoer die u wilt voors pellen.

Voor jaren statistici zijn steeds meer geavanceerde methoden ontwikkeld voor regressie. Dit geldt ook voor lineaire regressie. Deze module ondersteunt twee methoden om een fout te meten en te voldoen aan de regressie regel: normale, minimale kwadraten methode en daal van kleur overgang.

- **Gradient Daal** is een methode die de fout waarde bij elke stap van het model trainings proces minimaliseert. Er zijn veel variaties in Daal en de optimalisatie voor verschillende leer problemen is uitgebreid bestudeerd. Als u deze optie voor de **oplossings methode**kiest, kunt u een aantal para meters instellen om de stap grootte, het leer tempo, enzovoort te bepalen. Deze optie biedt ook ondersteuning voor het gebruik van een geïntegreerde parameter opruiming.

- **Normale minimale kwadraten** is een van de meest gebruikte technieken in lineaire regressie. Bijvoorbeeld, minste kwadraten is de methode die wordt gebruikt in Analysis ToolPak voor micro soft Excel.

    Normale minimale kwadraten verwijzen naar de functie verlies, waarbij fout wordt berekend als de som van het kwadraat van de afstand van de werkelijke waarde tot de voorspelde regel, en past het model aan door de gekwadrateerde fout te minimaliseren. Bij deze methode wordt uitgegaan van een sterke lineaire relatie tussen de invoer en de afhankelijke variabele.

## <a name="configure-linear-regression"></a>Lineaire regressie configureren

Deze module ondersteunt twee methoden voor het aanpassen van een regressie model met verschillende opties:

+ [Een regressie model maken met online verloop Daal](#bkmk_GradientDescent)

    Gradient Daal is een betere verlies functie voor modellen die ingewik kelder zijn of die te weinig trainings gegevens hebben, gezien het aantal variabelen.



+ [Een regressie model passend maken met behulp van normale, minimale kwadraten](#bkmk_OrdinaryLeastSquares)

    Voor kleine gegevens sets kunt u het beste normale minimale kwadraten selecteren. Dit moet vergelijk bare resultaten opleveren voor Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a>Een regressie model maken met behulp van normale, minimale kwadraten

1. Voeg de module **Linear regressie model** toe aan uw experiment in de-interface.

    U kunt deze module vinden in de categorie **machine learning** . Vouw **Initialiseer model**uit, vouw **regressie**uit en sleep de module **Linear regressie model** naar uw experiment.

2. Selecteer in het deel venster **Eigenschappen** in de vervolg keuzelijst **oplossings methode** de optie **normale kleinste kwadraten**. Met deze optie geeft u de reken methode op die wordt gebruikt om de regressie regel te vinden.

3. In **L2-regularisatie gewicht**typt u de waarde die moet worden gebruikt als gewicht voor de L2-regularisatie. U kunt het beste een andere waarde dan nul gebruiken om te voor komen dat deze wordt overpast.

     Raadpleeg dit artikel voor meer informatie over hoe regularisatie van invloed is op model fitting: [L1 en L2-regularisatie voor Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Selecteer de optie, **Voeg interceptie term**toe als u de term voor het snij punt wilt weer geven.

    Schakel deze optie uit als u de regressie formule niet hoeft te controleren.

5. Voor **wille keurig getal Seed**kunt u optioneel een waarde typen om de wille keurige generator te seeden die door het model wordt gebruikt.

    Het gebruik van een Seed-waarde is handig als u dezelfde resultaten wilt behouden in verschillende uitvoeringen van hetzelfde experiment. Anders wordt de standaard instelling gebruikt voor het gebruik van een waarde uit de systeem klok.


7. Voeg de module [Train model](./train-model.md) toe aan uw experiment en Verbind een gegevensset met een label.

8. Voer het experiment uit.

## <a name="results-for-ordinary-least-squares-model"></a>Resultaten voor model met de normale kleinste kwadraten

Nadat de training is voltooid:

+ Als u de para meters van het model wilt weer geven, klikt umet de rechter muisknop op de leider uitvoer en selecteert u visualiseren.

+ Als u voor spellingen wilt doen, verbindt u het getrainde model met de module [score model](./score-model.md) , samen met een gegevensset met nieuwe waarden. 


## <a name="bkmk_GradientDescent"></a>Een regressie model maken met online verloop Daal

1. Voeg de module **Linear regressie model** toe aan uw experiment in de-interface.

    U kunt deze module vinden in de categorie **machine learning** . Vouw **initialiseren model**uit, vouw **regressie**uit en sleep de module **Linear regressie model** naar uw experiment

2. Kies in het deel venster **Eigenschappen** in de vervolg keuzelijst **oplossings methode** de optie **online verloop Daal** als de reken methode die wordt gebruikt om de regressie lijn te vinden.

3. Voor de **modus trainer maken**geeft u aan of u het model wilt trainen met een vooraf gedefinieerde set para meters of als u het model wilt optimaliseren met behulp van een parameter sweep.

    + **Eén para meter**: Als u weet hoe u het lineaire regressie netwerk wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.

   
4. Voor het **leer tempo**geeft u het eerste leer tempo op voor de stochastische Daal Optimizer van een kleur overgang.

5. Voor het **aantal trainings**-epochen typt u een waarde die aangeeft hoe vaak de algoritme voor beelden moet herhalen. Voor gegevens sets met een klein aantal voor beelden moet dit aantal groot zijn om convergentie te bereiken.

6. **Functies normaliseren**: Als u de numerieke gegevens die worden gebruikt om het model te trainen al hebt genormaliseerd, kunt u deze optie deselecteren. Standaard normaleert de module alle numerieke invoer naar een bereik tussen 0 en 1.

    > [!NOTE]
    > 
    > Vergeet niet om dezelfde normalisatie methode toe te passen op nieuwe gegevens die worden gebruikt voor een score.

7. In **L2-regularisatie gewicht**typt u de waarde die moet worden gebruikt als gewicht voor de L2-regularisatie. U kunt het beste een andere waarde dan nul gebruiken om te voor komen dat deze wordt overpast.

    Raadpleeg dit artikel voor meer informatie over hoe regularisatie van invloed is op model fitting: [L1 en L2-regularisatie voor Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Selecteer de optie, **Verminder het leer**tempo als u wilt dat het leer tempo afneemt als de voortgang van herhalingen.  

10. Voor **wille keurig getal Seed**kunt u optioneel een waarde typen om de wille keurige generator te seeden die door het model wordt gebruikt. Het gebruik van een Seed-waarde is handig als u dezelfde resultaten wilt behouden in verschillende uitvoeringen van hetzelfde experiment.


12. Voeg een gegevensset met een label en een van de trainings modules toe.

    Als u geen parameter sweep gebruikt, gebruikt u de module [Train model](train-model.md) .

13. Voer het experiment uit.

## <a name="results-for-online-gradient-descent"></a>Resultaten voor online verloop Daal

Nadat de training is voltooid:

+ Als u voor spellingen wilt doen, verbindt u het getrainde model met de module [score model](./score-model.md) , samen met nieuwe invoer gegevens.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 