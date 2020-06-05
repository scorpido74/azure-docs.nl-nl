---
title: 'Zelfstudie: Een machine learning-model maken met R'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie gebruikt u de Azure Machine Learning R SDK voor het maken van een logistiek regressiemodel dat de kans op een dodelijk slachtoffer bij een auto-ongeluk voorspelt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: dea5b3fb6cf20924666668e59e370399664d6b28
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684743"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model"></a>Zelfstudie: Een machine learning-model maken met R
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie gebruikt u de Azure Machine Learning R SDK voor het maken van een logistiek regressiemodel dat de kans op een dodelijk slachtoffer bij een auto-ongeluk voorspelt. U ziet hoe de cloudresources van Azure Machine Learning samenwerken met R om een schaalbare omgeving te bieden voor het trainen en implementeren van een model.  

In deze zelfstudie voert u de volgende taken uit:
> [!div class="checklist"]
> * Een Azure Machine Learning-werkruimte maken
> * Een notebook-map klonen met de bestanden die nodig zijn om deze zelfstudie uit te voeren in uw werkruimte
> * RStudio openen vanuit uw werkruimte
> * Gegevens laden en voorbereiden voor training
> * Gegevens uploaden naar een gegevensarchief, zodat deze beschikbaar zijn voor training op afstand
> * Een rekenresource maken om het model op afstand te trainen
> * Een `caret`-model trainen om de kans op een dodelijk slachtoffer te voorspellen
> * Een voorspellingseindpunt implementeren
> * Het model testen vanuit R

Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).


## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werkruimte is een basisblok in de cloud dat u gebruikt voor het experimenteren, trainen en implementeren van machine learning-modellen. De klasse bindt uw Azure-abonnement en resourcegroep aan een eenvoudig te verbruiken object in de service. 

U maakt een werkruimte via de Azure-portal, een webconsole om uw Azure-resources te beheren. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Noteer uw **werkruimte** en **abonnement**. U hebt deze nodig om ervoor te zorgen dat u uw experiment op de juiste plek maakt. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Een notebook-map klonen

In dit voorbeeld wordt de cloudnotebook-server in uw werkruimte gebruikt voor een installatieloze en vooraf geconfigureerde ervaring. Gebruik [uw eigen omgeving](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) als u liever controle over uw omgeving, pakketten en afhankelijkheden hebt.

U doorloopt de volgende stappen voor het voorbereiden en uitvoeren van het experiment in Azure Machine Learning Studio, een geconsolideerde interface met hulpmiddelen voor machine learning waar gegevenswetenschappers, ongeacht hun vaardigheidsniveaus, scenario's kunnen uitvoeren.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com/).

1. Selecteer uw abonnement en de werkruimte die u heeft gemaakt.

1. Selecteer **Notebooks** aan de linkerkant.

1. Open de map **Samples**.

1. Open de map **R**.

1. Open de map met een versienummer.  Dit nummer geeft de huidige release van de R SDK aan.

1. Selecteer **...** rechts van de map **vignettes** en selecteer vervolgens **Klonen**.

    ![Map klonen](media/tutorial-1st-r-experiment/clone-folder.png)

1. Er wordt een lijst met mappen weergegeven, met de verschillende gebruikers die toegang hebben tot de werkruimte.  Selecteer uw map om de map **vignettes** daar te klonen.

## <a name="a-nameopenopen-rstudio"></a><a name="open">RStudio openen

Gebruik RStudio in een rekeninstantie of op een Notebook-VM om deze zelfstudie uit te voeren.  

1. Selecteer **Compute** aan de linkerkant.

1. Voeg een rekenresource toe als deze nog niet bestaat.

1. Als de rekenresource wordt uitgevoerd, gebruikt u de koppeling **RStudio** om RStudio te openen.

1. In RStudio vindt u de map *vignettes* een paar niveaus onder *Users* in het gedeelte **Files** rechtsonder.  Selecteer onder *vignettes*de map *train-and-deploy-to-aci* om de bestanden te vinden die u nodig hebt in deze zelfstudie.

> [!Important]
> De rest van dit artikel bevat dezelfde inhoud als die u ziet in het bestand *train-and-deploy-to-aci.Rmd*. Als u ervaring hebt met RMarkdown, kunt u ook de code uit dat bestand gebruiken.  Of u kunt de codefragmenten uit dat bestand of vanuit dit artikel kopiëren/plakken in een R-script of op de opdrachtregel.  


## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen
De volgende acties zijn vereist om uw ontwikkelomgeving te configureren voor deze zelfstudie:

* De vereiste pakketten installeren
* Verbinding maken met een werkruimte, zodat uw rekeninstantie kan communiceren met externe resources
* Een experiment maken om alle runs (uitvoeringen) bij te houden
* Een extern rekendoel maken voor trainingsdoeleinden

### <a name="install-required-packages"></a>De vereiste pakketten installeren

 * Installeer de meest recente versie van CRAN.

    ```R
    # install the latest version from CRAN
    install.packages("azuremlsdk")
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```
    
* Of installeer de ontwikkelversie van GitHub.

    ```R
    # or install the development version from GitHub
    remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```

Importeer nu het pakket **azuremlsdk**.

```R
library(azuremlsdk)
```

De trainings-en scorescripts (`accidents.R` en `accident_predict.R`) hebben een aantal aanvullende afhankelijkheden. Als u van plan bent om deze scripts lokaal uit te voeren, zorg er dan voor dat u ook over die vereiste pakketten beschikt.

### <a name="load-your-workspace"></a>De werkruimte laden
Maak een werkruimte-object vanuit uw bestaande werkruimte. Met de volgende code worden de werkruimtegegevens uit het bestand **config.json** geladen. U kunt een werkruimte ook ophalen met behulp van [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html).

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Een experiment maken
Een Azure ML-experiment houdt een groep van runs bij, meestal uit hetzelfde trainingsscript. Maak een experiment om de runs bij te houden voor het trainen van het caret-model op basis van de gegevens van ongelukken.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Een rekendoel maken
Met Azure Machine Learning Compute (AmlCompute), een beheerde service, kunnen gegevenswetenschappers Machine Learning-modellen trainen op clusters met virtuele Azure-machines. Voorbeelden hiervan zijn virtuele machines met GPU-ondersteuning. In deze zelfstudie maakt u een AmlCompute-cluster met één knooppunt als uw trainingsomgeving. Met de onderstaande code maakt u het rekencluster als dit nog niet bestaat in uw werkruimte.

Mogelijk moet u enkele minuten wachten totdat het rekencluster is ingericht als het nog niet bestaat.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Gegevens voorbereiden voor training
In deze zelfstudie worden gegevens gebruikt van de Amerikaanse [National Highway Traffic Safety Administration](https://cdan.nhtsa.gov/tsftables/tsfar.htm) (met dank aan [Mary C. Meyer en Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Deze gegevensset bevat gegevens van meer dan 25.000 auto-ongelukken in de VS, met variabelen die u kunt gebruiken om de kans op een dodelijk slachtoffer te voorspellen. Importeer de gegevens eerst in R en transformeer ze in een nieuw gegevensframe `accidents` voor analyse en exporteer ze daarna naar een `Rdata`-bestand.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Gegevens uploaden naar het gegevensarchief
Upload gegevens naar de cloud, zodat deze toegankelijk zijn vanuit uw externe trainingsomgeving. Elke Azure Machine Learning-werkruimte heeft een standaardgegevensarchief waarin de verbindingsgegevens worden opgeslagen voor de Azure-blobcontainer die is ingericht in het opslagaccount dat is gekoppeld aan de werkruimte. Met de volgende code worden de gegevens van ongelukken die u hierboven hebt gemaakt, geüpload naar het gegevensarchief.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Een model trainen

Voor deze zelfstudie past u een logistiek regressiemodel toe op de geüploade gegevens met behulp van uw externe rekencluster. Om een taak te verzenden, moet u het volgende doen:

* Het trainingsscript voorbereiden
* Een estimator maken
* De taak verzenden

### <a name="prepare-the-training-script"></a>Het trainingsscript voorbereiden
In de map met de zelfstudie staat ook een trainingsscript met de naam `accidents.R`. Let op de volgende details **in het trainingsscript** die zijn opgenomen om Azure Machine Learning in te zetten voor training:

* Het trainingsscript accepteert het argument `-d` om de map met de trainingsgegevens te vinden. Als u de taak definieert en later verzendt, verwijst u naar het gegevensarchief met dit argument. Azure ML koppelt de opslagmap aan het externe cluster voor de trainingstaak.
* Het trainingsscript registreert de uiteindelijke nauwkeurigheid als een meetwaarde voor de uitvoeringsrecord in Azure ML met behulp van `log_metric_to_run()`. De Azure ML SDK biedt een set logboekregistratie-API's voor het vastleggen van verschillende metrische gegevens tijdens trainingsruns. Deze metrische gegevens worden vastgelegd en blijven beschikbaar via de uitvoeringsrecord van het experiment. De metrische gegevens kunnen vervolgens op ieder gewenst moment worden geopend of weergegeven op de pagina met uitvoeringsdetails in [Studio](https://ml.azure.com). Raadpleeg de [naslaginformatie](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) voor de volledige set met methoden voor logboekregistratie`log_*()`.
* Het trainingsscript slaat uw model op in een map met de naam **outputs**. De map `./outputs` wordt op een speciale manier behandeld door Azure ML. Tijdens de training worden bestanden die worden weggeschreven naar `./outputs`, automatisch door Azure ML geüpload naar de uitvoeringsrecord en blijven de bestanden beschikbaar als artefacten. Door het getrainde model op te slaan in `./outputs`, kunt u uw modelbestand zelfs openen en ophalen als de run is voltooid en u geen toegang meer hebt tot uw externe trainingsomgeving.

### <a name="create-an-estimator"></a>Een estimator maken

Een Azure ML-estimator zorgt voor de inkapseling van de configuratiegegevens voor een run die nodig zijn voor het uitvoeren van een trainingsscript op het rekendoel. Azure ML-runs worden op het opgegeven rekendoel uitgevoerd als taken in containers. De Docker-installatiekopie die wordt gemaakt voor uw trainingstaak bevat standaard R, de Azure ML SDK en een set veelgebruikte R-pakketten. Bekijk hier de volledige lijst met standaardpakketten die zijn opgenomen.

Als u de estimator wilt maken, definieert u het volgende:

* De map die de scripts bevat die nodig zijn voor de training (`source_directory`). Alle bestanden in deze map worden geüpload naar de clusterknooppunten voor uitvoering. De map moet uw trainingsscript bevatten evenals eventuele andere vereiste scripts.
* Het trainingsscript dat wordt uitgevoerd (`entry_script`).
* Het rekendoel (`compute_target`), in dit geval het AmlCompute-cluster dat u eerder hebt gemaakt.
* De vereiste parameters uit het trainingsscript (`script_params`). Azure ML voert uw trainingsscript uit als een opdrachtregelscript met `Rscript`. In deze zelfstudie geeft u één argument op voor het script, het koppelpunt voor de gegevensmap, dat u kunt aangeven met `ds$path(target_path)`.
* Eventuele omgevingsafhankelijkheden die vereist zijn voor de training. De standaard-Docker-installatiekopie die voor de training is gebouwd, bevat al de drie pakketten (`caret`, `e1071`en `optparse`) die nodig zijn in het trainingsscript.  U hoeft dus geen aanvullende gegevens op te geven. Als u R-pakketten gebruikt die niet standaard zijn opgenomen, gebruikt u de parameter `cran_packages` van de estimator om extra CRAN-pakketten toe te voegen. Raadpleeg de naslaginformatie voor [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) voor de volledige set met opties die u kunt configureren.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>De taak verzenden naar het externe cluster

Verstuur de taak ten slotte voor uitvoering in uw cluster. `submit_experiment()` retourneert een Run-object dat u vervolgens gebruikt voor interactie met de run. In totaal duurt de eerste run **ongeveer tien minuten**. Maar voor latere runs wordt dezelfde Docker-installatiekopie opnieuw gebruikt als de scriptafhankelijkheden niet zijn gewijzigd.  In dit geval wordt de installatiekopie in de cache opgeslagen en is de opstarttijd van de container veel sneller.

```R
run <- submit_experiment(exp, est)
```

U kunt de details van de run bekijken in RStudio Viewer. Als u daar op de koppeling Web View klikt, gaat u naar Azure Machine Learning Studio, waar u de run kunt bekijken in de gebruikersinterface.

```R
view_run_details(run)
```

Training van het model gebeurt op de achtergrond. Wacht totdat het trainen van het model is voltooid voordat u andere code uitvoert.

```R
wait_for_run_completion(run, show_output = TRUE)
```

U, en collega's met toegang tot de werkruimte, kunnen tegelijkertijd meerdere experimenten indienen. Azure ML zorgt voor de planning van de taken in het rekencluster. U kunt het cluster zelfs configureren om automatisch op te schalen naar meerdere knooppunten en terug te schalen wanneer er geen rekentaken meer in de wachtrij staan. Deze configuratie is een rendabele manier voor teams om rekenresources te delen.

## <a name="retrieve-training-results"></a>Trainingsresultaten ophalen
Zodra de training van uw model klaar is, hebt u toegang tot de artefacten van uw taak die zijn opgeslagen in de uitvoeringsrecord, inclusief eventuele metrische gegevens die zijn vastgelegd en het uiteindelijke getrainde model.

### <a name="get-the-logged-metrics"></a>De vastgelegde metrische gegevens ophalen
In het trainingsscript `accidents.R` hebt u metrische gegevens uit uw model vastgelegd: de nauwkeurigheid van de voorspellingen in de trainingsgegevens. U kunt metrische gegevens bekijken in de [studio](https://ml.azure.com) of ze als volgt uitpakken naar de lokale sessie als een R-lijst:

```R
metrics <- get_run_metrics(run)
metrics
```

Als u meerdere experimenten hebt uitgevoerd (door bijvoorbeeld verschillende variabelen, algoritmen of hyperparameters te gebruiken), kunt u de metrische gegevens van elke run gebruiken om te vergelijken en het model kiezen dat u in productie gaat gebruiken.

### <a name="get-the-trained-model"></a>Het getrainde model ophalen
U kunt het getrainde model ophalen en de resultaten bekijken in uw lokale R-sessie. Met de volgende code wordt de inhoud van de map `./outputs` gedownload, met onder andere het modelbestand.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

U ziet enkele factoren die bijdragen aan een toename van de geschatte waarschijnlijkheid van een dodelijk ongeluk:

* hogere botsingssnelheid 
* mannelijk bestuurder
* oudere inzittende
* passagier

U ziet een lagere waarschijnlijkheid van overlijden bij:

* aanwezigheid van airbags
* aanwezigheids van gordels
* frontale aanrijding 

Het bouwjaar van het voertuig heeft geen significant effect.

U kunt dit model gebruiken om nieuwe voorspellingen te doen:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Als webservice implementeren

Met uw model kunt u het risico van overlijden als gevolg van een auto-ongeluk voorspellen. Gebruik Azure ML om uw model te implementeren als een voorspellingsservice. In deze zelfstudie gaat u de webservice implementeren in [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Het model registreren

Registreer het model dat u naar uw werkruimte hebt gedownload eerst met [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html). Een geregistreerd model kan bestaan uit elke verzameling bestanden, maar in dit geval is het model-object van R voldoende. Azure ML gebruikt het geregistreerde model voor implementatie.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>De afleidingsafhankelijkheden definiëren
Als u een webservice voor uw model wilt maken, moet u eerst een scorescript (`entry_script`) maken. Dit is een R-script dat variabele waarden (in JSON-indeling) accepteert als invoer en dat een voorspelling van uw model uitvoert. Gebruik voor deze zelfstudie het meegeleverde scorebestand `accident_predict.R`. Het scorescript moet een methode `init()` bevatten waarmee uw model wordt geladen en een functie wordt geretourneerd die het model gebruikt om een voorspelling te geven op basis van de invoergegevens. Raadpleeg de [documentatie](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) voor meer informatie.

Definieer vervolgens een Azure ML-**omgeving** voor de pakketafhankelijkheden van uw script. Met een omgeving geeft u R-pakketten (van CRAN of ergens anders) op die nodig zijn om uw script uit te voeren. U kunt ook de waarden van omgevingsvariabelen opgeven waarnaar het script kan verwijzen om het gedrag te wijzigen. Azure ML bouwt standaard dezelfde standaard Docker-installatiekopie die met de estimator is gebruikt voor trainingsdoeleinden. Aangezien voor de zelfstudie geen speciale vereisten gelden, kunt u een omgeving maken zonder speciale kenmerken.

```R
r_env <- r_environment(name = "basic_env")
```

Als u in plaats daarvan uw eigen Docker-installatiekopie wilt gebruiken voor implementatie, geeft u de parameter `custom_docker_image` op. Raadpleeg de naslaginformatie voor [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) voor de volledige set met opties voor het definiëren van een omgeving.

U hebt nu alles wat u nodig hebt om een **inference config** te maken voor het inkapselen van uw scorescript en omgevingsafhankelijkheden.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Implementeren naar ACI
In deze zelfstudie implementeert u de service naar ACI. Met deze code wordt één container ingericht om te reageren op binnenkomende aanvragen, wat geschikt is voor testen en lichte belastingen. Zie [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) voor aanvullende configureerbare opties. (Voor implementaties op productieschaal kunt u ook [implementeren naar Azure Kubernetes Service](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

U implementeert uw model nu als een webservice. De implementatie **kan enkele minuten duren**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>De geïmplementeerde service testen

Nu uw model is geïmplementeerd als een service, kunt u de service vanuit R testen met behulp van [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html).  Geef een nieuwe set gegevens op voor de voorspelling, converteer deze naar JSON en verzend de gegevens naar de service.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

U kunt ook het HTTP-eindpunt van de webservice ophalen, dat REST-clientaanroepen accepteert. Dit eindpunt kan worden gedeeld met iedereen die de webservice wil testen of wil integreren in een toepassing.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resources als u deze niet meer nodig hebt. Verwijder geen resources die u nog van plan bent te gaan gebruiken. 

De webservice verwijderen:
```R
delete_webservice(aci_service)
```

Het geregistreerde model verwijderen:
```R
delete_model(model)
```

Het rekencluster verwijderen:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Alles verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Bekijk de eigenschappen van de werkruimte en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Nu u uw eerste experiment van Azure Machine Learning in R hebt voltooid, wilt u vast meer weten over de [Azure Machine Learning SDK voor R](https://azure.github.io/azureml-sdk-for-r/index.html).

* Lees meer over Azure Machine Learning met R in de voorbeelden in de andere *vignettes*-mappen.
