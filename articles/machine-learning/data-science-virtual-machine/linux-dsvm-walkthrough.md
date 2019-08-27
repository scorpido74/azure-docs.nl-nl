---
title: Informatie over het gebruik van Linux Data Science Virtual Machine
titleSuffix: Azure
description: Het uitvoeren van enkele algemene datatechnologietaken met de Linux Data Science VM.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: vijetaj
ms.openlocfilehash: 8100108adc7115921948d3e8f10e415bc2705fbd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051646"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Gegevenswetenschap met een Linux Data Science Virtual Machine in Azure
Deze procedure ziet u hoe u enkele algemene datatechnologietaken met de Linux Data Science VM uitvoert. De Linux Data Science Virtual Machine (DSVM) is een installatiekopie van een virtuele machine op Azure die vooraf worden geïnstalleerd met een verzameling hulpprogramma's die doorgaans gebruikt voor gegevensanalyse en machine learning is beschikbaar. De belangrijke software-componenten zijn ingedeeld de [Linux Data Science Virtual Machine inrichten](linux-dsvm-intro.md) onderwerp. De VM-installatiekopie maakt het gemakkelijk om te beginnen gegevenswetenschap in minuten, zonder te installeren en configureren van elk van de hulpprogramma's afzonderlijk. U kunt eenvoudig opschalen van de virtuele machine, indien nodig, en stoppen wanneer deze niet in gebruik. Deze resource is zo flexibel en kostenefficiënt.

De data science-taken in dit scenario wordt gedemonstreerd, volg de stappen de [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview). Deze procedure biedt een systematische benadering voor data science waarmee teams van data scientists effectief samenwerken gedurende de levenscyclus van het bouwen van intelligente toepassingen. Het data science process biedt ook een iteratieve raamwerk voor datatechnologie die kan worden gevolgd door een persoon.

We analyseren de [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) gegevensset in dit scenario. Dit is een set van e-mailberichten die zijn gemarkeerd als spam of ham (wat betekent dat ze zijn niet spam), en bevat ook enkele statistieken over de inhoud van de e-mailberichten. De statistieken die zijn opgenomen zijn in de volgende maar één sectie besproken.

## <a name="prerequisites"></a>Vereisten
Voordat u een Linux Data Science Virtual Machine gebruiken kunt, moet u het volgende hebt:

* Een **Azure-abonnement**. Als u nog geen een, Zie [Maak vandaag nog uw gratis Azure-account](https://azure.microsoft.com/free/).
* Een [ **Linux data science VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Zie voor meer informatie over het inrichten van deze virtuele machine [Linux Data Science Virtual Machine inrichten](linux-dsvm-intro.md).
* [X2Go](https://wiki.x2go.org/doku.php) geïnstalleerd op uw computer en een XFCE-sessie hebt geopend. Zie [de X2Go-client installeren en configureren](linux-dsvm-intro.md#install-and-configure-the-x2go-client)voor meer informatie over het installeren en configureren van een **X2Go-client**.
* Voor een soepeler schuiven ervaring, uitschakelen van de vlag gfx.xrender.enabled in over:-configuratie in virtuele machines FireFox-browser. [Zie hier meer informatie. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Denk ook na over bij het omschakelen van *mousewheel.enable_pixel_scrolling* op False. [Hier instructies.](https://support.mozilla.org/questions/981140)
* Een **AzureML account**. Als u niet al hebt, zich aanmelden voor nieuwe resourcegroep op de [AzureML-startpagina](https://studio.azureml.net/). Er is een laag gratis gebruik om u aan de slag te helpen.

## <a name="download-the-spambase-dataset"></a>De gegevensset spambase downloaden
De [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) gegevensset is een relatief klein aantal gegevens die alleen 4601 voorbeelden bevat. Dit is een handige grootte moet worden gebruikt wanneer u aan te tonen dat enkele van de belangrijkste functies van de Data Science VM zoals deze houdt de resourcevereisten bescheiden.

> [!NOTE]
> In dit scenario is gemaakt op een D2 v2-grootte Linux Data Science Virtual Machine (CentOS-editie). Deze grootte DSVM is kan verwerken van de procedures in dit scenario.
>
>

Als u meer opslagruimte nodig hebt, kunt u extra schijven maakt en koppelt u ze aan uw virtuele machine. Deze schijven gebruik permanente opslag van Azure, zodat hun gegevens blijven behouden, zelfs wanneer de server vanwege het formaat is ingericht of wordt afgesloten. Als u wilt een schijf toevoegen en deze koppelen aan uw virtuele machine, volg de instructies in [een schijf toevoegen aan een Linux-VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Deze stappen gebruiken de Azure-opdrachtregelinterface (Azure CLI), die al is geïnstalleerd op de DSVM. Deze procedures is dus mogelijk geheel vanuit de virtuele machine zelf. Een andere optie voor het verhogen van opslag, is met [Azure files](../../storage/files/storage-how-to-use-files-linux.md).

Als u wilt de gegevens hebt gedownload, open een terminalvenster en voer deze opdracht uit:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Het gedownloade bestand heeft geen een rij met kolomkoppen, laten we een ander bestand dat u beschikt over een kop maken. Voer deze opdracht maakt u een bestand met de juiste headers:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

De twee bestanden samen met de opdracht vervolgens samen te voegen:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

De gegevensset heeft verschillende typen statistieken op elk e-mailbericht:

* Kolommen zoals ***word\_freq\_WORD*** geven het percentage van de woorden in het e-mailbericht die overeenkomen met *WORD*. Bijvoorbeeld, als *word\_freq\_maken* is 1, 1% van alle woorden in het e-mailbericht zijn *maken*.
* Kolommen zoals ***char\_freq\_CHAR*** geven het percentage van alle tekens in het e-mailbericht die waren *CHAR*.
* ***kapitaal\_uitvoeren\_lengte\_langste*** is de langste duur van een reeks hoofdletters.
* ***kapitaal\_uitvoeren\_lengte\_gemiddelde*** is de gemiddelde duur van alle reeksen hoofdletters.
* ***kapitaal\_uitvoeren\_lengte\_totale*** is de totale lengte van alle reeksen hoofdletters.
* ***spam*** geeft aan of het e-mailbericht is beschouwd als spam of niet (1 = spam, 0 = geen spam).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>De gegevensset met Microsoft R Open verkennen
Laten we de gegevens controleren en voer enkele eenvoudige machine learning met R. De Data Science VM wordt geleverd met [Microsoft R Open](https://mran.revolutionanalytics.com/open/) vooraf zijn geïnstalleerd. De dankzij de multi-threaded wiskundebibliotheken in deze versie van R biedt betere prestaties dan in verschillende versies met één thread. Microsoft R Open biedt ook reproduceerbaarheid met behulp van een momentopname van het pakket CRAN-opslagplaats.

Klonen als u exemplaren van de codevoorbeelden in dit scenario gebruikt, de **Azure-Machine-Learning-Data-Science** opslagplaats met behulp van git, die vooraf worden geïnstalleerd op de virtuele machine. Vanaf de opdrachtregel van de git-opdracht uitvoeren:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Open een terminalvenster en een nieuw R-sessie starten met de interactieve R-console of vooraf is geïnstalleerd op de machine RStudio gebruiken.


Als u wilt de gegevens te importeren en stel de omgeving, voert u de volgende uit:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Om te zien samenvattende statistieken over elke kolom:

    summary(data)

Voor een andere weergave van de gegevens:

    str(data)

Hiermee geeft u het type van elke variabele en de eerste paar waarden in de gegevensset.

De *spam* kolom als een geheel getal is gelezen, maar het is eigenlijk een categorische variabele (of van meerdere factoren). Instellen van het type:

    data$spam <- as.factor(data$spam)

Voor sommige verkennende analyse, gebruikt u de [ggplot2](https://ggplot2.tidyverse.org/) inpakken, een populaire grafische-bibliotheek voor R dat al is geïnstalleerd op de virtuele machine. Opmerking van de gegevens van de samenvatting weergegeven eerder, hebben we samenvattende statistieken over de frequentie van het teken uitroepteken. Laten we tekenen deze frequenties met de volgende opdrachten:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Omdat de balk met nul, de plot scheeftrekken is, laten we deze verwijderen uit:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Er is een essentieel dichtheid hierboven 1 dat interessant lijkt. Laten we kijken alleen die gegevens:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Deze vervolgens splitsen op basis van spam vs ham:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Deze voorbeelden moeten u soortgelijke grafieken van de andere kolommen om te verkennen van de gegevens in deze inschakelen.

## <a name="train-and-test-an-ml-model"></a>Trainen en testen van een ML-model
Nu gaan we een aantal machine learning-modellen voor het classificeren van de e-mailberichten in de gegevensset bevat van span of ham trainen. We trainen een decision tree-model en een willekeurige forestmodel in deze sectie en vervolgens de juistheid van hun voorspellingen.

> [!NOTE]
> Het pakket rpart (recursieve partitioneren en Regressiestructuren) wordt gebruikt in de volgende code is al geïnstalleerd op de Data Science VM.
>
>

Eerst gaan we de gegevensset te splitsen in trainings- en testset sets:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

En maak vervolgens een beslissingsstructuur voor het classificeren van de e-mailberichten.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Dit is het resultaat:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

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

We gaan ook een willekeurige forest-model. Willekeurige forests trainen van een groot aantal beslisbomen en uitvoer van een klasse die de modus van de classificaties uit alle van de afzonderlijke beslisbomen. Ze bieden een krachtige machine learning-benadering als ze de neiging van een decision tree model naar een gegevensset training overfit verhelpen.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Een model implementeren in Azure Machine Learning Studio
[Azure machine learning Studio](https://studio.azureml.net/) is een Cloud service waarmee u eenvoudig Predictive Analytics modellen kunt bouwen en implementeren. Een van de leuke functies van Azure Machine Learning Studio is de mogelijkheid om een R-functie als webservice te publiceren. Het Azure Machine Learning Studio R-pakket maakt implementatie eenvoudig vanuit onze R-sessie op de DSVM.

Voor het implementeren van de decision tree code uit de vorige sectie, moet u zich aanmeldt bij Azure Machine Learning Studio. U moet uw werkruimte-ID en een verificatietoken aan te melden. Ga als volgt te werk om deze waarden te vinden en de Azure Machine Learning variabelen te initialiseren:

Selecteer **instellingen** in het menu links. Houd er rekening mee uw **WERKRUIMTE-ID**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Selecteer **Autorisatietokens** in het overhead menu Opmerking uw **primaire Autorisatietoken**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Load de **AzureML** verpakt en vervolgens stelt u de waarden van variabelen met uw token en werkruimte-ID in uw R-sessie op de DSVM:

    if(!require("AzureML")) install.packages("AzureML")
    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Laten we het model om deze demonstratie gemakkelijker te implementeren vereenvoudigen. Kies de drie variabelen in de beslissingsstructuur die het dichtst bij de hoofdmap en bouwen van een nieuwe structuur met alleen deze drie variabelen:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

We moeten een voorspellingsfunctie die de functies neemt als invoer en retourneert de voorspelde waarden:

    predictSpam <- function(newdata) {
      predictDF <- predict(model.rpart, newdata = newdata)
      return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }


De functie predictSpam publiceren naar het AzureML met behulp van de **publishWebService** functie:

    spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)


Deze functie haalt de **predictSpam** functie, maakt u een webservice die met de naam **spamWebService** gedefinieerd met invoer en uitvoer en stuurt informatie over het nieuwe eindpunt.

Details bekijken van de meest recente gepubliceerde webservice, met inbegrip van de API-eindpunt en toegang tot sleutels met de opdracht:

    s<-tail(services(ws, name = "spamWebService"), 1)
    ep <- endpoints(ws,s)
    ep

Om het te proberen op de eerste 10 rijen van de test instellen:

    consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Deep Learning-zelfstudies en -scenario's

Naast de voorbeelden framework op basis van is een set van uitgebreide scenario's ook beschikbaar die zijn gevalideerd op de DLVM. Deze scenario's kunt u snel uw ontwikkeling van deep learning-toepassingen in domeinen, zoals afbeeldingen en tekst/language understanding. Meer zelfstudies voor end-to-end in verschillende domeinen en -technologie blijft worden toegevoegd.   


- Het [uitvoeren van Neural-netwerken in verschillende Frameworks](https://github.com/ilkarman/DeepLearningFrameworks): Een uitgebreid overzicht waarin wordt uitgelegd hoe u code van het ene naar het andere Framework migreert. Ook zien hoe om te vergelijken model en de prestaties van de tijd van frameworks uitvoeren. 

- [Een hand leiding met instructies voor het bouwen van een end-to-end oplossing voor het detecteren van producten in installatie kopieën](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Image detectie is een techniek waarmee objecten binnen afbeeldingen kunnen worden gevonden en geclassificeerd. Deze technologie is de mogelijkheid om enorme voordelen in veel echte leven domeinen. Bijvoorbeeld, kunnen detailhandelaren deze techniek gebruiken om te bepalen welk product een klant is opgehaald uit de plank. Deze informatie helpt op zijn beurt winkels productinventaris beheren. 

- [Deep learning voor audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) in deze zelfstudie leert u hoe u met het trainen van een deep learning-model voor detectie audio gebeurtenis op de [stedelijke geluiden gegevensset](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) en biedt een overzicht van hoe u met audiogegevens werkt.

- [Classificatie van tekst documenten](https://github.com/anargyri/lstm_han): In dit scenario ziet u hoe u twee verschillende Neural-netwerk architecturen bouwt en traint: Hiërarchische aandacht voor netwerk en LSTM-netwerk (Long Short Term Memory). Deze neurale netwerken voor het gebruik van de Keras-API voor deep learning voor het classificeren van documenten. Keras is een front-end tot drie van de populairste diep gaande lessen: Microsoft Cognitive Toolkit, tensor flow en Theano.

## <a name="use-other-tools-available"></a>Andere hulpprogramma's gebruiken
De resterende secties laten zien hoe u sommige van de beheerhulpprogramma's geïnstalleerd op de Linux Data Science VM. Hier volgt de lijst met hulpprogramma's besproken:

* XGBoost
* Python
* Jupyterhub
* Rammelaar
* PostgreSQL & Squirrel SQL
* SQL Server datawarehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) is een hulpprogramma waarmee de implementatie van een snelle en nauwkeurige boosted structuur.

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

XGBoost kunnen ook aanroepen vanuit python of een opdrachtregel.

## <a name="python"></a>Python
Voor het ontwikkelen met behulp van Python, zijn de distributies Anaconda Python 2.7 en 3.5 geïnstalleerd in de DSVM.

> [!NOTE]
> Bevat de distributie Anaconda [Conda](https://conda.pydata.org/docs/index.html), die kan worden gebruikt om te maken van aangepaste omgevingen voor Python met verschillende versies en/of pakketten die erin zijn geïnstalleerd.
>
>

Laten we enkele van de gegevensset spambase gelezen en de e-mailberichten classificeren met support vector machines in scikit-informatie:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Om voorspellingen te doen:

    clf.predict(X.ix[0:20, :])

Als u wilt laten zien hoe u een AzureML-eindpunt publiceert, laten we een eenvoudigere model de drie variabelen zoals we hebben gedaan wanneer we het model R eerder hebt gepubliceerd.

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Het model op AzureML publiceren:

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
> Dit is alleen beschikbaar voor python 2.7 en wordt nog niet ondersteund op 3.5. Uitvoeren met **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
De distributie Anaconda in de DSVM wordt geleverd met een Jupyter-notebook, een omgeving met meerdere platforms voor het delen van code voor Python, R of Julia en -analyse. De Jupyter-notebook toegankelijk is via JupyterHub. U aanmelden met uw lokale Linux-gebruikersnaam en wachtwoord op ***https://\<VM DNS-naam of IP-adres\>: 8000 /***. Alle configuratiebestanden voor JupyterHub vindt u in de directory **/etc/jupyterhub**.

> [!NOTE]
> Gebruik van de Python Package Manager (via de `pip` opdracht) van een Jupyter-notebook in de huidige kernel, de volgende opdracht kan worden gebruikt in de codecel, bijvoorbeeld:
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 
> 
> [!NOTE]
> Met het Conda-installatieprogramma (via de `conda` opdracht) van een Jupyter-notebook in de huidige kernel, de volgende opdracht kan worden gebruikt in de codecel, bijvoorbeeld:
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Verschillende voorbeeldnotitieblokken zijn al geïnstalleerd op de virtuele machine:

* Zie de [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) voor een voorbeeld van Python-notebook.
* Zie [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) voor een voorbeeld van een **R** notebook.
* Zie de [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) voor een ander voorbeeld **Python** notebook.

> [!NOTE]
> De Julia-taal is ook beschikbaar via de opdrachtregel op de Linux Data Science VM.
>
>

## <a name="rattle"></a>Rammelaar
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (de R analytische hulpprogramma om te leren eenvoudig) is een grafische R-hulpprogramma voor gegevensanalyse. Er is een intuïtieve interface waarmee u eenvoudig om te laden, verkennen, en gegevens transformeren en bouwen en evalueren van modellen.  Het artikel [Rattle: Een gegevens analyse-GUI voor](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) R biedt een overzicht van de functies.

Installeren en Rammelaar starten met de volgende opdrachten:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Installatie is niet vereist op de DSVM. Maar Rammelaar mogelijk gevraagd u extra pakketten te installeren wanneer deze wordt geladen.
>
>

Rammelaar maakt gebruik van een tabblad gebaseerde interface. De meeste van de tabbladen komen overeen met de stappen in de [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), zoals het laden van gegevens of wilt u deze. Het data science process loopt van links naar rechts de tabbladen. Maar het laatste tabblad bevat een logboek van de R-opdrachten uitvoeren door Rammelaar.

Laden en de gegevensset configureren:

* Voor het laden van het bestand, selecteer de **gegevens** tabblad vervolgens
* Kies de kiezer naast **Filename** en kies **spambaseHeaders.data**.
* Het bestand laden. Selecteer **Execute** in de bovenste rij knoppen. U ziet een overzicht van elke kolom, met inbegrip van het geïdentificeerde gegevenstype, of het nu invoer, doel of ander type variabele en het aantal unieke waarden.
* Rammelaar heeft geïdentificeerd de **spam** kolom als het doel. Selecteer de kolom spam en stel vervolgens de **gegevens doeltype** naar **Categoric**.

De gegevens verkennen:

* Selecteer de **verkennen** tabblad.
* Klik op **samenvatting**, klikt u vervolgens **Execute**om enkele gegevens over de typen variabelen en sommige samenvattende statistieken.
* Als u andere typen statistieken over elke variabele, selecteert u andere opties, zoals **beschrijving** of **basisbeginselen**.

De **verkennen** tabblad kunt u voor het genereren veel inzichtelijke grafieken. Om te tekenen een histogram van de gegevens:

* Selecteer **distributies**.
* Controleer **Histogram** voor **word_freq_remove** en **word_freq_you**.
* Selecteer **Uitvoeren**. Hier ziet u beide dichtheid grafieken in een venster van één grafiek waarin het is duidelijk weergegeven dat het woord 'u' veel vaker in e-mailberichten dan 'verwijderen'.

De correlatie-grafieken zijn ook interessant. Een te maken:

* Kies **correlatie** als de **Type**, klikt u vervolgens
* Selecteer **Uitvoeren**.
* Rammelaar waarschuwt u dat het wordt aanbevolen maximaal 40 variabelen. Selecteer **Ja** om de grafiek weer te geven.

Er zijn enkele interessante correlaties die zijn beschikbaar: "technologie" wordt ten zeerste gecorreleerd met "HP" en 'labs', bijvoorbeeld. Het is ook sterk gecorreleerd met de '650', omdat het netnummer van de gegevensset donoren 650.

De numerieke waarden voor de correlatie tussen woorden zijn beschikbaar in het venster verkennen. Het is interessant om te opmerking, bijvoorbeeld dat "technologie" negatief worden gecorreleerd met 'uw' en 'geld'.

Rammelaar kunt gebruiken om de gegevensset voor het afhandelen van enkele veelvoorkomende problemen. Bijvoorbeeld, kunt u oorspronkelijke functies, ontbrekende waarden worden toegerekend, uitbijters verwerken en variabelen of opmerkingen met ontbrekende gegevens verwijderen. Rammelaar kan ook identificeren aan de regels van de koppeling tussen opmerkingen en/of variabelen. Deze tabbladen zijn buiten het bereik van deze Introductie.

Rammelaar kan ook cluster analyses uitvoeren. Laten we uitsluiten sommige functies om de uitvoer eenvoudiger te lezen. Op de **gegevens** tabblad **negeren** naast elk van de variabelen, behalve deze tien items:

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

Ga vervolgens terug naar de **Cluster** tabblad **KMeans**, en stel de *aantal clusters* tot en met 4. Vervolgens **uitvoeren**. De resultaten worden weergegeven in het uitvoervenster weergegeven. Een cluster high-frequency van "george" en "hp" heeft en is waarschijnlijk een legitieme zakelijke e-mailadres.

Een eenvoudige decision tree machine learning-model bouwen:

* Selecteer de **Model** tabblad
* Kies **structuur** als de **Type**.
* Selecteer **Execute** om weer te geven van de structuur van de vorm van tekst in het uitvoervenster weergegeven.
* Selecteer de **tekenen** knop om een grafische versie weer te geven. Dit lijkt erg op de structuur die we eerder met verkregen *rpart*.

Een van de leuke functies van Rammelaar is de mogelijkheid voor het uitvoeren van verschillende methoden voor machine learning en ze snel kunt evalueren. Hier volgt de procedure:

* Kies **alle** voor de **Type**.
* Selecteer **Uitvoeren**.
* Nadat deze is voltooid, kunt u een enkel **Type**, bijvoorbeeld **SVM**, en de resultaten te bekijken.
* U kunt ook vergelijken met de prestaties van de modellen op de validatie is ingesteld met behulp van de **evalueren** tabblad. Bijvoorbeeld, de **fout Matrix** selectie ziet u de verwarringsmatrix, algemene fout en gemiddelde klasse fout voor elk model voor de validatie.
* U kunt ook tekenen ROC-curve, gevoeligheid analyses uitvoeren en andere soorten evaluaties model.

Zodra u klaar bent met het ontwikkelen van modellen, selecteert u de **Log** tabblad om de R-code uitvoeren door Rammelaar tijdens uw sessie. U kunt selecteren de **exporteren** knop op te slaan.

> [!NOTE]
> Er is een fout in de huidige release van Rammelaar. Als u het script wilt wijzigen of gebruiken om de stappen later te herhalen, moet u een #-teken invoegen voor het *exporteren van dit logboek...* in de tekst van het logboek.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
De DSVM wordt geleverd met PostgreSQL is geïnstalleerd. PostgreSQL is een geavanceerde, open-source relationele database. Deze sectie wordt beschreven hoe u onze gegevensset spam in PostgreSQL laden en vervolgens query's uitvoeren.

Voordat u de gegevens laadt kunt, moet u wachtwoordverificatie van de localhost toestaan. Bij een opdrachtprompt:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Aan de onderkant van het configuratiebestand worden verschillende regels die informatie over de toegestane verbindingen:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Hiermee wijzigt u de regel 'IPv4 lokale verbindingen' voor het gebruik van md5 in plaats van ident, zodat we kunnen zich aanmelden met een gebruikersnaam en wachtwoord:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

En de postgres-service opnieuw starten:

    sudo systemctl restart postgresql

Als u wilt starten psql, een terminal interactief voor PostgreSQL, als de gebruiker ingebouwde postgres, voer de volgende opdracht vanaf een opdrachtprompt:

    sudo -u postgres psql

Maak een nieuw gebruikersaccount, met behulp van de dezelfde gebruikersnaam als het Linux-account u momenteel bent aangemeld als en wijs hieraan een wachtwoord:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Vervolgens aan te melden bij psql als de gebruiker:

    psql

En de gegevens in een nieuwe database importeren:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Nu gaan we de gegevens verkennen en enkele query's uitvoeren met **Squirrel SQL**, een grafisch hulpprogramma waarmee u interactie met databases via een JDBC-stuurprogramma.

Als u wilt beginnen, start u Squirrel SQL in het menu toepassingen. Het stuurprogramma instellen:

* Selecteer **Windows**, klikt u vervolgens **stuurprogramma's weergeven**.
* Met de rechtermuisknop op **PostgreSQL** en selecteer **stuurprogramma wijzigen**.
* Selecteer **Extra klasse pad**, klikt u vervolgens **toevoegen**.
* Voer ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** voor de **bestandsnaam** en
* Selecteer **Openen**.
* Kies lijst van stuurprogramma's, en selecteer vervolgens **org.postgresql.Driver** in **klassenaam**, en selecteer **OK**.

De verbinding met de lokale server instellen:

* Selecteer **Windows**, klikt u vervolgens **aliassen weergeven.**
* Kies de **+** knop om te maken van een nieuwe alias.
* Geef het de naam *Spam database*, kiest u **PostgreSQL** in de **stuurprogramma** vervolgkeuzelijst.
* De URL ingesteld op *jdbc:postgresql://localhost/spam*.
* Voer uw *gebruikersnaam* en *wachtwoord*.
* Klik op **OK**.
* Om te openen de **verbinding** venster, dubbelklikt u op de ***Spam database*** alias.
* Selecteer **Verbinden**.

Sommige query's uitvoeren:

* Selecteer de **SQL** tabblad.
* Voer een eenvoudige query zoals `SELECT * from data;` in het tekstvak query aan de bovenkant van het SQL-tabblad.
* Druk op **Ctrl + Enter** uit te voeren. Squirrel SQL wordt standaard de eerste 100 rijen geretourneerd uit uw query.

Er zijn veel meer query's die u kunt uitvoeren om deze gegevens te verkennen. Hoe werkt bijvoorbeeld de frequentie van het woord *maken* verschillen tussen spam en ham?

    SELECT avg(word_freq_make), spam from data group by spam;

Of wat de kenmerken van e-mailbericht dat vaak bevatten *3d*?

    SELECT * from data order by word_freq_3d desc;

De meeste e-mailberichten waarvoor een hoge kans *3d* zijn blijkbaar spam, zodat deze een handige functie voor het bouwen van een Voorspellend model voor het classificeren van de e-mailberichten kan worden.

Als u uitvoeren van machine learning met gegevens die zijn opgeslagen in een PostgreSQL-database wilt, kunt u overwegen [MADlib](https://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server datawarehouse
Azure SQL Data Warehouse is een schaalbare clouddatabase die geschikt is voor het verwerken van grote hoeveelheden relationele en/of niet-relationele gegevens. Zie voor meer informatie, [wat is Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Verbinding maken met het datawarehouse en de tabel maken, voer de volgende opdracht uit vanaf een opdrachtprompt:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Klik op de sqlcmd-opdrachtprompt:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Gegevens kopiëren met bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> De regeleinden in het gedownloade bestand zijn Windows-stijl, maar bcp wordt verwacht dat UNIX-stijl, dus moet u bcp die met de vlag - r.
>
>

En query's uitvoeren met sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

U kunt ook een query met Squirrel SQL. Uitvoeren van gelijksoortige stappen voor PostgreSQL, met de Microsoft MSSQL Server JDBC-stuurprogramma die kan worden gevonden in ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Volgende stappen
Zie voor een overzicht van onderwerpen waarin wordt uitgelegd dat u de taken die deel uitmaken van de Data Science process in Azure, [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Zie voor een beschrijving van andere end-to-end-scenario's die laten zien van de stappen in het Team Data Science Process voor specifieke scenario's, [Team Data Science Process walkthroughs](../team-data-science-process/walkthroughs.md). De scenario's laten ook zien hoe u cloud en on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen.
