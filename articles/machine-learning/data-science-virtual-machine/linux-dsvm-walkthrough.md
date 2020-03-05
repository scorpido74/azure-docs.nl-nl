---
title: Linux verkennen
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het uitvoeren van verschillende algemene data Science-taken met behulp van de Linux-Data Science Virtual Machine.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: 9883256fc801d37acd4ea10226bd9e541f9135f7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268656"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Data Wetenschappen met een Linux-Data Science Virtual Machine in azure

In dit scenario ziet u hoe u verschillende algemene data Science-taken kunt uitvoeren met behulp van de Linux-Data Science Virtual Machine (DSVM). De Linux-DSVM is een installatie kopie van een virtuele machine die beschikbaar is in Azure en die vooraf is geïnstalleerd met een verzameling hulpprogram ma's die vaak worden gebruikt voor gegevens analyse en machine learning. De belangrijkste software onderdelen zijn gespecificeerd in [het inrichten van de Linux-Data Science virtual machine](linux-dsvm-intro.md). Met de DSVM-installatie kopie kunt u eenvoudig aan de slag met het uitvoeren van data Science in enkele minuten, zonder dat u elk van de hulpprogram ma's afzonderlijk hoeft te installeren en configureren. U kunt de DSVM eenvoudig omhoog schalen als dat nodig is, en u kunt deze stoppen wanneer deze niet in gebruik is. De DSVM-resource is zowel elastisch als kosten efficiënt.

De data Science-taken die in deze walkthrough worden getoond, volgen de stappen die worden beschreven in [Wat is het team data Science process?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Het proces van de team data Science is een systematische benadering van data Science, waarmee teams van gegevens effectief kunnen samen werken in de levens cyclus van het bouwen van intelligente toepassingen. Het data science process biedt ook een iteratieve raamwerk voor datatechnologie die kan worden gevolgd door een persoon.

In dit overzicht analyseren we de [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) -gegevensset. Spambase is een set e-mail berichten die zijn gemarkeerd als spam of per (geen spam). Spambase bevat ook enkele statistieken over de inhoud van de e-mail berichten. Verderop in dit overzicht wordt gecommuniceerd over de statistieken.

## <a name="prerequisites"></a>Vereisten

Voordat u een Linux-DSVM kunt gebruiken, moet u beschikken over de volgende vereisten:

* **Azure-abonnement**. Zie [vandaag nog uw gratis Azure-account maken](https://azure.microsoft.com/free/)om een Azure-abonnement te krijgen.
* [**Linux-Data Science virtual machine**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Zie [de Linux-Data Science virtual machine inrichten](linux-dsvm-intro.md)voor meer informatie over het inrichten van de virtuele machine.
* [**X2Go**](https://wiki.x2go.org/doku.php) is op uw computer geïnstalleerd met een open xfce-sessie. Zie [de X2Go-client installeren en configureren](linux-dsvm-intro.md#x2go)voor meer informatie.
* Voor een soepeler schuivende ervaring kunt u in de Firefox-webbrowser van DSVM de vlag `gfx.xrender.enabled` in `about:config`in-of uitschakelen. [Meer informatie](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). U kunt ook overwegen `mousewheel.enable_pixel_scrolling` in te stellen op `False`. [Meer informatie](https://support.mozilla.org/questions/981140).
* **Azure machine learning-account**. Als u er nog geen hebt, kunt u zich registreren voor een nieuw account op de [Start pagina van Azure machine learning](https://azure.microsoft.com/free/services/machine-learning//).

## <a name="download-the-spambase-dataset"></a>De gegevensset spambase downloaden

De [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) -gegevensset is een relatief kleine set gegevens met 4.601-voor beelden. De gegevensset is een handige grootte voor het demonstreren van enkele van de belangrijkste functies van de DSVM, omdat deze de resource vereisten voor een bescheiden houdt.

> [!NOTE]
> Deze walkthrough is gemaakt met behulp van een D2 v2-grootte Linux DSVM (CentOS Edition). U kunt een DSVM deze grootte gebruiken om de procedures uit te voeren die in dit overzicht worden getoond.

Als u meer opslag ruimte nodig hebt, kunt u extra schijven maken en deze koppelen aan uw DSVM. De schijven gebruiken permanente Azure-opslag, zodat de gegevens behouden blijven, zelfs als de server opnieuw wordt ingericht vanwege het wijzigen van de grootte of wordt uitgeschakeld. Als u een schijf wilt toevoegen en deze aan uw DSVM wilt koppelen, voert u de stappen in [een schijf toevoegen aan een virtuele Linux-machine](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)uit. De stappen voor het toevoegen van een schijf gebruiken de Azure CLI, die al is geïnstalleerd op de DSVM. U kunt de stappen volledig van de DSVM uitvoeren. Een andere optie om de opslag ruimte te verg Roten, is om [Azure files](../../storage/files/storage-how-to-use-files-linux.md)te gebruiken.

Als u de gegevens wilt downloaden, opent u een Terminal venster en voert u de volgende opdracht uit:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Het gedownloade bestand heeft geen veldnamenrij. Laten we nog een bestand maken dat een koptekst heeft. Voer deze opdracht maakt u een bestand met de juiste headers:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Voeg de twee bestanden vervolgens samen:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

De gegevensset heeft verschillende typen statistieken voor elk e-mail bericht:

* Kolommen zoals **word\_freq\__Word_**  het percentage woorden in het e-mail bericht aangeven dat overeenkomt met *Word*. Als bijvoorbeeld **word\_freq\_** **1**is, is 1% van alle woorden in het e-mail bericht *gemaakt*.
* Kolommen zoals **char\_freq\__teken_**  geven het percentage van alle tekens in het e-mail *bericht.*
* de **lengte van\_run\_length\_langste** is de langste lengte van een reeks hoofd letters.
* de **lengte van\_run\_\_gemiddelde** is de gemiddelde lengte van alle reeksen hoofd letters.
* de totale lengte van de **\_lengte\_totaal** is het totaal van alle reeksen hoofd letters.\_
* **spam** geeft aan of het e-mail bericht als spam is beschouwd of niet (1 = spam, 0 = geen spam).

## <a name="explore-the-dataset-by-using-r-open"></a>De gegevensset verkennen met R open

We gaan de gegevens bekijken en enkele basis machine learning doen met behulp van R. De DSVM wordt geleverd met [micro soft R open](https://mran.revolutionanalytics.com/open/) vooraf geïnstalleerd. De multi-threaded wiskundige bibliotheken in de vooraf geïnstalleerde versie van R bieden betere prestaties dan versies met één thread. R open biedt reproduceer baarheid via een moment opname van de opslag plaats voor de KRANs.

Als u kopieën wilt ophalen van de code voorbeelden die in dit overzicht worden gebruikt, gebruikt u Git om de opslag plaats Azure-machine-learning-data-Science te klonen. Git is vooraf geïnstalleerd op de DSVM. Voer op de Git-opdracht regel het volgende uit:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Open een Terminal venster en start een nieuwe R-sessie in de R Interactive-console. U kunt ook RStudio gebruiken, dat vooraf is geïnstalleerd op de DSVM.

De gegevens importeren en de omgeving instellen:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Om te zien samenvattende statistieken over elke kolom:

    summary(data)

Voor een andere weergave van de gegevens:

    str(data)

In deze weer gave ziet u het type van elke variabele en de eerste waarden in de gegevensset.

De kolom **spam** is gelezen als een geheel getal, maar het is wel echt een categorische-variabele (of factor). Instellen van het type:

    data$spam <- as.factor(data$spam)

Als u een verkennende analyse wilt uitvoeren, gebruikt u het [ggplot2](https://ggplot2.tidyverse.org/) -pakket, een populaire grafiek bibliotheek voor R die vooraf is geïnstalleerd op de DSVM. Op basis van de hierboven weer gegeven samenvattings gegevens hebben we een overzicht van de frequentie van het uitroep teken. We zetten deze frequenties hier door de volgende opdrachten uit te voeren:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Omdat de nul balk het waarnemings punt scheef stelt, gaan we deze elimineren:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Er is een niet-essentiële densiteit van meer dan 1 die interessant lijkt. Laten we alleen die gegevens bekijken:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Splits deze vervolgens op via spam versus per-app:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Deze voor beelden kunnen u helpen om Vergelijk bare grafieken te maken en gegevens in de andere kolommen te verkennen.

## <a name="train-and-test-a-machine-learning-model"></a>Een machine learning model trainen en testen

We gaan een aantal machine learning modellen trainen om de e-mail berichten in de gegevensset te classificeren met een of meer ongewenste e-mail. In deze sectie trainen we een beslissings structuur model en een wille keurig forest model. Vervolgens testen we de nauw keurigheid van de voor spellingen.

> [!NOTE]
> Het *rpart* -pakket (recursieve partitioneren en regressie structuren) dat in de volgende code wordt gebruikt, is al in de DSVM geïnstalleerd.

Eerst splitst u de gegevensset in opleidings sets en test sets:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Maak vervolgens een beslissings structuur om de e-mail berichten te classificeren:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Dit is het resultaat:

![Een diagram van de beslissings structuur die wordt gemaakt](./media/linux-dsvm-walkthrough/decision-tree.png)

Om te bepalen hoe goed wordt uitgevoerd voor de training, gebruik de volgende code:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Om te bepalen hoe goed wordt uitgevoerd voor de test:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

We gaan ook een willekeurige forest-model. Wille keurige forests trainen een groot aantal beslissings structuren en voeren een klasse uit die de modus van de classificaties van alle afzonderlijke beslissings structuren is. Ze bieden een krachtigere machine learning benadering omdat ze correct zijn voor de tendens van een beslissings structuur model om een overfit te maken.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>Een model implementeren naar Azure Machine Learning Studio (klassiek)

[Azure machine learning Studio (klassiek)](https://studio.azureml.net/) is een Cloud service waarmee u eenvoudig Predictive Analytics modellen kunt bouwen en implementeren. Een goede functie van Azure Machine Learning Studio (klassiek) is de mogelijkheid om elke R-functie als webservice te publiceren. Het Azure Machine Learning Studio (klassiek) R-pakket maakt implementatie eenvoudig, direct vanuit uw R-sessie op de DSVM.

Als u de code van de beslissings structuur wilt implementeren in de voor gaande sectie, meldt u zich aan bij Azure Machine Learning Studio (klassiek). U moet uw werkruimte-ID en een verificatietoken aan te melden. Voer de volgende stappen uit om deze waarden te vinden en de Azure Machine Learning variabelen te initialiseren:

1. Selecteer in het menu links de optie **instellingen**. Noteer de waarde voor de **werk ruimte-id**.

   ![De werk ruimte-ID Azure Machine Learning Studio (klassiek)](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Selecteer het tabblad **autorisatie tokens** . Noteer de waarde voor het **primaire autorisatie token**.

   ![Het primaire autorisatie token Azure Machine Learning Studio (klassiek)](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Laad het **AzureML** -pakket en stel vervolgens waarden van de variabelen in met uw token en werk ruimte-id in uw R-sessie op de DSVM:

        if(!require("devtools")) install.packages("devtools")
        devtools::install_github("RevolutionAnalytics/AzureML")
        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Laten we het model om deze demonstratie gemakkelijker te implementeren vereenvoudigen. Kies de drie variabelen in de beslissings structuur die het dichtst bij de hoofdmap ligt en bouw een nieuwe structuur door alleen die drie variabelen te gebruiken:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. We moeten een voorspellingsfunctie die de functies neemt als invoer en retourneert de voorspelde waarden:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }

1. Maak een bestand settings. json voor deze werk ruimte:

        vim ~/.azureml/settings.json

1. Zorg ervoor dat de volgende inhoud in de instellingen wordt geplaatst. json:

         {"workspace":{
           "id": "<workspace-id>",
           "authorization_token": "<authorization-token>",
           "api_endpoint": "https://studioapi.azureml.net",
           "management_endpoint": "https://management.azureml.net"
         }


1. Publiceer de functie **predictSpam** naar AzureML met behulp van de functie **publishWebService** :

        ws <- workspace()
        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Deze functie neemt de functie **predictSpam** , maakt een webservice met de naam **spamWebService** die de invoer en uitvoer heeft gedefinieerd, en retourneert vervolgens informatie over het nieuwe eind punt.

    Gebruik deze opdracht om de details van de meest recente gepubliceerde webservice weer te geven, waaronder het API-eind punt en de toegangs sleutels van de service:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Om het te proberen op de eerste 10 rijen van de test instellen:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Zelf studies en-scenario's voor uitgebreid leren

Naast de voor beelden op basis van het Framework, wordt ook een reeks uitgebreide instructies gegeven. Deze scenario's kunt u snel uw ontwikkeling van deep learning-toepassingen in domeinen, zoals afbeeldingen en tekst/language understanding.

- Het [uitvoeren van Neural-netwerken in verschillende Frameworks](https://github.com/ilkarman/DeepLearningFrameworks): een uitgebreid overzicht waarin wordt uitgelegd hoe u code van het ene naar het andere Framework migreert. Er wordt ook gedemonstreerd hoe u de prestaties van model en runtime in Frameworks kunt vergelijken. 

- [Een hand leiding voor het bouwen van een end-to-end oplossing voor het detecteren van producten binnen installatie kopieën](https://github.com/Azure/cortana-intelligence-product-detection-from-images): detectie van afbeeldingen is een techniek waarmee objecten in afbeeldingen kunnen worden gevonden en geclassificeerd. De technologie biedt de mogelijkheid om enorme voor delen te bieden in veel zakelijke domeinen in de praktijk. Bijvoorbeeld, kunnen detailhandelaren deze techniek gebruiken om te bepalen welk product een klant is opgehaald uit de plank. Deze informatie helpt op zijn beurt winkels productinventaris beheren. 

- [Diep gaande informatie over audio: in](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)deze zelf studie leert u hoe u een diepe leer model kunt trainen voor de detectie van audio gebeurtenissen op de [gegevensset stads geluiden](https://urbansounddataset.weebly.com/). De zelf studie biedt een overzicht van hoe u met audio gegevens werkt.

- [Classificatie van tekst documenten](https://github.com/anargyri/lstm_han): in dit scenario ziet u hoe u twee verschillende Neural-netwerk architecturen bouwt en traint: hiërarchische aandacht van netwerk en langlopende geheugen (LSTM). Deze neurale netwerken voor het gebruik van de Keras-API voor deep learning voor het classificeren van documenten. Keras is een front-end aan drie van de meest populaire deep learning-frameworks: Microsoft Cognitive Toolkit, TensorFlow en Theano.

## <a name="other-tools"></a>Andere hulpprogram ma's

In de overige secties ziet u hoe u een aantal van de hulpprogram ma's kunt gebruiken die zijn geïnstalleerd op de Linux-DSVM. Deze hulpprogram ma's worden besproken:

* XGBoost
* Python
* JupyterHub
* Rammelaar
* PostgreSQL en SQuirreL SQL
* SQL Server datawarehouse

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) biedt een snelle en nauw keurige implementatie van een boom structuur.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost kan ook worden aangeroepen vanuit Python of een opdracht regel.

### <a name="python"></a>Python

Voor de ontwikkeling van python worden de Anaconda python-distributies 3,5 en 2,7 geïnstalleerd op de DSVM.

> [!NOTE]
> De Anaconda-distributie omvat [Conda](https://conda.pydata.org/docs/index.html). U kunt Conda gebruiken om aangepaste python-omgevingen te maken waarin verschillende versies of pakketten zijn geïnstalleerd.

Laten we in een aantal spambase-gegevensset lezen en de e-mails classificeren met ondersteuning voor vector machines in Scikit-Learn:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Om voorspellingen te doen:

    clf.predict(X.ix[0:20, :])

Om te laten zien hoe u een Azure Machine Learning-eind punt publiceert, gaan we een meer basis model maken. We gebruiken de drie variabelen die we hebben gebruikt bij het publiceren van het R-model eerder:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Het model publiceren naar Azure Machine Learning:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)


> [!NOTE]
> Deze optie is alleen beschikbaar voor python 2,7. Het wordt nog niet ondersteund in Python 3,5. Gebruik **/Anaconda/bin/python2.7**om uit te voeren.

### <a name="jupyterhub"></a>JupyterHub

De Anaconda-distributie in het DSVM wordt geleverd met een Jupyter Notebook, een omgeving voor meerdere platforms voor het delen van python-, R-of Julia-code en-analyse. De Jupyter Notebook wordt geopend via JupyterHub. U meldt zich aan met behulp van uw lokale Linux-gebruikers naam en-wacht woord op https://\<DSVM DNS-naam of IP-adres\>: 8000/. Alle configuratie bestanden voor JupyterHub vindt u in/etc/jupyterhub.

> [!NOTE]
> Als u het python-pakket beheer (via de opdracht `pip`) van een Jupyter Notebook in de huidige kernel wilt gebruiken, gebruikt u deze opdracht in de cel code:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Als u het installatie programma Conda (via de opdracht `conda`) van een Jupyter Notebook in de huidige kernel wilt gebruiken, gebruikt u deze opdracht in een code-cel:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Er zijn al een aantal voor beelden van notitie blokken geïnstalleerd op de DSVM:

* Voor beelden van python-notebooks:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Voor beeld R-notitie blok:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> De Julia-taal is ook beschikbaar vanaf de opdracht regel in de Linux-DSVM.

### <a name="rattle"></a>Rammelaar

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*analytische *t*Ool *t*o *L*verdienen *E*asily) is een grafisch hulp programma voor gegevens analyse. Rattle heeft een intuïtieve interface waarmee u eenvoudig gegevens kunt laden, verkennen en transformeren, en modellen kunt bouwen en evalueren. [Rattle: een gegevens analyse-GUI voor R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) biedt een overzicht van de functies van Rattle.

Voer de volgende opdrachten uit om Rattle te installeren en te starten:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> U hoeft Rattle niet te installeren op de DSVM. U wordt echter mogelijk gevraagd om extra pakketten te installeren wanneer Rattle wordt geopend.

Rammelaar maakt gebruik van een tabblad gebaseerde interface. De meeste tabbladen komen overeen met de stappen in het [team data Science proces](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), zoals het laden van gegevens of het verkennen van gegevens. Het data science process loopt van links naar rechts de tabbladen. Het laatste tabblad bevat een logboek van de R-opdrachten die zijn uitgevoerd door Rattle.

Laden en de gegevensset configureren:

1. Als u het bestand wilt laden, selecteert u het tabblad **gegevens** .
1. Kies de selector naast **Bestands naam**en selecteer vervolgens **spambaseHeaders. data**.
1. Het bestand laden. Selecteer **uitvoeren**. U ziet een samen vatting van elke kolom, met inbegrip van het geïdentificeerde gegevens type. of het nu gaat om een invoer, een doel of een ander type variabele. en het aantal unieke waarden.
1. Rattle heeft de kolom met **ongewenste e-mail** op de juiste wijze geïdentificeerd als het doel. Selecteer de kolom tegen **ongewenste e-mail** en stel het **doel gegevens type** in op **Categoric**.

De gegevens verkennen:

1. Selecteer het tabblad **verkennen** .
1. Als u informatie wilt weer geven over de typen variabelen en een aantal samenvattings statistieken, selecteert u **samen vatting** > **uitvoeren**.
1. Als u andere typen statistieken over elke variabele wilt weer geven, selecteert u andere opties, zoals **Beschrijving** of **basis**.

U kunt ook het tabblad **verkennen** gebruiken om inkijkende grafieken te genereren. Om te tekenen een histogram van de gegevens:

1. Selecteer **distributies**.
1. Selecteer **histogram**voor **word_freq_remove** en **word_freq_you**.
1. Selecteer **Uitvoeren**. Als het goed is, _ziet u in_ één grafiek venster beide dichtheids grafieken, waar het woord duidelijker wordt weer gegeven in e-mail berichten dan _verwijderen_.

De **correlatie** punten zijn ook interessant. Een tekening maken:

1. Selecteer **correlatie**bij **type**.
1. Selecteer **Uitvoeren**.
1. Rammelaar waarschuwt u dat het wordt aanbevolen maximaal 40 variabelen. Selecteer **Ja** om het waarnemings punt weer te geven.

Er zijn interessante correlaties die beschikbaar zijn: _technologie_ is in het algemeen gecorreleerd aan _HP_ en _Labs_, bijvoorbeeld. Het is ook sterk gecorreleerd aan _650_ , omdat het netnummer van de gegevensset donateurs is 650.

De numerieke waarden voor de correlaties tussen woorden zijn beschikbaar in het venster **verkennen** . Het is bijvoorbeeld interessant om te weten dat de _technologie_ op een negatieve basis is afgestemd op _uw_ en _geld_.

Rammelaar kunt gebruiken om de gegevensset voor het afhandelen van enkele veelvoorkomende problemen. Het kan bijvoorbeeld onderdelen opnieuw schalen, ontbrekende waarden toerekeningen, uitschieters afhandelen en variabelen of waarnemingen verwijderen waarvoor gegevens ontbreken. Rattle kan ook koppelings regels identificeren tussen waarnemingen en variabelen. Deze tabbladen vallen niet onder deze inleiding.

Rattle kan ook cluster analyse uitvoeren. Laten we uitsluiten sommige functies om de uitvoer eenvoudiger te lezen. Op het tabblad **gegevens** selecteert u **negeren** naast elk van de variabelen, behalve deze 10 items:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Ga terug naar het tabblad **cluster** . Selecteer **KMeans**en stel het **aantal clusters** in op **4**. Selecteer **Uitvoeren**. De resultaten worden weergegeven in het uitvoervenster weergegeven. Een cluster heeft een hoge frequentie van _George_ en _HP_, en is waarschijnlijk een rechtmatig zakelijk e-mail adres.

Een basis machine learning model voor de beslissings structuur maken:

1. Selecteer het tabblad **model** ,
1. Selecteer **structuur**voor het **type**.
1. Selecteer **uitvoeren** om de structuur weer te geven in de tekst vorm in het uitvoer venster.
1. Selecteer de knop **tekenen** om een grafische versie weer te geven. De beslissings structuur ziet er ongeveer uit als de structuur die we eerder hebben verkregen met behulp van rpart.

Een handige functie van Rattle is de mogelijkheid om verschillende machine learning-methoden uit te voeren en deze snel te evalueren. Dit zijn de stappen:

1. Selecteer **alle**bij **type**.
1. Selecteer **Uitvoeren**.
1. Wanneer Rattle is voltooid, kunt u elk **type** waarde selecteren, zoals **SVM**, en de resultaten weer geven.
1. U kunt ook de prestaties van de modellen op de validatieset vergelijken met behulp van het tabblad **evalueren** . De selectie van de **fout matrix** toont bijvoorbeeld de Verwar ring matrix, algemene fout en gemiddeld aantal klassen fouten voor elk model in de validatieset. U kunt ook ROC curven uitzetten, gevoeligheids analyses uitvoeren en andere typen model evaluaties doen.

Wanneer u klaar bent met het bouwen van modellen, selecteert u het tabblad **logboek** om de R-code weer te geven die tijdens uw sessie werd uitgevoerd door Rattle. U kunt de knop **exporteren** selecteren om deze op te slaan.

> [!NOTE]
> De huidige release van Rattle bevat een bug. Als u het script wilt wijzigen of als u het wilt gebruiken om de stappen later te herhalen, moet u een **#** teken invoegen vóór het *exporteren van dit logboek...* in de tekst van het logboek.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL en SQuirreL SQL

De DSVM wordt geleverd met PostgreSQL is geïnstalleerd. PostgreSQL is een geavanceerde, open-source relationele database. In deze sectie wordt beschreven hoe u de spambase-gegevensset in PostgreSQL laadt en vervolgens een query uitvoert.

Voordat u de gegevens kunt laden, moet u wachtwoord verificatie van de localhost toestaan. Voer in een opdrachtprompt het volgende uit:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Aan de onderkant van het configuratiebestand worden verschillende regels die informatie over de toegestane verbindingen:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Wijzig de lijn voor **lokale IPv4-verbindingen** voor het gebruik van **MD5** in plaats van **ident**, zodat we zich kunnen aanmelden met een gebruikers naam en wacht woord:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Start de PostgreSQL-service vervolgens opnieuw:

    sudo systemctl restart postgresql

Als u *psql* (een interactieve terminal voor postgresql) als de ingebouwde post gres-gebruiker wilt starten, voert u deze opdracht uit:

    sudo -u postgres psql

Maak een nieuw gebruikers account met behulp van de gebruikers naam van het Linux-account dat u hebt gebruikt om u aan te melden. Een wacht woord maken:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Meld u aan bij psql:

    psql

Importeer de gegevens naar een nieuwe Data Base:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Nu gaan we de gegevens verkennen en enkele query's uitvoeren met behulp van SQuirreL SQL, een grafisch hulp programma dat u kunt gebruiken om met data bases te werken via een JDBC-stuur programma.

Om aan de slag te gaan, opent u SQuirreL SQL in het menu **toepassingen** . Het stuurprogramma instellen:

1. Selecteer **Windows** > - **weergave Stuur Programma's**.
1. Klik met de rechter muisknop op **postgresql** en selecteer **stuur programma wijzigen**.
1. Selecteer **extra klassepad** > **toevoegen**.
1. Voer voor **Bestands naam** **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**in.
1. Selecteer **Openen**.
1. Selecteer **Stuur Programma's weer geven**. Voor **klassenaam**selecteert u **org. postgresql. driver**en selecteert u **OK**.

De verbinding met de lokale server instellen:

1. Selecteer **Windows** - > **weergave aliassen.**
1. Selecteer de knop **+** om een nieuwe alias te maken. Voer voor de nieuwe alias naam **spam data base**in. 
1. Selecteer voor **stuur programma** **postgresql**.
1. Stel de URL in op **JDBC: postgresql://localhost/spam**.
1. Voer uw gebruikers naam en wacht woord in.
1. Selecteer **OK**.
1. Als u het venster **verbinding** wilt openen, dubbelklikt u op de alias voor de **spam-data base** .
1. Selecteer **Verbinden**.

Sommige query's uitvoeren:

1. Selecteer het tabblad **SQL** .
1. Voer in het vak query boven aan het tabblad **SQL** een Basic-query in, zoals `SELECT * from data;`.
1. Druk op CTRL + ENTER om de query uit te voeren. SQuirreL SQL retourneert standaard de eerste 100 rijen van uw query.

Er zijn veel meer query's die u kunt uitvoeren om deze gegevens te verkennen. Hoe kan de frequentie van *het woord bijvoorbeeld* verschillen tussen spam en de hoeveelheid?

    SELECT avg(word_freq_make), spam from data group by spam;

Of wat zijn de kenmerken van e-mail die regel matig *3D*bevat?

    SELECT * from data order by word_freq_3d desc;

De meeste e-mail berichten met een groot aantal *3D-* incidenten zijn vaak spam. Deze informatie kan nuttig zijn voor het maken van een voorspellend model voor het classificeren van e-mail berichten.

Als u machine learning wilt doen met behulp van gegevens die zijn opgeslagen in een PostgreSQL-data base, kunt u overwegen [MADlib](https://madlib.incubator.apache.org/)te gebruiken.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse is een scale-out-data base op basis van de cloud die grote hoeveel heden gegevens kan verwerken, zowel relationele als niet-relationeel. Zie [Wat is Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) voor meer informatie.

Verbinding maken met het datawarehouse en de tabel maken, voer de volgende opdracht uit vanaf een opdrachtprompt:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Voer de volgende opdracht uit op de Sqlcmd-prompt:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopieer de gegevens met behulp van BCP:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Het gedownloade bestand bevat Windows-stijl regel einden. Het hulp programma BCP verwacht dat er UNIX-lijn einden worden afgesloten. Gebruik de markering-r om BCP te vertellen.

Vervolgens voert u een query uit met Sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

U kunt ook een query uitvoeren met behulp van SQuirreL SQL. Volg de stappen die vergelijkbaar zijn met PostgreSQL met behulp van het SQL Server JDBC-stuur programma. Het JDBC-stuur programma bevindt zich in de map/usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Volgende stappen

Zie [team data Science process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)voor een overzicht van artikelen die u door lopen over de taken die het data Science-proces in azure vormen.

Zie [team data Science proces scenarios](../team-data-science-process/walkthroughs.md)(Engelstalig) voor een beschrijving van end-to-end-procedures die de stappen in het team data Science proces voor specifieke scenario's demonstreren. De scenario's laten ook zien hoe u cloud en on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen.
