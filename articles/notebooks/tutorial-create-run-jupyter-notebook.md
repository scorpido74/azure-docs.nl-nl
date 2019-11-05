---
title: Zelf studie-een Jupyter-notebook maken en uitvoeren op Azure
description: Het maken van een Jupyter-notebook in Azure Notebooks die het proces van lineaire regressie in data Science laat zien.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: 42072894b99ebf1cfcbb5e622c2a9c3a4321a944
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496665"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Zelf studie: een Jupyter-notebook maken en uitvoeren met python

In deze zelf studie wordt u begeleid bij het gebruik van Azure Notebooks voor het maken van een volledige Jupyter-notebook waarmee eenvoudige lineaire regressie wordt gedemonstreerd. In deze zelf studie leert u hoe u de gebruikers interface van de Jupyter-notebook kent, zoals het maken van verschillende cellen, het uitvoeren van cellen en het weer geven van het notitie blok als een diavoorstelling.

U kunt het voltooide notitie blok vinden op [github-Azure notebooks-voor beelden](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). In deze zelf studie begint echter met een nieuw project en een leeg notitie blok, zodat u deze stap voor stap kunt maken.

## <a name="create-the-project"></a>Het project maken

1. Ga naar [Azure notebooks](https://notebooks.azure.com) en meld u aan. (Zie Quick Start ( [Aanmelden bij Azure notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina:

    ![De koppeling Mijn projecten bovenaan in het browser venster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** **+ Nieuw project** (sneltoets: n); de knop kan alleen worden weer gegeven als **+** als het browser venster smal is:

    ![De opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. In de pop-up **Nieuw project maken** die wordt weer gegeven, voert u de volgende gegevens in of stelt u deze in en selecteert u **maken**:

    - **Project naam**: lineair regressie voorbeeld-cricket Chirps
    - **Project-id**: lineair-regressie-voor beeld
    - **Openbaar project**: (uitgeschakeld)
    - **Een README.MD maken**: (uitgeschakeld)

1. Na enkele ogen blikken gaat Azure Notebooks naar het nieuwe project.

## <a name="create-the-data-file"></a>Het gegevens bestand maken

Het lineaire regressie model dat u in het notitie blok maakt, tekent gegevens uit een bestand in uw project met de naam *cricket_chirps. CSV*. U kunt dit bestand maken door het te kopiëren van [github-Azure notebooks-voor beelden](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)of door de gegevens rechtstreeks in te voeren. In de volgende secties worden beide benaderingen beschreven.

### <a name="upload-the-data-file"></a>Het gegevens bestand uploaden

1. Selecteer > **uploaden** **van URL** in het project dashboard in azure notebooks
1. Voer in het pop-upvenster de volgende URL in **bestands-URL** en *cricket_chirps. CSV* in **Bestands naam**in en selecteer vervolgens **gereed**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Het bestand *cricket_chirps. CSV* moet nu worden weer gegeven in de lijst met bestanden van uw project:

    ![Zojuist gemaakt CSV-bestand dat wordt weer gegeven in de lijst Project bestand](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Een volledig nieuwe bestand maken

1. Selecteer **+ nieuw** > **leeg bestand** in het project dashboard in azure notebooks
1. Er wordt een veld weer gegeven in de lijst met bestanden van het project. Voer *cricket_chirps. CSV* in en druk op ENTER.
1. Klik met de rechter muisknop op *cricket_chirps. CSV* en selecteer **bestand bewerken**.
1. In de editor die wordt weer gegeven, voert u de volgende gegevens in:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Selecteer **bestand opslaan** om het bestand op te slaan en terug te keren naar het project dashboard.

## <a name="install-project-level-packages"></a>Pakketten op project niveau installeren

Binnen een notitie blok kunt u altijd opdrachten als `!pip install` in een code-cel gebruiken om de vereiste pakketten te installeren. Dergelijke opdrachten worden echter elke keer uitgevoerd wanneer u de code cellen van het notitie blok uitvoert. Dit kan veel tijd in beslag nemen. Daarom kunt u in plaats daarvan pakketten op project niveau installeren met behulp van een `requirements.txt` bestand.

1. Gebruik het proces beschreven in [een volledig nieuwe bestand maken](#create-a-file-from-scratch) om een bestand met de naam `requirements.txt` te maken met de volgende inhoud:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    U kunt ook een `requirements.txt`-bestand van uw lokale computer uploaden als u dat wilt, zoals wordt beschreven in [het gegevens bestand uploaden](#upload-the-data-file).

1. Selecteer in het project dashboard **project instellingen**.
1. In de pop-up die wordt weer gegeven, selecteert u het tabblad **omgeving** en selecteert u **+ toevoegen**.
1. Kies in de eerste vervolg keuzelijst (de bewerking) onder **installatie stappen**van de omgeving de optie **Requirements. txt**.
1. In de tweede vervolg keuzelijst (de bestands naam) kiest u *Requirements. txt* (het bestand dat u hebt gemaakt).
1. In de derde vervolg keuzelijst (de python-versie) kiest u **python-versie 3,6**.
1. Selecteer **Opslaan**.

![Het tabblad Project instellingen omgeving specificeert een bestand met vereisten. txt](media/tutorial/tutorial-requirements-txt.png)

Bij deze installatie stap wordt elk notitie blok dat u in het project uitvoert, uitgevoerd in een omgeving waarin deze pakketten zijn geïnstalleerd.

## <a name="create-and-run-a-notebook"></a>Een notebook maken en uitvoeren

Wanneer het gegevens bestand gereed is en de project omgeving is ingesteld, kunt u het notitie blok nu maken en openen.

1. Selecteer op het project dashboard **+ nieuw** > **notitie blok**.
1. Voer in het pop-upvenster een *lineaire regressie-voor beeld-cricket Chirps. ipynb* in voor de **naam**van het Item, kies **python 3,6** voor de taal en selecteer vervolgens **Nieuw**.
1. Wanneer het nieuwe notitie blok in de lijst met bestanden wordt weer gegeven, selecteert u dit om het notitie blok te starten. Er wordt automatisch een nieuw browser tabblad geopend.
1. Omdat u een bestand *Requirements. txt* in de omgevings instellingen hebt, ziet u het bericht ' wacht tot uw container is voor bereid. ' U kunt **OK** selecteren om het bericht te sluiten en verder te werken in het notitie blok. u kunt echter geen code cellen uitvoeren, totdat de omgeving volledig is ingesteld.
1. Het notitie blok wordt in de Jupyter-interface met één lege code-cel als standaard waarde geopend.

    [![eerste weer gave van een nieuw notitie blok in Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Rond leiding door de notebook interface

Als het notitie blok wordt uitgevoerd, kunt u de cellen code en prijs verlaging toevoegen, die cellen uitvoeren en de werking van het notitie blok beheren. Ten eerste is het echter een paar minuten om vertrouwd te raken met de interface. Voor volledige documentatie selecteert u de **Help-functie** > **notitie blok Help** menu opdracht.

Boven aan het venster ziet u de volgende items:

(A) de naam van uw notitie blok, dat u kunt bewerken door te klikken op.
(B) knoppen om te navigeren naar het dash board project en uw projecten, waarin nieuwe tabbladen in uw browser worden geopend.
(C) een menu met opdrachten voor het werken met het notitie blok.
(D) een werk balk met snelkoppelingen voor veelvoorkomende bewerkingen.
(E) het bewerkings teken dat cellen bevat.
(F) indicator of het notitie blok wordt vertrouwd (standaard wordt **niet vertrouwd**).
(G) de kernel die wordt gebruikt om het notitie blok samen met een activiteit indicator uit te voeren.

[![van de primaire UI-gebieden van de Jupyter-interface](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter biedt een ingebouwde rond leiding van de elementen van de primaire gebruikers interface. Start de rond leiding door de knop **Help** > **gebruikers interface rond leiding** te selecteren en door de pop-up te klikken.

De groepen menu opdrachten zijn als volgt:

| Menu | Beschrijving |
| --- | --- |
| File | Opdrachten voor het beheren van het notitieblok bestand, inclusief opdrachten voor het maken en kopiëren van notitie blokken, het weer geven van een afdruk voorbeeld en het downloaden van het notitie blok in verschillende indelingen. |
| Bewerken | Gebruikelijke opdrachten voor het knippen, kopiëren en plakken van cellen, het zoeken en vervangen van waarden, het beheren van bijlagen en het invoegen van afbeeldingen.  |
| Weergave | Opdrachten voor het beheren van de zicht baarheid van verschillende onderdelen van de Jupyter-gebruikers interface. |
| Invoegen | Opdrachten voor het invoegen van een nieuwe cel boven of onder de huidige cel. U gebruikt deze opdrachten regel matig bij het maken van een notitie blok. |
| Celinhoud | Met de verschillende **Run** -opdrachten worden een of meer cellen in verschillende combi Naties uitgevoerd. Met de opdrachten voor het **cellen type** wordt het type van een cel tussen **code**, **prijs verlaging**en **onbewerkte NBConvert** (tekst zonder opmaak) gewijzigd. De **huidige uitvoer** en **alle uitvoer** opdrachten bepalen hoe de uitvoer van de uitvoerings code wordt weer gegeven en bevatten een opdracht voor het wissen van alle uitvoer. |
| Kerneldebugger | Opdrachten voor het beheren van de manier waarop code wordt uitgevoerd in de kernel, samen met **wijzigings-kernel** om de taal of python-versie te wijzigen die wordt gebruikt voor het uitvoeren van het notitie blok. |
| Gegevens | Opdrachten voor het uploaden en downloaden van bestanden van het project of de sessie. Zie [werken met Project-gegevens bestanden](work-with-project-data-files.md) |
| Widgets | Opdrachten voor het beheren van [Jupyter widgets](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), die extra mogelijkheden bieden voor visualisatie, toewijzing en uitzetten.|
| Help | Opdrachten die hulp en documentatie bieden voor de Jupyter-interface. |

De meeste opdrachten op de werk balk hebben gelijkwaardige menu opdrachten. Een uitzonde ring is het **invoeren/bewerken**van een diavoorstelling voor verhoging, die wordt besproken op [shares en aanwezig notebooks](present-jupyter-notebooks-slideshow.md).

U gebruikt een aantal van deze opdrachten wanneer u het notitie blok vult in de volgende secties.

## <a name="create-a-markdown-cell"></a>Een afkorting-cel maken

1. Klik in de eerste lege cel die wordt weer gegeven op het canvas notebook. Een cel is standaard een **code** type, wat betekent dat het is ontworpen om uitvoer bare-code te bevatten voor de geselecteerde kernel (python, R of F#). Het huidige type wordt weer gegeven in de vervolg keuzelijst Type op de werk balk:

    ![Vervolg keuzelijst van de werk balk cellen type](media/tutorial/tutorial-cell-type-drop-down.png)

1. Wijzig het type van de cel in de vervolg **keuzelijst met werk** balken. u kunt ook de **cel** > **type cel** > de menu opdracht voor de **prijs verlaging** gebruiken:

    ![De menu opdracht voor een cellen type](media/tutorial/tutorial-cell-type-menu.png)

1. Klik in de cel om te beginnen met bewerken en voer de volgende prijs in:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Als u de prijs verlaging in HTML voor de browser wilt weer geven, selecteert u de opdracht **uitvoeren** op de werk balk of gebruikt u de **cel** > de opdracht **cellen uitvoeren** . De kortings code voor opmaak en koppelingen wordt nu weer gegeven zoals u verwacht in een browser.

1. Wanneer u de laatste cel in het notitie blok uitvoert, maakt Jupyter automatisch een nieuwe cel onder de naam die u hebt uitgevoerd. Plaats meer in deze cel door de stappen in deze sectie te herhalen met de volgende prijs:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Als u de prijs opgave opnieuw wilt bewerken, dubbelklikt u op de gerenderde cel. Voer de cel uit om HTML weer te geven nadat u de wijzigingen hebt aangebracht.

## <a name="create-a-code-cell-with-commands"></a>Een code-cel met opdrachten maken

Zoals u in de vorige rij voor de prijs opneemt, kunt u opdrachten rechtstreeks in het notitie blok toevoegen. U kunt opdrachten gebruiken om pakketten te installeren, krul of wget uit te voeren om gegevens op te halen, of iets anders. Jupyter-notebooks worden effectief uitgevoerd binnen een virtuele Linux-machine, zodat u de volledige Linux-opdracht hebt ingesteld om met te werken.

1. Voer de onderstaande opdrachten in de cel code in die werden weer gegeven nadat u **uitvoeren** hebt gebruikt op de vorige cel prijs verlaging. Als er geen nieuwe cel wordt weer gegeven, maakt u er een met **insert** > **cel invoegen hieronder** of gebruikt u de knop **+** op de werk balk.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Voordat u de cel uitvoert, maakt u een nieuwe cel met de knop **+** op de werk balk, stelt u deze in op prijs verlaging en voert u de volgende uitleg in:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Selecteer de **cel** > **alle opdracht uitvoeren** om alle cellen in het notitie blok uit te voeren. U ziet dat de cellen met prijs verlaging worden weer gegeven als HTML en de opdracht wordt uitgevoerd in de kernel en de kernel-indicator observeren zoals beschreven in de prijs opgave zelf:

    ![Indicator bezet voor de laptop-kernel](media/tutorial/tutorial-kernel-busy.png)

1. Het duurt ook enige tijd voor alle `pip install` opdrachten uit te voeren, en omdat u deze pakketten al hebt geïnstalleerd in de project omgeving (en omdat ze ook standaard zijn opgenomen in Azure Notebooks), ziet u veel berichten die worden gelezen, "vereiste al voldaan. ' Al deze uitvoer kan visueel afleiden, dus Selecteer die verkoop (met één klik) en gebruik vervolgens de **cel** > de uitvoer van de **cel** > **wissel knop** om de uitvoer te verbergen. U kunt ook de opdracht **wissen** in hetzelfde vervolg menu gebruiken om de uitvoer volledig te verwijderen.

    De **wissel knop** verbergt alleen de meest recente uitvoer van de cel. Als u de cel opnieuw uitvoert, wordt de uitvoer opnieuw weer gegeven.

1. Omdat de pakketten zijn geïnstalleerd in de project-omgeving, moet u de `! pip install`-opdrachten met behulp van `#`van commentaar voorzien. op deze manier kunnen ze in het notitie blok blijven als trainings materiaal, maar niet op elke keer dat ze worden uitgevoerd en geen onnodige uitvoer produceert. In dit geval worden ook de afhankelijkheden van het notitie blok aangegeven door de opmerkingen in het notitie blok te houden.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>De resterende cellen maken

Als u de rest van het notitie blok wilt vullen, maakt u vervolgens een reeks prijs-en code cellen. Voor elke cel die hieronder wordt weer gegeven, maakt u eerst de nieuwe cel, vervolgens stelt u het type in en plakt u de inhoud.

Hoewel u kunt wachten op het uitvoeren van het notitie blok nadat u elke cel hebt gemaakt, is het interessant om elke cel uit te voeren terwijl u deze maakt. Niet alle cellen bevatten uitvoer. Als er geen fouten worden weer geven, moet u ervan uitgaan dat de cel normaal is uitgevoerd.

Elke code-cel is afhankelijk van de code die in de vorige cellen is uitgevoerd, en als u een van de cellen wilt uitvoeren, kunnen latere cellen fouten veroorzaken. Als u vindt dat u bent verg eten een cel uit te voeren, probeert u de **cel** te gebruiken > **alles uit te voeren** voordat u de huidige cel uitvoert.

Als er onverwachte resultaten worden weer geven (wat u waarschijnlijk hebt!), controleer dan of elke cel is ingesteld op ' code ' of ' prijs verlaging ' als dat nodig is. Bijvoorbeeld: de fout ' ongeldige syntaxis ' treedt doorgaans op wanneer u een prijs categorie hebt opgegeven.

1. Cel prijs verlaging:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Code-cel; Als deze wordt uitgevoerd, wordt de inhoud van de tabel als uitvoer weer gegeven. U kunt de uitvoer onderdrukken door de `print`-instructie uit te voeren.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > U ziet mogelijk waarschuwingen van deze code over "numpy. dtype-grootte gewijzigd"; de waarschuwingen kunnen veilig worden genegeerd.

1. Cel prijs verlaging:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Code-cel; Deze cel heeft geen uitvoer wanneer deze wordt uitgevoerd.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Cel prijs verlaging:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Code-cel; Als deze cel wordt uitgevoerd, wordt de uitvoer weer gegeven `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Cel prijs verlaging:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Code-cel; Wanneer u deze cel uitvoert, worden resultaten weer gegeven als `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Cel prijs verlaging:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Cel prijs verlaging:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Code-cel; bij het uitvoeren van deze cel wordt een grafisch diagram gegenereerd. Als u het waarnemings punt de eerste keer niet ziet (en in plaats daarvan ' afbeeldings grootte 640x480 met 1 assen '), voert u de cel opnieuw uit.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![De uitvoer van de matplotlib-code uitzetten](media/tutorial/tutorial-plot-output.png)

1. Cel prijs verlaging:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Uitvoer wissen en alle cellen opnieuw uitvoeren

Nadat u de stappen in de vorige sectie hebt uitgevoerd om het hele notitie blok te vullen, hebt u in de context van een volledige zelf studie op lineaire regressie een stukje code gemaakt. Deze rechtstreekse combi natie van code en tekst is een van de grote voor delen van notitie blokken.

Probeer het hele notitie blok nu opnieuw uit te voeren:

1. Wis alle sessie gegevens van de kernel en alle uitvoer van de cel door **kernel** te selecteren > opnieuw op te **starten & uitvoer wissen**. Deze opdracht is altijd een goed idee om uit te voeren wanneer u een notitie blok hebt voltooid, zodat u zeker weet dat u geen vreemde afhankelijkheden tussen code cellen hebt gemaakt.

1. Voer het notitie blok opnieuw uit met behulp van de **cel** > **alles uitvoeren**. U ziet dat de kernel-indicator is ingevuld terwijl code wordt uitgevoerd.

    Als u een code hebt die te lang wordt uitgevoerd of op een andere manier vastloopt, kunt u de kernel stoppen met behulp van de **kernel** - > **IRQ** -opdracht.

1. Blader door het notitie blok om de resultaten te bekijken. (Als het waarnemings punt niet meer wordt weer gegeven, moet u die cel opnieuw uitvoeren.)

## <a name="save-halt-and-close-the-notebook"></a>Het notitie blok opslaan, stoppen en sluiten

Tijdens de tijd die u aan het bewerken van een notitie blok, kunt u de huidige status opslaan met het **bestand** > de opdracht **opslaan en controle punt** of de knop Opslaan op de werk balk. Een "controle punt" maakt een moment opname die u op elk gewenst moment tijdens de sessie kunt herstellen. Met controle punten kunt u een reeks experimentele wijzigingen aanbrengen, en als deze wijzigingen niet werken, hoeft u alleen maar een controle punt terug te zetten met behulp van het **bestand** > **terugkeren naar controlepunt** opdracht. Een alternatieve methode is het maken van extra cellen en het toevoegen van code die u niet wilt uitvoeren. Hoe werkt het?

U kunt ook het **bestand** > **een Kopieer opdracht maken** op elk gewenst moment om een kopie te maken van de huidige status van het notitie blok in een nieuw bestand in uw project. Deze kopie wordt automatisch in een nieuw browser tabblad geopend.

Wanneer u klaar bent met een notitie blok, gebruikt u de opdracht **bestand** > **sluiten en stoppen** , waarmee het notitie blok wordt gesloten en de kernel wordt afgesloten. Azure Notebooks wordt vervolgens het tabblad browser automatisch gesloten.

## <a name="debug-notebooks-using-visual-studio-code"></a>Fout opsporing voor notebooks met Visual Studio code

Als de code cellen in uw notitie blok zich niet op de verwachte manier gedragen, is het mogelijk dat er code fouten of andere defecten optreden. Met andere dan `print`-instructies gebruiken om de waarde van variabelen weer te geven, biedt een typische Jupyter-omgeving echter geen voorzieningen voor fout opsporing.

Gelukkig kunt u het *. ipynb* -bestand van de notebook downloaden en vervolgens openen in Visual Studio code met behulp van de python-extensie. Met de extensie wordt een notitie blok rechtstreeks geïmporteerd als één code bestand, zodat de geprijsde cellen in opmerkingen worden bewaard. Zodra u het notitie blok hebt geïmporteerd, kunt u het Visual Studio code-fout opsporingsprogramma gebruiken om uw code te door lopen, onderbrekings punten in te stellen, de status te controleren, enzovoort. Nadat u de code hebt gecorrigeerd, exporteert u het *. ipynb* -bestand vanuit Visual Studio code en uploadt u het terug naar Azure notebooks.

Zie [debug a Jupyter notebook](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) (Engelstalig) in de documentatie van Visual Studio code voor meer informatie.

Zie ook [Visual Studio code-Jupyter-ondersteuning](https://code.visualstudio.com/docs/python/jupyter-support) voor aanvullende functies van Visual Studio code for Jupyter-notebooks.

## <a name="next-steps"></a>Volgende stappen

- [Voorbeelden van notebooks verkennen](azure-notebooks-samples.md)

Artikelen met procedures:

- [Projecten maken en klonen](create-clone-jupyter-notebooks.md)
- [Projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Pakketten vanuit een notebook installeren](install-packages-jupyter-notebook.md)
- [Een diavoorstelling presenteren](present-jupyter-notebooks-slideshow.md)
- [Werken met gegevensbestanden](work-with-project-data-files.md)
- [Toegang tot gegevensbronnen](access-data-resources-jupyter-notebooks.md)
- [Azure Machine Learning gebruiken](use-machine-learning-services-jupyter-notebooks.md)
