---
title: '#6 voor beeld van visuele interface: Classificatie voor het voors pellen van vlucht vertragingen'
titleSuffix: Azure Machine Learning
description: In dit artikel wordt beschreven hoe u een machine learning model bouwt om vlucht vertragingen te voors pellen met behulp van de visuele interface voor slepen en neerzetten en aangepaste R-code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 3d1363d24aa6e5f8875ba90513a8d9d5b951c9db
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997113"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Voor beeld 6: classificatie: Vertragingen in de vlucht voors pellen met R

In dit experiment worden historische vlucht-en weers gegevens gebruikt om te voors pellen of een geplande reizigers vlucht meer dan 15 minuten wordt uitgesteld.

Dit probleem kan worden beschouwd als een classificatie probleem, waarbij twee klassen worden voor speld, of op tijd. Voor het bouwen van een classificatie maakt dit model gebruik van een groot aantal voor beelden van historische vlucht gegevens.

Hier volgt de laatste grafiek van het experiment:

[![Grafiek van het experiment](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecteer de knop **openen** voor het experiment sample 6:

    ![Het experiment openen](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>De gegevens ophalen

In dit experiment wordt gebruikgemaakt van de **gegevens gegevensset vlucht vertragingen** . Het maakt deel uit van de TranStats-gegevens verzameling van de Verenigde Staten Ministerie van Trans Port. De gegevensset bevat informatie over de vlucht vertraging van april tot oktober 2013. Voordat u de gegevens naar de visuele interface uploadt, is deze als volgt vooraf verwerkt:

* Gefilterd op de 70 drukste lucht havens in het continentale Verenigde Staten.
* Voor geannuleerde vluchten, met een label dat langer is dan 15 minuten.
* Gefilterde vluchten.
* 14 kolommen geselecteerd.

Als aanvulling op de vlucht gegevens wordt de **weer gegevensset** gebruikt. De weer gegevens bevatten op het land gebaseerde weers waarnemingen van NOAA en duiden op de waarnemingen van de weers stations van de lucht haven, met dezelfde periode van april-oktober 2013. Voordat u uploadt naar de Azure ML-Visual Interface, is deze vooraf verwerkt als volgt:

* Er zijn weer station-Id's toegewezen aan de bijbehorende luchthaven-Id's.
* Weer stations die niet zijn gekoppeld aan de 70 drukste lucht havens zijn verwijderd.
* De datum kolom is in afzonderlijke kolommen gesplitst: Jaar, maand en dag.
* Geselecteerde 26 kolommen.

## <a name="pre-process-the-data"></a>De gegevens vooraf verwerken

Een gegevensset vereist meestal een voor verwerking voordat deze kan worden geanalyseerd.

![gegevens proces](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Vlucht gegevens

De kolommen **Carrier**, **OriginAirportID**en **DestAirportID** worden opgeslagen als gehele getallen. Ze zijn echter categorische-kenmerken, gebruik de module **meta gegevens bewerken** om ze te converteren naar categorische.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Gebruik vervolgens de module **select columns** in dataset om de gegevensset-kolommen uit te sluiten van de kolom met mogelijke doel lekken: **DepDelay**, **DepDel15**, **ArrDelay**, **geannuleerd**, **jaar**. 

Als u de vlucht records wilt samen voegen met de weer records per uur, gebruikt u de geplande vertrek tijd als een van de koppelings sleutels. Als u de koppeling wilt uitvoeren, moet de kolom CSRDepTime naar beneden worden afgerond naar het dichtstbijzijnde uur. dit wordt gedaan door in de module **R-script uitvoeren** . 

### <a name="weather-data"></a>Weer gegevens

Kolommen met een groot deel van ontbrekende waarden worden uitgesloten met de module **project kolommen** . Deze kolommen bevatten alle kolommen met een teken reeks waarde: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**en **StationPressure**.

De module **clean Missing Data** wordt vervolgens toegepast op de resterende kolommen om rijen met ontbrekende gegevens te verwijderen.

De weer observatie tijden worden naar boven afgerond op het dichtstbijzijnde volledige uur. Geplande vlucht tijden en de weer observatie tijden worden in tegenovergestelde richting afgerond om ervoor te zorgen dat het model alleen weer voor de vlucht tijd gebruikt. 

Omdat weer gegevens worden gerapporteerd in de lokale tijd, worden tijdzone verschillen verwerkt door de tijd zone kolommen af te trekken van de geplande vertrek tijd en de weer observatie tijd. Deze bewerkingen worden uitgevoerd met de **script module Execute R** .

### <a name="joining-datasets"></a>Gegevens sets koppelen

Vlucht records worden toegevoegd aan de gegevens van het begin van de vlucht (**OriginAirportID**) met behulp van de **join data** -module.

 ![Sluit vlucht en weer aan de oorsprong](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Vlucht records worden gekoppeld aan weer gegevens die gebruikmaken van de bestemming van de vlucht (**DestAirportID**).

 ![Vlucht en weer aan de bestemming toevoegen](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Trainings-en test voorbeelden voorbereiden

In de module **Split data** worden de gegevens gesplitst in april tot en met september records voor training en oktober records voor test doeleinden.

 ![Training en test gegevens splitsen](media/ui-sample-classification-predict-flight-delay/split.png)

De kolommen Year, month en time zone worden verwijderd uit de trainings gegevensset met behulp van de module select columns.

## <a name="define-features"></a>Functies definiëren

In machine learning zijn onderdelen afzonderlijke meet bare eigenschappen van iets waarin u geïnteresseerd bent. Voor het zoeken naar een sterke set functies is experimenteren en domein kennis vereist. Bepaalde kenmerken zijn beter voor het voorspellen van het doel dan andere. Het is ook mogelijk dat sommige functies een sterke correlatie met andere functies hebben en er geen nieuwe informatie aan het model toe te voegen. Deze functies kunnen worden verwijderd.

Als u een model wilt bouwen, kunt u alle beschik bare functies gebruiken of een subset van de functies selecteren.

## <a name="choose-and-apply-a-learning-algorithm"></a>Een leeralgoritme kiezen en toepassen

Maak een model met behulp van de module **logistiek-regressie met twee klassen** en Train deze in de trainings-gegevensset. 

Het resultaat van de module **Train model** is een getraind classificatie model dat kan worden gebruikt om nieuwe voor beelden te scoren om voor spellingen te maken. Gebruik de testset om scores te genereren op basis van de getrainde modellen. Gebruik vervolgens de module **Evaluate model** om de kwaliteit van de modellen te analyseren en vergelijken.

Nadat u het experiment hebt uitgevoerd, kunt u de uitvoer van de module **score model** weer geven door te klikken op de uitvoer poort en **visualiseren**te selecteren. De uitvoer bevat de gescoorde labels en de waarschijnlijkheid voor de labels.

Ten slotte kunt u de kwaliteit van de resultaten testen door de module **Evaluate model** toe te voegen aan het canvas van het experiment en de linker invoer poort te koppelen aan de uitvoer van de module score model. Voer het experiment uit en Bekijk de uitvoer van de module **Evaluate model** door te klikken op de uitvoer poort en **visualiseren**te selecteren.

## <a name="evaluate"></a>Evalueren
Het logistiek regressie model heeft AUC van 0,631 in de testset.

 ![evalueren](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de visuele interface:

- [Voor beeld 1-regressie: De prijs van een auto voors pellen](ui-sample-regression-predict-automobile-price-basic.md)
- [Voor beeld 2-regressie: Algoritmen voor het voors pellen van prijzen vergelijken](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Voor beeld 3-classificatie: Krediet risico voors pellen](ui-sample-classification-predict-credit-risk-basic.md)
- [Voor beeld 4-classificatie: Voor speld krediet risico (kosten gevoelig)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: Verloop voors pellen](ui-sample-classification-predict-churn.md)
