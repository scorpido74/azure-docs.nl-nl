---
title: Aan de slag met R
titleSuffix: ML Studio (classic) - Azure
description: Gebruik deze zelf studie voor R-Program ma's om aan de slag te gaan met de R-taal met Azure Machine Learning Studio (klassiek) om een prognose oplossing te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 3972fb3c0717069f84b177c54e8fc002ec52f469
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152870"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Aan de slag met de programmeer taal R in Azure Machine Learning Studio (klassiek)

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Inleiding

Deze zelf studie helpt u bij het uitbreiden van Azure Machine Learning Studio (klassiek) met behulp van de programmeer taal R. Volg deze zelf studie over R-Program ma's om R-code te maken, te testen en uit te voeren in Studio (klassiek). Tijdens het werken met de zelf studie maakt u een volledige prognose oplossing met behulp van de R-taal in Studio (klassiek).  

Azure Machine Learning Studio (klassiek) bevat veel krachtige modules voor machine learning en gegevens manipulatie. De krachtige R-taal is beschreven als franca van Analytics. U kunt met behulp van R de analyse en gegevens manipulatie in Studio (klassiek) opwaarderen. Deze combi natie biedt de schaal baarheid en eenvoudigere implementatie van Studio (klassiek) met de flexibiliteit en diepe analyse van R.

### <a name="forecasting-and-the-dataset"></a>Prognoses en de gegevensset

Het maken van prognoses is een veelgebruikte en zeer nuttige analyse methode. Veelvoorkomende toepassingen variëren van het voors pellen van de verkoop van seizoensgebonden artikelen, het bepalen van optimale inventaris niveaus, voor het voors pellen van macro-variabelen. Prognoses worden doorgaans uitgevoerd met Time Series-modellen.

Time Series-gegevens zijn gegevens waarin de waarden een tijd index hebben. De tijd index kan normaal, bijvoorbeeld elke maand of elke minuut, of onregelmatig zijn. Een tijdreeks model is gebaseerd op Time Series-gegevens. De programmeer taal R bevat een flexibel Framework en uitgebreide analyses voor tijdreeks gegevens.

In deze hand leiding gaan we werken met productie-en prijs gegevens van Californië zuivel. Deze gegevens omvatten maandelijkse gegevens over de productie van verschillende zuivel producten en de prijs van melkvet, een benchmark product.

De gegevens die in dit artikel worden gebruikt, samen met R-scripts, kunnen worden gedownload van [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Gegevens in het bestand `cadairydata.csv` oorspronkelijk zijn gesynthesizerd op basis van de informatie die beschikbaar is via de University of Wisconsin op [https://dairymarkets.com](https://dairymarkets.com).

### <a name="organization"></a>Organisatie

Er worden verschillende stappen uitgevoerd voor het maken, testen en uitvoeren van analyse-en gegevens manipulatie R-code in de omgeving Azure Machine Learning Studio (klassiek).  

* Eerst gaan we de basis beginselen van het gebruik van de R-taal in de omgeving Azure Machine Learning Studio (klassiek) verkennen.
* Vervolgens wordt er geadviseerd om verschillende aspecten van I/O te bespreken voor gegevens, R-code en grafische afbeeldingen in de omgeving Azure Machine Learning Studio (klassiek).
* We bouwen nu het eerste deel van onze prognose oplossing door code te maken voor het opschonen van gegevens en trans formatie.
* Met onze gegevens die zijn voor bereid, voeren we een analyse uit van de correlaties tussen verschillende variabelen in onze gegevensset.
* Ten slotte gaan we een seizoen prognose model voor de productie van de melk maken.

## <a id="mlstudio"></a>Communiceren met R-taal in Machine Learning Studio (klassiek)

In deze sectie maakt u kennis met enkele basis principes van interactie met de programmeer taal R in de omgeving Machine Learning Studio (klassiek). De R-taal biedt een krachtig hulp programma voor het maken van aangepaste modules voor analyse en gegevens manipulatie in de omgeving Azure Machine Learning Studio (klassiek).

Ik gebruik RStudio om R-code op een kleine schaal te ontwikkelen, te testen en op te sporen. U kunt deze code vervolgens knippen en plakken in een module voor het [uitvoeren van R-scripts][execute-r-script] die kan worden uitgevoerd in azure machine learning Studio (klassiek).  

### <a name="the-execute-r-script-module"></a>De module R-script uitvoeren

In Machine Learning Studio (klassiek) worden R-scripts uitgevoerd in de module voor het uitvoeren van een [r-script][execute-r-script] . Een voor beeld van de module voor het [uitvoeren van R-scripts][execute-r-script] in machine learning Studio (klassiek) wordt weer gegeven in afbeelding 1.

 ![R-programmeer taal: de module voor het uitvoeren van R-scripts geselecteerd in Machine Learning Studio (klassiek)](./media/r-quickstart/fig1.png)

*Afbeelding 1. De Machine Learning Studio-omgeving (klassiek) waarin de module voor het uitvoeren van R-script is geselecteerd.*

Zie afbeelding 1 voor een overzicht van de belangrijkste onderdelen van de Machine Learning Studio (klassieke) omgeving voor het werken met de [script module Execute R][execute-r-script] .

* De modules in het experiment worden weer gegeven in het middelste deel venster.
* Het bovenste deel venster bevat een venster waarin u uw R-scripts kunt weer geven en bewerken.  
* Het onderste deel venster aan de rechter kant bevat enkele eigenschappen van het [R-script uitvoeren][execute-r-script]. U kunt de fout-en uitvoer logboeken weer geven door de juiste vlekken van dit deel venster te selecteren.

In de rest van dit artikel bespreken we natuurlijk het [Execute R-script][execute-r-script] in meer detail.

Wanneer u werkt met complexe R-functies, raden we u aan om te bewerken, testen en fouten op te sporen in RStudio. Net als bij elke ontwikkeling van software breidt u de code stapsgewijs uit en test u deze op kleine eenvoudige test cases. Knip en plak vervolgens uw functies in het R-Script venster van de module voor het uitvoeren van een [r-script][execute-r-script] . Met deze aanpak kunt u de RStudio-Integrated Development Environment (IDE) en de kracht van Azure Machine Learning Studio (klassiek) benutten.  

#### <a name="execute-r-code"></a>R-code uitvoeren

Alle R-code in de [script module Execute r][execute-r-script] wordt uitgevoerd wanneer u het experiment uitvoert door de knop **uitvoeren** te selecteren. Wanneer de uitvoering is voltooid, wordt er een vinkje weer gegeven op het pictogram voor het uitvoeren van een [R-script][execute-r-script] .

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Verdediging R-code ring voor Azure Machine Learning

Als u R-code ontwikkelt voor, bijvoorbeeld een webservice met behulp van Azure Machine Learning Studio (klassiek), moet u zeker plannen hoe uw code zal omgaan met een onverwachte gegevens invoer en uitzonde ringen. Als u de duidelijkheid wilt behouden, hebt u niet veel opgenomen in de manier waarop de controle of de uitzonde ring wordt verwerkt in de meeste code voorbeelden. We bieden echter verschillende voor beelden van functies door gebruik te maken van de functie voor het verwerken van uitzonde ringen.  

Als u een meer volledige behandeling van de verwerking van R-uitzonde ringen nodig hebt, raden we u aan om de toepasselijke secties van het boek te lezen in Wickham die hieronder worden vermeld in [verdere Lees bewerkingen](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Fouten opsporen en testen R in Machine Learning Studio (klassiek)

Als u wilt herhalen, raden we u aan om uw R-code te testen en fouten op een kleine schaal in RStudio op te lossen. Er zijn echter gevallen waarin u R-code problemen in het uitvoeren van het R- [script][execute-r-script] zelf moet volgen. Daarnaast is het raadzaam om de resultaten in Machine Learning Studio (klassiek) te controleren.

Uitvoer van de uitvoering van uw R-code en op het Azure Machine Learning Studio (klassiek)-platform wordt voornamelijk in output. log gevonden. Meer informatie wordt weer gegeven in fout. log.  

Als er een fout optreedt in Machine Learning Studio (klassiek) tijdens het uitvoeren van uw R-code, moet u eerst naar fout. log kijken. Dit bestand kan nuttige fout berichten bevatten om u te helpen uw fout te begrijpen en te verhelpen. Als u fout. log wilt weer geven, selecteert u **fout logboek weer geven** in het **deel venster Eigenschappen** voor het [script uitvoeren][execute-r-script] dat de fout bevat.

Bijvoorbeeld: Ik heb de volgende R-code uitgevoerd, met een niet-gedefinieerde variabele y, in een script module voor uitvoeren van een [r][execute-r-script] .

```R
x <- 1.0
z <- x + y
```

Deze code kan niet worden uitgevoerd, wat resulteert in een fout. Als u het **fouten logboek weer geven** in het **deel venster Eigenschappen** selecteert, wordt de weer gave in afbeelding 2 weer gegeven.

  ![Pop-upvenster voor fout berichten](./media/r-quickstart/fig2.png)

*Afbeelding 2. Pop-upvenster met fout berichten.*

Het lijkt erop dat er in output. log moet worden gezocht om het R-fout bericht te zien. Selecteer het [script Execute R][execute-r-script] en selecteer vervolgens het item **uitvoer weer geven. logboek** in het **deel venster Eigenschappen** aan de rechter kant. Er wordt een nieuw browser venster geopend en het volgende wordt weer gegeven.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Dit fout bericht bevat geen verrassingen en identificeert het probleem duidelijk.

Als u de waarde van een object in R wilt controleren, kunt u deze waarden afdrukken naar het bestand output. log. De regels voor het onderzoeken van object waarden zijn in wezen hetzelfde als in een interactieve R-sessie. Als u bijvoorbeeld de naam van een variabele op een regel typt, wordt de waarde van het object afgedrukt naar het bestand output. log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Pakketten in Machine Learning Studio (klassiek)

Studio wordt geleverd met meer dan 350 vooraf geïnstalleerde R-taal pakketten. U kunt de volgende code in de module voor het [uitvoeren van R-scripts][execute-r-script] gebruiken om een lijst met vooraf geïnstalleerde pakketten op te halen.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Lees de informatie over als u de laatste regel van deze code op dit moment niet begrijpt. In de rest van dit artikel wordt uitgebreide informatie over het gebruik van R in de studio-omgeving (klassiek) besproken.

### <a name="introduction-to-rstudio"></a>Inleiding tot RStudio

RStudio is een veel gebruikte IDE voor R. Ik gebruik RStudio voor het bewerken, testen en opsporen van fouten in een deel van de R-code die in deze hand leiding wordt gebruikt. Zodra de R-code is getest en klaar is, kunt u eenvoudig knippen en plakken vanuit de RStudio-editor naar een klassieke [R-script][execute-r-script] module voor het uitvoeren van een machine learning Studio.  

Als u de R-programmeer taal niet op uw computer hebt geïnstalleerd, raden we u aan om dit nu te doen. Gratis down loads van de open source R-taal zijn beschikbaar op het uitgebreide R-archief netwerk (KRANen) op [https://www.r-project.org/](https://www.r-project.org/). Er zijn down loads beschikbaar voor Windows, Mac OS en Linux/UNIX. Kies een nabijgelegen mirror en volg de download instructies. Daarnaast bevat KRANen een schat aan nuttige analyse-en gegevens manipulatie-pakketten.

Als u niet bekend bent met RStudio, moet u de desktop versie downloaden en installeren. U kunt de RStudio-down loads voor Windows, Mac OS en Linux/UNIX vinden op http://www.rstudio.com/products/RStudio/. Volg de instructies voor het installeren van RStudio op uw desktop computer.  

Een zelf studie Inleiding tot RStudio is beschikbaar op [het gebruik van de RSTUDIO IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Hieronder vindt u aanvullende informatie over het gebruik van RStudio in de [hand leiding voor RStudio-documentatie](#appendixa) hieronder.  

## <a id="scriptmodule"></a>Gegevens ophalen uit en uit de module voor het uitvoeren van R-scripts

In deze sectie wordt uitgelegd hoe u gegevens in en uit de module voor het [uitvoeren van R-script][execute-r-script] kunt ophalen. We bekijken hoe u verschillende gegevens typen kunt verwerken die in en uit de module voor het uitvoeren van een [R-script][execute-r-script] worden gelezen.

De volledige code voor deze sectie bevindt zich in [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Gegevens in Machine Learning Studio laden en controleren (klassiek)

#### <a id="loading"></a>De gegevensset laden

We gaan eerst het bestand **csdairydata. CSV** laden in azure machine learning Studio (klassiek).

1. Start uw Azure Machine Learning Studio-omgeving (klassiek).
1. Selecteer **+ Nieuw** linksonder in het scherm en selecteer **gegevensset**.
1. Selecteer een **lokaal bestand**en klik vervolgens op **Bladeren** om het bestand te selecteren.
1. Zorg ervoor dat u het **algemene CSV-bestand met de header (. CSV)** hebt geselecteerd als het type voor de gegevensset.
1. Schakel het selectie vakje in.
1. Nadat de gegevensset is geüpload, ziet u de nieuwe gegevensset door het tabblad **gegevens sets** te selecteren.  

#### <a name="create-an-experiment"></a>Een experiment maken

Nu we een aantal gegevens hebben in Machine Learning Studio (klassiek), moeten we een experiment maken om de analyse uit te voeren.  

1. Selecteer **+ Nieuw** linksonder en selecteer **experiment**en vervolgens **leeg experiment**.
1. U kunt uw experiment een naam bieden door te selecteren en te wijzigen, het **experiment gemaakt op...** titel boven aan de pagina. U kunt deze bijvoorbeeld wijzigen in **CA-zuivel analyse**.
1. Vouw aan de linkerkant van de pagina experiment **opgeslagen gegevens sets**en vervolgens **mijn gegevens sets**uit. U ziet het **cadairydata. CSV** -bestand dat u eerder hebt geüpload.
1. Sleep de **gegevensset csdairydata. CSV** naar het experiment en zet deze neer.
1. Typ in het vak **Zoek proef items** boven in het linkerdeel venster de opdracht [R-script uitvoeren][execute-r-script]. U ziet dat de module wordt weer gegeven in de lijst zoeken.
1. Sleep de module voor het [uitvoeren van R-scripts][execute-r-script] naar uw pallet.  
1. Verbind de uitvoer van de **csdairydata. CSV-gegevensset** naar de meest linkse invoer (**Dataset1**) van het [script Execute R][execute-r-script].
1. **Vergeet niet om ' opslaan ' te selecteren.**  

Op dit moment moet het experiment er ongeveer uitzien als afbeelding 3.

![Het analyse experiment van de CA-melk met de module gegevensset en execute R-script](./media/r-quickstart/fig3.png)

*Afbeelding 3. Het analyse experiment van de CA-melk met de module gegevensset en execute R-script.*

#### <a name="check-on-the-data"></a>De gegevens controleren

Laten we eens kijken naar de gegevens die we in ons experiment hebben geladen. In het experiment selecteert u de uitvoer van de **gegevensset cadairydata. CSV** en selecteert u **visualiseren**. U ziet iets als afbeelding 4.  

![Samen vatting van de cadairydata. CSV-gegevensset](./media/r-quickstart/fig4.png)

*Afbeelding 4. Samen vatting van de cadairydata. CSV-gegevensset.*

In deze weer gave zien we veel nuttige informatie. We kunnen de eerste verschillende rijen van die gegevensset zien. Als we een kolom selecteren, ziet u in de sectie statistieken meer informatie over de kolom. De rij functie type laat bijvoorbeeld zien welke gegevens typen Azure Machine Learning Studio (klassiek) aan de kolom zijn toegewezen. Een kort overzicht is een goede Sanity controle voordat we een ernstige hoeveelheid werk gaan uitvoeren.

### <a name="first-r-script"></a>Eerste R-script

Laten we een eenvoudig eerste R-script maken om te experimenteren binnen Azure Machine Learning Studio (klassiek). Ik heb het volgende script in RStudio gemaakt en getest.  

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

Ik moet dit script nu overdragen aan Azure Machine Learning Studio (klassiek). Ik kan eenvoudigweg knippen en plakken. In dit geval brengt ik echter mijn R-script over via een zip-bestand.

### <a name="data-input-to-the-execute-r-script-module"></a>Gegevens invoer van de module voor het uitvoeren van R-scripts

Laten we eens kijken naar de invoer voor de module [R-script uitvoeren][execute-r-script] . In dit voor beeld lezen we de gegevens van Californië zuivel in de module voor het [uitvoeren van R-scripts][execute-r-script] .  

Er zijn drie mogelijke invoer voor de module voor het uitvoeren van een [R-script][execute-r-script] . U kunt een of meer van deze invoer gebruiken, afhankelijk van uw toepassing. Het is ook zeer redelijk om een R-script te gebruiken dat helemaal geen invoer in beslag neemt.  

We gaan elk van deze invoer eens bekijken, van links naar rechts. U kunt de namen van elk van de invoer zien door de cursor over de invoer te plaatsen en de knop info te lezen.  

#### <a name="script-bundle"></a>Script bundel

Met de invoer van de script bundel kunt u de inhoud van een zip-bestand door geven in de module voor het uitvoeren van een [R-script][execute-r-script] . U kunt een van de volgende opdrachten gebruiken om de inhoud van het zip-bestand te lezen in de R-code.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (klassiek) behandelt bestanden in de post alsof ze zich in de map src/directory bevinden, dus u moet uw bestands namen met deze naam van de map voor voegsel. Als de zip bijvoorbeeld de bestanden `yourfile.R` en `yourData.rdata` in de hoofdmap van de zip bevat, kunt u deze als `src/yourfile.R` en `src/yourData.rdata` bij het gebruik van `source` en `load`.

[De gegevensset voor het](#loading)laden van gegevens sets is al besproken. Als u het R-script hebt gemaakt en getest dat in de vorige sectie wordt weer gegeven, gaat u als volgt te werk:

1. Sla het R-script op in een. R-bestand. Ik bel mijn script bestand "simpleplot. R. Dit is de inhoud.

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

1. Maak een zip-bestand en kopieer uw script naar dit zip-bestand. In Windows kunt u met de rechter muisknop op het bestand klikken en **verzenden naar**en **gecomprimeerde map**selecteren. Hiermee maakt u een nieuw zip-bestand met de ' simpleplot. R-bestand.

1. Voeg uw bestand toe aan de **gegevens sets** in azure machine learning Studio (klassiek), waarbij u het type als **zip**opgeeft. U ziet nu het zip-bestand in de gegevens sets.

1. Sleep het zip-bestand van **gegevens sets** naar het **ml Studio (klassiek)-canvas**.

1. Koppel de uitvoer van het pictogram **zip-gegevens** aan de invoer van de **script bundel** van de module voor het uitvoeren van een [R-script][execute-r-script] .

1. Typ de `source()` functie met de naam van het zip-bestand in het code venster voor de module voor het uitvoeren van een [R-script][execute-r-script] . Ik heb in mijn geval getypt `source("src/simpleplot.R")`.  

1. Zorg ervoor dat u **Opslaan**selecteert.

Zodra deze stappen zijn voltooid, wordt [het r][execute-r-script] -script in het zip-bestand uitgevoerd wanneer het experiment wordt uitgevoerd. Op dit moment moet het experiment er ongeveer als volgt uitzien, zoals in afbeelding 5.

![Experimenteren met het gebruik van een gezipt R-script](./media/r-quickstart/fig6.png)

*Afbeelding 5. Experimenteer met het gezipte R-script.*

#### <a name="dataset1"></a>Dataset1

U kunt een rechthoekige tabel met gegevens door geven aan uw R-code met behulp van de Dataset1-invoer. In ons eenvoudige script wordt de functie `maml.mapInputPort(1)` de gegevens van poort 1 gelezen. Deze gegevens worden vervolgens toegewezen aan de naam van een data frame-variabele in uw code. In ons eenvoudige script voert de eerste regel code de toewijzing uit.

```R
cadairydata <- maml.mapInputPort(1)
```

Voer uw experiment uit door de knop **uitvoeren** te selecteren. Wanneer de uitvoering is voltooid, selecteert u de module [R-script uitvoeren][execute-r-script] en selecteert u vervolgens **uitvoer logboek weer geven** in het deel venster Eigenschappen. Er wordt een nieuwe pagina weer gegeven in de browser die de inhoud van het bestand output. log weergeeft. Als u naar beneden schuift, ziet u iets zoals in het volgende.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Verder is de pagina meer gedetailleerde informatie over de kolommen. deze ziet er ongeveer als volgt uit.

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

Deze resultaten zijn voornamelijk zoals verwacht, met 228 waarnemingen en 9 kolommen in de data frame. De kolom namen, het gegevens Type R en een voor beeld van elke kolom worden weer gegeven.

> [!NOTE]
> Dezelfde gedrukte uitvoer is eenvoudig beschikbaar vanuit de R-uitvoer van de module voor het uitvoeren van een [r-script][execute-r-script] . De uitvoer van de module voor het [uitvoeren van R-script][execute-r-script] wordt in de volgende sectie besproken.  

#### <a name="dataset2"></a>Dataset2

Het gedrag van de Dataset2-invoer is gelijk aan die van Dataset1. Met deze invoer kunt u een tweede rechthoekige tabel met gegevens door geven aan uw R-code. De functie `maml.mapInputPort(2)`, met het argument 2, wordt gebruikt om deze gegevens door te geven.  

### <a name="execute-r-script-outputs"></a>Uitvoer van R-script uitvoeren

#### <a name="output-a-dataframe"></a>Een data frame uitvoeren

U kunt de inhoud van een R-data frame uitvoeren als een rechthoekige tabel via de Dataset1-poort van het resultaat met behulp van de functie `maml.mapOutputPort()`. In ons eenvoudige R-script wordt dit uitgevoerd door de volgende regel.

```
maml.mapOutputPort('cadairydata')
```

Na het uitvoeren van het experiment selecteert u het resultaat Dataset1 uitvoer poort en selecteert u vervolgens **visualiseren**. U ziet iets als afbeelding 6.

![De visualisatie van de uitvoer van de California zuivel gegevens](./media/r-quickstart/fig7.png)

*Afbeelding 6. De visualisatie van de uitvoer van de California zuivel-gegevens.*

Deze uitvoer ziet er precies zo uit als verwacht.  

### <a name="r-device-output"></a>R-apparaat-uitvoer

De uitvoer van het apparaat van de [script module Execute R][execute-r-script] bevat berichten en grafische uitvoer. Standaard uitvoer en standaard fout berichten van R worden verzonden naar de uitvoer poort van het R-apparaat.  

Als u de uitvoer van het R-apparaat wilt weer geven, selecteert u de poort en vervolgens op **visualiseren**. We zien de standaard uitvoer en de standaard fout van het R-script in afbeelding 7.

![Standaard uitvoer en standaard fout van de R-poort van apparaat](./media/r-quickstart/fig8.png)

*Afbeelding 7. De standaard uitvoer en de standaard fout van de R-poort van het apparaat.*

Schuif omlaag er wordt een grafische uitvoer van het R-script in afbeelding 8 weer geven.  

![Grafische uitvoer van de R-poort van apparaat](./media/r-quickstart/fig9.png)

*Afbeelding 8. Grafische uitvoer van de R-poort van het apparaat.*  

## <a id="filtering"></a>Gegevens filteren en trans formatie

In deze sectie worden enkele basis bewerkingen voor gegevens filtering en-trans formatie uitgevoerd voor de California zuivel-gegevens. Aan het einde van deze sectie hebben we gegevens in een indeling die geschikt is voor het bouwen van een analytisch model.  

In deze sectie worden echter verschillende algemene taken voor het opschonen van gegevens en trans formatie uitgevoerd: type trans formatie, filteren op dataframes, toevoegen van nieuwe berekende kolommen en waarde-trans formaties. Deze achtergrond zou u kunnen helpen bij het oplossen van de vele variaties die in praktijk problemen optreden.

De volledige R-code voor deze sectie is beschikbaar in [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Type trans formaties

Nu we de California zuivel-gegevens kunnen lezen in de R-code in de script module voor het [uitvoeren van r][execute-r-script] , moeten we ervoor zorgen dat de gegevens in de kolommen het beoogde type en de juiste indeling hebben.  

R is een dynamisch getypeerde taal, wat betekent dat de gegevens typen zo nodig naar een andere worden gedwongen. De atomische gegevens typen in R bevatten numeriek, logisch en teken. Het type factor wordt gebruikt om categorische-gegevens op te slaan. Meer informatie over gegevens typen vindt u in de verwijzingen in [meer Lees](#appendixb) hieronder.

Wanneer tabellaire gegevens in R van een externe bron worden gelezen, is het altijd een goed idee om de resulterende typen in de kolommen te controleren. Mogelijk wilt u een kolom van het type teken, maar in veel gevallen wordt dit weer gegeven als factor of andersom. In andere gevallen moet een kolom die u beschouwt numeriek zijn door teken gegevens, bijvoorbeeld ' 1,23 ' in plaats van 1,23 als een getal met drijvende komma.  

Gelukkig is het eenvoudig om het ene type naar het andere te converteren, zolang de toewijzing mogelijk is. U kunt bijvoorbeeld ' Nevada ' niet converteren naar een numerieke waarde, maar deze converteren naar een factor (categorische-variabele). Een ander voor beeld: u kunt een numerieke waarde 1 omzetten in een teken ' 1 ' of een factor.  

De syntaxis voor deze conversies is eenvoudig: `as.datatype()`. Deze functies voor type conversie zijn onder andere de volgende.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Bekijk de gegevens typen van de kolommen die we in de vorige sectie hebben ingevoerd: alle kolommen zijn van het type Numeriek, met uitzonde ring van de kolom met het label month, die van het type teken is. Laten we dit omzetten naar een factor en de resultaten testen.  

Ik heb de regel voor het maken van de scatterplot-matrix verwijderd en een regel toegevoegd die de kolom month omzet in een factor. In mijn experiment knipt u gewoon de R-code en plakt u deze in het code venster van de module voor het uitvoeren van een [R-script][execute-r-script] . U kunt het zip-bestand ook bijwerken en het uploaden naar Azure Machine Learning Studio (klassiek), maar dit heeft een aantal stappen.  

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

We gaan deze code uitvoeren en kijken naar het uitvoer logboek voor het R-script. De relevante gegevens uit het logboek worden weer gegeven in afbeelding 9.

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

*Afbeelding 9. Samen vatting van de data frame met een factor variabele.*

Het type voor maand moet nu '**factor w/14 niveaus**' uitspreken. Dit is een probleem omdat er slechts 12 maanden in het jaar zijn. U kunt ook controleren of het type in **visualiseren** van de**categorische**van de resultaat gegevensset is.

Het probleem is dat de kolom month niet systematisch is gecodeerd. In sommige gevallen wordt een maand april genoemd en wordt deze afgekort tot apr. We kunnen dit probleem oplossen door de teken reeks te verkleinen tot drie tekens. De regel code ziet er nu als volgt uit:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Voer het experiment opnieuw uit en Bekijk het uitvoer logboek. De verwachte resultaten worden weer gegeven in afbeelding 10.  

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

*Afbeelding 10. Samen vatting van de data frame met het juiste aantal factor niveaus.*

Onze factor variabele heeft nu de gewenste 12 niveaus.

### <a name="basic-data-frame-filtering"></a>Basis gegevens frames filteren

R dataframes ondersteunen krachtige filter mogelijkheden. Gegevens sets kunnen worden subsetted met behulp van logische filters voor rijen en kolommen. In veel gevallen is het complexe filter criterium vereist. De verwijzingen in [verdere Lees bewerkingen](#appendixb) bevatten uitgebreide voor beelden van het filteren van dataframes.  

Er zijn twee filters die in onze gegevensset moeten worden uitgevoerd. Als u de kolommen in de cadairydata-data frame bekijkt, worden er twee overbodige kolommen weer geven. De eerste kolom bevat alleen een rijnummer. Dit is niet erg nuttig. De tweede kolom, jaar. maand, bevat redundante informatie. We kunnen deze kolommen eenvoudig uitsluiten met behulp van de volgende R-code.

> [!NOTE]
> Vanaf nu in deze sectie ziet u alleen de extra code die ik toevoeg in de module voor het uitvoeren van een [R-script][execute-r-script] . Ik voeg elke nieuwe regel toe **voor** de functie `str()`. Ik gebruik deze functie om mijn resultaten in Azure Machine Learning Studio (klassiek) te controleren.

Ik voeg de volgende regel toe aan mijn R-code in de module voor het uitvoeren van een [r-script][execute-r-script] .

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Voer deze code uit in uw experiment en controleer het resultaat van het uitvoer logboek. Deze resultaten worden weer gegeven in afbeelding 11.

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

*Afbeelding 11. Samen vatting van de data frame met twee kolommen verwijderd.*

Goed nieuws. De verwachte resultaten worden opgehaald.

### <a name="add-a-new-column"></a>Een nieuwe kolom toevoegen

Als u een time series-model wilt maken, is het handig om een kolom te hebben die de maanden sinds het begin van de tijd reeks bevat. Er wordt een nieuwe kolom month. Count gemaakt.

Om de code te organiseren, maken we onze eerste eenvoudige functie `num.month()`. Vervolgens worden deze functies toegepast om een nieuwe kolom in de data frame te maken. De nieuwe code is als volgt.

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

Voer nu het bijgewerkte experiment uit en gebruik het uitvoer logboek om de resultaten weer te geven. Deze resultaten worden weer gegeven in afbeelding 12.

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

*Afbeelding 12. Samen vatting van de data frame met de aanvullende kolom.*

Het lijkt alsof alles werkt. We hebben de nieuwe kolom met de verwachte waarden in onze data frame.

### <a name="value-transformations"></a>Waarde-trans formaties

In deze sectie worden enkele eenvoudige trans formaties uitgevoerd op de waarden in een aantal van de kolommen van onze data frame. De R-taal ondersteunt bijna wille keurige waarden trans formaties. De verwijzingen in [verdere Lees bewerkingen](#appendixb) bevatten uitgebreide voor beelden.

Als u de waarden in de samen vattingen van onze data frame bekijkt, moet u hier iets oneven kijken. Is er meer ijs dan in Californië geproduceerde melk? Nee, uiteraard niet, als dit niet zinvol is, is het mogelijk dat er een deel van de Amerikaanse Ice room-Lovers is. De eenheden verschillen. De prijs is in eenheden van Amerikaanse ponden, melk in eenheden van 1 M VS pond, ijs is in eenheden van 1.000 VS gallons en huis kaas is in eenheden van 1.000 VS pond. Aangenomen dat ijs ongeveer 6,5 pond per gallon weegt, kunnen we de vermenigvuldiging eenvoudig uitvoeren om deze waarden te converteren zodat ze allemaal in gelijke eenheden van 1.000 Euro's liggen.

Voor ons prognose model gebruiken we een multiplicative-model voor trend en seizoen aanpassing van deze gegevens. Met een logboek transformatie kan ons een lineair model gebruiken, waardoor dit proces wordt vereenvoudigd. We kunnen de logboek transformatie Toep assen in dezelfde functie als de vermenigvuldiger wordt toegepast.

In de volgende code definieert u een nieuwe functie, `log.transform()`en past u deze toe op de rijen met de numerieke waarden. De functie R `Map()` wordt gebruikt om de functie `log.transform()` toe te passen op de geselecteerde kolommen van de data frame. `Map()` is vergelijkbaar met `apply()`, maar biedt meer dan een lijst met argumenten voor de functie. Houd er rekening mee dat een lijst met vermenigvuldigers het tweede argument levert aan de functie `log.transform()`. De functie `na.omit()` wordt gebruikt als een bit van opschoning om ervoor te zorgen dat er geen ontbrekende of niet-gedefinieerde waarden in de data frame zijn.

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

Er is een zeer beetje in de `log.transform()`-functie. Met de meeste van deze code wordt gecontroleerd op mogelijke problemen met de argumenten of het verwerken van uitzonde ringen. deze kunnen zich nog steeds voordoen tijdens de berekeningen. Slechts een paar regels van deze code voeren feitelijk de berekeningen uit.

Het doel van de verdedigings programmering is om te voor komen dat er een storing optreedt in een enkele functie waardoor de verwerking niet kan worden voortgezet. Een plotselinge uitval van een langlopende analyse kan heel frustrerend zijn voor gebruikers. Om deze situatie te voor komen, moeten er standaard retour waarden worden gekozen, waardoor de schade wordt beperkt tot downstream-verwerking. Er wordt ook een bericht gegenereerd om gebruikers te waarschuwen dat er iets verkeerd is gegaan.

Als u niet wordt gebruikt om een verdedigings programma in R te maken, is het mogelijk dat al deze code een beetje overweldigend lijkt. Ik begeleid u door de belangrijkste stappen:

1. Er wordt een vector van vier berichten gedefinieerd. Deze berichten worden gebruikt voor het communiceren van informatie over een aantal van de mogelijke fouten en uitzonde ringen die kunnen optreden met deze code.
2. Ik retour neer de waarde N.V.T. voor elke aanvraag. Er zijn veel andere mogelijkheden die mogelijk minder neven effecten hebben. Ik kan bijvoorbeeld een vector van nullen of de oorspronkelijke invoer vector retour neren.
3. Controles worden uitgevoerd op de argumenten voor de functie. Als er een fout wordt gedetecteerd, wordt er in elk geval een standaard waarde geretourneerd en wordt er een bericht gegenereerd door de functie `warning()`. Ik gebruik `warning()` in plaats van `stop()` omdat de laatste bewerking wordt beëindigd, precies wat ik probeer te voor komen. Houd er rekening mee dat de code in een procedure stijl is geschreven, zoals in dit geval een functionele benadering gecompliceerd en onzichtbaar is.
4. De logboek berekeningen worden in `tryCatch()` verpakt, zodat uitzonde ringen geen plotselinge onderbreking van de verwerking veroorzaken. Zonder `tryCatch()` de meeste fouten die zijn opgetreden door R-functies, resulteert dit in een stop signaal. Dit betekent alleen dat.

Voer deze R-code uit in uw experiment en Bekijk de gedrukte uitvoer in het bestand output. log. U ziet nu de getransformeerde waarden van de vier kolommen in het logboek, zoals wordt weer gegeven in afbeelding 13.

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

*Afbeelding 13. Samen vatting van de getransformeerde waarden in de data frame.*

We zien dat de waarden zijn getransformeerd. Zuivel productie overschrijdt nu de productie van zuivel producten, waardoor we nu een logaritmische schaal bekijken.

Onze gegevens worden op dit moment opgeruimd en we zijn klaar voor een model lering. Op basis van de visualisatie samenvatting van de uitvoer van de [script module Execute R][execute-r-script] wordt de kolom month ' categorische ' weer gegeven met 12 unieke waarden, net zoals u dat wilt.

## <a id="timeseries"></a>Tijd reeks objecten en correlatie analyse

In deze sectie gaan we een paar Basic R Time Series-objecten verkennen en de correlaties tussen sommige variabelen analyseren. Ons doel is om een data frame met de informatie over de Pairwise-correlatie op verschillende lags uit te voeren.

De volledige R-code voor deze sectie bevindt zich in [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Time Series-objecten in R

Zoals al vermeld, is de tijd reeks een reeks gegevens waarden die op tijd worden geïndexeerd. R-tijd reeks objecten worden gebruikt voor het maken en beheren van de time-index. Er zijn verschillende voor delen voor het gebruik van tijd reeks-objecten. Met tijdreeks objecten kunt u veel meer informatie over het beheren van de time series-index waarden die zijn ingekapseld in het object. Daarnaast kunt u met Time Series-objecten gebruikmaken van de vele tijdreeks methoden voor het uitzetten, afdrukken, model leren, enzovoort.

De POSIXct-klasse van de tijd reeks wordt meestal gebruikt en is relatief eenvoudig. Deze tijd reeks klasse meet tijd vanaf het begin van de epoche, 1 januari 1970. We gebruiken POSIXct Time Series-objecten in dit voor beeld. Andere veelgebruikte R Time Series-object klassen zijn onder meer Zoo en XTS, uitbreid bare tijd reeksen.

### <a name="time-series-object-example"></a>Voor beeld van een time Series-object

Laten we aan de slag met ons voor beeld. Sleep een **nieuwe** module voor het [uitvoeren van R-scripts][execute-r-script] naar uw experiment. Verbind het resultaat Dataset1 uitvoer poort van de bestaande [Execute r-script][execute-r-script] module naar de Dataset1-invoer poort van de nieuwe module voor het uitvoeren van een [r-script][execute-r-script] .

Tijdens de eerste voor beelden werd tijdens het uitvoeren van het voor beeld in sommige punten alleen de incrementele extra regels van de R-code weer gegeven bij elke stap.  

#### <a name="reading-the-dataframe"></a>De data frame lezen

Als eerste stap lezen we in een data frame en zorgen we ervoor dat de verwachte resultaten worden opgehaald. Voer de volgende code uit om de taak uit te voeren.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Voer nu het experiment uit. Het logboek van de nieuwe shape voor het uitvoeren R-script moet eruitzien als afbeelding 14.

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

*Afbeelding 14. Samen vatting van de data frame in de script module Execute R.*

Deze gegevens zijn van de verwachte typen en notatie. Houd er rekening mee dat de kolom ' month ' van het type factor is en het verwachte aantal niveaus heeft.

#### <a name="creating-a-time-series-object"></a>Een time Series-object maken

We moeten een tijd reeks object toevoegen aan onze data frame. Vervang de huidige code door het volgende, waarmee een nieuwe kolom van klasse POSIXct wordt toegevoegd.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Controleer nu het logboek. Deze moet eruitzien als afbeelding 15.

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

*Afbeelding 15. Samen vatting van de data frame met een time Series-object.*

In de samen vatting van de nieuwe kolom is de klasse POSIXct te zien.

### <a name="exploring-and-transforming-the-data"></a>De gegevens verkennen en transformeren

Laten we een aantal variabelen in deze gegevensset verkennen. Een scatterplot-matrix is een goede manier om een snel uiterlijk te maken. Ik vervang de `str()` functie in de vorige R-code door de volgende regel.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Voer deze code uit en Bekijk wat er gebeurt. Het waarnemings punt dat is geproduceerd op de poort R-apparaat moet eruitzien als afbeelding 16.

![Scatterplot matrix met geselecteerde variabelen](./media/r-quickstart/fig17.png)

*Afbeelding 16. Scatterplot matrix van geselecteerde variabelen.*

De relaties tussen deze variabelen hebben een enkele onevene structuur. Misschien is dit het gevolg van trends in de gegevens en van het feit dat we de variabelen niet hebben gestandaardiseerd.

### <a name="correlation-analysis"></a>Correlatie analyse

Om correlatie-analyses uit te voeren, moeten zowel de trend worden genormaliseerd als de variabelen worden gestandaardiseerd. We kunnen gewoon de functie R `scale()` gebruiken, waarmee variabelen worden gecentreerd en geschaald. Deze functie kan sneller worden uitgevoerd. Ik wil echter een voor beeld zien van een verdedigings programma in R.

Met de functie `ts.detrend()` die hieronder wordt weer gegeven, worden beide bewerkingen uitgevoerd. De volgende twee regels code detrenden de gegevens en vervolgens worden de waarden genormaliseerd.

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

Er is een zeer beetje in de `ts.detrend()`-functie. Met de meeste van deze code wordt gecontroleerd op mogelijke problemen met de argumenten of het verwerken van uitzonde ringen. deze kunnen zich nog steeds voordoen tijdens de berekeningen. Slechts een paar regels van deze code voeren feitelijk de berekeningen uit.

We hebben al een voor beeld van een verdedigings programma in waarde-trans formaties besproken. Beide reken blokken worden in `tryCatch()`verpakt. Voor sommige fouten is het zinvol om de oorspronkelijke invoer vector te retour neren, en in andere gevallen wordt er een vector van nullen geretourneerd.  

Houd er rekening mee dat de lineaire regressie die wordt gebruikt voor de trending een tijd reeks regressie is. De Voorspellings variabele is een time Series-object.  

Zodra `ts.detrend()` is gedefinieerd, worden deze toegepast op de variabelen van belang in onze data frame. We moeten de resulterende lijst die is gemaakt door `lapply()` op gegevens data frame, afdwingen met behulp van `as.data.frame()`. Vanwege de verdedigings aspecten van `ts.detrend()`kan het verwerken van een van de variabelen niet de juiste verwerking van de andere waarden verhinderen.  

De laatste regel code maakt een Pairwise-scatterplot. Na het uitvoeren van de R-code worden de resultaten van de scatterplot weer gegeven in afbeelding 17.

![Pairwise scatterplot van de getrendde en gestandaardiseerde time series](./media/r-quickstart/fig18.png)

*Afbeelding 17. Pairwise scatterplot van de getrendde en gestandaardiseerde tijd reeks.*

U kunt deze resultaten vergelijken met de waarden die worden weer gegeven in afbeelding 16. Als de trend is verwijderd en de variabelen zijn genormaliseerd, zien we een veel minder structuur in de relaties tussen deze variabelen.

De code voor het berekenen van de correlaties als R CCF-objecten is als volgt.

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

Als deze code wordt uitgevoerd, wordt het logboek gegenereerd dat wordt weer gegeven in afbeelding 18.

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

*Afbeelding 18. Lijst met CCF-objecten uit de Pairwise-correlatie analyse.*

Er is een correlatie waarde voor elke vertraging. Geen van deze correlatie waarden is groot genoeg om significant te zijn. Daarom kunnen we sluiten dat elke variabele onafhankelijk kan worden model leren.

### <a name="output-a-dataframe"></a>Een data frame uitvoeren
We hebben de Pairwise-correlaties berekend als een lijst met R CCF-objecten. Dit geeft een deel van een probleem als resultaat van de uitvoer poort van de resulterende gegevensset is een data frame vereist. Verder is het CCF-object een lijst en we willen alleen de waarden in het eerste element van deze lijst, de correlaties op de verschillende lags.

Met de volgende code worden de vertragings waarden geëxtraheerd uit de lijst met CCF-objecten, die zelf worden weer gegeven.

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

De eerste regel code is een beetje truc en enkele uitleg kan u helpen bij het begrijpen. Als u aan de slag gaat, hebt u het volgende nodig:

1. De operator ' **[[** ' met het argument '**1**' selecteert de vector van correlaties in de lags van het eerste element van de CCF-object lijst.
2. De functie `do.call()` past de `rbind()` functie toe op de elementen van de lijst die door `lapply()`worden geretourneerd.
3. Met de functie `data.frame()` wordt het resultaat dat door `do.call()` naar een data frame is geproduceerd, afgedwongen.

Houd er rekening mee dat de namen van de rijen in een kolom van de data frame staan. Hierbij blijven de rijnaam behouden wanneer deze worden uitgevoerd vanuit het [script Execute R][execute-r-script].

Als de code wordt uitgevoerd, wordt de uitvoer weer gegeven in afbeelding 19 wanneer de uitvoer wordt **gevisualiseerd** op basis van de poort van het resultaat van de gegevensset. De namen van de rijen bevinden zich in de eerste kolom, zoals bedoeld.

![Resultaten uitvoer van de correlatie analyse](./media/r-quickstart/fig20.png)

*Afbeelding 19. Resultaten uitvoer van de correlatie analyse.*

## <a id="seasonalforecasting"></a>Voor beeld van een tijd reeks: seizoen prognose

Onze gegevens bevinden zich nu in een formulier dat geschikt is voor analyse en we hebben vastgesteld dat er geen aanzienlijke correlaties tussen de variabelen zijn. We gaan nu een tijd reeks prognose model maken. Met behulp van dit model zullen we de melk productie van Californië voor de 12 maanden van 2013.

Het prognose model bevat twee onderdelen, een trend component en een seizoen component. De volledige prognose is het product van deze twee onderdelen. Dit type model wordt een multiplicative-model genoemd. Het alternatief is een additief model. We hebben al een logboek transformatie toegepast op de variabelen van interest, waardoor deze analyse kan worden ondertrekt.

De volledige R-code voor deze sectie bevindt zich in [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>De data frame maken voor analyse

Voeg eerst een **nieuwe** module voor het [uitvoeren van R-scripts][execute-r-script] toe aan uw experiment. Koppel de uitvoer **gegevensset** van de bestaande [Execute R-script][execute-r-script] module aan de **Dataset1** -invoer van de nieuwe module. Het resultaat ziet er ongeveer zo uit als in afbeelding 20.

![Het experiment waarbij de nieuwe methode voor het uitvoeren van de R-script module is toegevoegd](./media/r-quickstart/fig21.png)

*Afbeelding 20. Het experiment waarbij de nieuwe methode voor het uitvoeren van de R-script module is toegevoegd.*

Net als bij de correlatie analyse die we zojuist hebben voltooid, moeten we een kolom met een POSIXct Time Series-object toevoegen. De volgende code is alleen dit.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Voer deze code uit en Bekijk het logboek. Het resultaat moet eruit zien als afbeelding 21.

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

*Afbeelding 21. Een samen vatting van de data frame.*

Met dit resultaat gaan we onze analyse nu starten.

### <a name="create-a-training-dataset"></a>Een trainings gegevensset maken

Met de data frame die zijn gebouwd, moeten we een trainings gegevensset maken. Deze gegevens omvatten alle waarnemingen, met uitzonde ring van de laatste 12 van het jaar 2013, wat onze test gegevensset is. Met de volgende code worden de data frame en de productie-en prijs variabelen van zuivel gemaakt. Ik maak vervolgens een grafiek van de vier productie-en prijs variabelen. Een anonieme functie wordt gebruikt voor het definiëren van een aantal uitbrei ding voor het waarnemings punt en herhaalt vervolgens de lijst met andere twee argumenten met `Map()`. Als u denkt dat u hier een for-lus zou hebben gewerkt, bent u er goed aan. Maar omdat R een functionele taal is, wordt er een functionele benadering weer gegeven.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Als de code wordt uitgevoerd, wordt de reeks time series-grafieken gegenereerd op basis van de uitvoer van het R-apparaat, zoals weer gegeven in afbeelding 22. Houd er rekening mee dat de tijdas de datum eenheden heeft, een goed voor deel van de teken methode van de tijd reeks.

![Eerste fase van de tijd reeks van de productie-en prijs gegevens van Californië zuivel](./media/r-quickstart/unnamed-chunk-161.png)

![Seconde van de fase van de tijd reeks van de productie-en prijs gegevens van Californië zuivel](./media/r-quickstart/unnamed-chunk-162.png)

![Derde fase van de tijd reeksen van Californië zuivel productie-en prijs gegevens](./media/r-quickstart/unnamed-chunk-163.png)

![Vierde fase van de tijd reeks van Californië zuivel productie-en prijs gegevens](./media/r-quickstart/unnamed-chunk-164.png)

*Afbeelding 22. Tijd Series van de productie-en prijs gegevens van Californië zuivel.*

### <a name="a-trend-model"></a>Een trend model

Als u een time Series-object hebt gemaakt en de gegevens hebt bekeken, kunt u beginnen met het maken van een trend model voor de productie gegevens van de California-melk. We kunnen dit doen met een tijd reeks regressie. Het is echter duidelijk uit het waarnemings punt dat we meer dan een helling en snij punt nodig hebben om de waargenomen trend in de trainings gegevens nauw keurig te model leren.

Op basis van de kleine schaal van de gegevens maakt ik het model voor trend in RStudio en knip en plak je het resulterende model in Azure Machine Learning Studio (klassiek). RStudio biedt een interactieve omgeving voor dit type interactieve analyse.

Als eerste poging probeert ik een polynoom met een polynomiale regressie te maken met Maxi maal drie machtingen. Er is een echt risico dat dit soort modellen wordt overschreden. Daarom is het raadzaam om de voor waarden van een hoge orde te vermijden. Met de functie `I()` wordt de interpretatie van de inhoud geinterpreteerd (interpreteert de inhoud als is) en kunt u een letterlijk geïnterpreteerde functie schrijven in een regressie vergelijking.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Hiermee wordt het volgende gegenereerd.

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

Van de P-waarden (`Pr(>|t|)`) in deze uitvoer ziet u dat de gekwadrateerde term mogelijk niet significant is. Ik gebruik de `update()` functie om dit model te wijzigen door de vier Kante term neer te zetten.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Hiermee wordt het volgende gegenereerd.

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

Dit ziet er beter uit. Alle voor waarden zijn belang rijk. De 2e-16-waarde is echter een standaard waarde en mag niet te serieus worden genomen.  

Als Sanity-test kunt u een tijd reeks tekenen van de productie gegevens van Californië zuivel met de trend lijn die wordt weer gegeven. Ik heb de volgende code in het Azure Machine Learning Studio (klassiek)- [script][execute-r-script] model voor het uitvoeren van een R (niet RStudio) toegevoegd om het model te maken en een waarnemings punt uit te voeren. Het resultaat wordt weer gegeven in afbeelding 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Productie gegevens voor Californië melk met trend model weer gegeven](./media/r-quickstart/unnamed-chunk-18.png)

*Afbeelding 23. Productie gegevens voor Californië melk met trend model weer gegeven.*

Het lijkt erop dat het trend model op de juiste wijze in de gegevens past. Verder lijkt het niet alsof er sprake is van meerwaarde, zoals oneven Wiggles in de model curve.  

### <a name="seasonal-model"></a>Seizoen model

Met een trend model in handen moeten we de seizoensgebonden effecten pushen en toevoegen. We gebruiken de maand van het jaar als een dummy variabele in het lineaire model om het effect van de maand per maand vast te leggen. Houd er rekening mee dat wanneer u factor variabelen in een model introduceert, het snij punt niet moet worden berekend. Als u dit niet doet, wordt de formule boven opgegeven en wordt met R een van de gewenste factoren verwijderd, maar wordt de periode voor interceptie bewaard.

Omdat we een voldoende trend model hebben, kunnen we de functie `update()` gebruiken om de nieuwe voor waarden aan het bestaande model toe te voegen. De-1 in de update formule daalt de interceptie termijn. Gedurende het moment van RStudio voortzetten:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Hiermee wordt het volgende gegenereerd.

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

We zien dat het model geen interceptie periode meer heeft en 12 significante maand factoren heeft. Dit is precies wat we wilden zien.

Laten we nog een tijd reeks tekenen van de California zuivel productie gegevens om te zien hoe goed het seizoen model werkt. Ik heb de volgende code toegevoegd in het Azure Machine Learning Studio (klassiek) [R-script uitvoeren][execute-r-script] om het model te maken en een plot te creëren.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Als u deze code uitvoert in Azure Machine Learning Studio (klassiek), wordt het beeld gegenereerd dat wordt weer gegeven in afbeelding 24.

![Productie van Californië met model met inbegrip van seizoen effecten](./media/r-quickstart/unnamed-chunk-20.png)

*Afbeelding 24. Productie van Californië met model, met inbegrip van seizoen effecten.*

De aanpassing aan de gegevens die worden weer gegeven in afbeelding 24 is in plaats daarvan aan te moedigen. Zowel de trend als het seizoen effect (maandelijkse variatie) lijkt redelijk.

Als u een andere controle op ons model hebt, gaan we kijken naar de verschillen. Met de volgende code worden de voorspelde waarden van onze twee modellen berekend, worden de verschillen voor het seizoen model berekend en worden deze verschillen voor de opleidings gegevens weer gegeven.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Het residuele waarnemings punt wordt weer gegeven in afbeelding 25.

![Verschillen in het seizoen model voor de opleidings gegevens](./media/r-quickstart/unnamed-chunk-21.png)

*Afbeelding 25. Resten van het seizoen model voor de trainings gegevens.*

Deze verschillen zien er redelijk uit. Er is geen specifieke structuur, met uitzonde ring van het effect van de 2008-2009-recessie, waardoor ons model niet bijzonder goed werkt.

Het waarnemings punt dat wordt weer gegeven in afbeelding 25 is handig voor het detecteren van tijd afhankelijke patronen in de verschillen. De expliciete aanpak van het berekenen en uitzetten van de verschillen die ik heb gebruikt, plaatst de resten in de tijd volgorde op het waarnemings punt. Als ik op de andere kant een uitgezette `milk.lm$residuals`, zou het waarnemings punt niet in de juiste volg orde staan.

U kunt `plot.lm()` ook gebruiken om een reeks diagnostische grafieken te maken.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Deze code produceert een reeks diagnostische waarnemings punten die in afbeelding 26 worden weer gegeven.

![Eerste van de diagnose punten voor het seizoen model](./media/r-quickstart/unnamed-chunk-221.png)

![Seconde van de diagnostische waarnemings punten voor het seizoen model](./media/r-quickstart/unnamed-chunk-222.png)

![Derde van de diagnostische waarnemings punten voor het seizoen model](./media/r-quickstart/unnamed-chunk-223.png)

![Vierde van de diagnostische waarnemings punten voor het seizoen model](./media/r-quickstart/unnamed-chunk-224.png)

*Afbeelding 26. Diagnostische waarnemings punten voor het seizoen model.*

Er zijn een aantal zeer influentiale punten die in deze grafieken worden geïdentificeerd, maar er is niets om grote bezorgdheid te veroorzaken. Verder kunnen we zien uit de normale Q-Q-tekening dat de resten dicht bij normaal gedistribueerd zijn, een belang rijke veronderstelling voor lineaire modellen.

### <a name="forecasting-and-model-evaluation"></a>Prognoses en model evaluatie

Er is nog maar één ding voor het volt ooien van ons voor beeld. We moeten prognoses berekenen en de fout op de werkelijke gegevens meten. Onze prognose geldt voor de 12 maanden van 2013. We kunnen een fout meting voor deze prognose berekenen op basis van de werkelijke gegevens die geen deel uitmaken van onze trainings gegevensset. Daarnaast kunnen we de prestaties van de 18 jaar van de trainings gegevens vergelijken met de 12 maanden test gegevens.  

Een aantal metrische gegevens wordt gebruikt om de prestaties van Time Series-modellen te meten. In ons geval gebruiken we de fout melding root Square (RMS). Met de volgende functie wordt de RMS-fout tussen twee reeksen berekend.  

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

Net als bij de `log.transform()` functie die we in de sectie ' waarde transformaties ' hebben besproken, is er een groot aantal fout controles en code voor het herstellen van uitzonde ringen in deze functie. De gebruikte principes zijn hetzelfde. Het werk wordt uitgevoerd op twee plaatsen in `tryCatch()`. Ten eerste is de tijd reeks exponentiated, omdat we werken met de logboeken van de waarden. Ten tweede wordt de werkelijke RMS-fout berekend.  

Met een functie die de RMS-fout meet, gaan we een data frame maken en uitvoeren die de RMS-fouten bevat. We zullen alleen voor waarden voor het trend model en het volledige model met seizoensgebonden factoren meenemen. De volgende code voert de taak uit met behulp van de twee lineaire modellen die we hebben gebouwd.

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

Als deze code wordt uitgevoerd, wordt de uitvoer weer gegeven in afbeelding 27 van de uitvoer poort van de resultaat gegevensset.

![Vergelijking van RMS-fouten voor de modellen](./media/r-quickstart/fig26.png)

*Afbeelding 27. Vergelijking van RMS-fouten voor de modellen.*

Vanuit deze resultaten ziet u dat het toevoegen van de seizoensgebonden factoren aan het model de RMS-fout aanzienlijk reduceert. Het is niet te verrassend, de RMS-fout voor de trainings gegevens is een bit kleiner dan voor de prognose.

## <a id="appendixa"></a>Gids voor RStudio-documentatie

RStudio is behoorlijk goed gedocumenteerd. Hier vindt u enkele koppelingen naar de belangrijkste secties van de RStudio-documentatie om aan de slag te gaan.

* **Projecten maken** : u kunt uw R-code in projecten indelen en beheren met behulp van RStudio. Zie [projecten gebruiken](https://support.rstudio.com/hc/articles/200526207-Using-Projects) voor meer informatie. U wordt aangeraden deze instructies te volgen en een project te maken voor de R-code voorbeelden in dit artikel.  
* Het **bewerken en uitvoeren van r code** -RStudio biedt een geïntegreerde omgeving voor het bewerken en uitvoeren van r-code. Zie [code bewerken en uitvoeren](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) voor meer informatie.
* **Fout opsporing** -RStudio bevat krachtige functies voor fout opsporing. Zie [fout opsporing met RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) voor meer informatie over deze functies. Zie [probleem oplossing](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)voor meer informatie over functies voor het oplossen van onderbrekings punten.

## <a id="appendixb"></a>Meer lezen

Deze zelf studie R-programmeer behandelt de basis beginselen van wat u nodig hebt om de R-taal met Azure Machine Learning Studio (klassiek) te gebruiken. Als u niet bekend bent met R, zijn er twee inleidingen beschikbaar op KRANen:

* [R voor beginners](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) door Emmanuel Paradis is een goede plaats om te starten.  
* [Een inleiding tot R](https://cran.r-project.org/doc/manuals/R-intro.html) door W. N. Venables et. al. meer dieper.

Er zijn veel boeken op R die u kunnen helpen om aan de slag te gaan. Hier vindt u enkele nuttige informatie:

* De **Art of r-programmering: een rond leiding door het statistische software ontwerp** van Norman Matloff is een uitstekende inleiding tot het Program meren in R.  
* **R Cookbook** door Paul Teetor biedt een probleem en oplossings benadering voor het gebruik van R.  
* De **R in actie** van Robert Kabacoff is een ander handig inleidende boek. De [snelle R-website](https://www.statmethods.net/) van de Companion is een nuttige resource.
* **R Inferno** door Patrick branding is een verrassend humoristische Book dat een aantal trucige en moeilijke onderwerpen behandelt die tijdens het Program meren in R kunnen worden gevonden. Het boek is gratis beschikbaar op [het R-Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Als u een grondige kennis wilt hebben van geavanceerde onderwerpen in R, bekijkt u het boek **Advanced R** by Hadley Wickham. De online versie van dit boek is gratis beschikbaar op [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/).

Een catalogus met R time series-pakketten vindt u in de [taak weergave kranen: analyse van de tijd reeks](https://cran.r-project.org/web/views/TimeSeries.html). Raadpleeg de documentatie voor het desbetreffende pakket voor meer informatie over specifieke Series-object pakketten.

De **inleidende tijd reeks** van het boek met r door Paul Cowpertwait en Andrew Metcalfe biedt een inleiding tot het gebruik van R voor time series-analyses. Veel meer theoretische teksten bieden R-voor beelden.

Hier volgen enkele fantastische Internet bronnen:

* DataCamp u in het comfort van uw browser met video lessen en coderings oefeningen. Er zijn interactieve zelf studies voor de nieuwste R-technieken en-pakketten. Maak de gratis [interactieve R-zelf studie](https://www.datacamp.com/courses/introduction-to-r).
* [Meer informatie over R-Program ma's, de definitieve hand leiding](https://www.programiz.com/r-programming) van Programiz.
* Een snelle [R-zelf studie](https://www.cyclismo.org/tutorial/R/) van Kelly Black van Clarkson University.
* Er zijn meer dan 60 R-resources die worden weer gegeven in [de bovenste R-taal bronnen om uw gegevens vaardigheden te verbeteren](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
