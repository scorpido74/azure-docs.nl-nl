---
title: Aan de slag met R
titleSuffix: ML Studio (classic) - Azure
description: Gebruik deze R-programmeerzelfstudie om aan de slag te gaan met de R-taal met Azure Machine Learning Studio (klassiek) om een prognoseoplossing te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 1b347707b3c656bd692a29f0fd748c1503be4fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217997"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Aan de slag met de R-programmeertaal in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Inleiding

Met deze zelfstudie u azure machine learning studio (klassiek) uitbreiden met behulp van de Programmeertaal R. Volg deze R-programmeerzelfstudie om R-code te maken, te testen en uit te voeren binnen Studio (klassiek). Terwijl u door de zelfstudie werkt, maakt u een complete prognoseoplossing met behulp van de R-taal in Studio (klassiek).  

Azure Machine Learning Studio (klassiek) bevat veel krachtige machine learning- en datamanipulatiemodules. De krachtige R-taal is beschreven als de lingua franca van analytics. Gelukkig kunnen analytics en datamanipulatie in Studio (klassiek) worden uitgebreid met R. Deze combinatie biedt de schaalbaarheid en het gemak van implementatie van Studio (klassiek) met de flexibiliteit en diepgaande analyses van R.

### <a name="forecasting-and-the-dataset"></a>Prognoses en de gegevensset

Forecasting is een veel gebruikte en zeer nuttige analytische methode. Veelvoorkomende toepassingen variëren van het voorspellen van de verkoop van seizoensartikelen, het bepalen van optimale voorraadniveaus tot het voorspellen van macro-economische variabelen. Forecasting wordt meestal gedaan met tijdseriemodellen.

Tijdreeksgegevens zijn gegevens waarin de waarden een tijdsindex hebben. De tijdindex kan regelmatig zijn, bijvoorbeeld elke maand of elke minuut, of onregelmatig. Een tijdreeksmodel is gebaseerd op tijdreeksgegevens. De R programmeertaal bevat een flexibel framework en uitgebreide analyses voor tijdreeksgegevens.

In deze gids werken we met californische zuivelproductie- en prijsgegevens. Deze gegevens omvatten maandelijkse informatie over de productie van verschillende zuivelproducten en de prijs van melkvet, een benchmarkgrondstof.

De gegevens die in dit artikel worden gebruikt, samen met R-scripts, kunnen worden gedownload van [MachineLearningSamples-Notebooks/studio-samples.](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) Gegevens in `cadairydata.csv` het bestand werd oorspronkelijk gesynthetiseerd [https://dairymarkets.com](https://dairymarkets.com)uit informatie beschikbaar van de Universiteit van Wisconsin op .

### <a name="organization"></a>Organisatie

We doorlopen verschillende stappen terwijl u leert hoe u analyse- en gegevensmanipulatieR-code maken, testen en uitvoeren in de (klassieke) azure machine learning studio(klassieke) omgeving.  

* Eerst zullen we de basisprincipes van het gebruik van de R-taal in de Azure Machine Learning Studio (klassieke) omgeving verkennen.
* Vervolgens gaan we verder met het bespreken van verschillende aspecten van I/O voor gegevens, R-code en afbeeldingen in de Azure Machine Learning Studio (klassieke) omgeving.
* Vervolgens bouwen we het eerste deel van onze prognoseoplossing door code te maken voor het reinigen en transformeren van gegevens.
* Met onze data voorbereid zullen we een analyse uitvoeren van de correlaties tussen verschillende variabelen in onze dataset.
* Tot slot zullen we een seizoensgebonden tijdreeks voorspellingmodel voor de melkproductie.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interactie met R-taal in Machine Learning Studio (klassiek)

Deze sectie neemt je mee door een aantal basisprincipes van interactie met de R-programmeertaal in de Machine Learning Studio (klassieke) omgeving. De R-taal biedt een krachtig hulpmiddel om modules voor analyse- en gegevensmanipulatie op maat te maken binnen de (klassieke) azure machine learning studio-omgeving.

Ik zal RStudio gebruiken om R-code op kleine schaal te ontwikkelen, testen en debugen. Deze code wordt vervolgens geknipt en geplakt in een [Execute R Script-module][execute-r-script] die klaar is om te worden uitgevoerd in Azure Machine Learning Studio (klassiek).  

### <a name="the-execute-r-script-module"></a>De Scriptmodule R uitvoeren

Binnen Machine Learning Studio (klassiek) worden R-scripts uitgevoerd binnen de [Execute R Script-module.][execute-r-script] Een voorbeeld van de [Scriptmodule Uitvoeren][execute-r-script] in Machine Learning Studio (klassiek) wordt weergegeven in figuur 1.

 ![R programmeertaal: De Module R Script uitvoeren geselecteerd in Machine Learning Studio (klassiek)](./media/r-quickstart/fig1.png)

*Figuur 1. De Machine Learning Studio (klassieke) omgeving met de geselecteerde Scriptmodule Uitvoeren.*

Verwijzend naar figuur 1, laten we eens kijken naar enkele van de belangrijkste onderdelen van de Machine Learning Studio (klassieke) omgeving voor het werken met de [Execute R Script][execute-r-script] module.

* De modules in het experiment worden weergegeven in het middelste deelvenster.
* Het bovenste gedeelte van het rechterdeelvenster bevat een venster om uw R-scripts weer te geven en te bewerken.  
* In het onderste gedeelte van het rechterdeelvenster worden enkele eigenschappen van het [Script R uitvoeren][execute-r-script]weergegeven. U de fout- en uitvoerlogboeken bekijken door de juiste plekken in dit deelvenster te selecteren.

We zullen, natuurlijk, bespreken de [Execut R Script][execute-r-script] in meer detail in de rest van dit artikel.

Bij het werken met complexe R-functies raad ik u aan om in RStudio te bewerken, te testen en te debuggen. Zoals met elke software ontwikkeling, uitbreiden van uw code stapsgewijs en test het op kleine eenvoudige testcases. Knip en plak uw functies vervolgens in het R-scriptvenster van de [Scriptmodule Uitvoeren.][execute-r-script] Met deze aanpak u zowel de geïntegreerde Ontwikkelomgeving (IDE) van RStudio als de kracht van Azure Machine Learning Studio (klassiek) benutten.  

#### <a name="execute-r-code"></a>R-code uitvoeren

Elke R-code in de [Script-module Uitvoeren][execute-r-script] wordt uitgevoerd wanneer u het experiment uitvoert door de knop **Uitvoeren te** selecteren. Wanneer de uitvoering is voltooid, verschijnt er een vinkje op het pictogram [R-script uitvoeren.][execute-r-script]

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Defensieve R-codering voor Azure Machine Learning

Als u R-code ontwikkelt voor bijvoorbeeld een webservice met Azure Machine Learning Studio (klassiek), moet u zeker plannen hoe uw code omgaat met een onverwachte gegevensinvoer en uitzonderingen. Om de duidelijkheid te behouden, heb ik niet veel opgenomen in de manier van controleren of exception handling in de meeste van de getoonde code voorbeelden. Echter, als we verder gaan zal ik u een aantal voorbeelden van functies met behulp van R's uitzondering handling vermogen.  

Als u behoefte aan een meer volledige behandeling van R uitzondering behandeling, adviseer ik u de toepasselijke delen van het boek van Wickham hieronder vermeld in [Verder lezen](#appendixb)te lezen.

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Foutopsporing en test R in Machine Learning Studio (klassiek)

Om te herhalen, adviseer ik u uw R-code op kleine schaal te testen en te debuggen in RStudio. Er zijn echter gevallen waarin u problemen met de R-code moet opsporen in het [Script uitvoeren][execute-r-script] zelf. Daarnaast is het een goede gewoonte om uw resultaten te controleren in Machine Learning Studio (klassiek).

Uitvoer vanaf de uitvoering van uw R-code en op het Azure Machine Learning Studio (classic) platform vindt u voornamelijk in output.log. Sommige aanvullende informatie zal worden gezien in error.log.  

Als er een fout optreedt in Machine Learning Studio (klassiek) tijdens het uitvoeren van uw R-code, moet uw eerste cursus van actie zijn om te kijken naar error.log. Dit bestand kan nuttige foutberichten bevatten om u te helpen uw fout te begrijpen en te corrigeren. Als u error.log wilt weergeven, selecteert u **Foutlogboek weergeven** in het **eigenschappenvenster** voor het [Script uitvoeren][execute-r-script] dat de fout bevat.

Ik heb bijvoorbeeld de volgende R-code, met een niet-gedefinieerde variabele y, uitgevoerd in een [Script-module uitvoeren:][execute-r-script]

```R
x <- 1.0
z <- x + y
```

Deze code wordt niet uitgevoerd, wat resulteert in een foutvoorwaarde. Als u **het foutlogboek weergeven** selecteert in het **eigenschappenvenster,** wordt de weergave weergegeven in figuur 2.

  ![Foutbericht pop-up](./media/r-quickstart/fig2.png)

*Figuur 2. Pop-up foutbericht.*

Het lijkt erop dat we moeten kijken in output.log om de R foutmelding te zien. Selecteer het [Script uitvoeren en][execute-r-script] selecteer het item **Uitvoer.logboek weergeven** in het **eigenschappenvenster** rechts. Er wordt een nieuw browservenster geopend en ik zie het volgende.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Deze foutmelding bevat geen verrassingen en identificeert duidelijk het probleem.

Als u de waarde van een object in R wilt controleren, u deze waarden afdrukken naar het bestand output.log. De regels voor het onderzoeken van objectwaarden zijn in wezen hetzelfde als in een interactieve R-sessie. Als u bijvoorbeeld een variabele naam op een regel typt, wordt de waarde van het object afgedrukt op het bestand output.log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Pakketten in Machine Learning Studio (klassiek)

Studio komt met meer dan 350 vooraf geïnstalleerde R-taalpakketten. U de volgende code in de [Script-module Uitvoeren][execute-r-script] gebruiken om een lijst met vooraf geïnstalleerde pakketten op te halen.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Als u de laatste regel van deze code op dit moment niet begrijpt, lees dan verder. In de rest van dit artikel bespreken we uitgebreid het gebruik van R in de Studio (klassieke) omgeving.

### <a name="introduction-to-rstudio"></a>Inleiding tot RStudio

RStudio is een veelgebruikte IDE voor R. Ik zal RStudio gebruiken voor het bewerken, testen en debuggen van een deel van de R-code die in deze gids wordt gebruikt. Zodra de R-code is getest en klaar, u eenvoudig knippen en plakken van de RStudio-editor in een Machine Learning Studio (klassieke) [Execute R Script-module.][execute-r-script]  

Als u niet beschikt over de R programmeertaal geïnstalleerd op uw desktop machine, adviseer ik u dit nu doen. Gratis downloads van open-source R taal zijn beschikbaar op [https://www.r-project.org/](https://www.r-project.org/)de Comprehensive R Archive Network (CRAN) op . Er zijn downloads beschikbaar voor Windows, Mac OS en Linux/UNIX. Kies een spiegel in de buurt en volg de downloadaanwijzingen. Daarnaast bevat CRAN een schat aan nuttige analyse- en gegevensmanipulatiepakketten.

Als u nieuw bent bij RStudio, moet u de desktopversie downloaden en installeren. U vindt de RStudio downloads voor Windows, Mac http://www.rstudio.com/products/RStudio/OS en Linux/UNIX op . Volg de aanwijzingen die zijn verstrekt om RStudio op uw desktopmachine te installeren.  

Een tutorial inleiding tot RStudio is beschikbaar op [het gebruik van de RStudio IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Ik geef wat aanvullende informatie over het gebruik van RStudio in [Guide to RStudio documentatie](#appendixa) hieronder.  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Gegevens in en uit de Script-module uitvoeren krijgen

In deze sectie bespreken we hoe u gegevens in en uit de [Execute R Script][execute-r-script] module krijgt. We bekijken hoe u verschillende gegevenstypen verwerken die in en uit de [Script-module uitvoeren][execute-r-script] worden gelezen.

De volledige code voor deze sectie is in [MachineLearningSamples-Notebooks/studio-samples.](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Gegevens laden en controleren in Machine Learning Studio (klassiek)

#### <a name="load-the-dataset"></a><a id="loading"></a>De gegevensset laden

We beginnen met het laden van het **csdairydata.csv-bestand** in Azure Machine Learning Studio (klassiek).

1. Start uw Azure Machine Learning Studio (klassieke) omgeving.
1. Selecteer **+ NIEUW** linksonder in het scherm en selecteer **Gegevensset**.
1. Selecteer **Uit lokaal bestand**en blader **om** het bestand te selecteren.
1. Zorg ervoor dat u **algemeen CSV-bestand met koptekst (.csv)** hebt geselecteerd als type voor de gegevensset.
1. Selecteer het vinkje.
1. Nadat de gegevensset is geüpload, moet u de nieuwe gegevensset bekijken door het tabblad **Gegevenssets te** selecteren.  

#### <a name="create-an-experiment"></a>Een experiment maken

Nu we wat gegevens hebben in Machine Learning Studio (klassiek), moeten we een experiment maken om de analyse te doen.  

1. Selecteer **+ NIEUW** linksonder en selecteer **Experiment**en vervolgens Leeg **Experiment**.
1. U uw experiment een naam geven door het experiment dat op... titel boven aan de pagina **is gemaakt,** te selecteren en te wijzigen. Bijvoorbeeld, het veranderen van het in **CA Dairy Analysis**.
1. Vouw opgeslagen **gegevenssets**en vervolgens **Mijn gegevenssets**uit aan de linkerkant van de experimentpagina. U ziet de **cadairydata.csv** die u eerder hebt geüpload.
1. Sleep en drop de **csdairydata.csv-gegevensset** op het experiment.
1. Typ [R Script uitvoeren][execute-r-script]in het vak **Proefitems** zoeken boven aan het linkerdeelvenster . U ziet de module verschijnen in de zoeklijst.
1. Sleep en zet de [Script-module Uitvoeren][execute-r-script] op uw pallet.  
1. Verbind de uitvoer van de **gegevensset csdairydata.csv** met de meest linkse invoer **(Dataset1**) van het [Script Uitvoeren r][execute-r-script].
1. **Vergeet niet om 'Opslaan' te selecteren!**  

Op dit punt moet uw experiment er ongeveer uitzien als figuur 3.

![De CA Dairy Analysis-experiment met de gegevensset en De R-scriptmodule uitvoeren](./media/r-quickstart/fig3.png)

*Figuur 3. De CA Dairy Analysis experimentmet dataset en Execute R Script module.*

#### <a name="check-on-the-data"></a>Controleer de gegevens

Laten we eens kijken naar de gegevens die we hebben geladen in ons experiment. Selecteer in het experiment de uitvoer van de **cadairydata.csv-gegevensset** en selecteer **visualiseren.** Je zou iets als figuur 4 moeten zien.  

![Samenvatting van de cadairydata.csv-gegevensset](./media/r-quickstart/fig4.png)

*Figuur 4. Samenvatting van de cadairydata.csv dataset.*

In deze weergave zien we veel nuttige informatie. We kunnen de eerste rijen van die gegevensset zien. Als we een kolom selecteren, wordt in de sectie Statistieken meer informatie over de kolom weergegeven. In de rij Feature Type ziet u bijvoorbeeld welke gegevenstypen Azure Machine Learning Studio (klassiek) aan de kolom zijn toegewezen. Na een snelle look als dit is een goede geestelijke gezondheid te controleren voordat we beginnen met een serieus werk te doen.

### <a name="first-r-script"></a>Eerste R-script

Laten we een eenvoudig eerste R-script maken om te experimenteren binnen Azure Machine Learning Studio (klassiek). Ik heb het volgende script gemaakt en getest in RStudio.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Nu moet ik dit script overbrengen naar Azure Machine Learning Studio (klassiek). Ik kan gewoon knippen en plakken. Echter, in dit geval zal ik mijn R script over te dragen via een zip-bestand.

### <a name="data-input-to-the-execute-r-script-module"></a>Gegevensinvoer naar de scriptmodule R-script uitvoeren

Laten we eens kijken naar de ingangen van de [Script-module uitvoeren.][execute-r-script] In dit voorbeeld lezen we de zuivelgegevens van Californië in de [Execute R Script-module.][execute-r-script]  

Er zijn drie mogelijke ingangen voor de [Execute R Script module.][execute-r-script] Afhankelijk van uw toepassing u een of meer van deze ingangen gebruiken. Het is ook volkomen redelijk om een R-script te gebruiken dat helemaal geen invoer vergt.  

Laten we eens kijken naar elk van deze ingangen, gaande van links naar rechts. U de namen van elk van de ingangen zien door de cursor over de invoer te plaatsen en de knopinfo te lezen.  

#### <a name="script-bundle"></a>Scriptbundel

Met de scriptbundel-invoer u de inhoud van een zip-bestand doorgeven aan [de Script-module uitvoeren.][execute-r-script] U een van de volgende opdrachten gebruiken om de inhoud van het zip-bestand in uw R-code te lezen.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (klassiek) behandelt bestanden in de zip alsof ze zich in de src/directory bevinden, dus u moet uw bestandsnamen vooraf herstellen met deze naam van de map. Als de zip bijvoorbeeld de `yourfile.R` `yourData.rdata` bestanden bevat en in de hoofdmap `src/yourfile.R` `src/yourData.rdata` van `source` de `load`rits, zou u deze aanpakken als en wanneer deze worden gebruikt en .

We hebben al gesproken over het laden van gegevenssets in [Load the dataset.](#loading) Ga als volgt te werk nadat u het R-script in de vorige sectie hebt gemaakt en getest:

1. Sla het R-script op in een . R-bestand. Ik noem mijn script bestand "simpleplot. R". Hier is de inhoud.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Maak een zip-bestand en kopieer je script naar dit zip-bestand. In Windows u met de rechtermuisknop op het bestand klikken en **verzenden naar**en vervolgens **gecomprimeerde map**selecteren. Dit zal leiden tot een nieuwe zip-bestand met de "simpleplot. R" bestand.

1. Voeg uw bestand toe aan de **gegevenssets** in Azure Machine Learning Studio (klassiek), waarbij het type als **zip wordt opgegeven.** U ziet nu het zip-bestand in uw gegevenssets.

1. Sleep en zet het zip-bestand uit **gegevenssets** naar het **ML Studio -canvas (klassiek).**

1. Verbind de uitvoer van het **zip-gegevenspictogram** met de **scriptbundelinvoer** van de [Scriptmodule uitvoeren.][execute-r-script]

1. Typ `source()` de functie met de naam van het zip-bestand in het codevenster voor de [Script-module Uitvoeren.][execute-r-script] In mijn geval `source("src/simpleplot.R")`typte ik .  

1. Zorg ervoor dat u **Opslaan**selecteert.

Zodra deze stappen zijn voltooid, voert de [Script-module R][execute-r-script] uitvoeren het R-script uit in het zip-bestand wanneer het experiment wordt uitgevoerd. Op dit punt moet uw experiment er ongeveer uitzien als figuur 5.

![Experiment met het R-script met rits](./media/r-quickstart/fig6.png)

*Figuur 5. Experimenteer met een R-script met rits.*

#### <a name="dataset1"></a>Gegevensset1

U een rechthoekige tabel met gegevens doorgeven aan uw R-code met behulp van de invoer gegevensset1. In ons eenvoudige `maml.mapInputPort(1)` script leest de functie de gegevens uit poort 1. Deze gegevens worden vervolgens toegewezen aan een naam van een dataframevariabele in uw code. In ons eenvoudige script voert de eerste regel code de opdracht uit.

```R
cadairydata <- maml.mapInputPort(1)
```

Voer het experiment uit door de knop **Uitvoeren te** selecteren. Wanneer de uitvoering is voltooid, selecteert u de module [R-script uitvoeren][execute-r-script] en selecteert u **Het uitvoerlogboek van Weergave** op het eigenschappenvenster. Er moet een nieuwe pagina in uw browser worden weergegeven met de inhoud van het bestand output.log. Wanneer u naar beneden scrollt, moet u iets als het volgende zien.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Verder op de pagina is meer gedetailleerde informatie over de kolommen, die er ongeveer als volgt uitzien.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Deze resultaten zijn meestal zoals verwacht, met 228 waarnemingen en 9 kolommen in het dataframe. We kunnen de kolomnamen, het R-gegevenstype en een voorbeeld van elke kolom zien.

> [!NOTE]
> Deze zelfde afgedrukte uitvoer is gemakkelijk beschikbaar via de R-apparaatuitvoer van de [Script-module Uitvoeren.][execute-r-script] In de volgende sectie bespreken we de uitgangen van de [Script-module uitvoeren.][execute-r-script]  

#### <a name="dataset2"></a>Gegevensset2

Het gedrag van de dataset2-invoer is identiek aan dat van Gegevensset1. Met deze invoer u een tweede rechthoekige tabel met gegevens doorgeven aan uw R-code. De `maml.mapInputPort(2)`functie , met het argument 2, wordt gebruikt om deze gegevens door te geven.  

### <a name="execute-r-script-outputs"></a>R Script-uitvoer uitvoeren

#### <a name="output-a-dataframe"></a>Een gegevensframe uitvoeren

U de inhoud van een R-gegevensframe als een rechthoekige `maml.mapOutputPort()` tabel via de poort Resultaatgegevensset1 uitvoeren met behulp van de functie. In ons eenvoudige R-script wordt dit uitgevoerd door de volgende regel.

```
maml.mapOutputPort('cadairydata')
```

Selecteer na het uitvoeren van het experiment de uitvoerpoort Resultaatgegevensset1 en selecteer **Vervolgens Visualiseren**. Je zou iets als figuur 6 moeten zien.

![De visualisatie van de output van de Californische zuivelgegevens](./media/r-quickstart/fig7.png)

*Figuur 6. De visualisatie van de output van de Californische zuivelgegevens.*

Deze uitvoer ziet er identiek uit als de invoer, precies zoals we hadden verwacht.  

### <a name="r-device-output"></a>R-apparaatuitvoer

De apparaatuitvoer van de [Script-module Uitvoeren][execute-r-script] bevat berichten en grafische uitvoer. Zowel standaarduitvoer- als standaardfoutmeldingen van R worden naar de uitvoerpoort van het R-apparaat verzonden.  

Als u de uitvoer van het R-apparaat wilt weergeven, selecteert u de poort en vervolgens op **Visualiseren**. We zien de standaard uitvoer en standaardfout van het R-script in figuur 7.

![Standaarduitvoer en standaardfout van de R-apparaatpoort](./media/r-quickstart/fig8.png)

*Figuur 7. Standaarduitvoer en standaardfout van de R-apparaatpoort.*

Scrollen naar beneden zien we de grafische output van onze R script in figuur 8.  

![Grafische uitvoer vanaf de R-apparaatpoort](./media/r-quickstart/fig9.png)

*Figuur 8. Grafische uitvoer vanaf de R-apparaatpoort.*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Gegevensfiltering en -transformatie

In deze sectie zullen we een aantal basisgegevensfiltering en transformatie operaties op de Californische zuivelgegevens uit te voeren. Tegen het einde van deze sectie hebben we gegevens in een formaat dat geschikt is voor het bouwen van een analytisch model.  

Meer specifiek zullen we in deze sectie verschillende veelvoorkomende taken voor gegevensreiniging en -transformatie uitvoeren: typetransformatie, filteren op dataframes, toevoegen van nieuwe berekende kolommen en waardetransformaties. Deze achtergrond moet u helpen omgaan met de vele variaties ondervonden in de echte wereld problemen.

De volledige R-code voor deze sectie is beschikbaar in [MachineLearningSamples-Notebooks/studio-samples.](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)

### <a name="type-transformations"></a>Typetransformaties

Nu we de zuivelgegevens van Californië in de R-code in de [Execut R Script-module][execute-r-script] kunnen lezen, moeten we ervoor zorgen dat de gegevens in de kolommen het beoogde type en formaat hebben.  

R is een dynamisch getypte taal, wat betekent dat gegevenstypen naar behoefte van de ene naar de andere worden afgetrokken. De atoomgegevenstypen in R bevatten numeriek, logisch en karakter. Het factortype wordt gebruikt om categorische gegevens compact op te slaan. In de verwijzingen vindt u veel meer informatie over gegevenstypen in [Onderstaande referenties.](#appendixb)

Wanneer tabelgegevens in R worden gelezen vanuit een externe bron, is het altijd een goed idee om de resulterende typen in de kolommen te controleren. U wilt misschien een kolom van het type teken, maar in veel gevallen zal dit worden weergegeven als factor of vice versa. In andere gevallen wordt een kolom die volgens u numeriek moet zijn, weergegeven door tekengegevens, bijvoorbeeld '1,23' in plaats van 1,23 als zwevend puntnummer.  

Gelukkig is het gemakkelijk om het ene type naar het andere te converteren, zolang mapping mogelijk is. U 'Nevada' bijvoorbeeld niet omzetten in een numerieke waarde, maar u deze omzetten in een factor (categorische variabele). Als een ander voorbeeld u een numerieke 1 omzetten in een teken '1' of een factor.  

De syntaxis voor een van `as.datatype()`deze conversies is eenvoudig: . Deze typeconversiefuncties omvatten de volgende functies.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Kijkend naar de gegevenstypen van de kolommen die we in de vorige sectie invoeren: alle kolommen zijn van type numeriek, met uitzondering van de kolom met het label 'Maand', die van het typeteken is. Laten we dit omzetten in een factor en de resultaten testen.  

Ik heb de regel verwijderd die de scatterplotmatrix heeft gemaakt en een regel heeft toegevoegd die de kolom 'Maand' omzet naar een factor. In mijn experiment knip en plak ik de R-code in het codevenster van de [Scriptmodule uitvoeren.][execute-r-script] U het zip-bestand ook bijwerken en uploaden naar Azure Machine Learning Studio (klassiek), maar dit neemt verschillende stappen.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Laten we deze code uitvoeren en kijken naar het uitvoerlogboek voor het R-script. De relevante gegevens uit het logboek worden weergegeven in figuur 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figuur 9. Samenvatting van het dataframe met een factorvariabele.*

Het type voor maand moet nu zeggen '**Factor w / 14 niveaus**'. Dit is een probleem, omdat er slechts 12 maanden in het jaar. U ook controleren of het type in **Visualize** van de poort Van de resultatengegevensset **'Categorisch'** is.

Het probleem is dat de kolom 'Maand' niet systematisch is gecodeerd. In sommige gevallen wordt een maand april genoemd en in andere wordt het afgekort als april. We kunnen dit probleem oplossen door de snaar bij te snijden tot 3 tekens. De coderegel ziet er nu als volgt uit:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Voer het experiment opnieuw uit en bekijk het uitvoerlogboek. De verwachte resultaten worden weergegeven in figuur 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figuur 10. Samenvatting van het gegevensframe met het juiste aantal factorniveaus.*

Onze factor variabele heeft nu de gewenste 12 niveaus.

### <a name="basic-data-frame-filtering"></a>Basisgegevensframefilteren

R-dataframes ondersteunen krachtige filtermogelijkheden. Gegevenssets kunnen worden subseten met behulp van logische filters op rijen of kolommen. In veel gevallen zijn complexe filtercriteria vereist. De referenties in [Verder lezen](#appendixb) hieronder bevatten uitgebreide voorbeelden van het filteren van dataframes.  

Er is een beetje filtering die we moeten doen op onze dataset. Als u de kolommen in het cadairydata-gegevensframe bekijkt, ziet u twee onnodige kolommen. De eerste kolom bevat alleen een rijnummer, wat niet erg handig is. De tweede kolom, Year.Month, bevat overbodige informatie. We kunnen deze kolommen eenvoudig uitsluiten met behulp van de volgende R-code.

> [!NOTE]
> Van nu af aan in deze sectie, zal ik gewoon laten zien de extra code die ik toevoeg in de [Execute R Script][execute-r-script] module. Ik zal elke **before** nieuwe `str()` regel toevoegen voor de functie. Ik gebruik deze functie om mijn resultaten te verifiëren in Azure Machine Learning Studio (klassiek).

Ik voeg de volgende regel toe aan mijn R-code in de [Script-module Uitvoeren.][execute-r-script]

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Voer deze code uit in uw experiment en controleer het resultaat van het uitvoerlogboek. Deze resultaten worden weergegeven in figuur 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figuur 11. Samenvatting van het gegevensframe met twee kolommen verwijderd.*

Goed nieuws! We krijgen de verwachte resultaten.

### <a name="add-a-new-column"></a>Een nieuwe kolom toevoegen

Om tijdseriemodellen te maken is het handig om een kolom te hebben met de maanden sinds het begin van de tijdreeks. We maken een nieuwe kolom 'Month.Count'.

Om te helpen bij het organiseren van `num.month()`de code zullen we onze eerste eenvoudige functie te creëren, . We passen deze functie dan toe om een nieuwe kolom in het dataframe te maken. De nieuwe code is als volgt.

```R
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Voer nu het bijgewerkte experiment uit en gebruik het uitvoerlogboek om de resultaten weer te geven. Deze resultaten zijn weergegeven in figuur 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figuur 12. Samenvatting van het gegevensframe met de extra kolom.*

Het lijkt erop dat alles werkt. We hebben de nieuwe kolom met de verwachte waarden in ons dataframe.

### <a name="value-transformations"></a>Waardetransformaties

In deze sectie zullen we enkele eenvoudige transformaties uitvoeren op de waarden in sommige kolommen van ons dataframe. De R-taal ondersteunt bijna willekeurige waardetransformaties. De referenties in [Verder lezen](#appendixb) hieronder bevatten uitgebreide voorbeelden.

Als je kijkt naar de waarden in de samenvattingen van ons dataframe moet je hier iets vreemds zien. Is er meer ijs dan melk geproduceerd in Californië? Nee, natuurlijk niet, want dit heeft geen zin, triest als dit feit kan worden voor sommigen van ons ijs liefhebbers. De eenheden zijn verschillend. De prijs is in eenheden van Amerikaanse ponden, melk is in eenheden van 1 M Amerikaanse ponden, ijs is in eenheden van 1.000 Amerikaanse liter, en kwark is in eenheden van 1.000 Amerikaanse ponden. Ervan uitgaande dat ijs weegt ongeveer 6,5 pond per gallon, kunnen we gemakkelijk doen de vermenigvuldiging om deze waarden om te zetten, zodat ze allemaal in gelijke eenheden van 1.000 pond.

Voor ons prognosemodel gebruiken we een multipliciteer model voor trend- en seizoensaanpassing van deze gegevens. Een log transformatie stelt ons in staat om een lineair model te gebruiken, waardoor dit proces wordt vereenvoudigd. We kunnen de logtransformatie toepassen in dezelfde functie waar de multiplier wordt toegepast.

In de volgende code definieer ik `log.transform()`een nieuwe functie en pas deze toe op de rijen met de numerieke waarden. De `Map()` functie R wordt `log.transform()` gebruikt om de functie toe te passen op de geselecteerde kolommen van het gegevensframe. `Map()`is vergelijkbaar `apply()` met, maar zorgt voor meer dan een lijst van argumenten voor de functie. Houd er rekening mee dat een lijst `log.transform()` met multipliers het tweede argument aan de functie levert. De `na.omit()` functie wordt gebruikt als een beetje opschonen om ervoor te zorgen dat we geen ontbrekende of ongedefinieerde waarden in het gegevensframe hebben.

```R
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warningmessage to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Er is nogal wat `log.transform()` gebeurt in de functie. Het grootste deel van deze code is het controleren op mogelijke problemen met de argumenten of het omgaan met uitzonderingen, die nog steeds kunnen ontstaan tijdens de berekeningen. Slechts een paar regels van deze code daadwerkelijk doen de berekeningen.

Het doel van de defensieve programmering is om te voorkomen dat het falen van een enkele functie die voorkomt dat de verwerking voort te zetten. Een abrupte mislukking van een langlopende analyse kan heel frustrerend zijn voor gebruikers. Om deze situatie te voorkomen, moeten standaardretourwaarden worden gekozen die de schade voor downstreamverwerking beperken. Er wordt ook een bericht opgesteld om gebruikers te waarschuwen dat er iets mis is gegaan.

Als u niet gewend bent aan defensieve programmering in R, al deze code lijkt misschien een beetje overweldigend. Ik zal je door de grote stappen brengen:

1. Er wordt een vector van vier berichten gedefinieerd. Deze berichten worden gebruikt om informatie over enkele mogelijke fouten en uitzonderingen die kunnen optreden met deze code te communiceren.
2. Ik geef een waarde van NA voor elk geval. Er zijn vele andere mogelijkheden die minder bijwerkingen kunnen hebben. Ik kan bijvoorbeeld een vector van nullen of de oorspronkelijke invoervector retourneren.
3. Er worden controles uitgevoerd op de argumenten voor de functie. In elk geval, als een fout wordt gedetecteerd, wordt een standaardwaarde geretourneerd en wordt een bericht geproduceerd door de `warning()` functie. Ik gebruik `warning()` in `stop()` plaats van als de laatste zal de uitvoering te beëindigen, precies wat ik probeer te vermijden. Merk op dat ik deze code heb geschreven in een procedurele stijl, zoals in dit geval een functionele aanpak leek complex en duister.
4. De logboekberekeningen zijn zo `tryCatch()` verpakt dat uitzonderingen niet leiden tot een abrupte stopzetting van de verwerking. Zonder `tryCatch()` de meeste fouten die door R-functies worden aangekaart, resulteert dit in een stopsignaal, wat precies dat doet.

Voer deze R-code uit in uw experiment en bekijk de afgedrukte uitvoer in het bestand output.log. U ziet nu de getransformeerde waarden van de vier kolommen in het logboek, zoals weergegeven in figuur 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figuur 13. Overzicht van de getransformeerde waarden in het gegevensframe.*

We zien dat de waarden zijn getransformeerd. De melkproductie overtreft nu ruimschoots alle andere zuivelproducten, eraan herinnerend dat we nu kijken naar een logschaal.

Op dit punt onze gegevens is opgeruimd en we zijn klaar voor een aantal modellering. Kijkend naar de visualisatiesamenvatting voor de resultaatuitvoer van onze [Execute R Script-module,][execute-r-script] ziet u de kolom 'Maand' is 'Categorisch' met 12 unieke waarden, nogmaals, precies zoals we wilden.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Tijdreeksobjecten en correlatieanalyse

In deze sectie zullen we een paar fundamentele R-tijdreeksobjecten verkennen en de correlaties tussen enkele variabelen analyseren. Ons doel is om een dataframe met de pairwise correlatie-informatie op verschillende vertragingen uit te brengen.

De volledige R-code voor deze sectie staat in [MachineLearningSamples-Notebooks/studio-samples.](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)

### <a name="time-series-objects-in-r"></a>Tijdreeksobjecten in R

Zoals reeds vermeld, zijn tijdreeksen een reeks gegevenswaarden geïndexeerd door de tijd. Objecten met de R-tijdreeks worden gebruikt om de tijdindex te maken en te beheren. Er zijn verschillende voordelen aan het gebruik van tijdserie objecten. Tijdreeksobjecten bevrijden u van de vele details van het beheren van de tijdreeksindexwaarden die in het object zijn ingekapseld. Bovendien u met tijdreeksobjecten de vele methoden voor tijdreeksen gebruiken voor het plotten, afdrukken, modelleren, enz.

De POSIXct tijdserie klasse wordt vaak gebruikt en is relatief eenvoudig. Deze tijdserieklasse meet de tijd vanaf het begin van het tijdperk, 1 januari 1970. We zullen posixct tijdserie objecten gebruiken in dit voorbeeld. Andere veel gebruikte R tijdserie object klassen omvatten dierentuin en xts, uitbreidbare tijd serie.

### <a name="time-series-object-example"></a>Voorbeeld van object tijdreeksen

Laten we beginnen met ons voorbeeld. Sleep en drop een **nieuwe** [Script-module Uitvoeren][execute-r-script] in het experiment. Verbind de outputpoort Resultaatdataset1 van de bestaande [Script-scriptmodule uitvoeren][execute-r-script] met de invoerpoort Gegevensset1 van de nieuwe [Scriptmodule uitvoeren.][execute-r-script]

Zoals ik deed voor de eerste voorbeelden, als we verder door het voorbeeld, op sommige punten zal ik alleen de incrementele extra regels van R-code bij elke stap.  

#### <a name="reading-the-dataframe"></a>Het gegevensframe lezen

Als eerste stap, laten we lezen in een dataframe en zorg ervoor dat we de verwachte resultaten te krijgen. De volgende code moet het werk doen.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Voer het experiment uit. Het logboek van de nieuwe Script-shape uitvoeren moet er uitzien als figuur 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Figuur 14. Samenvatting van het gegevensframe in de Script-module R uitvoeren.*

Deze gegevens zijn van de verwachte typen en indeling. Houd er rekening mee dat de kolom 'Maand' van typefactor is en het verwachte aantal niveaus heeft.

#### <a name="creating-a-time-series-object"></a>Een tijdreeksobject maken

We moeten een tijdreeksobject toevoegen aan ons dataframe. Vervang de huidige code door het volgende, dat een nieuwe kolom van klasse POSIXct toevoegt.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Controleer nu het logboek. Het moet op figuur 15 lijken.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figuur 15. Samenvatting van het gegevensframe met een tijdreeksobject.*

We kunnen zien uit de samenvatting dat de nieuwe kolom is in feite van klasse POSIXct.

### <a name="exploring-and-transforming-the-data"></a>De gegevens verkennen en transformeren

Laten we een aantal van de variabelen in deze gegevensset verkennen. Een scatterplot matrix is een goede manier om een snelle look te produceren. Ik vervang de `str()` functie in de vorige R-code door de volgende regel.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Voer deze code uit en kijk wat er gebeurt. Het perceel geproduceerd op de R Device poort moet eruit zien als figuur 16.

![Scatterplotmatrix van geselecteerde variabelen](./media/r-quickstart/fig17.png)

*Figuur 16. Scatterplotmatrix van geselecteerde variabelen.*

Er is een aantal vreemd uitziende structuur in de relaties tussen deze variabelen. Misschien komt dit voort uit trends in de gegevens en uit het feit dat we de variabelen niet hebben gestandaardiseerd.

### <a name="correlation-analysis"></a>Correlatieanalyse

Om correlatieanalyse uit te voeren moeten we zowel de-trend en standaardiseren van de variabelen. We kunnen gewoon `scale()` gebruik maken van de R-functie, die zowel centra en schalen variabelen. Deze functie kan wel eens sneller worden uitgevoerd. Ik wil u echter een voorbeeld van defensieve programma's in R laten zien.

De `ts.detrend()` onderstaande functie voert beide bewerkingen uit. De volgende twee regels code de-trend de-trend van de gegevens en vervolgens standaardiseren van de waarden.

```R
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Er is nogal wat `ts.detrend()` gebeurt in de functie. Het grootste deel van deze code is het controleren op mogelijke problemen met de argumenten of het omgaan met uitzonderingen, die nog steeds kunnen ontstaan tijdens de berekeningen. Slechts een paar regels van deze code daadwerkelijk doen de berekeningen.

We hebben al gesproken over een voorbeeld van defensieve programmering in Value transformaties. Beide berekeningsblokken zijn `tryCatch()`verpakt in . Voor sommige fouten is het zinvol om de oorspronkelijke invoervector terug te keren, en in andere gevallen, ik keer een vector van nullen.  

Houd er rekening mee dat de lineaire regressie die wordt gebruikt voor detrending een regressie van tijdreeksen is. De predictor variabele is een tijdreeks object.  

Eenmaal `ts.detrend()` gedefinieerd passen we het toe op de variabelen die van belang zijn in ons dataframe. We moeten de resulterende lijst `lapply()` die is gemaakt `as.data.frame()`door gegevensgegevensframe met behulp van . Vanwege defensieve aspecten `ts.detrend()`van , niet te verwerken een van de variabelen zal niet voorkomen dat de juiste verwerking van de anderen.  

De laatste regel code maakt een pairwise scatterplot. Na het uitvoeren van de R-code worden de resultaten van de scatterplot weergegeven in figuur 17.

![Pairwise scatterplot van gedetrendeerde en gestandaardiseerde tijdreeksen](./media/r-quickstart/fig18.png)

*Figuur 17. Pairwise scatterplot van gedetrendeerde en gestandaardiseerde tijdreeksen.*

U deze resultaten vergelijken met de resultaten in figuur 16. Met de trend verwijderd en de variabelen gestandaardiseerd, zien we een stuk minder structuur in de relaties tussen deze variabelen.

De code om de correlaties als R ccf-objecten te berekenen, is als volgt.

```R
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Het uitvoeren van deze code produceert het logboek in figuur 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Figuur 18. Lijst van ccf-objecten uit de pairwise correlatieanalyse.*

Er is een correlatiewaarde voor elke vertraging. Geen van deze correlatiewaarden is groot genoeg om significant te zijn. We kunnen dus concluderen dat we elke variabele onafhankelijk kunnen modelleren.

### <a name="output-a-dataframe"></a>Een gegevensframe uitvoeren
We hebben de pairwise correlaties berekend als een lijst van R ccf-objecten. Dit vormt een beetje een probleem, omdat de outputpoort van de resultatenset echt een dataframe vereist. Verder is het ccf-object zelf een lijst en willen we alleen de waarden in het eerste element van deze lijst, de correlaties bij de verschillende vertragingen.

Met de volgende code worden de lagwaarden uit de lijst met ccf-objecten, die zelf lijsten zijn, uitdeiningen.

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

De eerste regel code is een beetje lastig, en enige uitleg kan u helpen begrijpen. Werken van binnenuit hebben we het volgende:

1. De operator**met**het argument '**1**' selecteert de vector van correlaties bij de vertragingen uit het eerste element van de ccf-objectlijst.
2. De `do.call()` functie `rbind()` past de functie toe op `lapply()`de elementen van de lijst retourneert door .
3. De `data.frame()` functie coeraën `do.call()` het resultaat geproduceerd door een dataframe.

Houd er rekening mee dat de rijnamen zich in een kolom van het gegevensframe bevinden. Als u dit doet, blijft de rijnamen behouden wanneer deze worden uitgevoerd in het [Script R uitvoeren][execute-r-script].

Als u de code uitvoert, wordt de uitvoer geproduceerd die wordt weergegeven in figuur 19 wanneer ik de uitvoer **visualiseer** op de poort Van de gegevenssetvan het resultaat. De rijnamen staan in de eerste kolom, zoals bedoeld.

![Resultaten output van de correlatieanalyse](./media/r-quickstart/fig20.png)

*Figuur 19. Resultaten output van de correlatie analyse.*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Voorbeeld van tijdreeksen: seizoensprognoses

Onze gegevens zijn nu in een vorm die geschikt is voor analyse, en we hebben vastgesteld dat er geen significante correlaties zijn tussen de variabelen. Laten we verder gaan en een tijdreeksvoorspellingsmodel maken. Met behulp van dit model zullen we de californische melkproductie voorspellen voor de 12 maanden van 2013.

Ons prognosemodel zal twee componenten hebben, een trendcomponent en een seizoenscomponent. De volledige prognose is het product van deze twee componenten. Dit type model staat bekend als een multipliciteiatief model. Het alternatief is een additief model. We hebben al een log transformatie toegepast op de variabelen van belang, waardoor deze analyse hardnekkig.

De volledige R-code voor deze sectie staat in [MachineLearningSamples-Notebooks/studio-samples.](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)

### <a name="creating-the-dataframe-for-analysis"></a>Het gegevensframe maken voor analyse

Begin met het toevoegen van een **nieuwe** [Script-module Uitvoeren][execute-r-script] aan uw experiment. Verbind de **resultaatgegevenssetuitvoer** van de bestaande [Execute R Script-module][execute-r-script] met de **gegevensset1-invoer** van de nieuwe module. Het resultaat moet op figuur 20 lijken.

![Het experiment met de nieuwe Execute R Script-module toegevoegd](./media/r-quickstart/fig21.png)

*Figuur 20. Het experiment met de nieuwe Execute R Script-module is toegevoegd.*

Net als bij de correlatieanalyse die we net hebben voltooid, moeten we een kolom toevoegen met een POSIXct-tijdreeksobject. De volgende code zal dit doen.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Voer deze code uit en kijk naar het logboek. Het resultaat moet lijken op figuur 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figuur 21. Een samenvatting van het dataframe.*

Met dit resultaat zijn we klaar om onze analyse te starten.

### <a name="create-a-training-dataset"></a>Een trainingsgegevensset maken

Met het dataframe gebouwd moeten we een trainingsdataset maken. Deze gegevens bevatten alle waarnemingen, behalve de laatste 12, van het jaar 2013, dat is onze testdataset. De volgende code subsets het dataframe en creëert percelen van de zuivelproductie en prijsvariabelen. Vervolgens maak ik percelen van de vier productie- en prijsvariabelen. Een anonieme functie wordt gebruikt om een aantal augments voor plot te definiëren `Map()`en vervolgens herhalen over de lijst van de andere twee argumenten met . Als u denkt dat een voor lus zou hebben gewerkt prima hier, je hebt gelijk. Maar omdat R een functionele taal is, laat ik je een functionele benadering zien.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Het uitvoeren van de code produceert de reeks tijdreeksen plots van de R-apparaat uitvoer weergegeven in figuur 22. Merk op dat de tijdas is in eenheden van datums, een mooi voordeel van de tijdreeks plot methode.

![Eerste van de tijd reeks percelen van Californië zuivelproductie en prijs gegevens](./media/r-quickstart/unnamed-chunk-161.png)

![Tweede van de tijd reeks percelen van Californië zuivelproductie en prijs gegevens](./media/r-quickstart/unnamed-chunk-162.png)

![Derde van de tijd reekspercelen van de zuivelproductie en de prijsgegevens van Californië](./media/r-quickstart/unnamed-chunk-163.png)

![Vierde van de tijd reeks percelen van Californië zuivelproductie en prijs gegevens](./media/r-quickstart/unnamed-chunk-164.png)

*Figuur 22. Tijdreekspercelen van de zuivelproductie en de prijsgegevens van Californië.*

### <a name="a-trend-model"></a>Een trendmodel

Na het maken van een tijdreeks object en een blik op de gegevens, laten we beginnen met het bouwen van een trendmodel voor de Californische melkproductie gegevens. We kunnen dit doen met een tijdreeks regressie. Echter, het is duidelijk uit het perceel dat we meer dan een helling nodig en onderscheppen om nauwkeurig model van de waargenomen trend in de opleiding gegevens.

Gezien de kleine schaal van de gegevens, zal ik het model voor trend in RStudio bouwen en vervolgens het resulterende model knippen en plakken in Azure Machine Learning Studio (klassiek). RStudio biedt een interactieve omgeving voor dit soort interactieve analyses.

Als een eerste poging, zal ik proberen een polynomial regressie met bevoegdheden tot 3. Er is een reëel gevaar van over-montage van dit soort modellen. Daarom is het het beste om hoge bestelvoorwaarden te vermijden. De `I()` functie remt de interpretatie van de inhoud (interpreteert de inhoud 'as is') en stelt u in staat om een letterlijk geïnterpreteerde functie te schrijven in een regressievergelijking.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Dit genereert het volgende.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Uit P-waarden (`Pr(>|t|)`) in deze uitvoer, kunnen we zien dat de kwadraat term kan niet significant zijn. Ik zal `update()` de functie gebruiken om dit model te wijzigen door de kwadraatterm te laten vallen.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Dit genereert het volgende.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Dit ziet er beter uit. Alle termen zijn belangrijk. De waarde van 2e-16 is echter een standaardwaarde en moet niet al te serieus worden genomen.  

Als een geestelijke gezondheid test, laten we een tijdreeks plot van de Californische zuivelproductie gegevens met de trendcurve getoond. Ik heb de volgende code toegevoegd in het Azure Machine Learning Studio (klassiek) [Execute R Script-model][execute-r-script] (niet RStudio) om het model te maken en een plot te maken. Het resultaat wordt weergegeven in figuur 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Californische melkproductiegegevens met getoond trendmodel](./media/r-quickstart/unnamed-chunk-18.png)

*Figuur 23. De gegevens van de melkproductie van Californië met getoond trendmodel.*

Het lijkt erop dat de trend model past de gegevens vrij goed. Verder lijkt er geen bewijs van over-fitting, zoals oneven wiggles in het model curve.  

### <a name="seasonal-model"></a>Seizoensmodel

Met een trendmodel in de hand, moeten we doorgaan en de seizoensgebonden effecten opnemen. We gebruiken de maand van het jaar als dummy variabele in het lineaire model om het effect van maand tot maand vast te leggen. Houd er rekening mee dat wanneer u factorvariabelen in een model introduceert, de onderschepping niet mag worden berekend. Als u dit niet doet, is de formule over-gespecificeerd en zal R een van de gewenste factoren laten vallen, maar de onderscheppingsterm behouden.

Aangezien we een bevredigend trendmodel hebben, kunnen we de functie gebruiken om de `update()` nieuwe termen toe te voegen aan het bestaande model. De -1 in de updateformule laat de onderscheppingstermijn vallen. Doorgaan in RStudio voor het moment:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Dit genereert het volgende.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

We zien dat het model niet langer een onderscheppingstermijn heeft en 12 belangrijke maandfactoren heeft. Dit is precies wat we wilden zien.

Laten we een andere tijdserie plot van de Californische zuivelproductie gegevens om te zien hoe goed het seizoensgebonden model werkt. Ik heb de volgende code toegevoegd in de Azure Machine Learning Studio (klassiek) [R Script uitvoeren][execute-r-script] om het model te maken en een plot te maken.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Als u deze code uitvoert in Azure Machine Learning Studio (klassiek) wordt het plot in figuur 24 geproduceerd.

![Californische melkproductie met model inclusief seizoenseffecten](./media/r-quickstart/unnamed-chunk-20.png)

*Figuur 24. Californische melkproductie met model inclusief seizoenseffecten.*

De geschiktheid voor de gegevens in figuur 24 is nogal bemoedigend. Zowel de trend als het seizoenseffect (maandelijkse variatie) zien er redelijk uit.

Als een andere controle op ons model, laten we eens kijken naar de restanten. De volgende code berekent de voorspelde waarden van onze twee modellen, berekent de restanten voor het seizoensmodel en brengt deze restanten vervolgens in kaart voor de trainingsgegevens.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Het resterende perceel is weergegeven in figuur 25.

![Restanten van het seizoensmodel voor de opleidingsgegevens](./media/r-quickstart/unnamed-chunk-21.png)

*Figuur 25. Restanten van het seizoensmodel voor de trainingsgegevens.*

Deze resten zien er redelijk uit. Er is geen bijzondere structuur, behalve het effect van de recessie van 2008-2009, die ons model niet bijzonder goed voor zijn rekening neemt.

De plot in figuur 25 is nuttig voor het detecteren van tijdsafhankelijke patronen in de resten. De expliciete benadering van het berekenen en plotten van de resten die ik gebruikte plaatst de resten in de tijd orde op het perceel. Als ik daarentegen had uitgezet, `milk.lm$residuals`zou het perceel niet op tijd zijn geweest.

U `plot.lm()` ook gebruiken om een reeks diagnostische percelen te produceren.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Deze code produceert een reeks diagnostische percelen in figuur 26.

![Eerste van diagnostische percelen voor het seizoensmodel](./media/r-quickstart/unnamed-chunk-221.png)

![Tweede van diagnostische percelen voor het seizoensmodel](./media/r-quickstart/unnamed-chunk-222.png)

![Derde van de diagnostische percelen voor het seizoensmodel](./media/r-quickstart/unnamed-chunk-223.png)

![Vierde van diagnostische percelen voor het seizoensmodel](./media/r-quickstart/unnamed-chunk-224.png)

*Figuur 26. Diagnostische percelen voor het seizoensmodel.*

Er zijn een paar zeer invloedrijke punten geïdentificeerd in deze percelen, maar niets om grote bezorgdheid te veroorzaken. Verder kunnen we zien aan de Normale Q-Q plot dat de restanten zijn dicht bij normaal verdeeld, een belangrijke veronderstelling voor lineaire modellen.

### <a name="forecasting-and-model-evaluation"></a>Prognoses en modelevaluatie

Er is nog een ding te doen om ons voorbeeld te voltooien. We moeten prognoses berekenen en de fout meten aan de hand van de werkelijke gegevens. Onze prognose zal zijn voor de 12 maanden van 2013. We kunnen een foutmeting voor deze prognose berekenen aan de werkelijke gegevens die geen deel uitmaken van onze trainingsgegevensset. Daarnaast kunnen we de prestaties op de 18 jaar trainingsgegevens vergelijken met de 12 maanden testgegevens.  

Een aantal statistieken worden gebruikt om de prestaties van tijdreeksmodellen te meten. In ons geval zullen we gebruik maken van de root mean square (RMS) fout. Met de volgende functie wordt de RMS-fout tussen twee reeksen berekend.  

```R
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Net als `log.transform()` bij de functie die we besproken in de "Waarde transformaties" sectie, is er heel veel fout controle en uitzondering herstelcode in deze functie. De gehanteerde principes zijn hetzelfde. Het werk wordt gedaan op `tryCatch()`twee plaatsen verpakt in . Ten eerste, de tijdreeksen zijn exponentiert, omdat we hebben gewerkt met de logs van de waarden. Ten tweede wordt de werkelijke RMS-fout berekend.  

Uitgerust met een functie om de RMS-fout te meten, laten we een gegevensframe met de RMS-fouten bouwen en uitvoer. We zullen termen voor het trendmodel alleen en het complete model met seizoensgebonden factoren. De volgende code doet het werk met behulp van de twee lineaire modellen die we hebben gebouwd.

```R
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Als u deze code uitvoert, wordt de uitvoer geproduceerd in figuur 27 op de uitvoerpoort van de resultatengegevensset.

![Vergelijking van RMS-fouten voor de modellen](./media/r-quickstart/fig26.png)

*Figuur 27. Vergelijking van RMS fouten voor de modellen.*

Uit deze resultaten zien we dat het toevoegen van de seizoensgebonden factoren aan het model de RMS-fout aanzienlijk vermindert. Niet zo verrassend, de RMS fout voor de opleiding gegevens is een beetje minder dan voor de prognose.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Gids voor RStudio-documentatie

RStudio is heel goed gedocumenteerd. Hier zijn enkele links naar de belangrijkste secties van de RStudio-documentatie om u op weg te helpen.

* **Projecten maken** - U uw R-code organiseren en beheren in projecten met RStudio. Zie [Projecten gebruiken](https://support.rstudio.com/hc/articles/200526207-Using-Projects) voor meer informatie. Ik raad u aan deze aanwijzingen te volgen en een project te maken voor de R-code voorbeelden in dit artikel.  
* **Het bewerken en uitvoeren van R-code** - RStudio biedt een geïntegreerde omgeving voor het bewerken en uitvoeren van R-code. Zie [Code bewerken en uitvoeren](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) voor meer informatie.
* **Debugging** - RStudio bevat krachtige debugging mogelijkheden. Zie [Foutopsporing met RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) voor meer informatie over deze functies. Zie Probleemoplossing voor breekpunten voor informatie over probleemoplossingsfuncties voor [breekpunten.](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)

## <a name="further-reading"></a><a id="appendixb"></a>Meer informatie

Deze r-programmeerzelfgaat om de basisprincipes van wat u nodig hebt om de R-taal te gebruiken met Azure Machine Learning Studio (klassiek). Als u niet bekend bent met R, zijn er twee introducties beschikbaar op CRAN:

* [R voor beginners](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) van Emmanuel Paradis is een goede plek om te beginnen.  
* [Een inleiding tot R](https://cran.r-project.org/doc/manuals/R-intro.html) door W. N. Venables e.a. Al. gaat in een beetje meer diepte.

Er zijn veel boeken over R die u kunnen helpen aan de slag. Hier zijn een paar vind ik nuttig:

* The **Art of R Programming: A Tour of Statistical Software Design van** Norman Matloff is een uitstekende introductie tot programmeren in R.  
* **R Kookboek** van Paul Teetor biedt een probleem en oplossing benadering van het gebruik van R.  
* **R in Actie** door Robert Kabacoff is een ander nuttig inleidend boek. De bijbehorende [Quick R-website](https://www.statmethods.net/) is een nuttige bron.
* **R Inferno** van Patrick Burns is een verrassend humoristisch boek dat zich bezighoudt met een aantal lastige en moeilijke onderwerpen die kunnen worden aangetroffen bij het programmeren in R. Het boek is gratis verkrijgbaar bij [The R Inferno.](https://www.burns-stat.com/documents/books/the-r-inferno/)
* Als u een diepe duik in geavanceerde onderwerpen in R wilt, neem een kijkje op het boek **Advanced R** van Hadley Wickham. De online versie van dit boek [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/)is gratis beschikbaar op .

Een catalogus met R-tijdreekspakketten is te vinden in [CRAN-taakweergave: time-serieanalyse.](https://cran.r-project.org/web/views/TimeSeries.html) Voor informatie over specifieke tijdreeksobjectpakketten verwijzen u naar de documentatie voor dat pakket.

Het boek **Introductory Time Series** with R van Paul Cowpertwait en Andrew Metcalfe biedt een inleiding tot het gebruik van R voor tijdserie analyse. Veel meer theoretische teksten geven R voorbeelden.

Hier zijn een aantal geweldige internet bronnen:

* DataCamp leert R in het comfort van uw browser met videolessen en codeeroefeningen. Er zijn interactieve tutorials over de nieuwste R technieken en pakketten. Neem de gratis [interactieve R tutorial](https://www.datacamp.com/courses/introduction-to-r).
* [Leer R Programmeren, de definitieve gids](https://www.programiz.com/r-programming) van Programiz.
* Een snelle [R Tutorial](https://www.cyclismo.org/tutorial/R/) door Kelly Black van Clarkson University.
* Er zijn meer dan 60 R-bronnen vermeld bij taalbronnen van [Top R om uw gegevensvaardigheden te verbeteren.](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html)

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
