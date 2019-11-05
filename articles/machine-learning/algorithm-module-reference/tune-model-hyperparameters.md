---
title: Model Hyper parameters afstemmen
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de Hyper parameters-module voor het afstemmen van het model in Azure Machine Learning service om een parameter sweep uit te voeren op een model om de optimale parameter instellingen te bepalen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 06adfe66bfe894d7b3c95e3d416da866c7d103b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515653"
---
# <a name="tune-model-hyperparameters"></a>Model Hyper parameters afstemmen

In dit artikel wordt beschreven hoe u de module [model Hyper parameters](tune-model-hyperparameters.md) in azure machine learning Designer (preview) gebruikt om de optimale Hyper parameters voor een bepaald machine learning model te bepalen. De module bouwt en test meerdere modellen, met verschillende combi Naties van instellingen en vergelijkt metrische gegevens over alle modellen om de combi natie van instellingen op te halen. 

De termen *para meter* en *afstemming* kunnen verwarrend zijn. De *para meters* van het model zijn wat u in het deel venster Eigenschappen hebt ingesteld. In principe voert deze module een *opruiming* van de para meters uit op basis van de opgegeven parameter instellingen en leert u een optimale set _Hyper parameters_, die mogelijk verschillend is voor elke specifieke beslissings structuur, gegevensset of regressie methode. Het proces van het vinden van de optimale configuratie wordt soms *afstemming*genoemd. 

De module ondersteunt de volgende methode voor het vinden van de optimale instellingen voor een model 

**Geïntegreerde trein en afstemming**: u configureert een set para meters die u wilt gebruiken, en laat de module vervolgens meerdere combi Naties door lopen, waardoor de nauw keurigheid wordt bepaald totdat een ' beste ' model wordt gevonden. Met de meeste leer modules kunt u kiezen welke para meters tijdens het trainings proces moeten worden gewijzigd en wat vast moet blijven.

    Depending on how long you want the tuning process to run, you might decide to exhaustively test all combinations, or you could shorten the process by establishing a grid of parameter combinations and testing a randomized subset of the parameter grid.

 Met deze methode wordt een getraind model gegenereerd dat u kunt opslaan voor hergebruik.  

### <a name="related-tasks"></a>Gerelateerde taken

+ Voor het afstemmen moet u de functie selectie Toep assen om de kolommen of variabelen te bepalen die de hoogste gegevens waarde hebben.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Optimalisatie model Hyper parameters configureren  

Over het algemeen is voor het leren van de optimale Hyper parameters voor een bepaald machine learning model aanzienlijke pipelineation.

### <a name="train-a-model-using-a-parameter-sweep"></a>Een model trainen met behulp van een parameter sweep  

In deze sectie wordt beschreven hoe u een eenvoudige para meter-sweep uitvoert, waarmee een model wordt getraind met behulp van de Hyper parameters-module [model](tune-model-hyperparameters.md) .

1.  Voeg de module [model Hyper parameters afstemmen](tune-model-hyperparameters.md) toe aan uw pijp lijn in de ontwerp functie.

2.  Verbind een niet-traind model met de meest linkse invoer. 

3. Stel de optie **trainer maken** in op het **parameter bereik** en gebruik de **opbouw functie bereik** om een waardebereik op te geven dat u wilt gebruiken in de para meter sweep.  

    Bijna alle modules voor classificatie en regressie bieden ondersteuning voor het opruimen van geïntegreerde para meters. Voor de cursisten die geen ondersteuning bieden voor het configureren van een parameter bereik, kunnen alleen de beschik bare parameter waarden worden getest.

    U kunt de waarde voor een of meer para meters hand matig instellen en vervolgens de resterende para meters opruimen. Dit kan enige tijd besparen.

4.  Voeg de gegevensset toe die u voor training wilt gebruiken en verbind deze met de middelste invoer van het [model Hyper parameters voor het afstemmen](tune-model-hyperparameters.md).  

    Als u een gecodeerde gegevensset hebt, kunt u deze koppelen aan de meest rechtse invoer poort (**optionele validatie gegevensset**). Zo kunt u de nauw keurigheid meten tijdens de training en het afstemmen.

5.  Kies in het deel venster **Eigenschappen** van [stem model Hyper parameters](tune-model-hyperparameters.md)een waarde voor de modus voor het afwijzen van de **para meter**. Met deze optie bepaalt u hoe de para meters worden geselecteerd.

    - **Volledig raster**: wanneer u deze optie selecteert, wordt de module herhaald volgens een raster dat vooraf is gedefinieerd door het systeem, om verschillende combi Naties te proberen en de beste kenniser te identificeren. Deze optie is handig voor gevallen waarin u niet weet wat de beste parameter instellingen zijn en u alle mogelijke combi Naties van waarden wilt proberen.

    - **Wille keurige sweep**: wanneer u deze optie selecteert, selecteert de module wille keurig parameter waarden boven een door het systeem gedefinieerd bereik. U moet het maximum aantal runs opgeven dat door de module moet worden uitgevoerd. Deze optie is handig voor gevallen waarin u de prestaties van het model wilt verhogen met behulp van de metrische gegevens van uw keuze, maar wel voor het besparen van computer bronnen.

    Als u een wille keurige sweep kiest, kunt u het **maximum aantal uitvoeringen op wille keurige sweep**opgeven. Dit betekent hoe vaak het model moet worden getraind met een wille keurige combi natie van parameter waarden.

6.  Voor de **kolom Label**, start u de kolom kiezer om een kolom met één label te kiezen.

7.  **Maximum aantal uitvoeringen op wille keurige sweep**: als u een wille keurige sweep kiest, kunt u opgeven hoe vaak het model moet worden getraind met behulp van een wille keurige combi natie van parameter waarden.

    **Maximum aantal uitvoeringen op wille keurig raster**: deze optie bepaalt ook het aantal iteraties voor een wille keurige steek proef van parameter waarden, maar de waarden worden niet wille keurig van het opgegeven bereik gegenereerd. in plaats daarvan wordt een matrix gemaakt met alle mogelijke combi Naties van parameter waarden en wordt een wille keurige steek proef van de matrix genomen. Deze methode is efficiënter en minder gevoelig voor regionale oversteek proeven of ondersampling.

8.  Kies één metrische waarde om te gebruiken bij het **classificeren** van de modellen.

    Wanneer u een para meter opruimen uitvoert, worden alle toepasselijke metrische gegevens voor het model type berekend en geretourneerd in het rapport met opschonings **resultaten** . Afzonderlijke metrische gegevens worden gebruikt voor regressie-en classificatie modellen.

    De metriek die u kiest, bepaalt echter hoe de modellen worden gerangschikt. Alleen het bovenste model, zoals geclassificeerd door de gekozen metriek, wordt uitgevoerd als een getraind model om te gebruiken voor een score.

9.  Voor **wille keurig zaad**typt u een getal dat moet worden gebruikt bij het initialiseren van de parameter sweep. 

10. Voer de pijplijn uit.

## <a name="results-of-hyperparameter-tuning"></a>Resultaten van afstemming tuning

Wanneer de training is voltooid:

+ Als u een set nauw keurige metrische gegevens voor het beste model wilt weer geven, klikt u met de rechter muisknop op de module, selecteert u opschonings **resultaten**en selecteert u vervolgens **visualiseren**.

    Alle metrische gegevens over nauw keurigheid die van toepassing zijn op het model type worden uitgevoerd, maar de metrische gegevens die u hebt geselecteerd voor de rang schikking bepalen welk model als ' Best ' wordt beschouwd.

+ Als u het model wilt gebruiken om te scoren in andere pijp lijnen, zonder dat u het afstemmings proces hoeft te herhalen, klikt u met de rechter muisknop op de model uitvoer en selecteert u **Opslaan als getraind model**. 


## <a name="technical-notes"></a>Technische opmerkingen

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

### <a name="how-a-parameter-sweep-works"></a>Hoe een para meter sweep werkt

In deze sectie wordt beschreven hoe para meters opruimen in het algemeen werkt en hoe de opties in deze module communiceren.

Wanneer u een parameter opschoning instelt, definieert u het bereik van uw zoek opdracht, om een eindig aantal para meters te gebruiken die wille keurig zijn geselecteerd, of een uitgebreide zoek opdracht over een door u gedefinieerde parameter ruimte.

+ **Wille keurige sweep**: met deze optie wordt een model getraind met een ingesteld aantal herhalingen. 

     U geeft een reeks waarden op die moeten worden herhaald en de module gebruikt een wille keurig gekozen subset van die waarden.  Waarden worden gekozen met vervanging, wat betekent dat getallen die eerder zijn gekozen, niet worden verwijderd uit de groep beschik bare getallen. De kans dat een waarde wordt geselecteerd, blijft dus hetzelfde voor alle fasen.  

+ **Volledig raster**: de optie voor het gebruik van het hele raster betekent alleen dat: elke combi natie wordt getest. Deze optie kan worden beschouwd als de meest uitgebreide, maar is de meeste tijd. 

### <a name="controlling-the-length-and-complexity-of-training"></a>De lengte en complexiteit van training bepalen

Het uitvoeren van meerdere combi Naties van instellingen kan tijdrovend zijn, dus de module biedt verschillende manieren om het proces te beperken:

+ Het aantal iteraties beperken dat wordt gebruikt voor het testen van een model
+ De parameter ruimte beperken
+ Het aantal iteraties en de parameter ruimte beperken

We raden u aan om met de instellingen een pijp lijn te nemen om de meest efficiënte opleidings methode voor een bepaalde gegevensset en model te bepalen.

### <a name="choosing-an-evaluation-metric"></a>Een evaluatie-metric kiezen

Een rapport met de nauw keurigheid voor elk model wordt aan het einde weer gegeven, zodat u de metrische resultaten kunt bekijken. Een uniforme set metrische gegevens wordt gebruikt voor alle binaire classificatie modellen, nauw keurigheid wordt gebruikt voor alle classificatie modellen met meerdere klassen en er wordt een andere set metrische gegevens gebruikt voor regressie modellen. Tijdens de training moet u echter **één** metrische waarde kiezen om te gebruiken bij het rangschikken van de modellen die tijdens het afstemmings proces worden gegenereerd. Het kan voor komen dat de beste maat eenheid varieert, afhankelijk van het probleem van uw bedrijf en de kosten voor fout-positieve en ONWAAR negatieven.

#### <a name="metrics-used-for-binary-classification"></a>Metrische gegevens die worden gebruikt voor binaire classificatie

-   **Nauw keurigheid** Het deel van de werkelijke resultaten tot het totale aantal cases.  

-   **Precisie** Het aandeel van de werkelijke resultaten op positieve resultaten.  

-   **Intrekken** De Fractie van alle juiste resultaten ten opzichte van alle resultaten.  

-   **F-Score** Een meting die de nauw keurigheid en intrekken vergelijkt.  

-   **AUC** Een waarde die het gebied onder de curve vertegenwoordigt wanneer fout-positieven worden getekend op de x-as en waar positieven worden getekend op de y-as.  

-   **Gemiddeld verlies logboek** Het verschil tussen de twee kansverdelingen: de werkelijke waarde en de eerste in het model.  

#### <a name="metrics-used-for-regression"></a>Metrische gegevens die worden gebruikt voor regressie

-   **Gemiddelde absolute fout** betekent dat alle fouten in het model al dan niet zijn opgetreden, waarbij fout de afstand van de voorspelde waarde van de werkelijke waarde is. Vaak afgekort als **Mae**.  

-   **Wortel van kwadraat fout** meet het gemiddelde van de kwadraten van de fouten en neemt vervolgens de hoofdmap van die waarde. Vaak afgekort als **RMSE**  

-   **Relatieve absolute fout** vertegenwoordigt de fout als een percentage van de waarde True.  

-   Bij **relatieve kwadratische fout** wordt het totale aantal gekwadrateerde fouten genormaliseerd door te delen door de totale kwadraat fout van de voorspelde waarden.  

-   **Determinatie coëfficiënt** Een enkel getal dat aangeeft hoe goed gegevens in een model passen. Een waarde van één betekent dat het model exact overeenkomt met de gegevens. de waarde 0 betekent dat de gegevens wille keurig of anderszins niet op het model passen. Wordt vaak **r<sup>2</sup>** , **r<sup>2</sup>** of **r-kwadraat**genoemd.  

### <a name="modules-that-do-not-support-a-parameter-sweep"></a>Modules die geen para meter-sweep ondersteunen

Bijna alle leer Azure Machine Learning ondersteunen Kruis validatie met een geïntegreerde para meter-sweep, waarmee u de para meters voor pijp lijn kunt kiezen. Als de cursist geen ondersteuning biedt voor het instellen van een reeks waarden, kunt u deze nog steeds gebruiken voor kruis validatie. In dit geval wordt een bereik van toegestane waarden geselecteerd voor de opschoning. 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 

