---
title: 'Multi Class Neural Network: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de netwerk module Multi Class Neural in Azure Machine Learning voor het maken van een Neural-netwerk model dat kan worden gebruikt om een doel met meerdere waarden te voors pellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b3d79438797f47dfcc178640af60f5207b4f7650
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314637"
---
# <a name="multiclass-neural-network-module"></a>Multi Class Neural-netwerk module

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module voor het maken van een Neural-netwerk model dat kan worden gebruikt om een doel met meerdere waarden te voors pellen. 

Neural-netwerken van dit type kunnen bijvoorbeeld worden gebruikt in complexe computer vision-taken, zoals cijfer herkenning, document classificatie en patroon herkenning.

Classificatie met behulp van Neural-netwerken is een bewaakte leer methode en vereist daarom een *gecodeerde gegevensset* die een kolom Label bevat.

U kunt het model trainen door het model en de gecodeerde gegevensset op te geven als invoer voor het [trainen van modellen](./train-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden te voors pellen voor de nieuwe invoer voorbeelden.  

## <a name="about-neural-networks"></a>Over Neural-netwerken

Een Neural-netwerk bestaat uit een reeks onderling verbonden lagen. De invoer is de eerste laag en is verbonden met een uitvoer laag door een acyclische grafiek die bestaat uit gewogen randen en knoop punten.

Tussen de invoer-en uitvoer lagen kunt u meerdere verborgen lagen invoegen. De meeste voorspellende taken kunnen eenvoudig met slechts één of enkele verborgen lagen worden uitgevoerd. Het recente onderzoek heeft echter geleerd dat diepe Neural-netwerken (DNN) met veel lagen effectief kunnen zijn in complexe taken zoals afbeeldingen of spraak herkenning. De opeenvolgende lagen worden gebruikt voor het model leren van de toenemende niveaus van semantische diepte.

De relatie tussen invoer en uitvoer wordt geleerd van het trainen van het Neural-netwerk op de invoer gegevens. De richting van de grafiek loopt van de invoer via de verborgen laag en naar de uitvoer laag. Alle knoop punten in een laag worden verbonden door de gewogen randen op knoop punten in de volgende laag.

Voor het berekenen van de uitvoer van het netwerk voor een bepaalde invoer wordt een waarde berekend op elk knoop punt in de verborgen lagen en in de uitvoer laag. De waarde wordt ingesteld door de gewogen som van de waarden van de knoop punten uit de vorige laag te berekenen. Een activerings functie wordt vervolgens toegepast op die gewogen som.

## <a name="configure-multiclass-neural-network"></a>Multi Class Neural-netwerk configureren

1. Voeg de **netwerk module Multi Class Neural** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder **machine learning**, **initialiseren**, in de categorie **classificatie** .

2. De **trainer modus maken**: gebruik deze optie om op te geven hoe u wilt dat het model wordt getraind:

    - **Eén para meter**: Kies deze optie als u al weet hoe u het model wilt configureren.

    

3. **Specificatie van verborgen laag**: Selecteer het type netwerk architectuur dat u wilt maken.

    - **Volledig gekoppelde Case**: Selecteer deze optie om een model te maken met behulp van de standaard Neural-netwerk architectuur. Voor multi class Neural-netwerk modellen zijn de standaard instellingen als volgt:

        - Eén verborgen laag
        - De uitvoer laag is volledig verbonden met de verborgen laag.
        - De verborgen laag is volledig verbonden met de invoer laag.
        - Het aantal knoop punten in de invoer laag wordt bepaald door het aantal functies in de trainings gegevens.
        - Het aantal knoop punten in de verborgen laag kan worden ingesteld door de gebruiker. De standaardwaarde is 100.
        - Het aantal knoop punten in de uitvoer laag is afhankelijk van het aantal klassen.
  
   

5. **Aantal verborgen knoop punten**: met deze optie kunt u het aantal verborgen knoop punten in de standaard architectuur aanpassen. Typ het aantal verborgen knoop punten. De standaard waarde is één verborgen laag met 100 knoop punten.

6. **Het leer tempo**: Definieer de grootte van de stap die bij elke herhaling moet worden uitgevoerd, vóór correctie. Een hogere waarde voor het leer tempo kan ertoe leiden dat het model sneller wordt geconvergeerd, maar dat het lokale minima kan overschrijden.

7. **Aantal leer herhalingen**: Geef het maximum aantal keren op dat de trainings cases moeten worden verwerkt door de algoritme.

8. **De eerste leer gewicht diameter**: Geef de knooppunt gewichten aan het begin van het leer proces op.

9. **De impuls**: Geef een gewicht op dat moet worden toegepast tijdens het leren naar knoop punten uit eerdere iteraties.
  
11. **Voor beelden in wille keurige volg orde**: Selecteer deze optie om cases in wille keurige volg orde te plaatsen

    Als u deze optie uitschakelt, worden cases in precies dezelfde volg orde verwerkt telkens wanneer u de pijp lijn uitvoert.

12. **Wille keurig getal Seed**: Typ een waarde die als seed moet worden gebruikt als u de Herhaal baarheid wilt waarborgen voor uitvoeringen van dezelfde pijp lijn.

14. Verbind een trainings gegevensset en een van de [trainings modules](module-reference.md): 

    - Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u [Train model](train-model.md).  
  

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

- Als u een moment opname van het getrainde model wilt opslaan, selecteert u het tabblad **uitvoer** in het rechterdeel venster van de module **Train model** . Selecteer het pictogram **gegevensset registreren** om het model als een herbruikbare module op te slaan.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 