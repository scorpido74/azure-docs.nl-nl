---
title: 'Neural-netwerk met twee klassen: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de Neural-netwerk module met twee klassen in Azure Machine Learning-service om een Neural-netwerk model te maken dat kan worden gebruikt om een doel te voors pellen met slechts twee waarden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 6f0ad3cc6f506efdc0579f7b8949c41b539ade6a
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128353"
---
# <a name="two-class-neural-network-module"></a>Neural-netwerk module met twee klassen

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module voor het maken van een Neural-netwerk model dat kan worden gebruikt voor het voors pellen van een doel met slechts twee waarden.

Classificatie met behulp van Neural-netwerken is een bewaakte leer methode en vereist daarom een *gecodeerde gegevensset*, die een kolom Label bevat. U kunt dit Neural-netwerk model bijvoorbeeld gebruiken om binaire resultaten te voors pellen, zoals of een patiënt al een bepaalde ziekte heeft of een machine waarschijnlijk niet kan worden uitgevoerd binnen een bepaald tijd venster.  

Nadat u het model hebt gedefinieerd, traint u het met een gecodeerde gegevensset en het model als invoer voor het [trainen van modellen](./train-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden voor nieuwe invoer te voors pellen.

### <a name="more-about-neural-networks"></a>Meer informatie over Neural-netwerken

Een Neural-netwerk bestaat uit een reeks onderling verbonden lagen. De invoer is de eerste laag en is verbonden met een uitvoer laag door een acyclische grafiek die bestaat uit gewogen randen en knoop punten.

Tussen de invoer-en uitvoer lagen kunt u meerdere verborgen lagen invoegen. De meeste voorspellende taken kunnen eenvoudig met slechts één of enkele verborgen lagen worden uitgevoerd. Het recente onderzoek heeft echter geleerd dat diepe Neural-netwerken (DNN) met veel lagen effectief kunnen zijn in complexe taken zoals afbeeldingen of spraak herkenning. De opeenvolgende lagen worden gebruikt voor het model leren van de toenemende niveaus van semantische diepte.

De relatie tussen invoer en uitvoer wordt geleerd van het trainen van het Neural-netwerk op de invoer gegevens. De richting van de grafiek loopt van de invoer via de verborgen laag en naar de uitvoer laag. Alle knoop punten in een laag worden verbonden door de gewogen randen op knoop punten in de volgende laag.

Voor het berekenen van de uitvoer van het netwerk voor een bepaalde invoer wordt een waarde berekend op elk knoop punt in de verborgen lagen en in de uitvoer laag. De waarde wordt ingesteld door de gewogen som van de waarden van de knoop punten uit de vorige laag te berekenen. Een activerings functie wordt vervolgens toegepast op die gewogen som.
  
## <a name="how-to-configure"></a>Configureren

1.  Voeg de **Neural-netwerk module met twee klassen** toe aan uw experiment. U kunt deze module vinden onder **machine learning**, **initialiseren**, in de categorie **classificatie** .  
  
2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: Kies deze optie als u al weet hoe u het model wilt configureren.  

3.  Selecteer bij **verborgen laag specificatie**het type netwerk architectuur dat moet worden gemaakt.  
  
    -   **Volledig gekoppelde Case**: Maakt gebruik van de standaard Neural-netwerk architectuur, die als volgt is gedefinieerd voor Neural-netwerken met twee klassen:
  
        -   Heeft één verborgen laag.
  
        -   De uitvoer laag is volledig verbonden met de verborgen laag en de verborgen laag is volledig verbonden met de invoer laag.
  
        -   Het aantal knoop punten in de invoer laag is gelijk aan het aantal functies in de trainings gegevens.
  
        -   Het aantal knoop punten in de verborgen laag wordt ingesteld door de gebruiker. De standaardwaarde is 100.
  
        -   Het aantal knoop punten is gelijk aan het aantal klassen. Voor een Neural-netwerk met twee klassen betekent dit dat alle invoer waarden moeten worden toegewezen aan een van de twee knoop punten in de uitvoer laag.

5.  Voor **leer snelheid**definieert u de grootte van de stap die bij elke herhaling moet worden uitgevoerd, vóór correctie. Een hogere waarde voor het leer tempo kan ertoe leiden dat het model sneller wordt geconvergeerd, maar dat het lokale minima kan overschrijden.

6.  Geef voor **aantal Learning**-iteraties het maximum aantal keren op dat het algoritme de trainings aanvragen moet verwerken.

7.  Voor **de eerste wegings diameter**geeft u de knooppunt gewichten aan het begin van het leer proces op.

8.  Geef voor **de momentum**een gewicht op dat moet worden toegepast tijdens het leren naar knoop punten uit eerdere iteraties  

10. Selecteer de optie **voor beelden** in wille keurige volg orde om cases van elkaar af te plaatsen tussen herhalingen. Als u deze optie uitschakelt, worden cases in precies dezelfde volg orde verwerkt telkens wanneer u het experiment uitvoert.
  
11. Typ voor **wille keurig getal zaad**een waarde die moet worden gebruikt als seed.
  
     Het opgeven van een Seed-waarde is handig als u de Herhaal baarheid van hetzelfde experiment wilt garanderen.  Anders wordt een waarde van de systeem klok gebruikt als seed, wat kan leiden tot enigszins verschillende resultaten wanneer u het experiment uitvoert.
  
13. Voeg een gecodeerde gegevensset toe aan het experiment en Verbind een van de [trainings modules](module-reference.md).  
  
    -   Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u de module [Train model](train-model.md) .  
  
14. Voer het experiment uit.

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u een samen vatting van de para meters van het model wilt weer geven, samen met de functie gewichten die zijn geleerd uit training en andere para meters van het Neural-netwerk, klikt u met de rechter muisknop op de uitvoer van [Train model](./train-model.md)en selecteert u visualiseren.  

+ Als u een moment opname van het getrainde model wilt opslaan, klikt u met de rechter muisknop op de getrainde **model** uitvoer en selecteert u **Opslaan als opgeleid model**. Dit model wordt niet bijgewerkt op opeenvolgende uitvoeringen van hetzelfde experiment.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
