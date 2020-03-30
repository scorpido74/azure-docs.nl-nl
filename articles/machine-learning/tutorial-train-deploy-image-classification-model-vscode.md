---
title: 'Zelfstudie: Een model trainen en implementeren met de extensie Visual Studio Code'
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen en implementeren van een afbeeldingsclassificatiemodel met TensorFlow en de Azure Machine Learning Visual Studio Code Extension
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: ba9cd2e7dc0248aa351cb7bc4519689763f1adda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239880"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Een TensorFlow-model voor afbeeldingsclassificatie trainen en implementeren met behulp van de Azure Machine Learning Visual Studio Code Extension

Meer informatie over het trainen en implementeren van een afbeeldingsclassificatiemodel om handgeschreven getallen te herkennen met TensorFlow en de Azure Machine Learning Visual Studio Code Extension.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De code begrijpen
> * Een werkruimte maken
> * Een experiment maken
> * Computerdoelen configureren
> * Een configuratiebestand uitvoeren
> * Een model trainen
> * Een model registreren
> * Een model implementeren

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement. Als u er geen hebt, meldt u zich aan om de gratis of betaalde versie van Azure Machine Learning uit te [proberen.](https://aka.ms/AMLFree)
- Installeer [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), een lichtgewicht, cross-platform code editor.
- Azure Machine Learning Studio Visual Studio Code-extensie. Zie de zelfstudie van de [installatie van Azure Machine Learning Visual Studio Code-extensie](./tutorial-setup-vscode-extension.md) voor installatie-instructies voor installatie

## <a name="understand-the-code"></a>De code begrijpen

De code voor deze zelfstudie maakt gebruik van TensorFlow om een machine learning-model voor beeldclassificatie te trainen dat handgeschreven cijfers categoriseert van 0-9. Het doet dit door het creëren van een neuraal netwerk dat de pixelwaarden van 28 px x 28 px beeld neemt als input en uitgangen een lijst van 10 waarschijnlijkheden, een voor elk van de cijfers worden geclassificeerd. Hieronder vindt u een voorbeeld van hoe de gegevens eruit zien.  

![MNIST-cijfers](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Download de code voor deze zelfstudie door de [VS Code Tools voor AI-repository](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) overal op uw computer te downloaden en uit te pakken.

## <a name="create-a-workspace"></a>Een werkruimte maken

Het eerste wat u moet doen om een toepassing in Azure Machine Learning te bouwen, is het maken van een werkruimte. Een werkruimte bevat de middelen om modellen en de getrainde modellen zelf te trainen. Zie voor meer informatie [wat een werkruimte is.](./concept-workspace.md) 

1. Selecteer op de activiteitsbalk van Visual Studio Code het **Azure-pictogram** om de azure machine learning-weergave te openen.
1. Klik met de rechtermuisknop op uw Azure-abonnement en selecteer **Werkruimte maken**. 
    
    > [!div class="mx-imgBorder"]
    > ![Een werkruimte maken](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Standaard wordt een naam gegenereerd die de datum en het tijdstip van creatie bevat. Wijzig in het tekstinvoervak de naam in 'TeamWorkspace' en druk op **Enter**.
1. Selecteer **Een nieuwe resourcegroep maken**. 
1. Geef uw resourcegroep een naam "TeamWorkspace-rg" en druk op **Enter**. 
1. Kies een locatie voor uw werkruimte. Het wordt aanbevolen om een locatie te kiezen die het dichtst bij de locatie ligt die u van plan bent om uw model te implementeren. Bijvoorbeeld "West US 2".
1. Wanneer u wordt gevraagd het type werkruimte te selecteren, selecteert u **Basisom** een basiswerkruimte te maken. Zie overzicht van [Azure Machine Learning](./overview-what-is-azure-ml.md#sku)voor meer informatie over verschillende werkruimteaanbiedingen.

Op dit moment wordt een aanvraag voor Azure ingediend om een nieuwe werkruimte in uw account te maken. Na een paar minuten wordt de nieuwe werkruimte weergegeven in uw abonnementsknooppunt. 

## <a name="create-an-experiment"></a>Een experiment maken

In uw werkruimte kunnen een of meer experimenten worden gemaakt om afzonderlijke modeltrainingsruns bij te houden en te analyseren. Runs kunnen worden uitgevoerd in de Azure-cloud of op uw lokale machine.

1. Selecteer op de activiteitsbalk van visual studiocode het **Azure-pictogram.** De azure machine learning-weergave wordt weergegeven.
1. Vouw uw abonnementsknooppunt uit.
1. Het **TeamWorkspace-knooppunt** uitvouwen. 
1. Klik met de rechtermuisknop op het knooppunt **Experimenten.**
1. Selecteer **Experiment maken** in het contextmenu.

    > [!div class="mx-imgBorder"]
    > ![Een experiment maken](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Geef uw experiment 'MNIST' een naam en druk op **Enter** om het nieuwe experiment te maken. 

Net als werkruimten wordt een aanvraag naar Azure verzonden om een experiment met de meegeleverde configuraties te maken. Na enkele minuten wordt het nieuwe experiment weergegeven in het knooppunt *Experimenten* van uw werkruimte. 

## <a name="configure-compute-targets"></a>Compute-doelen configureren

Een compute target is de computerbron of -omgeving waarin u scripts uitvoert en getrainde modellen implementeert. Zie de azure [Machine Learning-rekendoelendocumentatie voor](./concept-compute-target.md)meer informatie.

Ga als u een rekendoel maken:

1. Selecteer op de activiteitsbalk van visual studiocode het **Azure-pictogram.** De azure machine learning-weergave wordt weergegeven. 
1. Vouw uw abonnementsknooppunt uit. 
1. Het **TeamWorkspace-knooppunt** uitvouwen. 
1. Klik onder het werkruimteknooppunt met de rechtermuisknop op het **compute-knooppunt** en kies **Compute maken**. 

    > [!div class="mx-imgBorder"]
    > ![Een rekendoel maken](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Selecteer **Azure Machine Learning Compute (AmlCompute)**. Azure Machine Learning Compute is een beheerde compute-infrastructuur waarmee de gebruiker eenvoudig één of meerdere gegevensberekenen kan maken die kunnen worden gebruikt met andere gebruikers in uw werkruimte.
1. Kies een VM-grootte. Selecteer **Standard_F2s_v2** in de lijst met opties. De grootte van uw VM heeft een impact op de hoeveelheid tijd die nodig is om uw modellen te trainen. Zie formaten voor virtuele [Linux-machines in Azure voor](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)meer informatie over VM-formaten.
1. Geef uw rekenkracht "TeamWkspc-com" een naam en druk op **Enter** om uw rekenkracht te maken.

Na een paar minuten wordt het nieuwe compute-doel weergegeven in het *compute-knooppunt* van uw werkruimte.

## <a name="create-a-run-configuration"></a>Een run-configuratie maken

Wanneer u een trainingsrun indient op een rekendoel, dient u ook de configuratie in die nodig is om de trainingstaak uit te voeren. Het script dat bijvoorbeeld de trainingscode bevat en de Python-afhankelijkheden die nodig zijn om het uit te voeren.

Ga als lid van het werk om een run-configuratie te maken:

1. Selecteer op de activiteitsbalk van visual studiocode het **Azure-pictogram.** De azure machine learning-weergave wordt weergegeven. 
1. Vouw uw abonnementsknooppunt uit. 
1. Het **TeamWorkspace-knooppunt** uitvouwen. 
1. Klik onder het werkruimteknooppunt met de rechtermuisknop op het **compute-knooppunt teamwkspc-com** en kies **Configuratie maken**.

    > [!div class="mx-imgBorder"]
    > ![Een run-configuratie maken](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Geef uw runconfiguratie "MNIST-rc" een naam en druk op **Enter** om uw runconfiguratie te maken.
1. Selecteer vervolgens **TensorFlow Single-Node Training** als het type trainingstaak.
1. Druk op **Enter** om door het scriptbestand te bladeren om op de compute uit te voeren. In dit geval is het script `train.py` om het `vscode-tools-for-ai/mnist-vscode-docs-sample` model te trainen het bestand in de map.
1. Voer het volgende in het invoervak in om de vereiste pakketten op te geven.
    
    ```text
    pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
    ```
    
    Er wordt `MNIST-rc.runconfig` een bestand weergegeven in VS-code met inhoud die vergelijkbaar is met het onderstaande:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "python": {
                "userManagedDependencies": false,
                "condaDependencies": {
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                }
            },
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:0.2.4",
                "enabled": true,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                }
            }
        },
        "nodeCount": 1,
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Wanneer u tevreden bent met de configuratie, opent u het opdrachtpalet door **Weergave > opdrachtpalet**te selecteren.
1. Voer de volgende opdracht in het opdrachtpalet in om het uitvoeren van het configuratiebestand op te slaan.

    ```text
    Azure ML: Save and Continue
    ```

De `MNIST-rc` runconfiguratie wordt toegevoegd onder het *Compute Node teamwkspc-com.*

## <a name="train-the-model"></a>Het model trainen

Tijdens het trainingsproces wordt een TensorFlow-model gemaakt door de trainingsgegevens en leerpatronen die erin zijn ingebed voor elk van de respectievelijke cijfers die worden geclassificeerd, te verwerken. 

Ga als een Azure Machine Learning-experiment als gevolg van het uitvoeren van een Azure Machine Learning-experiment:

1. Selecteer op de activiteitsbalk van visual studiocode het **Azure-pictogram.** De azure machine learning-weergave wordt weergegeven. 
1. Vouw uw abonnementsknooppunt uit. 
1. Het knooppunt **Van TeamWorkspace > Experimenten** uitbreiden. 
1. Klik met de rechtermuisknop op het **MNIST-experiment.**
1. Selecteer **Experiment uitvoeren**.

    > [!div class="mx-imgBorder"]
    > ![Een experiment uitvoeren](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. Selecteer in de lijst met rekendoelopties het **gegevensdoel TeamWkspc-com.**
1. Selecteer vervolgens de **MNIST-rc-run-configuratie.**
1. Op dit moment wordt een aanvraag verzonden naar Azure om uw experiment uit te voeren op het geselecteerde rekendoel in uw werkruimte. Dit proces duurt enkele minuten. De hoeveelheid tijd voor het uitvoeren van de trainingstaak wordt beïnvloed door verschillende factoren, zoals het rekentype en de grootte van trainingsgegevens. Als u de voortgang van uw experiment wilt bijhouden, klikt u met de rechtermuisknop op het huidige run-knooppunt en selecteert u **Weergave uitvoeren in Azure-portal**.
1. Wanneer het dialoogvenster waarin wordt gevraagd een externe website te openen wordt weergegeven, selecteert u **Openen**.

    > [!div class="mx-imgBorder"]
    > ![Voortgang van het experiment bijhouden](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Wanneer het model klaar is met trainen, wordt het statuslabel naast het run-knooppunt bijgewerkt naar 'Voltooid'.

## <a name="register-the-model"></a>Het model registreren

Nu u uw model hebt getraind, u het registreren in uw werkruimte. 

Ga als u uw model registreren:

1. Selecteer op de activiteitsbalk van visual studiocode het **Azure-pictogram.** De azure machine learning-weergave wordt weergegeven.
1. Vouw uw abonnementsknooppunt uit. 
1. Breid het **TeamWorkspace-> Experimenten > MNIST-knooppunt** uit.
1. Haal de modeluitvoer gegenereerd uit het trainen van het model. Klik met de rechtermuisknop op het knooppunt **Uitvoeren 1** uitvoeren en selecteer **Uitvoer downloaden**. 

    > [!div class="mx-imgBorder"]
    > ![Modeluitvoer downloaden](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Kies de map om de gedownloade uitvoer op te slaan. Standaard worden de uitvoer geplaatst in de map die momenteel wordt geopend in Visual Studio Code.
1. Klik met de rechtermuisknop op het knooppunt **Modellen** en kies **Model registreren**.

    > [!div class="mx-imgBorder"]
    > ![Een model registreren](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Geef uw model een naam "MNIST-TensorFlow-model" en druk op **Enter**.
1. Een TensorFlow model bestaat uit verschillende bestanden. Selecteer **De map Model** als modelpadindeling in de lijst met opties. 
1. Selecteer `azureml_outputs/Run_1/outputs/outputs/model` de map.

    Een bestand met uw modelconfiguraties wordt weergegeven in Visual Studio Code met vergelijkbare inhoud als hieronder:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Zodra u tevreden bent met uw configuratie, slaat u deze op door het opdrachtpalet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Save and Continue
    ```

Na een paar minuten verschijnt het model onder het knooppunt *Modellen.*

## <a name="deploy-the-model"></a>Het model implementeren

In Visual Studio Code u uw model als webservice implementeren om:

+ Azure Container Instances (ACI).
+ Azure Kubernetes-service (AKS).

U hoeft geen ACI-container te maken om van tevoren te testen, omdat ACI-containers naar behoefte worden gemaakt. U moet echter wel vooraf AKS-clusters configureren. Zie Modellen implementeren met [Azure Machine Learning voor](how-to-deploy-and-where.md) meer informatie over implementatieopties.

Een webservice implementeren als EEN ACI:

1. Selecteer op de activiteitsbalk van visual studiocode het **Azure-pictogram.** De azure machine learning-weergave wordt weergegeven.
1. Vouw uw abonnementsknooppunt uit. 
1. Het knooppunt **Van TeamWorkspace > Modellen** uitvouwen. 
1. Klik met de rechtermuisknop op het **MNIST-TensorFlow-model** en selecteer **Service implementeren vanuit geregistreerd model**.

    > [!div class="mx-imgBorder"]
    > ![Model implementeren](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Selecteer **Azure Container Instances**.
1. Geef uw service een naam "mnist-tensorflow-svc" en druk op **Enter**.
1. Kies het script dat in de container moet worden uitgevoerd `score.py` door `mnist-vscode-docs-sample` op **Enter** in het invoervak te drukken en te bladeren naar het bestand in de map.
1. Geef de afhankelijkheden op die nodig zijn om het script `env.yml` uit `mnist-vscode-docs-sample` te voeren door op **Enter** in het invoervak te drukken en te bladeren naar het bestand in de map.

    Een bestand met uw modelconfiguraties wordt weergegeven in Visual Studio Code met vergelijkbare inhoud als hieronder:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```
1. Zodra u tevreden bent met uw configuratie, slaat u deze op door het opdrachtpalet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Save and Continue
    ```

Op dit moment wordt een aanvraag verzonden naar Azure om uw webservice te implementeren. Dit proces duurt enkele minuten. Eenmaal geïmplementeerd wordt de nieuwe service weergegeven onder het knooppunt *Eindpunten.*

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: Modellen trainen met Azure Machine Learning](tutorial-train-models-with-aml.md)voor een walkthrough over hoe u trainen met Azure Machine Learning buiten Visual Studio Code.
* Zie de [python hello-world-zelfstudie](https://code.visualstudio.com/docs/Python/Python-tutorial)voor een walkthrough van het lokaal bewerken, uitvoeren en debuggen van code.

