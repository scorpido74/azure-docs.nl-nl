---
title: Een Python-ontwikkelomgeving instellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het configureren van uw ontwikkelomgeving voor Azure Machine Learning. Gebruik Conda-omgevingen, maak configuratiebestanden en configureer uw eigen cloudgebaseerde notebookserver, Jupyter-notitieblokken, Azure Databricks, IdEs, codeeditors en de Virtuele Machine Data Science.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 10ea0002b2e99c4675f56e48a638f3c1cb87e6c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399011"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Een ontwikkelomgeving configureren voor Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u een ontwikkelomgeving configureert om te werken met Azure Machine Learning. Azure Machine Learning is platformagnostisch. De enige harde vereiste voor uw ontwikkelomgeving is Python 3. Een geïsoleerde omgeving zoals Anaconda of Virtualenv wordt ook aanbevolen.

De volgende tabel toont elke ontwikkelomgeving die in dit artikel wordt behandeld, samen met voor- en nadelen.

| Omgeving | Voordelen | Nadelen |
| --- | --- | --- |
| [Op cloud gebaseerde Azure Machine Learning-rekeninstantie (voorbeeld)](#compute-instance) | Gemakkelijkste manier om te beginnen. De volledige SDK is al geïnstalleerd in de VM van uw werkruimte en notebookzelfstudies zijn vooraf gekloond en klaar om uit te voeren. | Gebrek aan controle over uw ontwikkelomgeving en afhankelijkheden. Extra kosten voor Linux VM (VM kan worden gestopt wanneer niet in gebruik om kosten te voorkomen). Zie [prijsdetails](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Lokale omgeving](#local) | Volledige controle over uw ontwikkelomgeving en afhankelijkheden. Voer uit met elk buildtool, omgeving of IDE naar keuze. | Het duurt langer om te beginnen. Noodzakelijke SDK-pakketten moeten worden geïnstalleerd en er moet ook een omgeving worden geïnstalleerd als u er nog geen hebt. |
| [Azure Databricks](#aml-databricks) | Ideaal voor het uitvoeren van grootschalige intensieve machine learning workflows op het schaalbare Apache Spark platform. | Overkill voor experimentele machine learning, of kleinere experimenten en workflows. Extra kosten voor Azure Databricks. Zie [prijsdetails](https://azure.microsoft.com/pricing/details/databricks/). |
| [De Data Science Virtual Machine (DSVM)](#dsvm) | Vergelijkbaar met de cloud-gebaseerde compute instance (Python en de SDK zijn vooraf geïnstalleerd), maar met extra populaire data science en machine learning tools vooraf geïnstalleerd. Eenvoudig te schalen en te combineren met andere aangepaste tools en workflows. | Een tragere ervaring in het begin in vergelijking met de cloudgebaseerde compute-instantie. |

In dit artikel vindt u ook aanvullende gebruikstips voor de volgende hulpmiddelen:

* [Jupyter Notebooks:](#jupyter)Als u de Jupyter Notebook al gebruikt, heeft de SDK een aantal extra's die u moet installeren.

* [Visual Studio Code:](#vscode)Als u Visual Studio Code gebruikt, bevat de [Azure Machine Learning-extensie](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) uitgebreide taalondersteuning voor Python en functies om het werken met de Azure Machine Learning veel handiger en productiever te maken.

## <a name="prerequisites"></a>Vereisten

Een Azure Machine Learning-werkruimte. Zie Een Azure [Machine Learning-werkruimte maken](how-to-manage-workspace.md)als u de werkruimte wilt maken. Een werkruimte is alles wat u nodig hebt om aan de slag te gaan met uw eigen [cloudgebaseerde notebookserver,](#compute-instance)een [DSVM](#dsvm)of [Azure Databricks.](#aml-databricks)

Als u de SDK-omgeving voor uw [lokale computer](#local)wilt installeren, hebt u ook [de Jupyter Notebook-server](#jupyter) of [Visual Studio Code](#vscode) nodig:

- Ofwel de [Anaconda](https://www.anaconda.com/download/) of [Miniconda](https://conda.io/miniconda.html) package manager.

- Op Linux of macOS heb je de bash shell nodig.

    > [!TIP]
    > Als u op Linux of macOS bent en een andere shell dan bash gebruikt (bijvoorbeeld zsh) u fouten ontvangen wanneer u een aantal opdrachten uitvoert. Om dit probleem te `bash` omzeilen, gebruikt u de opdracht om een nieuwe bashshell te starten en de opdrachten daar uit te voeren.

- Op Windows hebt u de opdrachtprompt of Anaconda prompt (geïnstalleerd door Anaconda en Miniconda) nodig.

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Uw eigen cloudgebaseerde compute-exemplaar

De Azure Machine Learning [compute instance (preview)](concept-compute-instance.md) is een veilig, cloudgebaseerd Azure-workstation dat gegevenswetenschappers voorziet van een Jupyter-notebookserver, JupyterLab en een volledig voorbereide ML-omgeving.

Er is niets te installeren of te configureren voor een compute-instantie.  Maak er op elk gewenst moment een vanuit uw Azure Machine Learning-werkruimte. Geef alleen een naam op en geef een Azure VM-type op. Probeer het nu met deze [zelfstudie: installatieomgeving en werkruimte](tutorial-1st-experiment-sdk-setup.md).

Meer informatie over [rekeninstanties](concept-compute-instance.md).

Als u wilt stoppen met het maken van rekenkosten, [stopt u de rekeninstantie](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

De DSVM is een aangepaste virtuele machine (VM) afbeelding. Het is ontworpen voor data science werk dat vooraf is geconfigureerd met:

  - Pakketten zoals TensorFlow, PyTorch, Scikit-learn, XGBoost en de Azure Machine Learning SDK
  - Populaire data science tools zoals Spark Standalone en Drill
  - Azure-hulpprogramma's zoals de Azure CLI, AzCopy en Storage Explorer
  - Geïntegreerde ontwikkelomgevingen (IdEs) zoals Visual Studio Code en PyCharm
  - Jupyter-laptopserver

De Azure Machine Learning SDK werkt op de Ubuntu- of Windows-versie van de DSVM. Maar als u van plan bent om de DSVM ook als compute target te gebruiken, wordt alleen Ubuntu ondersteund.

Ga als een ontwikkelomgeving met de DSVM:

1. Maak een DSVM in een van de volgende omgevingen:

    * De Azure-portal:

        * [Een Ubuntu Data Science Virtual Machine maken](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Een Windows Data Science Virtual Machine maken](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * De Azure CLI:

        > [!IMPORTANT]
        > * Wanneer u de Azure CLI gebruikt, moet u zich `az login` eerst aanmelden bij uw Azure-abonnement met behulp van de opdracht.
        >
        > * Wanneer u de opdrachten in deze stap gebruikt, moet u een naam van de brongroep, een naam voor de vm, een gebruikersnaam en een wachtwoord opgeven.

        * Als u een Virtuele Ubuntu Data Science-machine wilt maken, gebruikt u de volgende opdracht:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Als u een Virtuele Machine van Windows Data Science wilt maken, gebruikt u de volgende opdracht:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. De Azure Machine Learning SDK is al geïnstalleerd op de DSVM. Als u de Conda-omgeving wilt gebruiken die de SDK bevat, gebruikt u een van de volgende opdrachten:

    * Voor Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Voor Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. Gebruik de volgende Python-code om te controleren of u toegang hebt tot de SDK en de versie controleren:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Zie de sectie [Een configuratiebestand voor werkruimte maken](#workspace) als u de DSVM wilt configureren om uw Azure Machine Learning-werkruimte te gebruiken.

Zie [Virtuele machines van Data Science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)voor meer informatie.

## <a name="local-computer"></a><a id="local"></a>Lokale computer

Wanneer u een lokale computer gebruikt (wat ook een externe virtuele machine kan zijn), maakt u een Anaconda-omgeving en installeert u de SDK. Hier volgt een voorbeeld:

1. Download en installeer [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7 versie) als je het nog niet hebt.

1. Open een Anaconda-prompt en maak een omgeving met de volgende opdrachten:

    Voer de volgende opdracht uit om de omgeving te maken.

    ```bash
    conda create -n myenv python=3.6.5
    ```

    Activeer vervolgens de omgeving.

    ```bash
    conda activate myenv
    ```

    In dit voorbeeld wordt een omgeving gemaakt met python 3.6.5, maar kunnen specifieke subversies worden gekozen. SDK-compatibiliteit is mogelijk niet gegarandeerd met bepaalde grote versies (3,5+ wordt aanbevolen) en het wordt aanbevolen om een andere versie/subversie in uw Anaconda-omgeving te proberen als u fouten tegenkomt. Het duurt enkele minuten om de omgeving te maken terwijl onderdelen en pakketten worden gedownload.

1. Voer de volgende opdrachten uit in uw nieuwe omgeving om omgevingsspecifieke IPython-kernels in te schakelen. Dit zorgt voor het verwachte importgedrag van kernel- en pakketten bij het werken met Jupyter-laptops binnen Anaconda-omgevingen:

    ```bash
    conda install notebook ipykernel
    ```

    Voer vervolgens de volgende opdracht uit om de kernel te maken:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Gebruik de volgende opdrachten om pakketten te installeren:

    Met deze opdracht installeert u de `automl` basis Azure Machine Learning SDK met notitieblokken en extra's. De `automl` extra is een grote installatie, en kan worden verwijderd uit de beugels als u niet van plan om geautomatiseerde machine learning experimenten uit te voeren. De `automl` extra bevat ook de Azure Machine Learning Data Prep SDK standaard als afhankelijkheid.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Als u een bericht ontvangt dat PyYAML niet kan worden verwijderd, gebruikt u in plaats daarvan de volgende opdracht:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Beginnend met macOS Catalina, zsh (Z shell) is de standaard login shell en interactieve shell. Gebruik in zsh de volgende opdracht die\\ontsnapt aan haakjes met " " (backslash):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Het installeren van de SDK duurt enkele minuten. Zie de [installatiehandleiding](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)voor meer informatie over installatieopties.

1. Installeer andere pakketten voor uw machine learning-experimenten.

    Gebruik een van de volgende opdrachten en vervang * \<nieuwe pakketpakketten>* door het pakket dat u wilt installeren. Het installeren `conda install` van pakketten via vereist dat het pakket deel uitmaakt van de huidige kanalen (nieuwe kanalen kunnen worden toegevoegd in Anaconda Cloud).

    ```bash
    conda install <new package>
    ```

    U ook pakketten `pip`installeren via.

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebooks maken deel uit van het [Jupyter Project.](https://jupyter.org/) Ze bieden een interactieve coderingservaring waarbij u documenten maakt die live code combineren met verhalende tekst en afbeeldingen. Jupyter-notitieblokken zijn ook een geweldige manier om uw resultaten met anderen te delen, omdat u de uitvoer van uw codesecties in het document opslaan. U Jupyter-laptops op verschillende platforms installeren.

De procedure in de sectie [Lokale computer](#local) installeert noodzakelijke componenten voor het uitvoeren van Jupyter-laptops in een Anaconda-omgeving.

Ga als volgende over de volgende onderdelen in uw Jupyter-laptopomgeving:

1. Open een Anaconda prompt en activeer je omgeving.

    ```bash
    conda activate myenv
    ```

1. Kloon [de GitHub-opslagplaats](https://aka.ms/aml-notebooks) voor een set voorbeeldnotitieblokken.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Start de Jupyter Notebook-server met de volgende opdracht:

    ```bash
    jupyter notebook
    ```

1. Als u wilt controleren of Jupyter Notebook de SDK kan gebruiken, maakt u een **nieuw** notitieblok, selecteert u **Python 3** als kernel en voert u de volgende opdracht uit in een notitieblokcel:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Als u problemen ondervindt bij `ModuleNotFoundError`het importeren van modules en een , zorgt u ervoor dat uw Jupyter-kernel is verbonden met het juiste pad voor uw omgeving door de volgende code in een notitieblokcel uit te voeren.

    ```python
    import sys
    sys.path
    ```

1. Als u het Jupyter-notitieblok wilt configureren om uw Azure Machine Learning-werkruimte te gebruiken, gaat u naar de sectie [Een werkruimteconfiguratiebestand maken.](#workspace)

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code is een zeer populaire cross-platform code editor die een uitgebreide set programmeertalen en tools ondersteunt door middel van extensies beschikbaar in de [Visual Studio marktplaats.](https://marketplace.visualstudio.com/vscode) De [Azure Machine Learning-extensie](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) installeert de [Python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python) voor codering in alle typen Python-omgevingen (virtueel, Anaconda, enz.). Bovendien biedt het handige functies voor het werken met Azure Machine Learning-resources en het uitvoeren van Azure Machine Learning-experimenten zonder Visual Studio Code te verlaten.

Ga als een gebruiksaanwijzing voor Visual Studio Code voor ontwikkeling:

1. Installeer de Azure Machine Learning-extensie voor Visual Studio Code, zie [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Zie [Azure Machine Learning gebruiken voor Visual Studio Code voor](tutorial-setup-vscode-extension.md)meer informatie.

1. Zie Aan [de slag met Python in VSCode](https://code.visualstudio.com/docs/python/python-tutorial)voor meer informatie over het gebruik van Visual Studio Code voor elk type Python-ontwikkeling.

    - Als u de SDK Python-omgeving wilt selecteren die de SDK bevat, opent u VS-code en selecteert u Ctrl+Shift+P (Linux en Windows) of Command+Shift+P (Mac).
        - Het __opdrachtpalet__ wordt geopend.

    - __Python invoeren: Tolk selecteren__en vervolgens de juiste omgeving selecteren

1. Als u wilt valideren dat u de SDK gebruiken, maakt u een nieuw Python-bestand (.py) dat de volgende code bevat:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Voer deze code uit door op de CodeLens "Cel uitvoeren" te klikken of druk gewoon op shift-enter.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks is een op Apache Spark gebaseerde omgeving in de Azure-cloud. Het biedt een collaboratieve notebook-gebaseerde omgeving met CPU of GPU-gebaseerde compute cluster.

Hoe Azure Databricks werkt met Azure Machine Learning:
+ U een model trainen met Spark MLlib en het model implementeren in ACI/AKS vanuit Azure Databricks.
+ U ook [geautomatiseerde machine learning-mogelijkheden](concept-automated-ml.md) gebruiken in een speciale Azure ML SDK met Azure Databricks.
+ U Azure Databricks gebruiken als rekendoel van een [Azure Machine Learning-pijplijn.](concept-ml-pipelines.md)

### <a name="set-up-your-databricks-cluster"></a>Uw Databricks-cluster instellen

Een [cluster met Databricks maken](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Sommige instellingen zijn alleen van toepassing als u de SDK installeert voor geautomatiseerde machine learning op Databricks.
**Het duurt enkele minuten om het cluster te maken.**

Gebruik deze instellingen:

| Instelling |Van toepassing op| Waarde |
|----|---|---|
| Clusternaam |altijd| yourclusternaam |
| Databricks Runtime |altijd|Niet-ML Runtime 6.5 (scala 2.11, vonk 2.4.3) |
| Python-versie |altijd| 3 |
| Werknemers |altijd| 2 of hoger |
| VM-typen werkknooppunt <br>(bepaalt het maximumaantal gelijktijdige iteraties) |Geautomatiseerde machine learning<br>Alleen| Geheugengeoptimaliseerde VM-voorkeur |
| Automatisch schalen inschakelen |Geautomatiseerde machine learning<br>Alleen| Uitschakelen |

Wacht tot het cluster wordt uitgevoerd voordat u verder gaat.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>De juiste SDK installeren in een Databricks-bibliotheek
Zodra het cluster actief is, [maakt u een bibliotheek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) om het juiste Azure Machine Learning SDK-pakket aan uw cluster te koppelen.

1. Klik met de rechtermuisknop op de huidige map Werkruimte waar u de bibliotheek wilt opslaan. Selecteer**Bibliotheek** **maken** > .

1. Kies **slechts één** optie (er wordt geen andere SDK-installatie ondersteund)

   |SDK-pakket&nbsp;&nbsp;extra's|Bron|PyPi-naam&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Voor Databricks| Python Egg of PyPI uploaden | azureml-sdk[databricks]|
   |Voor Databricks -met-<br> geautomatiseerde ML-mogelijkheden| Python Egg of PyPI uploaden | azureml-sdk[automl]|

   > [!Warning]
   > Er kunnen geen andere SDK-extra's worden geïnstalleerd. Kies slechts één van de voorgaande opties [databricks] of [automl].

   * Selecteer Niet **automatisch koppelen aan alle clusters**.
   * Selecteer **Bijvoegen** naast de naam van het cluster.

1. Controleer op fouten totdat de status is gewijzigd **in Attached,** wat enkele minuten kan duren.  Als deze stap mislukt:

   Probeer het cluster opnieuw te starten door:
   1. Selecteer **Clusters**in het linkerdeelvenster .
   1. Selecteer in de tabel de naam van het cluster.
   1. Selecteer opnieuw **starten**op het tabblad **Bibliotheken** .

   Denk ook aan:
   + Voeg in AutoML config bij het gebruik van Azure Databricks de volgende parameters toe:
       1. ```max_concurrent_iterations```is gebaseerd op het aantal werknemersknooppunten in uw cluster.
        2. ```spark_context=sc```is gebaseerd op de standaardvonkcontext.
   + Of als u een oude SDK-versie hebt, schakelt u deze uit de geïnstalleerde libs van het cluster uit en gaat u naar de prullenbak. Installeer de nieuwe SDK-versie en start het cluster opnieuw. Als er een probleem is na het opnieuw opstarten, moet u het cluster loskoppelen en opnieuw koppelen.

Als de installatie is geslaagd, moet de geïmporteerde bibliotheek er als een van deze uitzien:

SDK voor **_without_** Databricks zonder ![geautomatiseerde machine learning Azure Machine Learning SDK voor Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK voor **WITH** Databricks MET ![geautomatiseerde machine learning SDK met geautomatiseerde machine learning geïnstalleerd op Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Begin met verkennen

Probeer het uit:
+ Hoewel er veel voorbeeldnotitieblokken beschikbaar zijn, **werken alleen [deze voorbeeldnotitieblokken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) met Azure Databricks.**

+ Importeer deze voorbeelden rechtstreeks vanuit uw werkruimte. Zie hieronder: ![](./media/how-to-configure-environment/azure-db-screenshot.png)
![Selecteer Importpanel importeren](./media/how-to-configure-environment/azure-db-import.png)

+ Meer informatie over het [maken van een pijplijn met Databricks als trainingsberekening.](how-to-create-your-first-pipeline.md)

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Een configuratiebestand voor werkruimtes maken

Het configuratiebestand voor werkruimtes is een JSON-bestand dat de SDK vertelt hoe u met uw Azure Machine Learning-werkruimte moet communiceren. Het bestand heeft de naam *config.json*en heeft de volgende indeling:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Dit JSON-bestand moet zich in de mapstructuur bevinden die uw Python-scripts of Jupyter-notitieblokken bevat. Het kan zich in dezelfde map bevinden, een submap met de naam *.azureml,* of in een bovenliggende map.

Als u dit bestand wilt `ws=Workspace.from_config()`gebruiken vanaf uw code, gebruikt u . Deze code laadt de informatie uit het bestand en maakt verbinding met uw werkruimte.

U het configuratiebestand op drie manieren maken:

* **Gebruik [ws.write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)**: om een *config.json-bestand* te schrijven. Het bestand bevat de configuratiegegevens voor uw werkruimte. U de *config.json* downloaden of kopiëren naar andere ontwikkelomgevingen.

* **Het bestand downloaden**: Selecteer in de [Azure-portal](https://ms.portal.azure.com) **config.json downloaden** in het gedeelte **Overzicht** van uw werkruimte.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Het bestand programmatisch maken:** in het volgende codefragment maakt u verbinding met een werkruimte door de naam van de abonnements-id, de brongroep en de werkruimteop te geven. Vervolgens wordt de werkruimteconfiguratie opgeslagen in het bestand:

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

    Deze code schrijft het configuratiebestand naar het *bestand .azureml/config.json.*

## <a name="next-steps"></a>Volgende stappen

- [Een model trainen](tutorial-train-models-with-aml.md) op Azure Machine Learning met de MNIST-gegevensset
- De [Azure Machine Learning SDK voor Python-verwijzing](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) weergeven
