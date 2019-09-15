---
title: Visual Studio code gebruiken voor machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over het installeren van Azure Machine Learning voor Visual Studio code en het maken van een eenvoudig experiment in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ronglu
author: ronglums
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 0507080a390a4bb9f981b0d911961337e9b2c62a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997180"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Aan de slag met Azure Machine Learning voor Visual Studio code

In dit artikel leert u hoe u de uitbrei ding voor Azure Machine Learning voor Visual Studio code kunt gebruiken om machine learning en diepe leer modellen te trainen en te implementeren.

[Azure machine learning](overview-what-is-azure-ml.md) biedt ondersteuning voor experimenten die lokaal en op externe Compute-doelen worden uitgevoerd. Voor elke experiment, u kunt bijhouden van meerdere wordt uitgevoerd als u wilt vaak iteratief probeert verschillende technieken en hyperparameters. Azure Machine Learning kunt u aangepaste metrische gegevens bijhouden en experimenteren wordt uitgevoerd, data science reproduceerbaarheid en controleerbaarheid inschakelen.

U kunt deze modellen ook implementeren voor uw test-en productie behoeften.

## <a name="prerequisites"></a>Vereisten

+ Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

+ Visual Studio code moet zijn geïnstalleerd. Visual Studio code is een licht gewicht, maar krachtige bron code-editor die op uw bureau blad wordt uitgevoerd. Het wordt geleverd met ingebouwde ondersteuning voor python en andere programmeer talen. [Lees hoe](https://code.visualstudio.com/docs/setup/setup-overview)u Visual Studio code nog niet hebt geïnstalleerd.

+ [Installeer Python 3,5 of hoger](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>De uitbrei ding voor de Azure Machine Learning voor Visual Studio code installeren

Wanneer u de uitbrei ding van Azure Machine Learning installeert, worden er automatisch twee uitbrei dingen geïnstalleerd (als u toegang hebt tot internet). Ze zijn de [Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) en de [micro soft python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Als u met Azure Machine Learning wilt werken, moet u Visual Studio code omzetten in een python-Integrated Development Environment (IDE). U hebt de micro soft python-extensie nodig voor het gebruik [van python in Visual Studio code](https://code.visualstudio.com/docs/languages/python). Deze uitbrei ding wordt automatisch geïnstalleerd met de extensie Azure Machine Learning. De uitbrei ding maakt Visual Studio code een uitstekende IDE en werkt op elk besturings systeem met diverse python-interpreters. De micro soft python-extensie maakt gebruik van de kracht van Visual Studio code om AutoAanvullen, IntelliSense, pluis, fout opsporing en eenheids tests te bieden. De uitbrei ding biedt u ook de mogelijkheid om eenvoudig te scha kelen tussen python-omgevingen, met inbegrip van virtuele en Conda omgevingen. Zie de zelf studie over het gebruik van [python Hello-wereld](https://code.visualstudio.com/docs/python/python-tutorial)voor meer informatie over het bewerken, uitvoeren en fout opsporing van python-code.

De Azure Machine Learning-extensie installeren:

1. Open Visual Studio Code.

1. Ga in een webbrowser naar [Azure machine learning voor Visual Studio code extension (preview)](https://aka.ms/vscodetoolsforai).

1. Selecteer op die webpagina **installeren**. 

1. Selecteer **installeren**op het tabblad uitbrei ding.

1. Er wordt een welkomst tabblad voor de uitbrei ding geopend in Visual Studio code en het Azure-symbool (dat rood wordt weer gegeven in de volgende scherm afbeelding) wordt toegevoegd aan de activiteiten balk.

   ![Azure-pictogram op de activiteiten balk van Visual Studio](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Selecteer **Aanmelden** in het dialoog venster en volg de prompts om te verifiëren met Azure. 
   
   De extensie van het Azure-account, dat samen met de Azure Machine Learning voor Visual Studio code-extensie is geïnstalleerd, helpt u bij het verifiëren van uw Azure-account. Zie de pagina voor de [Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)voor een lijst met opdrachten.

> [!Tip] 
> Bekijk de [IntelliCode-extensie voor Visual Studio code (preview)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode biedt een reeks AI-ondersteunde mogelijkheden voor IntelliSense in Python, zoals het uitstellen van de meest relevante automatisch aansluitingen op basis van de huidige code context.

## <a name="install-the-azure-machine-learning-sdk"></a>De Azure Machine Learning SDK installeren

1. Zorg ervoor dat python 3,5 of hoger is geïnstalleerd en wordt herkend door Visual Studio code. Als u de app nu installeert, start u Visual Studio code opnieuw en [selecteert u een Python-interpreter](https://code.visualstudio.com/docs/python/python-tutorial).

1. Geef in het venster geïntegreerd Terminal de Python-interpreter op die u wilt gebruiken. Of selecteer ENTER om uw standaard Python-interpreter te gebruiken.

   ![De interpreter kiezen](./media/vscode-tools-for-ai/python.png)

1. In de rechter benedenhoek van het venster wordt een melding weer gegeven dat aangeeft dat de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) automatisch wordt geïnstalleerd. De zojuist gemaakte python-omgeving is lokaal en privé en bevat de Visual Studio-code vereisten voor het werken met Azure Machine Learning.

   ![De Azure Machine Learning SDK voor python installeren](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Aan de slag met Azure Machine Learning

Voordat u begint met het trainen en implementeren van machine learning modellen in Visual Studio code, moet u een [Azure machine learning-werk ruimte](concept-workspace.md) maken in de Cloud. Deze werk ruimte bevat uw modellen en resources. 

Om een werk ruimte te maken en uw eerste experiment toe te voegen:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

   [![Een werk ruimte maken](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Klik met de rechter muisknop op uw Azure-abonnement en selecteer **werk ruimte maken**. Er wordt een lijst weer gegeven. In het voor beeld van een afbeelding met animatie is de naam van het abonnement een **gratis proef versie**en is de werk ruimte **TeamWorkspace**. 

1. Selecteer een resource groep in de lijst of maak een nieuwe met behulp van de wizard in het opdracht palet.

1. Typ in het veld een unieke en duidelijke naam voor uw nieuwe werk ruimte. In de voorbeeld afbeelding heeft de werk ruimte de naam **TeamWorkspace**.

1. Selecteer ENTER om de nieuwe werk ruimte te maken. Deze wordt weer gegeven in de boom structuur, onder de naam van het abonnement.

1. Klik met de rechter muisknop op het knoop punt **experiment** en kies **experiment maken** in het context menu.  Met experimenten houdt u uw uitvoeringen bij met Azure Machine Learning.

1. Voer in het veld een naam in voor uw experiment. In de scherm afbeeldingen voor beeld wordt het experiment **MNIST**genoemd.
 
1. Selecteer ENTER om het nieuwe experiment te maken. Het experiment wordt weer gegeven in de boom structuur onder de naam van de werk ruimte.

1. In een werk ruimte kunt u met de rechter muisknop op een experiment klikken om dit als het **actieve** experiment in te stellen. Het **actieve** experiment is uw huidige experiment. Uw geopende map in Visual Studio code wordt gekoppeld aan dit experiment in de Cloud. Deze map moet uw lokale python-scripts bevatten.

Nu worden de belangrijkste metrische gegevens opgeslagen in de geschiedenis van het experiment. Op dezelfde manier worden de modellen die u traint automatisch geüpload naar Azure Machine Learning en opgeslagen naast de metrische gegevens en logboeken van uw experiment. 

[![Een map in Visual Studio code koppelen](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Maken en beheren van compute-doelen

Met Azure Machine Learning voor Visual Studio code kunt u uw gegevens voorbereiden, modellen trainen en deze zowel lokaal als op externe Compute-doelen implementeren.

De extensie ondersteunt diverse externe Compute-doelen voor Azure Machine Learning. Zie de volledige lijst met ondersteunde COMPUTE- [doelen voor Azure machine learning](how-to-set-up-training-targets.md)voor meer informatie.

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Reken doelen voor Azure Machine Learning maken in Visual Studio code

Een reken doel maken:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

2. Vouw in de structuur weergave uw Azure-abonnement en Azure Machine Learning-werk ruimte uit. In het volgende voor beeld is de naam van het abonnement een **gratis proef versie**en is de werk ruimte **TeamWorkspace**. 

3. Onder het werkruimteknooppunt met de rechtermuisknop op de **Compute** knooppunt en kies **maken Compute**.

4. Kies het doeltype compute in de lijst. 

5. Selecteer de grootte van een virtuele machine in het opdracht palet.

6. Voer in het opdracht palet in het veld een naam in voor het berekenings doel. 

7. In het JSON-configuratie bestand dat wordt geopend op een nieuw tabblad, geeft u geavanceerde eigenschappen op. U kunt eigenschappen opgeven, zoals een maximum aantal knoop punten.

8. Wanneer u klaar bent met het configureren van uw reken doel, selecteert u in de rechter benedenhoek van het venster **verzenden**.

Hier volgt een voor beeld van het maken van een Azure Machine Learning Compute (AMLCompute):

[![AML-berekeningen maken in Visual Studio code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Het configuratie bestand voor de uitvoering

De Visual Studio code extension maakt automatisch een lokale Compute-doel en voert configuraties uit voor uw lokale en docker-omgevingen op uw lokale computer. U vindt de uitvoer configuratie bestanden onder het knoop punt gekoppelde reken doel. 

## <a name="train-and-tune-models"></a>Modellen trainen en afstemmen

Gebruik Azure Machine Learning voor Visual Studio code (preview) om snel uw code te herhalen, stapsgewijs te door lopen en fouten op te lossen en uw oplossing te gebruiken voor broncode beheer. 

Als u uw experiment lokaal wilt uitvoeren met behulp van Azure Machine Learning:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

1. Vouw in de structuur weergave uw Azure-abonnement en Azure Machine Learning-werk ruimte uit. 

1. Vouw onder het knoop punt werk ruimte het **reken** knooppunt uit en klik met de rechter muisknop op de **Voer configuratie** van de compute die u wilt gebruiken. 

1. Selecteer **Experiment uit te voeren**.

1. Selecteer in de Verkenner het script dat u wilt uitvoeren. 

1. Selecteer **experimenteren weer geven** om de geïntegreerde Azure machine learning portal te bekijken om uw uitvoeringen te controleren en uw getrainde modellen te bekijken.

Hier volgt een voor beeld van het lokaal uitvoeren van een experiment:

[![Een lokaal experiment uitvoeren](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Externe berekeningen gebruiken voor experimenten in Visual Studio code

Als u een extern Compute-doel voor training wilt gebruiken, moet u een uitvoerings configuratie bestand maken. Dit bestand vertelt Azure Machine Learning-niet alleen waar u uw experiment uit te voeren, maar ook hoe u de omgeving voorbereidt.

#### <a name="the-conda-dependencies-file"></a>Het conda-afhankelijkheidsbestand

Er wordt standaard een nieuwe Conda-omgeving voor u gemaakt en uw installatie-afhankelijkheden worden beheerd. U moet echter uw afhankelijkheden en hun versie opgeven in het bestand *aml_config/conda_dependencies. yml* . 

Het volgende code fragment van de standaard *aml_config/conda_dependencies. yml* geeft aan `tensorflow=1.12.0`. Als u de versie van de afhankelijkheid niet opgeeft, wordt de meest recente versie gebruikt. U kunt extra afhankelijkheden toevoegen in het configuratiebestand.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

Als u uw experiment wilt uitvoeren met Azure Machine Learning op een extern Compute-doel:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

1. Vouw in de structuur weergave uw Azure-abonnement en Azure Machine Learning-werk ruimte uit. 

1. Klik in het editor-venster met de rechter muisknop op het python- **script en selecteer AML: Voer uit als experiment in**Azure. 

1. Selecteer in het opdracht palet het reken doel. 

1. Voer in het opdracht palet in het veld de naam van de uitvoerings configuratie in. 

1. Bewerk het bestand *conda_dependencies. yml* om de runtime-afhankelijkheden van het experiment op te geven. Selecteer vervolgens in de rechter benedenhoek van het venster **verzenden**. 

1. Selecteer **experimenteren weer geven** om de geïntegreerde Azure machine learning portal te bekijken om uw uitvoeringen te controleren en uw getrainde modellen te bekijken.

Hier volgt een voor beeld van het uitvoeren van een experiment op een extern Compute-doel:

[![Een experiment uitvoeren op een extern doel](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Modellen implementeren en beheren
In Azure Machine Learning kunt u uw machine learning modellen implementeren en beheren in de Cloud en aan de rand. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Uw model registreren bij Azure Machine Learning van Visual Studio code

Nu u uw model hebt getraind, kunt u dit registreren in uw werk ruimte. U kunt geregistreerde modellen volgen en implementeren.

Uw model registreren:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

1. Vouw in de structuur weergave uw Azure-abonnement en Azure Machine Learning-werk ruimte uit.

1. Onder het werkruimteknooppunt met de rechtermuisknop op **modellen** en kies **Model registreren**.

1. Voer in het opdracht palet in het veld een model naam in. 

1. Kies in de lijst of u een **model bestand** (voor één model) of een **modelsubsite** (voor modellen met meerdere bestanden, zoals tensor flow) wilt uploaden. 

1. Selecteer uw map of bestand.

1. Wanneer u klaar bent met het configureren van de model eigenschappen, selecteert u in de rechter benedenhoek van het venster **verzenden**. 

Hier volgt een voor beeld van het registreren van uw model bij Azure Machine Learning:

[![Een model registreren voor AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Implementeer uw service vanuit Visual Studio code

In Visual Studio code kunt u uw webservice implementeren voor het volgende:
+ Azure Container Instances (ACI) voor het testen.
+ Azure Kubernetes service (AKS) voor productie.

U hoeft geen ACI-container te maken om vooraf te testen, omdat ACI-containers in de vlucht worden gemaakt. U moet echter wel AKS-clusters vooraf configureren. Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie.

Een webservice implementeren:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

1. Vouw uw Azure-abonnement en uw Azure Machine Learning-werk ruimte uit in de structuur weergave.

1. Vouw onder het werkruimteknooppunt, de **modellen** knooppunt.

1. Klik met de rechter muisknop op het model dat u wilt implementeren en kies **service implementeren vanuit geregistreerd model** in het context menu.

1. Kies in het opdracht palet het berekenings doel waarnaar u wilt implementeren. 

1. Voer in het opdracht palet in het veld een naam in voor deze service.  

1. Selecteer in het opdracht palet de Enter-toets op het toetsen bord om het script bestand te zoeken en te selecteren.

1. Selecteer in het opdracht palet de Enter-toets op het toetsen bord om naar het Conda-afhankelijkheids bestand te bladeren en dit te selecteren.

1. Wanneer u klaar bent met het configureren van de service-eigenschappen, selecteert u in de rechter benedenhoek van het venster **verzenden** om te implementeren. In het eigenschappen bestand van de service kunt u een lokaal docker-bestand of een schema. JSON-bestand opgeven.

De webservice is nu geïmplementeerd.

Hier volgt een voor beeld van hoe u een webservice implementeert:

[![Een webservice implementeren](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Sneltoetsen gebruiken

U kunt het toetsen bord gebruiken om toegang te krijgen tot Azure Machine Learning functies in Visual Studio code. De belangrijkste toetsenbord snel toets die u wilt weten is CTRL + SHIFT + P, waarmee het opdracht palet wordt weer gegeven. Vanuit het opdracht palet hebt u toegang tot alle functies van Visual Studio code, inclusief sneltoetsen voor de meest voorkomende bewerkingen.

[![Sneltoetsen voor de Azure Machine Learning voor Visual Studio code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie voor een overzicht van het trainen van Azure machine learning buiten Visual Studio code: Train modellen met Azure Machine Learning](tutorial-train-models-with-aml.md).
* Zie de zelf studie over het gebruik van de [python Hello-wereld](https://code.visualstudio.com/docs/python/python-tutorial)voor een overzicht van het lokaal bewerken, uitvoeren en fout opsporing van code.
