---
title: 'Zelfstudie: R gebruiken om een machine learning-model te maken'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie gebruikt u de Azure Machine Learning R SDK om een logistiek regressiemodel te maken dat de kans op een dodelijk ongeval voorspelt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 5b1c6561519bc25c2b7ac77f0a25eff89413a07a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256481"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model"></a>Zelfstudie: R gebruiken om een machine learning-model te maken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie gebruikt u de Azure Machine Learning R SDK om een logistiek regressiemodel te maken dat de kans op een dodelijk ongeval voorspelt. U ziet hoe de Azure Machine Learning-cloudresources werken met R om een schaalbare omgeving te bieden voor het trainen en implementeren van een model.  

In deze zelfstudie voert u de volgende taken uit:
> [!div class="checklist"]
> * Een Azure Machine Learning-werkruimte maken
> * Een notitieblokmap klonen met de bestanden die nodig zijn om deze zelfstudie in uw werkruimte uit te voeren
> * Open RStudio vanuit uw werkruimte
> * Gegevens laden en voorbereiden op training
> * Gegevens uploaden naar een datastore, zodat deze beschikbaar zijn voor training op afstand
> * Een rekenbron maken om het model op afstand te trainen
> * Train `caret` een model om de kans op dodelijk ongeval te voorspellen
> * Een voorspellingseindpunt implementeren
> * Test het model van R

Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)


## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werkruimte is een basisbron in de cloud die u gebruikt om machine learning-modellen te experimenteren, te trainen en te implementeren. Het koppelt uw Azure-abonnement en resourcegroep aan een gemakkelijk verbruikt object in de service. 

U maakt een werkruimte via de Azure-portal, een webconsole voor het beheren van uw Azure-bronnen. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Let op uw **werkruimte** en **abonnement.** U hebt deze nodig om ervoor te zorgen dat u uw experiment op de juiste plaats maakt. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Een notitieblokmap klonen

In dit voorbeeld wordt de cloudnotebookserver in uw werkruimte gebruikt voor een installatievrije en vooraf geconfigureerde ervaring. Gebruik [uw eigen omgeving](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) als u liever controle hebt over uw omgeving, pakketten en afhankelijkheden.

U voltooit de volgende experimentset-up en voert stappen uit in Azure Machine Learning-studio, een geconsolideerde interface met machine learning-tools om data science-scenario's uit te voeren voor praktijkvoorbeelden van alle vaardigheidsniveaus.

1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com/).

1. Selecteer uw abonnement en de werkruimte die u hebt gemaakt.

1. Selecteer **Notitieblokken** aan de linkerkant.

1. Open de map **Voorbeelden.**

1. Open de **Map R.**

1. Open de map met een versienummer erop.  Dit nummer vertegenwoordigt de huidige release voor de R SDK.

1. Selecteer de **map "..."** rechts van de map **vignetten** en selecteer **Vervolgens Kloon**.

    ![Kloonmap](media/tutorial-1st-r-experiment/clone-folder.png)

1. In een lijst met mappen wordt elke gebruiker weergegeven die toegang heeft tot de werkruimte.  Selecteer uw map om de **map vignetten** daar te klonen.

## <a name="a-nameopenopen-rstudio"></a><a name="open">RStudio openen

Gebruik RStudio op een rekeninstantie of Notebook VM om deze zelfstudie uit te voeren.  

1. Selecteer **Berekenen** aan de linkerkant.

1. Voeg een compute resource toe als deze nog niet bestaat.

1. Zodra de compute is uitgevoerd, gebruikt u de **RStudio-koppeling** om RStudio te openen.

1. In RStudio is uw *vignettenmap* een paar niveaus lager dan *gebruikers* in de sectie **Bestanden** rechtsonder.  Selecteer *onder vignetten*de *map train-and-deploy-to-aci* om de bestanden te vinden die nodig zijn in deze zelfstudie.

> [!Important]
> De rest van dit artikel bevat dezelfde inhoud als u ziet in de *trein-en-deploy-to-aci. Rmd-bestand.* Als u ervaring hebt met RMarkdown, u de code uit dat bestand gebruiken.  Of u de codefragmenten van daaruit kopiëren/plakken, of uit dit artikel naar een R-script of de opdrachtregel.  


## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen
De instelling voor uw ontwikkelingswerk in deze zelfstudie bevat de volgende acties:

* De vereiste pakketten installeren
* Verbinding maken met een werkruimte, zodat uw rekeninstantie kan communiceren met externe bronnen
* Een experiment maken om uw runs bij te houden
* Een extern rekendoel maken dat u gebruiken voor training

### <a name="install-required-packages"></a>De vereiste pakketten installeren

 * Installeer de nieuwste versie van CRAN.

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

Ga nu verder en importeer het **azuremlsdk-pakket.**

```R
library(azuremlsdk)
```

De trainings- en`accidents.R` `accident_predict.R`scoringsscripts (en) hebben een aantal extra afhankelijkheden. Als u van plan bent deze scripts lokaal uit te voeren, moet u er ook voor zorgen dat u de vereiste pakketten hebt.

### <a name="load-your-workspace"></a>Uw werkruimte laden
Maak een werkruimteobject van uw bestaande werkruimte. Met de volgende code worden de werkruimtegegevens uit het **bestand config.json** geladen. U ook een [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html)werkruimte ophalen met behulp van.

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Een experiment maken
Een Azure ML-experiment houdt een groep uitvoeringen bij, meestal vanuit hetzelfde trainingsscript. Maak een experiment om de runs te volgen voor het trainen van het caret-model op de ongevallengegevens.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Een rekendoel maken
Met Azure Machine Learning Compute (AmlCompute), een beheerde service, kunnen gegevenswetenschappers Machine Learning-modellen trainen op clusters met virtuele Azure-machines. Voorbeelden hiervan zijn virtuele machines met GPU-ondersteuning. In deze zelfstudie maakt u een AmlCompute-cluster met één knooppunt als trainingsomgeving. Met de onderstaande code wordt het rekencluster voor u gemaakt als deze nog niet in uw werkruimte bestaat.

Mogelijk moet u een paar minuten wachten voordat uw compute cluster is ingericht als het nog niet bestaat.

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
Deze tutorial maakt gebruik van gegevens van de Amerikaanse [National Highway Traffic Safety Administration](https://cdan.nhtsa.gov/tsftables/tsfar.htm) (met dank aan Mary [C. Meyer en Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Deze gegevensset bevat gegevens van meer dan 25.000 auto-ongelukken in de VS, met variabelen die u gebruiken om de kans op een dodelijk ongeval te voorspellen. Importeer eerst de gegevens in R en zet `accidents` deze om in een `Rdata` nieuw gegevensframe voor analyse en exporteer deze naar een bestand.

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

### <a name="upload-data-to-the-datastore"></a>Gegevens uploaden naar de datastore
Upload gegevens naar de cloud, zodat deze toegankelijk zijn via uw externe trainingsomgeving. Elke Azure Machine Learning-werkruimte wordt geleverd met een standaardgegevensarchief dat de verbindingsgegevens opslaat met de Azure blob-container die is ingericht in het opslagaccount dat aan de werkruimte is gekoppeld. Met de volgende code worden de ongevallengegevens die u hierboven hebt gemaakt, geüpload naar dat gegevensarchief.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Een model trainen

Voor deze zelfstudie past u een logistiek regressiemodel op uw geüploade gegevens met behulp van uw externe compute cluster. Als u een vacature wilt indienen, moet u het:

* Het trainingsscript voorbereiden
* Een estimator maken
* De taak verzenden

### <a name="prepare-the-training-script"></a>Het trainingsscript voorbereiden
Een trainingsscript `accidents.R` dat voor u is aangeroepen in dezelfde map als deze zelfstudie. Let op de volgende details **in het trainingsscript** die zijn uitgevoerd om Azure Machine Learning te gebruiken voor training:

* Het trainingsscript heeft `-d` een argument nodig om de map te vinden die de trainingsgegevens bevat. Wanneer u uw taak later definieert en indient, wijst u voor dit argument naar het gegevensarchief. Azure ML monteert de opslagmap aan het externe cluster voor de trainingstaak.
* Het trainingsscript registreert de uiteindelijke nauwkeurigheid als een statistiek `log_metric_to_run()`voor de run record in Azure ML met behulp van . De Azure ML SDK biedt een set logboekregistratie-API's voor het registreren van verschillende statistieken tijdens trainingsuitvoeringen. Deze statistieken worden geregistreerd en blijven bestaan in de experimentrunrecord. De statistieken kunnen dan worden geopend op elk gewenst moment of bekeken in de run details pagina in [de studio](https://ml.azure.com). Zie de [referentie](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) voor de volledige `log_*()`set logboekmethoden.
* Het trainingsscript slaat uw model op in een map met de naam **uitvoer**. De `./outputs` map krijgt een speciale behandeling van Azure ML. Tijdens de training `./outputs` worden bestanden die zijn geschreven automatisch geüpload naar uw run record door Azure ML en blijven als artefacten. Door het getrainde `./outputs`model op te slaan, u uw modelbestand openen en ophalen, zelfs nadat de run is afgelopen en hebt u geen toegang meer tot uw trainingsomgeving op afstand.

### <a name="create-an-estimator"></a>Een estimator maken

Een Azure ML-schatter bevat de uitvoeringsconfiguratiegegevens die nodig zijn voor het uitvoeren van een trainingsscript op het rekendoel. Azure ML-uitvoeringen worden uitgevoerd als containereriseerde taken op het opgegeven rekendoel. Standaard bevat de Docker-afbeelding die is gemaakt voor uw trainingstaak R, de Azure ML SDK en een reeks veelgebruikte R-pakketten. Bekijk hier de volledige lijst met standaardpakketten.

Als u de schatter wilt maken, definieert u:

* De map met uw scripts`source_directory`die nodig zijn voor training ( ). Alle bestanden in deze map worden geüpload naar het clusterknooppunt(s) voor uitvoering. De map moet uw trainingsscript en eventuele aanvullende scripts bevatten die nodig zijn.
* Het trainingsscript dat wordt`entry_script`uitgevoerd ( ).
* Het rekendoel`compute_target`( ), in dit geval het AmlCompute-cluster dat u eerder hebt gemaakt.
* De parameters die vereist`script_params`zijn in het trainingsscript ( ). Azure ML voert uw trainingsscript uit als `Rscript`een opdrachtregelscript met . In deze zelfstudie geeft u één argument op aan het script, het montagepunt van de gegevensmap, waarmee u toegang hebt met `ds$path(target_path)`.
* Elke omgeving afhankelijkheden die nodig zijn voor training. De standaard Docker-afbeelding die is gebouwd`caret` `e1071`voor `optparse`training bevat al de drie pakketten ( , en ) die nodig zijn in het trainingsscript.  U hoeft dus geen aanvullende informatie op te geven. Als u R-pakketten gebruikt die niet standaard zijn opgenomen, gebruikt u de parameter van `cran_packages` de schatter om extra CRAN-pakketten toe te voegen. Zie [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) de referentie voor de volledige set configureerbare opties.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>De taak verzenden op het externe cluster

Tot slot de taak indienen die op uw cluster moet worden uitgevoerd. `submit_experiment()`retourneert een run-object dat u vervolgens gebruikt om te communiceren met de run. In totaal duurt de eerste run **ongeveer tien minuten**. Maar voor latere uitvoeringen wordt dezelfde Docker-afbeelding opnieuw gebruikt zolang de scriptafhankelijkheden niet veranderen.  In dit geval wordt de afbeelding in de cache opgeslagen en is de opstarttijd van de container veel sneller.

```R
run <- submit_experiment(exp, est)
```

U de details van de run bekijken in RStudio Viewer. Als u op de meegeleverde koppeling 'Webweergave' klikt, u naar Azure Machine Learning-studio gaan, waar u de uitvoering in de gebruikersinterface controleren.

```R
view_run_details(run)
```

Model training gebeurt op de achtergrond. Wacht totdat het trainen van het model is voltooid voordat u andere code uitvoert.

```R
wait_for_run_completion(run, show_output = TRUE)
```

U - en collega's met toegang tot de werkruimte - kunnen meerdere experimenten parallel indienen en Azure ML neemt het plannen van de taken op het compute cluster. U het cluster zelfs zo configureren dat het automatisch wordt opgeschaald naar meerdere knooppunten en terugschalen wanneer er geen rekentaken meer in de wachtrij staan. Deze configuratie is een kosteneffectieve manier voor teams om rekenbronnen te delen.

## <a name="retrieve-training-results"></a>Trainingsresultaten ophalen
Zodra uw model klaar is met trainen, hebt u toegang tot de artefacten van uw taak die zijn voortgeduurd tot de runrecord, inclusief alle geregistreerde statistieken en het uiteindelijkgetrainde model.

### <a name="get-the-logged-metrics"></a>De geregistreerde statistieken opmaken
In het `accidents.R`trainingsscript hebt u een statistiek van uw model geregistreerd: de nauwkeurigheid van de voorspellingen in de trainingsgegevens. U statistieken in de [studio](https://ml.azure.com)bekijken of als volgt naar de lokale sessie halen als een R-lijst:

```R
metrics <- get_run_metrics(run)
metrics
```

Als u meerdere experimenten hebt uitgevoerd (bijvoorbeeld met behulp van verschillende variabelen, algoritmen of hyperparamers), u de statistieken van elke run gebruiken om het model te vergelijken en te kiezen dat u in de productie zult gebruiken.

### <a name="get-the-trained-model"></a>Haal het getrainde model
U het getrainde model ophalen en de resultaten bekijken in uw lokale R-sessie. De volgende code downloadt `./outputs` de inhoud van de map, die het modelbestand bevat.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

U ziet een aantal factoren die bijdragen aan een toename van de geschatte kans op overlijden:

* hogere impactsnelheid 
* mannelijke bestuurder
* oudere bewoner
* Passagier

Je ziet lagere waarschijnlijkheden van overlijden met:

* aanwezigheid van airbags
* aanwezigheid veiligheidsgordels
* frontale botsing 

Het productiejaar van het voertuig heeft geen significant effect.

U dit model gebruiken om nieuwe voorspellingen te doen:

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

Met uw model u het gevaar van de dood door een botsing voorspellen. Gebruik Azure ML om uw model te implementeren als voorspellingsservice. In deze zelfstudie implementeert u de webservice in [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Het model registreren

Registreer eerst het model waarmee u [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html)naar uw werkruimte hebt gedownload. Een geregistreerd model kan elke verzameling bestanden zijn, maar in dit geval is het R-modelobject voldoende. Azure ML gebruikt het geregistreerde model voor implementatie.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>De afhankelijkheden van gevolgtrekking definiëren
Als u een webservice voor uw model wilt maken, moet u eerst een scorescript maken (`entry_script`), een R-script dat wordt aangenomen als invoervariabele waarden (in JSON-indeling) en een voorspelling van uw model uitvoeren. Gebruik voor deze zelfstudie `accident_predict.R`het opgegeven scorebestand . Het scorescript moet `init()` een methode bevatten die uw model laadt en een functie retourneert die het model gebruikt om een voorspelling te doen op basis van de invoergegevens. Zie de [documentatie](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) voor meer informatie.

Definieer vervolgens een Azure **ML-omgeving** voor de pakketafhankelijkheden van uw script. Met een omgeving geeft u R-pakketten op (van CRAN of elders) die nodig zijn om uw script uit te voeren. U ook de waarden van omgevingsvariabelen opgeven waarnaar uw script kan verwijzen om het gedrag ervan te wijzigen. Azure ML bouwt standaard dezelfde standaard Docker-afbeelding die wordt gebruikt bij de schatter voor training. Aangezien de zelfstudie geen speciale vereisten heeft, maakt u een omgeving zonder speciale kenmerken.

```R
r_env <- r_environment(name = "basic_env")
```

Als u in plaats daarvan uw eigen Docker-afbeelding wilt gebruiken voor implementatie, geeft u de `custom_docker_image` parameter op. Zie [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) de referentie voor de volledige set configureerbare opties voor het definiëren van een omgeving.

Nu heb je alles wat je nodig hebt om een **conclusie te** maken voor het inkapselen van je scorescript en omgevingsafhankelijkheden.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Implementeren naar ACI
In deze zelfstudie implementeert u uw service naar ACI. Deze code voorziet in één container om te reageren op binnenkomende aanvragen, die geschikt is voor het testen en lichte belastingen. Zie [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) voor extra configureerbare opties. (Voor implementaties op productieschaal u ook [worden geïmplementeerd in Azure Kubernetes Service](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Nu implementeert u uw model als een webservice. Implementatie **kan enkele minuten duren.** 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>De geïmplementeerde service testen

Nu uw model als service is geïmplementeerd, u [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html)de service van R testen met behulp van.  Geef een nieuwe set gegevens om van te voorspellen, deze om te zetten naar JSON en stuur deze naar de service.

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

U ook het HTTP-eindpunt van de webservice krijgen, waarin REST-clientoproepen worden geaccepteerd. Dit eindpunt kan worden gedeeld met iedereen die de webservice wil testen of wil integreren in een toepassing.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de bronnen zodra u ze niet meer nodig hebt. Verwijder geen resource die u nog wilt gebruiken. 

De webservice verwijderen:
```R
delete_webservice(aci_service)
```

Het geregistreerde model verwijderen:
```R
delete_model(model)
```

Het compute cluster verwijderen:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Alles verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Geef de eigenschappen van de werkruimte weer en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Nu u uw eerste Azure Machine Learning-experiment in R hebt voltooid, leest u meer over de [Azure Machine Learning SDK voor R.](https://azure.github.io/azureml-sdk-for-r/index.html)

* Meer informatie over Azure Machine Learning with R vindt u in de voorbeelden in de andere *vignettenmappen.*
