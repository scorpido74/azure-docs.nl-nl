---
title: Model Hyperparameters afstemmen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Hyperparameters van tunemodel in Azure Machine Learning om een parametersweep op een model uit te voeren om de optimale parameterinstellingen te bepalen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: ff0ccbf201f2b83dd446859d8054d115a70f402e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064163"
---
# <a name="tune-model-hyperparameters"></a>Model Hyperparameters afstemmen

In dit artikel wordt beschreven hoe u de module Hyperparameters van tunemodel gebruiken in Azure Machine Learning-ontwerper (voorbeeld). Het doel is om de optimale hyperparameters voor een machine learning model te bepalen. De module bouwt en test meerdere modellen met behulp van verschillende combinaties van instellingen. Het vergelijkt metrische gegevens over alle modellen om de combinaties van instellingen te krijgen. 

De termen *parameter* en *hyperparameter* kunnen verwarrend zijn. De *parameters* van het model zijn wat u in het rechterdeelvenster van de module instelt. In principe voert deze module een *parametersweep* uit over de opgegeven parameterinstellingen. Het leert een optimale set _hyperparameters,_ die voor elke specifieke beslissingsstructuur, gegevensset of regressiemethode anders kunnen zijn. Het proces van het vinden van de optimale configuratie wordt ook wel *tuning*genoemd. 

De module ondersteunt de volgende methode voor het vinden van de optimale instellingen voor een model: *geïntegreerde trein en tune.* Met deze methode configureert u een set parameters die u wilt gebruiken. Vervolgens laat u de module herhalen over meerdere combinaties. De module meet de nauwkeurigheid totdat hij een "beste" model vindt. Bij de meeste leerlingmodules u kiezen welke parameters tijdens het trainingsproces moeten worden gewijzigd en welke vast moeten blijven.

Afhankelijk van hoe lang u het stemproces wilt uitvoeren, u besluiten om alle combinaties volledig te testen. Of u het proces verkorten door een raster van parametercombinaties vast te stellen en een gerandomiseerde subset van het parameterraster te testen.

Deze methode genereert een getraind model dat u opslaan voor hergebruik.  

> [!TIP] 
> U een gerelateerde taak uitvoeren. Voordat u begint met stemmen, past u functieselectie toe om de kolommen of variabelen met de hoogste informatiewaarde te bepalen.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Hyperparameters van Tune Model configureren  

Het leren van de optimale hyperparameters voor een machine learning model vereist een aanzienlijk gebruik van pijpleidingen.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Een model trainen met behulp van een parametersweep  

In deze sectie wordt beschreven hoe u een basisparametersweep uitvoert, die een model traint met behulp van de module Tune Model Hyperparameters.

1.  Voeg de module Hyperparameters van tunemodel toe aan uw pijplijn in de ontwerper.

2.  Sluit een ongetraind model aan op de meest linkse invoer. 



4.  Voeg de gegevensset toe die u wilt gebruiken voor training en sluit deze aan op de middelste invoer van Tune Model Hyperparameters.  

    Als u een gelabelde gegevensset hebt, u deze optioneel koppelen aan de meest rechtse invoerpoort **(optionele validatiegegevensset).** Hiermee u nauwkeurigheid meten tijdens het trainen en afstemmen.

5.  Kies in het rechterdeelvenster van Tune Model Hyperparameters een waarde voor **parameterveegmodus**. Met deze optie bepaalt u hoe de parameters zijn geselecteerd.

    - **Hele raster**: Wanneer u deze optie selecteert, loopt de module over een raster dat vooraf door het systeem is gedefinieerd, om verschillende combinaties uit te proberen en de beste leerling te identificeren. Deze optie is handig als u niet weet wat de beste parameterinstellingen kunnen zijn en alle mogelijke combinaties van waarden wilt proberen.

    - **Willekeurige sweep:** Wanneer u deze optie selecteert, selecteert de module willekeurig parameterwaarden boven een door het systeem gedefinieerd bereik. U moet het maximum aantal uitvoeringen opgeven dat de module moet uitvoeren. Deze optie is handig wanneer u de prestaties van het model wilt verhogen door gebruik te maken van de statistieken van uw keuze, maar toch computerbronnen wilt besparen.    

6.  Open **bij de kolom Label**de kolomkiezer om één kolom met één label te kiezen.

7.  Kies het aantal runs:

    1. **Maximaal aantal runs op willekeurige sweep:** Als u kiest voor een willekeurige sweep, u opgeven hoe vaak het model moet worden getraind, met behulp van een willekeurige combinatie van parameterwaarden.

8.  Kies bij **Ranking**één statistiek die u wilt gebruiken voor het rangschikken van de modellen.

    Wanneer u een parametersweep uitvoert, berekent de module alle toepasselijke statistieken voor het modeltype en retourneert deze in het rapport **Sweep-resultaten.** De module maakt gebruik van afzonderlijke statistieken voor regressie- en classificatiemodellen.

    De statistiek die u kiest, bepaalt echter hoe de modellen worden gerangschikt. Alleen het topmodel, zoals gerangschikt op de gekozen statistiek, is uitvoer als een getraind model om te gebruiken voor het scoren.

9.  Voer **bij Willekeurig zaad**een getal in dat u wilt gebruiken voor het starten van de parametersweep. 

10. Verzend de pijplijn.

## <a name="results-of-hyperparameter-tuning"></a>Resultaten van hyperparameter tuning

Wanneer de training is voltooid:

+ Als u een set nauwkeurigheidsstatistieken voor het beste model wilt weergeven, klikt u met de rechtermuisknop op de module en selecteert u **Visual .**

    De uitvoer bevat alle nauwkeurigheidsstatistieken die van toepassing zijn op het modeltype, maar de statistiek die u hebt geselecteerd voor rangschikking bepaalt welk model als 'het beste' wordt beschouwd.

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van de **module Treinmodel.** Selecteer het pictogram **Gegevensset registreren** om het model op te slaan als een herbruikbare module.


## <a name="technical-notes"></a>Technische notities

Deze sectie bevat implementatiedetails en tips.

### <a name="how-a-parameter-sweep-works"></a>Hoe een parametersweep werkt

Wanneer u een parametersweep instelt, bepaalt u het bereik van uw zoekopdracht. De zoekopdracht kan een eindig aantal parameters gebruiken die willekeurig zijn geselecteerd. Of het kan een uitputtende zoekopdracht zijn over een parameterruimte die u definieert.

+ **Random sweep**: Deze optie traint een model met behulp van een vast aantal iteraties. 

  U geeft een bereik op van waarden om over te herhalen en de module gebruikt een willekeurig gekozen subset van die waarden. Waarden worden gekozen met vervanging, wat betekent dat nummers die eerder willekeurig zijn gekozen, niet worden verwijderd uit de pool van beschikbare getallen. Dus de kans dat een waarde wordt geselecteerd blijft hetzelfde over alle passen.  

+ **Volledig net**: De optie om het hele net te gebruiken betekent dat elke combinatie wordt getest. Deze optie is de meest grondige, maar het vergt de meeste tijd. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controle van de lengte en complexiteit van de opleiding

Het herhalen van veel combinaties van instellingen kan tijdrovend zijn, dus de module biedt verschillende manieren om het proces te beperken:

+ Beperk het aantal iteraties dat wordt gebruikt om een model te testen.
+ Beperk de parameterruimte.
+ Beperk zowel het aantal iteraties als de parameterruimte.

We raden u aan de instellingen te gebruiken om de meest efficiënte trainingsmethode voor een bepaalde gegevensset en model te bepalen.

### <a name="choosing-an-evaluation-metric"></a>Een evaluatiestatistiek kiezen

Aan het einde van de tests presenteert het model een rapport met de nauwkeurigheid voor elk model, zodat u de metrische resultaten bekijken:

- Voor alle binaire classificatiemodellen wordt een uniforme set metrische gegevens gebruikt.
- Nauwkeurigheid wordt gebruikt voor alle classificatiemodellen van meerdere klassen.
- Een andere set statistieken wordt gebruikt voor regressiemodellen. 

Tijdens de training moet u echter *één* statistiek kiezen die u wilt gebruiken bij het rangschikken van de modellen die tijdens het stemproces worden gegenereerd. Mogelijk vindt u dat de beste statistiek varieert, afhankelijk van uw bedrijfsprobleem en de kosten van false positives en false negatives.

#### <a name="metrics-used-for-binary-classification"></a>Statistieken die worden gebruikt voor binaire classificatie

-   **Nauwkeurigheid** is het aandeel van de werkelijke resultaten aan de totale gevallen.  

-   **Precisie** is het aandeel van de werkelijke resultaten tot positieve resultaten.  

-   **Recall** is de fractie van alle juiste resultaten over alle resultaten.  

-   **F-score** is een maat die precisie en terugroep balans.  

-   **AUC** is een waarde die het gebied onder de curve vertegenwoordigt wanneer valse positieven op de x-as worden uitgezet en echte positieven worden uitgezet op de y-as.  

-   **Gemiddelde logverlies** is het verschil tussen twee waarschijnlijkheidsverdelingen: de ware en de verdeling in het model.  

#### <a name="metrics-used-for-regression"></a>Statistieken die worden gebruikt voor regressie

-   **Gemiddelde absolute fout** gemiddeldalle fouten in het model, waarbij *fout* betekent dat de afstand van de voorspelde waarde van de werkelijke waarde. Het is vaak afgekort als *MAE*.  

-   **Root van gemiddelde kwadraatfout** meet het gemiddelde van de vierkanten van de fouten en neemt vervolgens de wortel van die waarde. Het is vaak afgekort als *RMSE*.  

-   **Relatieve absolute fout** vertegenwoordigt de fout als een percentage van de werkelijke waarde.  

-   **Relatieve kwadraatfout** normaliseert de totale kwadraatfout door te delen door de totale kwadraatfout van de voorspelde waarden.  

-   **De bepalingscoëfficiënt** is één getal dat aangeeft hoe goed gegevens in een model passen. Een waarde van één betekent dat het model precies overeenkomt met de gegevens. Een waarde van nul betekent dat de gegevens willekeurig zijn of anderszins niet geschikt zijn voor het model. Het wordt vaak *r<sup>2,</sup>* *R<sup>2</sup>* of *r-kwadraat genoemd.*  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Modules die geen parametersweep ondersteunen

Bijna alle leerlingen in Azure Machine Learning ondersteunen cross-validatie met een geïntegreerde parametersweep, waarmee u de parameters kiezen waarmee u de pijplijnparameters maken. Als de leerling geen ondersteuning biedt voor het instellen van een reeks waarden, u deze nog steeds gebruiken in cross-validatie. In dit geval wordt een reeks toegestane waarden geselecteerd voor de sweep. 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 

