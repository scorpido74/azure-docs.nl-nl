---
title: Ontdek Linux
titleSuffix: Azure Data Science Virtual Machine
description: Leer hoe u verschillende veelvoorkomende gegevenswetenschappelijke taken uitvoeren met behulp van de Linux Data Science Virtual Machine.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: 1d15d53816d916bd28841aae39255685524faa2d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477865"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Data science met een Linux Data Science Virtual Machine in Azure

Deze walkthrough laat u zien hoe u verschillende veelvoorkomende data science-taken uitvoeren met behulp van de Linux Data Science Virtual Machine (DSVM). De Linux DSVM is een virtuele machineafbeelding die beschikbaar is in Azure en die vooraf is geïnstalleerd met een verzameling hulpprogramma's die vaak worden gebruikt voor gegevensanalyse en machine learning. De belangrijkste softwarecomponenten zijn gespecificeerd in [provision the Linux Data Science Virtual Machine](linux-dsvm-intro.md). De DSVM-afbeelding maakt het gemakkelijk om binnen enkele minuten aan de slag te gaan met data science, zonder dat u elk van de hulpprogramma's afzonderlijk hoeft te installeren en configureren. U de DSVM eenvoudig opschalen als dat nodig is, en u deze stoppen wanneer deze niet in gebruik is. De DSVM-bron is zowel elastisch als kostenefficiënt.

De data science taken die in deze walkthrough worden gedemonstreerd, volgen de stappen die zijn beschreven in [Wat is het Team Data Science Process?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Het Team Data Science Process is een systematische benadering van data science die teams van data scientists helpt effectief samen te werken gedurende de levenscyclus van het bouwen van intelligente applicaties. Het data science-proces biedt ook een iteratief kader voor data science dat door een individu kan worden gevolgd.

In deze walkthrough analyseren we de [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) dataset. Spambase is een set e-mails die zijn gemarkeerd als spam of ham (geen spam). Spambase bevat ook enkele statistieken over de inhoud van de e-mails. We praten later in de walkthrough over de statistieken.

## <a name="prerequisites"></a>Vereisten

Voordat u een Linux DSVM gebruiken, moet u de volgende vereisten hebben:

* **Azure-abonnement**. Zie [Uw gratis Azure-account vandaag nog maken](https://azure.microsoft.com/free/)als u een Azure-abonnement wilt afsluiten.
* [**Linux Data Science Virtual Machine**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Zie De Virtuele Machine van [Linux Data Science inrichten](linux-dsvm-intro.md)voor informatie over het inrichten van de virtuele machine.
* [**X2Go**](https://wiki.x2go.org/doku.php) geïnstalleerd op uw computer met een open XFCE-sessie. Zie [De X2Go-client installeren en configureren](linux-dsvm-intro.md#x2go)voor meer informatie.
* Voor een soepelere scrollervaring schakelt u in de Firefox-webbrowser `gfx.xrender.enabled` van `about:config`de DSVM de vlag in . [Meer informatie](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Overweeg ook `mousewheel.enable_pixel_scrolling` `False`om . [Meer informatie](https://support.mozilla.org/questions/981140).
* **Azure Machine Learning-account**. Als u er nog geen hebt, meldt u zich aan voor een nieuw account op de [startpagina van Azure Machine Learning.](https://azure.microsoft.com/free/services/machine-learning//)

## <a name="download-the-spambase-dataset"></a>Download de spambase-gegevensset

De [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) dataset is een relatief kleine set gegevens die 4.601 voorbeelden bevat. De gegevensset is een handige grootte voor het aantonen van enkele van de belangrijkste functies van de DSVM, omdat het houdt de resource vereisten bescheiden.

> [!NOTE]
> Deze walkthrough is gemaakt met behulp van een D2 v2-formaat Linux DSVM (CentOS Edition). U een DSVM van dit formaat gebruiken om de procedures te voltooien die in deze walkthrough worden gedemonstreerd.

Als u meer opslagruimte nodig hebt, u extra schijven maken en deze aan uw DSVM koppelen. De schijven maken gebruik van permanente Azure-opslag, zodat hun gegevens behouden blijven, zelfs als de server opnieuw is ingericht vanwege het formaat of wordt afgesloten. Als u een schijf wilt toevoegen en aan uw DSVM wilt koppelen, voert u de stappen uit in [Een schijf toevoegen aan een Linux-vm.](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) De stappen voor het toevoegen van een schijf gebruiken de Azure CLI, die al is geïnstalleerd op de DSVM. U de stappen volledig uitvoeren vanuit de DSVM zelf. Een andere optie om de opslag te vergroten, is het gebruik van [Azure Files.](../../storage/files/storage-how-to-use-files-linux.md)

Als u de gegevens wilt downloaden, opent u een terminalvenster en voert u deze opdracht uit:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Het gedownloade bestand heeft geen koptekstrij. Laten we een ander bestand maken dat wel een koptekst heeft. Voer deze opdracht uit om een bestand met de juiste kopteksten te maken:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Voeg vervolgens de twee bestanden samen:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

De gegevensset heeft verschillende soorten statistieken voor elke e-mail:

* Kolommen zoals **\_woord\_freq WORD** geven het percentage woorden in de e-mail aan dat overeenkomt met *WORD*. Bijvoorbeeld, als **\_woord\_freq maken** is **1,** dan 1% van alle woorden in de e-mail werden *te maken*.
* Kolommen zoals **\_char\_freq CHAR** geven het percentage van alle tekens in de e-mail die *CHAR*.
* **kapitaal\_\_run\_lengte langste** is de langste lengte van een reeks van hoofdletters.
* **kapitaal\_\_run\_lengte gemiddelde** is de gemiddelde lengte van alle sequenties van hoofdletters.
* **kapitaal\_\_run\_lengte totaal** is de totale lengte van alle sequenties van hoofdletters.
* **spam** geeft aan of de e-mail werd beschouwd als spam of niet (1 = spam, 0 = geen spam).

## <a name="explore-the-dataset-by-using-r-open"></a>De gegevensset verkennen met R Open

Laten we de gegevens onderzoeken en wat basismachine learning doen met Behulp van R. De DSVM wordt geleverd met [Microsoft R Open](https://mran.revolutionanalytics.com/open/) vooraf geïnstalleerd. De multithreaded wiskundebibliotheken in de vooraf geïnstalleerde versie van R bieden betere prestaties dan single-threaded versies. R Open biedt ook reproduceerbaarheid door middel van een momentopname van de CRAN-pakketrepository.

Als u kopieën wilt krijgen van de codevoorbeelden die in deze walkthrough worden gebruikt, gebruikt u git om de Azure-Machine-Learning-Data-Science-repository te klonen. Git is vooraf geïnstalleerd op de DSVM. Voer bij de git-opdrachtregel het als:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Open een terminalvenster en start een nieuwe R-sessie in de interactieve R-console. U ook RStudio gebruiken, dat vooraf is geïnstalleerd op de DSVM.

Ga als u de gegevens importeert en de omgeving instelt:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Ga als het gaat om overzichtsstatistieken over elke kolom:

    summary(data)

Voor een andere kijk op de gegevens:

    str(data)

In deze weergave ziet u het type van elke variabele en de eerste waarden in de gegevensset.

De **spam** kolom werd gelezen als een geheel getal, maar het is eigenlijk een categorische variabele (of factor). Ga als u het type in:

    data$spam <- as.factor(data$spam)

Om een verkennende analyse uit te voeren, gebruikt u het [ggplot2-pakket,](https://ggplot2.tidyverse.org/) een populaire grafische bibliotheek voor R die vooraf is geïnstalleerd op de DSVM. Op basis van de eerder weergegeven overzichtsgegevens hebben we overzichtsstatistieken over de frequentie van het teken van het uitroepteken. Laten we plot die frequenties hier door het uitvoeren van de volgende commando's:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Omdat de nulbalk de plot scheeftrekt, laten we deze elimineren:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Er is een niet-triviale dichtheid boven 1 die er interessant uitziet. Laten we alleen die gegevens bekijken:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Vervolgens, split het door spam versus ham:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Met deze voorbeelden u vergelijkbare plots maken en gegevens in de andere kolommen verkennen.

## <a name="train-and-test-a-machine-learning-model"></a>Een machine learning-model trainen en testen

Laten we een paar machine learning-modellen trainen om de e-mails in de gegevensset te classificeren als spam of ham. In deze sectie trainen we een decision tree model en een willekeurig bosmodel. Vervolgens testen we de nauwkeurigheid van de voorspellingen.

> [!NOTE]
> Het *rpart-pakket* (Recursieve partitionering- en regressiebomen) dat in de volgende code wordt gebruikt, is al geïnstalleerd op de DSVM.

Laten we eerst de gegevensset opsplitsen in trainingssets en testsets:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Maak vervolgens een beslissingsstructuur om de e-mails te classificeren:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Hier is het resultaat:

![Een diagram van de beslissingsboom die is gemaakt](./media/linux-dsvm-walkthrough/decision-tree.png)

Gebruik de volgende code om te bepalen hoe goed het presteert op de trainingsset:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Ga als volgt te werk om te bepalen hoe goed het presteert op de testset:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Laten we ook een willekeurig bosmodel proberen. Willekeurige bossen trainen een veelheid van beslissingsbomen en output een klasse die de wijze van de classificaties van alle individuele beslissingsbomen. Ze bieden een krachtigere machine learning-benadering omdat ze corrigeren voor de neiging van een beslissingsboommodel om een trainingsgegevensset te overfiten.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>Een model implementeren in Azure Machine Learning Studio (klassiek)

[Azure Machine Learning Studio (klassiek)](https://studio.azureml.net/) is een cloudservice die het eenvoudig maakt om voorspellende analysemodellen te bouwen en te implementeren. Een leuke functie van Azure Machine Learning Studio (klassiek) is de mogelijkheid om elke R-functie als een webservice te publiceren. Het Azure Machine Learning Studio (classic) R-pakket maakt implementatie eenvoudig, direct vanaf uw R-sessie op de DSVM.

Als u de beslissingsstructuurcode uit de vorige sectie wilt implementeren, meldt u zich aan bij Azure Machine Learning Studio (klassiek). U hebt uw werkruimte-id en een autorisatietoken nodig om u aan te melden. Voer de volgende stappen uit om deze waarden te vinden en de Azure Machine Learning-variabelen hiermee te initialiseren:

1. Selecteer **Instellingen**in het linkermenu . Let op de waarde voor **WERKRUIMTE-id**.

   ![De Azure Machine Learning Studio (klassieke) werkruimte-id](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Selecteer het tabblad **Autorisatietokens.** Let op de waarde voor **token primaire autorisatie**.

   ![Het azure machine learning studio (klassieke) primaire autorisatietoken](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Laad het **AzureML-pakket** en stel vervolgens waarden in van de variabelen met uw token- en werkruimte-id in uw R-sessie op de DSVM:

        if(!require("devtools")) install.packages("devtools")
        devtools::install_github("RevolutionAnalytics/AzureML")
        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Laten we het model vereenvoudigen om deze demonstratie gemakkelijker te implementeren. Kies de drie variabelen in de beslissingsstructuur die het dichtst bij de wortel staat en bouw een nieuwe boom met alleen die drie variabelen:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. We hebben een voorspellingsfunctie nodig die de functies als invoer neemt en de voorspelde waarden retourneert:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }

1. Maak een bestand settings.json voor deze werkruimte:

        vim ~/.azureml/settings.json

1. Zorg ervoor dat de volgende inhoud in settings.json wordt geplaatst:

         {"workspace":{
           "id": "<workspace-id>",
           "authorization_token": "<authorization-token>",
           "api_endpoint": "https://studioapi.azureml.net",
           "management_endpoint": "https://management.azureml.net"
         }


1. Publiceer de **functie predictSpam** naar AzureML met de functie **publishWebService:**

        ws <- workspace()
        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Deze functie neemt de **functie predictSpam** over, maakt een webservice met de naam **spamWebService** die invoer en uitvoer heeft gedefinieerd en retourneert vervolgens informatie over het nieuwe eindpunt.

    Gebruik deze opdracht om details van de meest recente gepubliceerde webservice weer te geven, inclusief het API-eindpunt en de toegangssleutels:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Ga als een proef om het uit te proberen op de eerste 10 rijen van de testset:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Deep learning tutorials en walkthroughs

Naast de framework-based samples is er ook een reeks uitgebreide walkthroughs voorzien. Deze walkthroughs helpen u bij het opstarten van uw ontwikkeling van deep learning-toepassingen in domeinen zoals beeld- en tekst-/taalbegrip.

- [Neurale netwerken uitvoeren in verschillende frameworks:](https://github.com/ilkarman/DeepLearningFrameworks)een uitgebreide walkthrough die u laat zien hoe u code van het ene framework naar het andere migreren. Het laat ook zien hoe model- en runtime-prestaties tussen frameworks kunnen worden vergeleken. 

- [Een handleiding voor het bouwen van een end-to-end oplossing om producten in afbeeldingen te detecteren:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)Beelddetectie is een techniek die objecten in afbeeldingen kan lokaliseren en classificeren. De technologie heeft het potentieel om enorme beloningen te brengen in vele real-life zakelijke domeinen. Retailers kunnen deze techniek bijvoorbeeld gebruiken om te bepalen welk product een klant uit het schap heeft gehaald. Deze informatie helpt winkels op hun beurt bij het beheren van productvoorraad. 

- [Deep learning voor audio:](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)Deze tutorial laat zien hoe je een deep learning-model traint voor audio-eventdetectie op de [stedelijke geluidsgegevensset.](https://urbansounddataset.weebly.com/) De zelfstudie geeft een overzicht van hoe u met audiogegevens werken.

- [Classificatie van tekstdocumenten](https://github.com/anargyri/lstm_han): Deze walkthrough laat zien hoe je twee verschillende neurale netwerkarchitecturen bouwen en trainen: Hierarchical Attention Network en Long Short Term Memory (LSTM). Deze neurale netwerken gebruiken de Keras API voor deep learning om tekstdocumenten te classificeren. Keras is een front-end van drie van de meest populaire deep learning frameworks: Microsoft Cognitive Toolkit, TensorFlow en Theano.

## <a name="other-tools"></a>Andere hulpprogramma's

De overige secties laten zien hoe u een aantal van de tools die zijn geïnstalleerd op de Linux DSVM te gebruiken. We bespreken deze tools:

* XGBoost
* Python
* JupyterHub JupyterHub
* Rattle
* PostgreSQL en SQuirreL SQL
* SQL Server-gegevensmagazijn

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) biedt een snelle en nauwkeurige boost tree implementatie.

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

XGBoost kan ook bellen vanuit Python of een command line.

### <a name="python"></a>Python

Voor python-ontwikkeling zijn de Anaconda Python-distributies 3.5 en 2.7 geïnstalleerd op de DSVM.

> [!NOTE]
> De Anaconda distributie omvat [Conda](https://conda.pydata.org/docs/index.html). U Conda gebruiken om aangepaste Python-omgevingen te maken met verschillende versies of pakketten die erin zijn geïnstalleerd.

Laten we eens lezen in een aantal van de spambase dataset en classificeren de e-mails met ondersteuning vector machines in Scikit-leren:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Ga als het gaat om voorspellingen te doen:

    clf.predict(X.ix[0:20, :])

Als u wilt laten zien hoe u een Azure Machine Learning-eindpunt publiceert, maken we een meer basismodel. We gebruiken de drie variabelen die we gebruikten toen we het R-model eerder publiceerden:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Ga als u het model publiceren naar Azure Machine Learning:

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
> Deze optie is alleen beschikbaar voor Python 2.7. Het wordt nog niet ondersteund op Python 3.5. Als u wilt uitvoeren, gebruikt u **/anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub JupyterHub

De Anaconda-distributie in de DSVM wordt geleverd met een Jupyter Notebook, een cross-platform omgeving voor het delen van Python, R of Julia code en analyse. De Jupyter Notebook is toegankelijk via JupyterHub. U meldt zich aan met uw lokale\<Linux gebruikersnaam en wachtwoord\>op https:// DSVM DNS naam of IP-adres :8000/. Alle configuratiebestanden voor JupyterHub zijn te vinden in /etc/jupyterhub.

> [!NOTE]
> Als u python-pakketbeheer `pip` (via de opdracht) wilt gebruiken vanuit een Jupyter-notitieblok in de huidige kernel, gebruikt u deze opdracht in de codecel:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Als u het Conda-installatieprogramma (via de `conda` opdracht) wilt gebruiken vanuit een Jupyter-notitieblok in de huidige kernel, gebruikt u deze opdracht in een codecel:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Verschillende voorbeeldnotitieblokken zijn al geïnstalleerd op de DSVM:

* Voorbeeld van Python-notitieblokken:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Voorbeeld R-notitieblok:
  * [IntroTutorialinR (IntroTutorialinR)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> De Julia taal is ook beschikbaar vanaf de command line op de Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*nalytical *T*ool *T*o *L*verdienen *E*asily) is een grafische R tool voor data mining. Rattle heeft een intuïtieve interface die het gemakkelijk maakt om gegevens te laden, te verkennen en te transformeren en modellen te bouwen en te evalueren. [Rattle: Een Data Mining GUI voor R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) biedt een walkthrough die rattle's functies demonstreert.

Installeer en start Rattle door deze opdrachten uit te voeren:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> U hoeft Rattle niet op de DSVM te installeren. U wordt echter mogelijk gevraagd om extra pakketten te installeren wanneer Rattle wordt geopend.

Rattle maakt gebruik van een op tabbladen gebaseerde interface. De meeste tabbladen komen overeen met stappen in het [Team Data Science Process,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)zoals het laden van gegevens of het verkennen van gegevens. Het data science-proces stroomt van links naar rechts door de tabbladen. Het laatste tabblad bevat een logboek van de R-opdrachten die door Rattle zijn uitgevoerd.

Ga als u de gegevensset laadt en configureert:

1. Als u het bestand wilt laden, selecteert u het tabblad **Gegevens.**
1. Kies de kiezer naast **Bestandsnaam**en selecteer **vervolgens spambaseHeaders.data**.
1. Om het bestand te laden. selecteer **Uitvoeren**. U ziet een samenvatting van elke kolom, inclusief het geïdentificeerde gegevenstype. of het nu gaat om een invoer, een doel of een ander type variabele; en het aantal unieke waarden.
1. Rattle heeft de **spamkolom** correct geïdentificeerd als het doelwit. Selecteer de **spamkolom** en stel het **doelgegevenstype** in **op Categoric**.

Ga als het gaat om de gegevens:

1. Selecteer het tabblad **Verkennen.**
1. Als u informatie wilt zien over de variabele typen en enkele overzichtsstatistieken, selecteert u **Overzicht** > **uitvoeren**.
1. Als u andere typen statistieken over elke variabele wilt weergeven, selecteert u andere opties, zoals **Beschrijven** of **Basisbeginselen**.

U ook het tabblad **Verkennen** gebruiken om inzichtelijke plots te genereren. Een histogram van de gegevens in kaart brengen:

1. Selecteer **Distributies**.
1. Selecteer **Histogram**voor **word_freq_remove** en **word_freq_you**.
1. Selecteer **Uitvoeren**. U ziet beide dichtheidsplots in één grafiekvenster, waarbij het duidelijk is dat het woord _dat u_ veel vaker in e-mails voorkomt dan _verwijdert._

De **Correlatie** percelen zijn ook interessant. Ga als lid van het werk om een plot te maken:

1. Selecteer Voor **Tekst** **de optie Correlatie**.
1. Selecteer **Uitvoeren**.
1. Rattle waarschuwt u dat het maximaal 40 variabelen aanbeveelt. Selecteer **Ja** om het plot weer te geven.

Er zijn een aantal interessante correlaties die komen: _technologie_ is sterk gecorreleerd aan _HP_ en _labs_, bijvoorbeeld. Het is ook sterk gecorreleerd aan _650_ omdat het netnummer van de gegevenssetdonoren 650 is.

De numerieke waarden voor de correlaties tussen woorden zijn beschikbaar in het venster **Verkennen.** Het is interessant om op te merken, bijvoorbeeld, dat _de technologie_ is negatief gecorreleerd met _uw_ en _geld_.

Rattle kan de gegevensset transformeren om een aantal veelvoorkomende problemen af te handelen. Het kan bijvoorbeeld functies opnieuw schalen, ontbrekende waarden aanwijzen, uitschieters verwerken en variabelen of waarnemingen met ontbrekende gegevens verwijderen. Rammelaar kan ook associatieregels tussen waarnemingen en variabelen identificeren. Deze tabbladen zijn niet gedekt in deze inleidende walkthrough.

Rattle kan ook clusteranalyse uitvoeren. Laten we een aantal functies uitsluiten om de uitvoer beter leesbaar te maken. Selecteer op het tabblad **Gegevens** de optie **Negeren** naast elk van de variabelen, behalve deze 10 items:

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

Ga terug naar het tabblad **Cluster.** Selecteer **KMeans**en stel **aantal clusters** in op **4**. Selecteer **Uitvoeren**. De resultaten worden weergegeven in het uitvoervenster. Een cluster heeft een hoge frequentie van _George_ en _HP_, en is waarschijnlijk een legitieme zakelijke e-mail.

Ga als lid van het leermodel voor een basisbeslissingsboom:

1. Selecteer het tabblad **Model,**
1. Selecteer Voor **het type**De optie **Structuur**.
1. Selecteer **Uitvoeren** om de structuur in tekstvorm weer te geven in het uitvoervenster.
1. Selecteer de knop **Tekenen** om een grafische versie weer te geven. De beslisboom lijkt op de boom die we eerder hebben verkregen met behulp van rpart.

Een handig kenmerk van Rattle is de mogelijkheid om verschillende machine learning-methoden uit te voeren en ze snel te evalueren. Hier zijn de stappen:

1. Selecteer **Alles** **voor Tekst**.
1. Selecteer **Uitvoeren**.
1. Wanneer Rattle klaar is met hardlopen, u een **typewaarde** selecteren, zoals **SVM,** en de resultaten bekijken.
1. U ook de prestaties van de modellen op de validatieset vergelijken met het tabblad **Evalueren.** In de selectie **Foutmatrix** ziet u bijvoorbeeld de verwarringsmatrix, algemene fout en gemiddelde klassefout voor elk model op de validatieset. U ook ROC-curven in kaart brengen, gevoeligheidsanalyses uitvoeren en andere soorten modelevaluaties uitvoeren.

Wanneer u klaar bent met het bouwen van modellen, selecteert u het tabblad **Logboek** om de R-code weer te geven die tijdens uw sessie door Rattle is uitgevoerd. U de knop **Exporteren** selecteren om deze op te slaan.

> [!NOTE]
> De huidige release van Rattle bevat een bug. Als u het script wilt wijzigen of wilt gebruiken **#** om uw stappen later te herhalen, moet u een teken invoegen voor *Dit logboek exporteren ...* in de tekst van het logboek.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL en SQuirreL SQL

De DSVM wordt geleverd met PostgreSQL geïnstalleerd. PostgreSQL is een geavanceerde, open-source relationele database. In deze sectie ziet u hoe u de spambase-gegevensset in PostgreSQL laadt en vervolgens opvraagt.

Voordat u de gegevens laden, moet u wachtwoordverificatie toestaan van de localhost. Voer in een opdrachtprompt het volgende uit:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Aan de onderkant van het config-bestand staan verschillende regels die de toegestane verbindingen beschrijven:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Wijzig de **iPv4-lokale verbindingsregel** om **md5** te gebruiken in plaats van **ident**, zodat we kunnen inloggen met een gebruikersnaam en wachtwoord:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Start vervolgens de PostgreSQL-service opnieuw:

    sudo systemctl restart postgresql

Voer de volgende opdracht uit om *psql* (een interactieve terminal voor PostgreSQL) als de ingebouwde postgres-gebruiker te starten:

    sudo -u postgres psql

Maak een nieuw gebruikersaccount aan met de gebruikersnaam van het Linux-account dat u hebt gebruikt om in te loggen. Een wachtwoord maken:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Log in bij psql:

    psql

Importeer de gegevens naar een nieuwe database:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Laten we nu de gegevens verkennen en enkele query's uitvoeren met SQuirreL SQL, een grafische tool die u gebruiken om met databases te communiceren via een JDBC-stuurprogramma.

Open SQuirreL SQL om aan de slag te gaan in het menu **Toepassingen.** Ga als gevolg van het instellen van de bestuurder:

1. Selecteer **Windows** > **Weergavestuurprogramma's**.
1. Klik met de rechtermuisknop op **PostgreSQL** en selecteer **Stuurprogramma wijzigen**.
1. Selecteer **Extra klassepad** > **toevoegen**.
1. Voer **voor bestandsnaam** **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**in .
1. Selecteer **Openen**.
1. Selecteer **Lijststuurprogramma's**. Selecteer **voor Klassenaam** **org.postgresql.Driver**en selecteer **OK**.

Ga als u de verbinding met de lokale server in:

1. Selecteer **Windows** > **Weergavealiassen.**
1. Selecteer **+** de knop om een nieuwe alias te maken. Voer de **spamdatabase**in voor de nieuwe aliasnaam . 
1. Selecteer **PostgreSQL**voor **Stuurprogramma**.
1. Stel de URL in op **jdbc:postgresql://localhost/spam**.
1. Voer uw gebruikersnaam en wachtwoord in.
1. Selecteer **OK**.
1. Dubbelklik op de alias **spamdatabase** als u het **verbindingsvenster** wilt openen.
1. Selecteer **Verbinden**.

Ga als u een aantal query's wilt uitvoeren:

1. Selecteer het **SQL-tabblad.**
1. Voer in het queryvak boven aan het **SQL-tabblad** een basisquery in, zoals `SELECT * from data;`.
1. Druk op Ctrl+Enter om de query uit te voeren. SQuirreL SQL retourneert standaard de eerste 100 rijen van uw query.

Er zijn nog veel meer query's die u uitvoeren om deze gegevens te verkennen. Bijvoorbeeld, hoe maakt de frequentie van het woord *te maken* verschillen tussen spam en ham?

    SELECT avg(word_freq_make), spam from data group by spam;

Of, wat zijn de kenmerken van e-mail die vaak *bevatten 3D?*

    SELECT * from data order by word_freq_3d desc;

De meeste e-mails die een hoog optreden van *3D* blijkbaar zijn spam. Deze informatie kan handig zijn voor het bouwen van een voorspellend model om e-mails te classificeren.

Als u machine learning wilt doen met behulp van gegevens die zijn opgeslagen in een PostgreSQL-database, u [overwegen MADlib](https://madlib.incubator.apache.org/)te gebruiken.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse is een cloudgebaseerde, scale-out database die enorme hoeveelheden gegevens kan verwerken, zowel relationeel als niet-relationeel. Zie Wat is Azure SQL Data Warehouse voor meer [informatie?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Als u verbinding wilt maken met het gegevensmagazijn en de tabel wilt maken, voert u de volgende opdracht uit vanuit een opdrachtprompt:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Voer de opdracht uit bij de sqlcmd-prompt:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopieer de gegevens met behulp van bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Het gedownloade bestand bevat line-eindes in Windows-stijl. De bcp-tool verwacht unix-stijl lijneinde. Gebruik de -r-vlag om bcp te vertellen.

Query vervolgens met sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

U ook query met behulp van SQuirreL SQL. Volg stappen die vergelijkbaar zijn met PostgreSQL met het SQL Server JDBC-stuurprogramma. De JDBC driver bevindt zich in de map /usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Volgende stappen

Zie [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)voor een overzicht van artikelen die u door de taken leiden die het data science-proces in Azure omvatten.

Zie [walkthroughs van teamdatascienceprocessen](../team-data-science-process/walkthroughs.md)voor een beschrijving van end-to-end-walkthroughs die de stappen in het Team Data Science-proces voor specifieke scenario's demonstreren. De walkthroughs illustreren ook hoe cloud- en on-premises tools en services kunnen worden gecombineerd in een workflow of pijplijn om een intelligente toepassing te maken.
