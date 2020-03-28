---
title: Zelfstudie - een Jupyter-notitieblok maken en uitvoeren - Azure Notebooks Preview
description: Meer informatie over het maken en uitvoeren van een Jupyter-notitieblok in Azure Notebooks Preview dat het proces van lineaire regressie in gegevenswetenschap demonstreert.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 8a1c13f41ef1588b040b3540b852d83764c6ce79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75660814"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Zelfstudie: een Jupyter-notitieblok maken en uitvoeren met Python

In deze zelfstudie wordt u door het proces geleid van het gebruik van Azure-notitieblokken om een compleet Jupyter-notitieblok te maken dat eenvoudige lineaire regressie demonstreert. In de loop van deze zelfstudie maakt u kennis met de gebruikersinterface van het Jupyter-notitieblok, waaronder het maken van verschillende cellen, het uitvoeren van cellen en het presenteren van het notitieblok als diavoorstelling.

Het voltooide notitieblok is te vinden op [GitHub - Azure Notebooks Samples](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Deze zelfstudie begint echter met een nieuw project en een leeg notitieblok, zodat u ervaren het maken van het stap voor stap.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een projectnotitieblok maken met enkele voorbeeldgegevens
> * De notebookinterface gebruiken om verschillende typen cellen te maken
> * Het notitieblok uitvoeren
> * Het notitieblok opslaan
> * Het notitieblok debuggen in Visual Studio Code

## <a name="create-the-project"></a>Het project maken

1. Ga naar [Azure-notitieblokken](https://notebooks.azure.com) en meld u aan. (Zie [Snelstart - Aanmelden bij Azure-notitieblokken voor](quickstart-sign-in-azure-notebooks.md)meer informatie).

1. Selecteer op uw openbare profielpagina **Mijn projecten** boven aan de pagina:

    ![Koppeling Mijn projecten boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer **op** de pagina Mijn projecten **+ Nieuw project** (sneltoets: n); de knop mag **+** alleen worden weergegeven alsof het browservenster smal is:

    ![Opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Selecteer in de pop-up **Nieuw project maken** dat wordt weergegeven, voer de volgende details in of stel u in en selecteer Vervolgens **Maken:**

   - **Projectnaam**: Lineair Regressievoorbeeld - Cricket Chirps
   - **Project ID**: lineair-regressie-voorbeeld
   - **Openbaar project**: (gewist)
   - **Een README.md maken**: (gewist)

1. Na een paar ogenblikken navigeert Azure Notebooks u naar het nieuwe project.

## <a name="create-the-data-file"></a>Het gegevensbestand maken

Het lineaire regressiemodel dat u in het notitieblok maakt, put gegevens uit een bestand in uw project genaamd *cricket_chirps.csv*. U dit bestand maken door het te kopiëren van [GitHub - Azure Notebooks Samples](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)of door de gegevens rechtstreeks in te voeren. In de volgende secties worden beide benaderingen beschreven.

### <a name="upload-the-data-file"></a>Het gegevensbestand uploaden

1. Selecteer **Uploaden** > **vanaf URL** in uw projectdashboard in Azure-notitieblokken
1. Voer in de pop-up de volgende URL in **bestandsURL** en *cricket_chirps.csv* in **Bestandsnaam**in en selecteer **Gereed**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Het *bestand cricket_chirps.csv* moet nu worden weergegeven in de bestandslijst van uw project:

    ![Nieuw gemaakt CSV-bestand dat wordt weergegeven in de lijst met projectbestanden](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Een bestand helemaal opnieuw maken

1. Selecteer **+ Nieuw** > **leeg bestand** op uw projectdashboard in Azure-notitieblokken
1. Er wordt een veld weergegeven in de bestandslijst van het project. Voer *cricket_chirps.csv* in en druk op Enter.
1. Klik met de rechtermuisknop op *cricket_chirps.csv* en selecteer **Bestand bewerken**.
1. Voer in de editor die wordt weergegeven de volgende gegevens in:

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

1. Selecteer **Bestand opslaan** om het bestand op te slaan en terug te keren naar het projectdashboard.

## <a name="install-project-level-packages"></a>Pakketten op projectniveau installeren

In een notitieblok u altijd `!pip install` opdrachten gebruiken, zoals in een codecel, om vereiste pakketten te installeren. Dergelijke opdrachten worden echter elke keer uitgevoerd wanneer u de codecellen van het notitieblok uitvoert en kan veel tijd in beslag nemen. Om deze reden u pakketten op projectniveau installeren met behulp van een `requirements.txt` bestand.

1. Gebruik het proces dat is beschreven in Een `requirements.txt` bestand helemaal opnieuw [maken](#create-a-file-from-scratch) om een bestand te maken met de volgende inhoud:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    U desgevraagd ook een `requirements.txt` bestand uploaden vanaf uw lokale computer, zoals beschreven in Het [gegevensbestand uploaden.](#upload-the-data-file)

1. Selecteer **projectinstellingen**in het projectdashboard .
1. Selecteer in de pop-up die wordt weergegeven het tabblad **Omgeving** en selecteer **+Toevoegen**.
1. Kies **Requirements.txt**in de eerste vervolgkeuzebesturingselement (de bewerking) onder **Stappen voor omgevingsinstellingen**.
1. Kies in de tweede vervolgkeuzebesturingselement (de bestandsnaam) *requirements.txt* (het bestand dat u hebt gemaakt).
1. Kies in de derde vervolgkeuzebesturingselement (de Python-versie) **Python-versie 3.6**.
1. Selecteer **Opslaan**.

![Het tabblad Projectinstellingen-omgeving waarin een bestand requirements.txt wordt opgegeven](media/tutorial/tutorial-requirements-txt.png)

Met deze installatiestap wordt elk notitieblok dat u in het project uitvoert, uitgevoerd in een omgeving waarin deze pakketten zijn geïnstalleerd.

## <a name="create-and-run-a-notebook"></a>Een notebook maken en uitvoeren

Met het gegevensbestand klaar en de projectomgevingset u het notitieblok nu maken en openen.

1. Selecteer **+ Nieuw** > **notitieblok in**het projectdashboard .
1. Voer in de pop-up *Voorbeeld van Lineaire regressie in - Cricket Chirps.ipynb* voor **objectnaam**, kies **Python 3.6** voor de taal en selecteer **Vervolgens Nieuw**.
1. Nadat het nieuwe notitieblok in de bestandslijst is weergegeven, selecteert u het notitieblok om het notitieblok te starten. Een nieuw browsertabblad wordt automatisch geopend.
1. Omdat u een *requirements.txt-bestand* in de omgevinginstellingen hebt, ziet u het bericht 'Wachten tot uw container klaar is met de voorbereiding'. U **OK** selecteren om het bericht te sluiten en verder te werken in het notitieblok. u echter geen codecellen uitvoeren totdat de omgeving volledig is ingesteld.
1. Het notitieblok wordt standaard geopend in de Jupyter-interface met één lege codecel.

    [![Eerste weergave van een nieuw notitieblok in Azure-notitieblokken](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Tour door de notebook-interface

Terwijl het notitieblok wordt uitgevoerd, u code- en markdowncellen toevoegen, deze cellen uitvoeren en de werking van het notitieblok beheren. Ten eerste is het echter de moeite waard om een paar minuten te nemen om vertrouwd te raken met de interface. Selecteer voor volledige documentatie de menuopdracht > **Help-notitieblok.** **Help**

Boven aan het venster ziet u de volgende items:

(A) De naam van uw notitieblok, die u bewerken door te klikken.
(B) Knoppen om te navigeren naar het project met betrekking tot het project en uw projectdashboard, die nieuwe tabbladen openen in uw browser.
(C) Een menu met opdrachten voor het werken met het notitieblok.
(D) een werkbalk met sneltoetsen voor algemene bewerkingen.
(E) het bewerkingscanvas dat cellen bevat.
(F) indicator of het notitieblok wordt vertrouwd (standaard is **niet vertrouwd).**
(G) de kernel die wordt gebruikt om het notitieblok samen met een activiteitsindicator uit te voeren.

[![Primaire gebruikersinterfacegebieden van de Jupyter-interface](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter biedt een ingebouwde rondleiding door de primaire UI-elementen. Begin de tour door de opdracht > **Help-gebruikersinterfacetour te** selecteren en door de pop-ups te klikken. **Help**

De groepen menuopdrachten zijn als volgt:

| Menu | Beschrijving |
| --- | --- |
| File | Opdrachten voor het beheren van het notitieblokbestand, inclusief opdrachten voor het maken en kopiëren van notitieblokken, het weergeven van een afdrukvoorbeeld en het downloaden van het notitieblok in verschillende indelingen. |
| Bewerken | Typische opdrachten voor het knippen, kopiëren en plakken van cellen, het zoeken en vervangen van waarden, het beheren van celbijlagen en het invoegen van afbeeldingen.  |
| Weergave | Opdrachten om de zichtbaarheid van verschillende delen van de Jupyter-gebruikersinterface te regelen. |
| Invoegen | Opdrachten om een nieuwe cel boven of onder de huidige cel in te voegen. U gebruikt deze opdrachten vaak bij het maken van een notitieblok. |
| Mobiel | De verschillende **opdrachten Uitvoeren** voeren een of meer cellen uit in verschillende combinaties. Met de opdrachten **Celtype** wordt het type cel tussen **Code**, **Markdown**en **Raw NBConvert** (platte tekst) gewijzigd. De opdrachten **Huidige uitvoer** en **Alle uitvoer** bepalen hoe de uitvoer van run-code wordt weergegeven en bevatten een opdracht om alle uitvoer te wissen. |
| Kernel | Opdrachten om te beheren hoe code wordt uitgevoerd in de kernel, samen met **De kernel wijzigen** om de taal of Python-versie te wijzigen die wordt gebruikt om het notitieblok uit te voeren. |
| Gegevens | Opdrachten voor het uploaden en downloaden van bestanden uit het project of de sessie. Zie [Werken met projectgegevensbestanden](work-with-project-data-files.md) |
| Widgets | Opdrachten voor het beheren van [Jupyter-widgets,](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)die extra mogelijkheden bieden voor visualisatie, toewijzing en plotten.|
| Help | Opdrachten die hulp en documentatie bieden voor de Jupyter-interface. |

De meeste opdrachten op de werkbalk hebben gelijkwaardige menuopdrachten. Een uitzondering is **Enter/Edit RISE Slideshow**, die wordt besproken op [Delen en huidige notitieblokken](present-jupyter-notebooks-slideshow.md).

U gebruikt een aantal van deze opdrachten terwijl u het notitieblok vult in de volgende secties.

## <a name="create-a-markdown-cell"></a>Een markdowncel maken

1. Klik in de eerste lege cel op het notitieblokcanvas. Standaard is een cel een **codetype,** wat betekent dat deze is ontworpen om runnable code voor de geselecteerde kernel (Python, R of F#) te bevatten. Het huidige type wordt weergegeven in de vervolgkeuzelijst tekst op de werkbalk:

    ![Vervolgkeuzelijst werkbalk Celtype](media/tutorial/tutorial-cell-type-drop-down.png)

1. Wijzig het celtype in **Markering** met de vervolgkeuzelijst werkbalk. afwisselend de menuopdracht**Het afsteken van** **celceltypen** >  **Cell** > gebruiken:

    ![Menu celtype, opdracht](media/tutorial/tutorial-cell-type-menu.png)

1. Klik in de cel om te beginnen met bewerken en voer de volgende markdown in:

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

1. Als u de markering wilt omzetten in HTML voor de browser, selecteert u de opdracht **Uitvoeren** op de werkbalk of gebruikt u de opdracht**Celruncellen.** **Cell** >  De Markdown-code voor opmaak en koppelingen wordt nu weergegeven zoals u verwacht in een browser.

1. Wanneer u de laatste cel in het notitieblok uitvoert, maakt Jupyter automatisch een nieuwe cel onder de cel die u hebt uitgevoerd. Plaats meer markdown in deze cel door de stappen in deze sectie te herhalen met de volgende markering:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Als u de markering opnieuw wilt bewerken, dubbelklikt u in de gerenderde cel. Als u HTML opnieuw wilt renderen nadat u wijzigingen hebt aangebracht, voert u de cel uit.

## <a name="create-a-code-cell-with-commands"></a>Een codecel maken met opdrachten

Zoals in de vorige Markdown-cel is uitgelegd, u opdrachten rechtstreeks in het notitieblok opnemen. U opdrachten gebruiken om pakketten te installeren, curl of wget uit te voeren om gegevens op te halen of iets anders. Jupyter notebooks effectief draaien binnen een Linux virtuele machine, dus je hebt de volledige Linux commando ingesteld om mee te werken.

1. Voer de onderstaande opdrachten in de codecel in die is verschenen nadat u **Run** op de vorige Markdown-cel hebt gebruikt. Als u geen nieuwe cel ziet, maakt u er een **+** met Cel **invoegen** > **hieronder** of gebruikt u de knop op de werkbalk.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Voordat u de cel uitvoert, **+** maakt u een nieuwe cel met de knop op de werkbalk, stelt u deze in op Markdown en voert u de volgende uitleg in:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Selecteer de opdracht **Cel** > **uitvoeren alles** om alle cellen in het notitieblok uit te voeren. Merk op dat de Markdown-cellen als HTML worden weergegeven en dat de opdracht in de kernel wordt uitgevoerd en de kernelindicator observeren zoals beschreven in de markdown zelf:

    ![Drukke indicator voor de notebookkernel](media/tutorial/tutorial-kernel-busy.png)

1. Het duurt ook een beetje `pip install` tijd voordat alle opdrachten worden uitgevoerd en omdat u deze pakketten al hebt geïnstalleerd in de projectomgeving (en omdat ze standaard ook in Azure-notitieblokken zijn opgenomen), ziet u veel berichten met de tekst :"Vereiste die al is voldaan." Al die uitvoer kan visueel storend zijn, dus selecteer die cel (met één klik) en gebruik vervolgens de > **schakeloptie**  > **Celceluitvoer**om de uitvoer te verbergen. **Cell** U de opdracht **Wissen** ook in hetzelfde submenu gebruiken om de uitvoer volledig te verwijderen.

    Met de opdracht **Schakelen** worden alleen de meest recente uitvoer uit de cel verborgen. als u de cel opnieuw uitvoert, wordt de uitvoer opnieuw weergegeven.

1. Omdat de pakketten zijn geïnstalleerd in de `! pip install` projectomgeving, becommentarieert u de opdrachten met behulp van `#`; op deze manier kunnen ze blijven in de notebook als instructiemateriaal, maar zal geen tijd duren om uit te voeren en zal geen onnodige output te produceren. In dit geval geeft het bewaren van de becommentarieerde opdrachten in het notitieblok ook de afhankelijkheden van het notitieblok aan.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>De resterende cellen maken

Als u de rest van het notitieblok wilt vullen, maakt u vervolgens een reeks markerings- en codecellen. Voor elke cel die hieronder wordt vermeld, maakt u eerst de nieuwe cel, stelt u het type in en plakvervolgens in de inhoud.

Hoewel u wachten om het notitieblok uit te voeren nadat u elke cel hebt gemaakt, is het interessant om elke cel uit te voeren terwijl u deze maakt. Niet alle cellen tonen uitvoer; Als u geen fouten ziet, gaat u ervan uit dat de cel normaal wordt uitgevoerd.

Elke codecel is afhankelijk van de code die in vorige cellen is uitgevoerd en als u verzuimt een van de cellen uit te voeren, kunnen latere cellen fouten veroorzaken. Als u merkt dat u vergeten bent een cel uit te voeren, probeert u de **Cel** > **Run All Above** te gebruiken voordat u de huidige cel uitvoert.

Als u onverwachte resultaten ziet (wat u waarschijnlijk zult doen!), controleert u of elke cel is ingesteld op "Code" of "Markdown" indien nodig. Een fout met een 'ongeldige syntaxis' treedt bijvoorbeeld meestal op wanneer u Markdown hebt ingevoerd in de codecel.

1. Afwaarderingscel:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Codecel; wanneer u wordt uitgevoerd, wordt de inhoud van de tabel weergegeven als uitvoer. U de uitvoer onderdrukken `print` door de instructie uit te geven.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Mogelijk ziet u waarschuwingen uit deze code over "numpy.dtype size changed"; de waarschuwingen kunnen veilig worden genegeerd.

1. Afwaarderingscel:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Codecel; wanneer deze cel wordt uitgevoerd, heeft deze cel geen uitvoer.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Afwaarderingscel:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Codecel; wanneer deze cel wordt uitgevoerd, wordt de uitvoer weergegeven. `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Afwaarderingscel:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Codecel; wanneer deze cel wordt `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`uitgevoerd, worden resultaten weergegeven zoals .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Afwaarderingscel:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Afwaarderingscel:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Codecel; wanneer uitgevoerd, deze cel produceert een grafische plot. Als u de plot de eerste keer niet ziet (en in plaats daarvan 'Figuurgrootte 640x480 met 1 assen' ziet, voert u de cel opnieuw uit.

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

    ![Plotuitvoer van de matplotlib-code](media/tutorial/tutorial-plot-output.png)

1. Afwaarderingscel:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Uitvoer wissen en alle cellen opnieuw uitvoeren

Nadat u de stappen in de vorige sectie hebt gevolgd om het hele notitieblok te vullen, hebt u zowel een stukje loopcode gemaakt in de context van een volledige zelfstudie over lineaire regressie. Deze directe combinatie van code en tekst is een van de grote voordelen van notebooks!

Probeer het hele notitieblok nu opnieuw uit te voeren:

1. Verwijder alle sessiegegevens en alle celuitvoer van de kernel door **Kernel** > **Restart & Clear Output te**selecteren. Deze opdracht is altijd een goede om uit te voeren wanneer u een notitieblok hebt voltooid, alleen maar om ervoor te zorgen dat u geen vreemde afhankelijkheden tussen codecellen hebt gemaakt.

1. Voer het notitieblok opnieuw uit met **Cell** > **Run All.** Let op: de kernelindicator wordt ingevuld terwijl de code wordt uitgevoerd.

    Als u een code hebt die te lang wordt uitgevoerd of anderszins vastloopt, u de kernel stoppen met de opdracht **Kernel** > **Interrupt.**

1. Blader door het notitieblok om de resultaten te onderzoeken. (Als de plot opnieuw niet wordt weergegeven, wordt die cel opnieuw uitgevoerd.)

## <a name="save-halt-and-close-the-notebook"></a>Het notitieblok opslaan, stoppen en sluiten

Tijdens de tijd dat u een notitieblok bewerkt, u de huidige status opslaan met de opdracht **Bestand** > **opslaan en controlepunt** of de knop Opslaan op de werkbalk. Een controlepunt maakt een momentopname waar naar u op elk gewenst moment tijdens de sessie terugkeren. Met controlepunten u een reeks experimentele wijzigingen aanbrengen en als deze wijzigingen niet werken, u gewoon terugkeren naar een controlepunt met de opdracht **Bestand** > **terugschakelen naar controlepunt.** Een alternatieve benadering is om extra cellen te maken en commentaar uit een code die u niet wilt uitvoeren; hoe dan ook werkt.

U de opdracht **Bestand** > **een kopie** op elk gewenst moment gebruiken om een kopie van de huidige status van het notitieblok in een nieuw bestand in uw project te maken. Die kopie wordt automatisch geopend in een nieuw browsertabblad.

Wanneer u klaar bent met een notitieblok, gebruikt u de opdracht **Bestand** > **sluiten en stoppen,** waarbij het notitieblok wordt gesloten en de kernel wordt afgesloten waarop het is uitgevoerd. Azure Notebooks sluit vervolgens het browsertabblad automatisch.

## <a name="debug-notebooks-using-visual-studio-code"></a>Foutopsporingsnotitieblokken met Visual Studio Code

Als de codecellen in uw notitieblok zich niet gedragen zoals u verwacht, u codebugs of andere defecten hebben. Echter, anders `print` dan het gebruik van verklaringen om de waarde van variabelen weer te geven, een typische Jupyter omgeving biedt geen debugging faciliteiten.

Gelukkig kun je het *.ipynb-bestand* van de notebook downloaden en vervolgens openen in Visual Studio Code met behulp van de Python-extensie. De extensie importeert een notitieblok rechtstreeks als één codebestand, waarbij uw Markdown-cellen in opmerkingen behouden blijven. Zodra u het notitieblok hebt geïmporteerd, u de foutopsporing van de Visual Studio-code gebruiken om door uw code te stappen, breekpunten in te stellen, de status te onderzoeken, enzovoort. Nadat u correcties in uw code hebt aangebracht, exporteert u het *.ipynb-bestand* uit Visual Studio Code en uploadt u het terug naar Azure Notebooks.

Zie [Een Jupyter-notitieblok debugen](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) in de documentatie van visual studiocode voor meer informatie.

Zie ook [Visual Studio Code - Jupyter-ondersteuning](https://code.visualstudio.com/docs/python/jupyter-support) voor extra functies van Visual Studio Code voor Jupyter-notebooks.

## <a name="next-steps"></a>Volgende stappen

- [Voorbeelden van notebooks verkennen](azure-notebooks-samples.md)

Artikelen met procedures:

- [Projecten maken en klonen](create-clone-jupyter-notebooks.md)
- [Projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Pakketten vanuit een notebook installeren](install-packages-jupyter-notebook.md)
- [Een diavoorstelling presenteren](present-jupyter-notebooks-slideshow.md)
- [Werken met gegevensbestanden](work-with-project-data-files.md)
- [Toegang tot gegevensresources](access-data-resources-jupyter-notebooks.md)
- [Azure Machine Learning gebruiken](use-machine-learning-services-jupyter-notebooks.md)
