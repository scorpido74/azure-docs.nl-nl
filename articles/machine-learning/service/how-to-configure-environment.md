---
title: Een python-ontwikkel omgeving instellen
titleSuffix: Azure Machine Learning service
description: Meer informatie over het configureren van een ontwikkel omgeving wanneer u met de Azure Machine Learning-service werkt. In dit artikel vindt u informatie over het gebruik van Conda-omgevingen, het maken van configuratie bestanden en het configureren van uw eigen cloud-gebaseerde Notebook server, Jupyter-notebooks, Azure Databricks, Azure Notebooks, Ide's, code editors en de Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 8bf83f483bb7680b71bf928430858240deb3d603
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278812"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Een ontwikkelomgeving configureren voor Azure Machine Learning

In dit artikel leert u hoe u een ontwikkel omgeving kunt configureren om met Azure Machine Learning-service te werken. Azure Machine Learning-service is platform neutraal. De enige harde vereiste voor uw ontwikkel omgeving is python 3. Een geïsoleerde omgeving zoals Anaconda of virtualenv wordt ook aanbevolen.

In de volgende tabel ziet u elke ontwikkel omgeving die in dit artikel wordt behandeld, samen met de voor-en nadelen.

| Omgeving | Professionals | Nadelen |
| --- | --- | --- |
| [VM op basis van de Cloud-notebook](#notebookvm) | Eenvoudigste manier om aan de slag te gaan. De volledige SDK is al geïnstalleerd in uw werk ruimte-VM en de notitieblok zelfstudies zijn vooraf gekloond en klaar om te worden uitgevoerd. | Geen controle over uw ontwikkel omgeving en afhankelijkheden. Er zijn extra kosten verbonden aan de virtuele Linux-machine (VM kan worden gestopt wanneer deze niet wordt gebruikt om kosten te voor komen). Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Lokale omgeving](#local) | Volledig beheer van uw ontwikkel omgeving en afhankelijkheden. Voer uit met een hulp programma, omgeving of IDE van uw keuze. | Duurt langer om aan de slag te gaan. De benodigde SDK-pakketten moeten worden geïnstalleerd en er moet ook een omgeving worden geïnstalleerd als u er nog geen hebt. |
| [Azure Databricks](#aml-databricks) | Ideaal voor het uitvoeren van grootschalige intensieve machine learning werk stromen op het schaal bare Apache Spark platform. | Overbodig voor experimentele machine learning, of kleinschalige experimenten en werk stromen. Er zijn extra kosten gemaakt voor Azure Databricks. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/databricks/). |
| [De Data Science Virtual Machine (DSVM)](#dsvm) | Net als bij de cloud-gebaseerde Notebook-VM (python en de SDK zijn vooraf geïnstalleerd), maar met extra populaire gegevens wetenschap en machine learning-hulpprogram ma's vooraf geïnstalleerd. Eenvoudig te schalen en combi neren met andere aangepaste hulp middelen en werk stromen. | Een langzamere aan de slag-ervaring vergeleken met de virtuele machine op de Cloud-notebook. |
| [Azure Notebooks](#aznotebooks) | De gratis en lichte aan de slag-ervaring, met python en de SDK vooraf geïnstalleerd. | Minder krachtige Vm's die beschikbaar zijn in vergelijking met VM op basis van een virtuele machine. Geïsoleerd van werk ruimte en andere resources. |

Dit artikel bevat ook extra tips voor het gebruik van de volgende hulpprogram ma's:

* [Jupyter-notebooks](#jupyter): Als u de Jupyter Notebook al gebruikt, heeft de SDK enkele extra's die u moet installeren.

* [Visual Studio Code](#vscode): Als u Visual Studio code gebruikt, beschikt u over een aantal nuttige uitbrei dingen die u kunt installeren.

## <a name="prerequisites"></a>Vereisten

Een werkruimte van Azure Machine Learning-service. Zie [een Azure machine learning service werkruimte maken](how-to-manage-workspace.md)om de werk ruimte te maken. Een werk ruimte is alles wat u nodig hebt om aan de slag te gaan met uw eigen [cloud-gebaseerde Notebook server](#notebookvm), een [DSVM](#dsvm), [Azure Databricks](#aml-databricks)of [Azure notebooks](#aznotebooks).

Als u de SDK-omgeving voor uw [lokale computer](#local)wilt installeren, [Jupyter notebook server](#jupyter) of [Visual Studio code](#vscode) die u ook nodig hebt:

- Het [Anaconda](https://www.anaconda.com/download/) -of [Miniconda](https://conda.io/miniconda.html) -pakket beheer.

- Op Linux of macOS hebt u de bash-shell nodig.

    > [!TIP]
    > Als u gebruikmaakt van Linux of macOS en een andere shell dan bash gebruikt (bijvoorbeeld zsh), kunnen er fouten optreden wanneer u sommige opdrachten uitvoert. U kunt dit probleem omzeilen, gebruikt u de `bash` opdracht voor het starten van een nieuwe bash-shell en voer de opdrachten er uit.

- Op Windows moet u de opdrachtprompt of Anaconda-prompt (geïnstalleerd door Anaconda en Miniconda).

## <a id="notebookvm"></a>Uw eigen Cloud-VM op basis van een virtuele machine

De virtuele machine van het notebook (preview) is een veilig Azure-werk station in de cloud dat gegevens wetenschappers bevat met een Jupyter notebook-server, Jjupyterlab en een volledig bereide ML-omgeving.

De VM van het notebook is:

+ **Secure**. Omdat de toegang tot de virtuele machine en het notebook is beveiligd met HTTPS en Azure Active Directory standaard kunnen IT-professionals de eenmalige aanmelding en andere beveiligings functies, zoals multi-factor Authentication, gemakkelijk afdwingen.

+ **Vooraf geconfigureerd**. Deze volledig voor bereide python ML-omgeving tekent de pedigree van de populaire IaaS Data Science VM en omvat:
  + Azure ML python SDK (nieuwste)
  + Automatische configuratie voor samen werking met uw werk ruimte
  + Een Jupyter-notebook server
  + Jjupyterlab notebook IDE
  + Vooraf geconfigureerde GPU-Stuur Programma's
  + Een keuze uit een diep gaande lessen


  Als u in code werkt, bevat de virtuele machine zelf studies en voor beelden om u te helpen te verkennen en hoe u Azure Machine Learning-service kunt gebruiken. De voorbeeld notitieblokken worden opgeslagen in het Azure Blob Storage-account van uw werk ruimte zodat ze kunnen worden gedeeld via Vm's. Wanneer ze worden uitgevoerd, hebben ze ook toegang tot de gegevens archieven en reken resources van uw werk ruimte.

+ **Eenvoudige installatie**: Maak een wille keurig moment in uw Azure Machine Learning-werk ruimte. Geef een naam op en geef een Azure VM-type op. Probeer het nu met deze [zelf studie: Omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md)instellen.

+ **Aanpasbaar**. Tijdens een beheerde en beveiligde VM-aanbieding behoudt u volledige toegang tot de hardware-mogelijkheden en past u deze aan op de wensen van uw hart. Zo kunt u snel de nieuwste NVidia V100-VM maken om stapsgewijze fout opsporing van nieuwe Neural-netwerk architectuur uit te voeren.

[Stop de VM van het notitie](tutorial-1st-experiment-sdk-train.md#clean-up-resources)blok om de VM-kosten van de notebook te stoppen. 

## <a id="dsvm"></a>Virtuele Machine voor Datatechnologie

De DSVM is een aangepaste installatie kopie van een virtuele machine (VM). Het is ontworpen voor data wetenschappen werk dat vooraf is geconfigureerd met:

  - Pakketten zoals tensor flow, PyTorch, Scikit-learn, XGBoost en de Azure Machine Learning SDK
  - Populaire hulp middelen voor gegevens wetenschap, zoals zelfstandig en analyse van Spark
  - Azure-hulpprogram ma's zoals Azure CLI, AzCopy en Storage Explorer
  - Integrated Development Environments (Ide's) zoals Visual Studio code en PyCharm
  - Jupyter Notebook-Server

De Azure Machine Learning SDK werkt op de Ubuntu-of Windows-versie van de DSVM. Maar als u van plan bent om de DSVM te gebruiken als een reken doel, wordt alleen Ubuntu ondersteund.

Ga als volgt te werk om de DSVM te gebruiken als een ontwikkel omgeving:

1. Maak een DSVM in een van de volgende omgevingen:

    * De Azure Portal:

        * [Een Ubuntu-Data Science Virtual Machine maken](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Een Windows-Data Science Virtual Machine maken](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * De Azure CLI:

        > [!IMPORTANT]
        > * Wanneer u de Azure cli gebruikt, moet u zich eerst aanmelden bij uw Azure-abonnement met behulp van de `az login` opdracht.
        >
        > * Wanneer u de opdrachten in deze stap gebruikt, moet u een naam opgeven voor de resource groep, een naam voor de virtuele machine, een gebruikers naam en een wacht woord.

        * Als u een Ubuntu-Data Science Virtual Machine wilt maken, gebruikt u de volgende opdracht:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Als u een Windows-Data Science Virtual Machine wilt maken, gebruikt u de volgende opdracht:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. De Azure Machine Learning SDK is al geïnstalleerd op de DSVM. Als u de Conda-omgeving met de SDK, gebruikt u een van de volgende opdrachten:

    * Voor Ubuntu DSVM:

        ```shell
        conda activate py36
        ```

    * Voor Windows DSVM:

        ```shell
        conda activate AzureML
        ```

1. Als u wilt controleren of u kunt toegang krijgen tot de SDK en controleer de versie, gebruik de volgende Python-code:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Zie de sectie [een configuratie bestand voor een werk ruimte maken](#workspace) om de DSVM te configureren voor het gebruik van uw Azure machine learning service-werk ruimte.

Zie [Data Science virtual machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)voor meer informatie.

## <a id="local"></a>Lokale computer

Wanneer u een lokale computer gebruikt (dit kan ook een externe virtuele machine zijn), maakt u een Anaconda-omgeving en installeert u de SDK door het volgende te doen:

1. Down load en Installeer [Anaconda](https://www.anaconda.com/distribution/#download-section) (python 3,7-versie) als u dit nog niet hebt gedaan.

1. Open een Anaconda-prompt en maak een omgeving met de volgende opdrachten:

    Voer de volgende opdracht uit om de omgeving te maken.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Activeer vervolgens de omgeving.

    ```shell
    conda activate myenv
    ```

    In dit voor beeld wordt een omgeving gemaakt met behulp van python 3.6.5, maar alle specifieke subversies kunnen worden gekozen. SDK-compatibiliteit is mogelijk niet gegarandeerd met bepaalde primaire versies (3.5 + wordt aanbevolen) en het wordt aanbevolen een andere versie/Subversion te gebruiken in uw Anaconda-omgeving als u fouten ondervindt. Het duurt enkele minuten om de omgeving te maken terwijl onderdelen en pakketten worden gedownload.

1. Voer de volgende opdrachten uit in uw nieuwe omgeving om specifieke ipython-kernels in te scha kelen. Dit zorgt voor een verwachte kernel-en pakket import gedrag bij het werken met Jupyter-notebooks in Anaconda-omgevingen:

    ```shell
    conda install notebook ipykernel
    ```

    Voer vervolgens de volgende opdracht uit om de kernel te maken:

    ```shell
    ipython kernel install --user
    ```

1. Gebruik de volgende opdrachten om pakketten te installeren:

    Met deze opdracht wordt de basis-Azure Machine Learning SDK met notebook-en automl-extra's geïnstalleerd. De `automl` extra is een grote installatie. u kunt de vier Kante haken verwijderen als u niet van plan bent om automatische machine learning experimenten uit te voeren. De `automl` extra bevat ook de Azure machine learning data prep SDK standaard als een afhankelijkheid.

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Als u een bericht krijgt dat PyYAML niet kan worden verwijderd, gebruikt u in plaats daarvan de volgende opdracht:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Vanaf macOS Catalina is zsh (Z Shell) de standaard aanmeldings shell en de interactieve shell. Gebruik in zsh de volgende opdracht waarmee haakjes worden voorafgegaan door "\\" (back slash):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   Het duurt enkele minuten voordat de SDK is geïnstalleerd. Zie de [installatie handleiding](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)voor meer informatie over installatie opties.

1. Installeer andere pakketten voor uw machine learning experimenten.

    Gebruik een van de volgende opdrachten en vervang  *\<nieuw pakket >* door het pakket dat u wilt installeren. Voor het installeren `conda install` van pakketten via moet het pakket deel uitmaken van de huidige kanalen (nieuwe kanalen kunnen worden toegevoegd in de Anaconda-Cloud).

    ```shell
    conda install <new package>
    ```

    U kunt ook pakketten installeren via `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter-Notebooks

Jupyter-notitieblokken maken deel uit van de [Jupyter Project](https://jupyter.org/). Ze bieden een interactieve ervaring met codering waar het maken van documenten die live code met verhalende tekst en afbeeldingen combineren. Jupyter-notebooks zijn ook een fantastische manier om uw resultaten te delen met anderen, omdat u de uitvoer van uw code secties in het document kunt opslaan. U kunt Jupyter-Notebooks installeren op een aantal verschillende platformen.

De procedure in de sectie [lokale computer](#local) installeert de vereiste onderdelen voor het uitvoeren van Jupyter-notebooks in een Anaconda-omgeving. Ga als volgt te werk om deze onderdelen in uw Jupyter Notebook omgeving in te scha kelen:

1. Open een Anaconda-prompt en activeer uw omgeving.

    ```shell
    conda activate myenv
    ```
    
1. Kloon [de GitHub-opslag plaats](https://aka.ms/aml-notebooks) voor een aantal voorbeeld notitieblokken.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Start de Jupyter Notebook-server met de volgende opdracht:

    ```shell
    jupyter notebook
    ```

1. Als u wilt controleren of Jupyter Notebook de SDK kunt gebruiken, maakt u een **nieuwe** notebook, selecteert u **python 3** als uw kernel en voert u de volgende opdracht uit in een notebook-cel:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Als u problemen ondervindt bij het importeren `ModuleNotFoundError`van modules en een ontvangt, moet u ervoor zorgen dat uw Jupyter-kernel is verbonden met het juiste pad voor uw omgeving door de volgende code in een notebook-cel uit te voeren.

    ```python
    import sys
    sys.path
    ```
    
1. Als u de Jupyter Notebook wilt configureren voor het gebruik van uw Azure Machine Learning service-werk ruimte, gaat u naar de sectie [een werkruimte configuratie bestand maken](#workspace) .


### <a id="vscode"></a>Visual Studio Code

Visual Studio Code is een platformoverschrijdende code-editor. Afhankelijk van een lokale installatie van Python 3 en Conda voor Python-ondersteuning, maar het biedt extra hulpmiddelen voor het werken met AI. Het biedt ook ondersteuning voor het selecteren van de Conda-omgeving in de code-editor.

Ga als volgt te werk om Visual Studio code te gebruiken voor ontwikkeling:

1. Zie aan de [slag met python in VSCode](https://code.visualstudio.com/docs/python/python-tutorial)voor meer informatie over het gebruik van Visual Studio code voor python-ontwikkeling.

1. Als u de Conda-omgeving wilt selecteren, opent u VS code en selecteert u vervolgens CTRL + SHIFT + P (Linux en Windows) of Command + Shift + P (Mac).
    De __opdracht pallet__ wordt geopend.

1. Python __invoeren: Selecteer interpreter__en selecteer vervolgens de Conda-omgeving.

1. Als u wilt valideren dat u de SDK kunt gebruiken, maakt u een nieuw python-bestand (. py) en voert u dit uit met de volgende code:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Zie [Hulpprogram ma's voor AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)om de Azure machine learning extensie voor Visual Studio code te installeren.

    Zie [Azure machine learning voor Visual Studio code gebruiken](how-to-vscode-tools.md)voor meer informatie.

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks is een op Apache Spark gebaseerde omgeving in de Azure-Cloud. Het biedt een samen werkende notebook omgeving met een CPU of een reken cluster op basis van GPU.

Hoe Azure Databricks werkt met Azure Machine Learning-service:
+ U kunt een model trainen met Spark MLlib en het model implementeren naar ACI/AKS in Azure Databricks.
+ U kunt ook gebruikmaken van [geautomatiseerde machine learning](concept-automated-ml.md) mogelijkheden in een speciale Azure ml SDK met Azure Databricks.
+ U kunt Azure Databricks als een reken doel van een [Azure machine learning pijp lijn](concept-ml-pipelines.md)gebruiken.

### <a name="set-up-your-databricks-cluster"></a>Uw Databricks-cluster instellen

Maak een [Databricks-cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Sommige instellingen zijn alleen van toepassing als u de SDK installeert voor automatische machine learning op Databricks.
**Het duurt enkele minuten om het cluster te maken.**

Gebruik deze instellingen:

| Instelling |Van toepassing op| Value |
|----|---|---|
| Clusternaam |altijd| yourclustername |
| Databricks Runtime |altijd| Een niet-ML-runtime (niet ML 4. x, 5. x) |
| Python-versie |altijd| 3 |
| Medewerkers |altijd| 2 of hoger |
| VM-typen worker-knoop punt <br>(bepaalt het maximum aantal gelijktijdige iteraties) |Geautomatiseerde machine learning<br>alleen| Voorkeurs-VM geoptimaliseerd voor geheugen |
| Automatisch schalen inschakelen |Geautomatiseerde machine learning<br>alleen| Schakel |

Wacht totdat het cluster wordt uitgevoerd voordat u doorgaat.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>De juiste SDK installeren in een Databricks-bibliotheek
Zodra het cluster wordt uitgevoerd, [maakt u een bibliotheek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) om het juiste Azure machine learning SDK-pakket aan uw cluster toe te voegen.

1. Kies **slechts één** optie (er worden geen andere SDK-installaties ondersteund)

   |SDK&nbsp;-&nbsp;pakket extra's|Source|PyPi&nbsp;-naam&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Voor Databricks| Python-ei of PyPI uploaden | azureml-sdk[databricks]|
   |Voor Databricks-with-<br> automatische ML-mogelijkheden| Python-ei of PyPI uploaden | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Er kunnen geen andere SDK-extra's worden geïnstalleerd. Kies slechts een van de voor gaande opties [databricks] of [automl_databricks].

   * Selecteer niet **automatisch koppelen aan alle clusters**.
   * Selecteer **koppelen** naast de naam van uw cluster.

1. Controleer op fouten totdat de status is gewijzigd in **bijgevoegd**. Dit kan enkele minuten duren.  Als deze stap mislukt, controleert u het volgende:

   Probeer het cluster opnieuw te starten door:
   1. Selecteer **clusters**in het linkerdeel venster.
   1. Selecteer de naam van uw cluster in de tabel.
   1. Selecteer **opnieuw opstarten**op het tabblad **tape wisselaars** .

   Houd ook rekening met het volgende:
   + Bij het gebruik van Azure Databricks de volgende para meters toevoegen in AutoML config:
       1. ```max_concurrent_iterations```is gebaseerd op het aantal worker-knoop punten in uw cluster.
        2. ```spark_context=sc```is gebaseerd op de standaard Spark-context.
   + Als u een oude SDK-versie hebt, kunt u deze opheffen door de geïnstalleerde bibliotheken van het cluster te verwijderen en naar de Prullenbak te gaan. Installeer de nieuwe SDK-versie en start het cluster opnieuw op. Als er een probleem is nadat de computer opnieuw is opgestart, ontkoppelt u het cluster en koppelt u het opnieuw.

Als de installatie is voltooid, moet de geïmporteerde bibliotheek er ongeveer als volgt uitzien:

SDK voor Databricks **_zonder_** geautomatiseerde machine learning ![Azure machine learning SDK voor Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK voor Databricks **met** geautomatiseerde machine learning ![SDK met geautomatiseerde machine learning geïnstalleerd op Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Beginnen met verkennen

Uitproberen:
+ Down load het [archief bestand voor het notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) voor Azure Databricks/Azure machine learning SDK en [Importeer het archief bestand](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) in uw Databricks-cluster.
  Er zijn veel voorbeeld notitieblokken beschikbaar, **maar alleen [deze voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) werken met Azure Databricks.**

+ Meer informatie over het [maken van een pijp lijn met Databricks als de trainings Compute](how-to-create-your-first-pipeline.md).

## <a id="aznotebooks"></a>Azure-laptops

[Azure-notitieblokken](https://notebooks.azure.com) (preview) is een interactieve ontwikkelomgeving in de Azure-cloud. Het is een eenvoudige manier om aan de slag te gaan met Azure Machine Learning ontwikkeling.

* De Azure Machine Learning SDK is al geïnstalleerd.
* Nadat u een Azure Machine Learning service-werk ruimte hebt gemaakt in de Azure Portal, klikt u op een knop om uw Azure-notitieblok omgeving automatisch te configureren voor gebruik met de werk ruimte.

Gebruik de [Azure Portal](https://portal.azure.com) om aan de slag te gaan met Azure notebooks.  Open uw werk ruimte en selecteer in de sectie **overzicht** de optie **aan de slag in azure notebooks**.

Azure Notebooks maakt standaard gebruik van een gratis servicelaag die beperkt is tot 4 GB geheugen en 1 GB aan gegevens. U kunt deze limieten echter verwijderen door een Data Science Virtual Machine-exemplaar toe te voegen aan het Azure Notebooks-project. Zie [Azure notebooks projecten-Compute-laag beheren en configureren](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier)voor meer informatie.

## <a id="workspace"></a>Het configuratiebestand van een werkruimte maken

Het configuratie bestand van de werk ruimte is een JSON-bestand dat laat zien hoe de SDK communiceert met uw Azure Machine Learning service-werk ruimte. Het bestand heeft de naam *config. json*en heeft de volgende indeling:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Dit JSON-bestand moet zich in de mapstructuur bevinden die uw python-scripts of Jupyter-notebooks bevat. Deze kan zich in dezelfde map bevindt, in een submap met de naam *. azureml*of in een bovenliggende map.

U kunt dit bestand vanuit uw code gebruiken `ws=Workspace.from_config()`. Deze code wordt de informatie uit het bestand wordt geladen en maakt verbinding met uw werkruimte.

U kunt het configuratie bestand op drie manieren maken:

* **Gebruik [WS. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** : om een bestand *config. json* te schrijven. Het bestand bevat de configuratie gegevens voor uw werk ruimte. U kunt het *bestand config. json* downloaden of kopiëren naar andere ontwikkel omgevingen.

* **Het bestand downloaden**: Selecteer in de [Azure Portal](https://ms.portal.azure.com)de optie **down load config. json** in het gedeelte **overzicht** van uw werk ruimte.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Het bestand programmatisch maken**: In het volgende code fragment maakt u verbinding met een werk ruimte door de abonnements-ID, resource groep en werkruimte naam op te geven. Vervolgens wordt de werkruimte configuratie opgeslagen in het bestand:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Met deze code wordt het configuratie bestand naar het bestand *. azureml/config. json* geschreven.


## <a name="next-steps"></a>Volgende stappen

- [Een model](tutorial-train-models-with-aml.md) op Azure machine learning trainen met de MNIST-gegevensset
- Naslag informatie over de [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) weer geven