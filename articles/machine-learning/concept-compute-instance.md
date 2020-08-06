---
title: Wat is een Azure Machine Learning-rekeninstantie?
titleSuffix: Azure Machine Learning
description: Meer informatie over de Azure Machine Learning Compute-instantie, een volledig beheerd werk station in de Cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: f4938d517d9a5c244045798a79f31b96bacd03f5
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829438"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Wat is een Azure Machine Learning-rekeninstantie?

Een Azure Machine Learning Compute-instantie is een beheerd werk station in de Cloud voor gegevens wetenschappers.

Reken instanties maken het eenvoudig om aan de slag te gaan met Azure Machine Learning-ontwikkeling en bieden mogelijkheden voor het beheer en de bedrijfs voorbereiding voor IT-beheerders.  

Gebruik een reken instantie als uw volledig geconfigureerde en beheerde ontwikkel omgeving in de Cloud voor machine learning. Ze kunnen ook worden gebruikt als een reken doel voor training en demijnen voor ontwikkelings-en test doeleinden.  

Gebruik voor de training van productie kwaliteit model een [Azure machine learning Compute-Cluster](how-to-set-up-training-targets.md#amlcompute) met mogelijkheden voor schalen op meerdere knoop punten. Gebruik [Azure Kubernetes service-cluster](how-to-deploy-azure-kubernetes-service.md)voor productie kwaliteit van het model.

## <a name="why-use-a-compute-instance"></a>Waarom een reken instantie gebruiken?

Een reken instantie is een volledig beheerd werk station in de cloud dat is geoptimaliseerd voor uw machine learning-ontwikkel omgeving. Het biedt de volgende voor delen:

|Belangrijkste voordelen|Beschrijving|
|----|----|
|Productiviteit|U kunt modellen bouwen en implementeren met behulp van geïntegreerde notebooks en de volgende hulpprogram ma's in Azure Machine Learning studio:<br/>-Jupyter<br/>-Jjupyterlab<br/>-RStudio (preview-versie)<br/>Reken instantie is volledig geïntegreerd met Azure Machine Learning werk ruimte en Studio. U kunt notitie blokken en gegevens delen met andere gegevens wetenschappers in de werk ruimte. U kunt ook externe ontwikkeling via code instellen met behulp van [SSH](how-to-set-up-vs-code-remote.md) |
|Beheerde & beveiligd|Verminder uw beveiligings footprint en voeg naleving toe met beveiligings vereisten voor ondernemingen. Reken instanties bieden robuust beheer beleid en beveiligde netwerk configuraties zoals:<br/><br/>-Automatisch inrichten vanuit Resource Manager-sjablonen of Azure Machine Learning SDK<br/>- [Op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC)](/azure/role-based-access-control/overview)<br/>- [Ondersteuning voor virtuele netwerken](how-to-enable-virtual-network.md#compute-instance)<br/>-SSH-beleid voor het inschakelen/uitschakelen van SSH-toegang<br/>TLS 1,2 ingeschakeld |
|Vooraf geconfigureerd &nbsp; voor &nbsp; ml|Bespaar tijd bij het instellen van taken met vooraf geconfigureerde en up-to-date ML-pakketten, diepe leer frameworks, GPU-Stuur Programma's.|
|Volledig aanpasbaar|Uitgebreide ondersteuning voor Azure VM-typen, waaronder Gpu's en persistente aanpassing op laag niveau, zoals het installeren van pakketten en stuur Programma's, maakt een koud probleem van geavanceerde scenario's. |

## <a name="tools-and-environments"></a><a name="contents"></a>Hulpprogram ma's en omgevingen

> [!IMPORTANT]
> De hulpprogram ma's die zijn gemarkeerd (preview) zijn momenteel beschikbaar als open bare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met Azure Machine Learning Compute-instantie kunt u modellen ontwerpen, trainen en implementeren in een volledig geïntegreerde laptop ervaring in uw werk ruimte.

Deze hulpprogram ma's en omgevingen zijn geïnstalleerd op het reken exemplaar: 

|Algemene hulpprogram ma's & omgevingen|Details|
|----|:----:|
|Stuurprogramma's|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotheek||
|Azure CLI ||
|Azure Machine Learning-voor beelden ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|**R** -hulpprogram ma's & omgevingen|Details|
|----|:----:|
|RStudio server open source Edition (preview-versie)||
|R-kernel||
|Azure Machine Learning SDK voor R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK steekproeven|

|**PYTHON** -hulpprogram ma's & omgevingen|Details|
|----|----|
|Anaconda Python||
|Jupyter en-extensies||
|Jjupyterlab en-extensies||
[Azure Machine Learning-SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>van PyPI|Omvat het meren deel van de extra azureml-pakketten.  Als u de volledige lijst wilt weer geven, [opent u een Terminal venster op uw reken exemplaar](how-to-run-jupyter-notebooks.md#terminal) en voert u uit <br/> `conda list -n azureml_py36 azureml*` |
|Andere PyPI-pakketten|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-pakketten|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Uitgebreide leer pakketten|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-pakketten|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning python & R SDK-voor beelden||

Python-pakketten zijn allemaal geïnstalleerd in de **Python 3,6-AzureML-** omgeving.  

### <a name="installing-packages"></a>Pakketten installeren

U kunt pakketten rechtstreeks in een Jupyter-notebook of Rstudio installeren:

* RStudio gebruik het tabblad **pakketten** aan de rechter kant of op het tabblad **console** linksboven.  
* Python: Voeg installatie code toe en voer uit in een Jupyter notebook-cel.

Of u kunt op een van de volgende manieren toegang krijgen tot een Terminal venster:

* RStudio: Selecteer het tabblad **Terminal** aan de rechter bovenhoek.
* Jupyter Lab: Selecteer de tegel **Terminal** onder de **andere** kop op het tabblad Start.
* Jupyter: Selecteer **nieuwe>Terminal** rechtsboven op het tabblad bestanden.
* SSH naar de computer.  Installeer Python-pakketten vervolgens in de **Python 3,6-AzureML-** omgeving.  R-pakketten installeren in de **R** -omgeving.

## <a name="accessing-files"></a>Toegang tot bestanden

Notebooks en R-scripts worden opgeslagen in het standaard opslag account van uw werk ruimte in de Azure-bestands share.  Deze bestanden bevinden zich in de map gebruikers bestanden. Met deze opslag kunt u eenvoudig notitie blokken delen tussen Compute-exemplaren. Het opslag account zorgt er ook voor dat uw notitie blokken veilig blijven behouden wanneer u een reken instantie stopt of verwijdert.

Het account van de Azure-bestands share van uw werk ruimte is gekoppeld als een station op het reken exemplaar. Dit station is de standaard werkmap voor Jupyter, Jupyter Labs en RStudio. Dit betekent dat de notitie blokken en andere bestanden die u maakt in Jupyter, Jjupyterlab of RStudio automatisch worden opgeslagen op de bestands share en ook beschikbaar zijn voor gebruik in andere reken instanties.

De bestanden in de bestands share zijn toegankelijk vanuit alle reken instanties in dezelfde werk ruimte. Wijzigingen in deze bestanden op het reken exemplaar worden betrouwbaar weer gegeven in de bestands share.

U kunt de meest recente Azure Machine Learning-voor beelden ook klonen naar uw map in de map gebruikers bestanden in de werkruimte bestands share.

Het schrijven van kleine bestanden kan langzamer zijn op netwerk stations dan het schrijven naar de lokale schijf van de reken instantie zelf.  Als u veel kleine bestanden schrijft, kunt u een map rechtstreeks op het reken exemplaar gebruiken, zoals een `/tmp` Directory. Houd er rekening mee dat deze bestanden niet toegankelijk zijn vanuit andere compute-exemplaren. 

U kunt de `/tmp` Directory op het reken exemplaar voor uw tijdelijke gegevens gebruiken.  Schrijf echter geen grote gegevens bestanden op de besturingssysteem schijf van het reken exemplaar.  Gebruik in plaats daarvan [gegevens opslag](concept-azure-machine-learning-architecture.md#datasets-and-datastores) . Als u de Jjupyterlab Git-extensie hebt geïnstalleerd, kan deze ook leiden tot vertraging in de prestaties van reken instanties.

## <a name="managing-a-compute-instance"></a>Een reken instantie beheren

Selecteer in uw werk ruimte in Azure Machine Learning Studio **Compute**en selecteer vervolgens **Compute instance** bovenin.

![Een reken instantie beheren](./media/concept-compute-instance/manage-compute-instance.png)

U kunt de volgende acties uitvoeren:

* [Maak een reken instantie](#create). 
* Vernieuw het tabblad Compute instances.
* Een reken instantie starten, stoppen en opnieuw starten.  U betaalt voor de instantie wanneer deze wordt uitgevoerd. Stop de reken instantie wanneer u deze niet gebruikt om de kosten te verlagen. Als u een reken instantie stopt, wordt de toewijzing ervan ongedaan. Start het vervolgens opnieuw wanneer u het nodig hebt. 
* Een reken instantie verwijderen.
* De lijst met reken instanties filteren op de exemplaren die u hebt gemaakt.  Dit zijn de reken instanties waartoe u toegang hebt.

Voor elk reken exemplaar in uw werk ruimte waartoe u toegang hebt, kunt u het volgende doen:

* Toegang tot Jupyter, Jjupyterlab, RStudio op het reken exemplaar
* SSH naar Compute-instantie. SSH-toegang is standaard uitgeschakeld, maar kan worden ingeschakeld op het moment dat het reken proces wordt gemaakt. SSH-toegang is via het mechanisme voor open bare/persoonlijke sleutels. Op het tabblad krijgt u details over SSH-verbindingen zoals IP-adres, gebruikers naam en poort nummer.
* Details ophalen over een specifiek reken exemplaar, zoals het IP-adres en de regio.

Met [RBAC](/azure/role-based-access-control/overview) kunt u bepalen welke gebruikers in de werk ruimte een reken instantie kunnen maken, verwijderen, starten, stoppen en opnieuw starten. Alle gebruikers in de rol Inzender en eigenaar van de werk ruimte kunnen reken instanties maken, verwijderen, starten, stoppen en opnieuw starten in de werk ruimte. Alleen de maker van een specifiek reken exemplaar heeft echter toegang tot Jupyter, Jjupyterlab en RStudio op die reken instantie. De maker van het reken exemplaar heeft het reken exemplaar dat eraan is toegewezen, heeft toegang tot de hoofdmap en kan worden terminal in via Jupyter/Jjupyterlab/RStudio. Reken instantie heeft een aanmelding voor één gebruiker van de maker gebruiker en alle acties gebruiken de identiteit van die gebruiker voor RBAC en de toewijzing van experimenten. SSH-toegang wordt beheerd via het mechanisme voor open bare/persoonlijke sleutels.

Deze acties kunnen worden beheerd door RBAC:
* *Micro soft. MachineLearningServices/werk ruimten/reken-en lees bewerkingen*
* *Micro soft. MachineLearningServices/werk ruimten/reken kracht/schrijven*
* *Micro soft. MachineLearningServices/werk ruimten/berekenen/verwijderen*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/starten/actie*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/stoppen/actie*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/opnieuw opstarten/actie*

### <a name="create-a-compute-instance"></a><a name="create"></a>Een reken instantie maken

Maak in uw werk ruimte in Azure Machine Learning Studio een nieuw reken exemplaar van het gedeelte **Compute** of in de sectie **notebooks** , wanneer u klaar bent om een van uw notitie blokken uit te voeren.

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Een nieuw reken exemplaar maken":::


|Veld  |Beschrijving  |
|---------|---------|
|Naam berekening     |  <li>De naam is vereist en moet tussen de 3 en 24 tekens lang zijn.</li><li>Geldige tekens zijn onder andere hoofd letters, cijfers en het **-** teken.</li><li>De naam moet beginnen met een letter</li><li>De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest, niet uniek is</li><li>Als het **-** teken wordt gebruikt, moet dit worden gevolgd door ten minste één letter later in de naam</li>     |
|Type virtuele machine |  Kies CPU of GPU. Dit type kan niet worden gewijzigd nadat het is gemaakt     |
|Grootte van de virtuele machine     |  Ondersteunde grootten voor virtuele machines kunnen worden beperkt in uw regio. De [beschikbaarheids lijst](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) controleren     |
|SSH-toegang inschakelen/uitschakelen     |   SSH-toegang is standaard uitgeschakeld.  SSH-toegang kan niet. gewijzigd na het maken. Zorg ervoor dat u toegang inschakelt als u van plan bent om interactief fouten op te lossen met [versus externe code](how-to-set-up-vs-code-remote.md)   |
|Geavanceerde instellingen     |  Optioneel. Configureer een virtueel netwerk. Geef de **resource groep**, het **virtuele netwerk**en het **subnet** op om het reken exemplaar te maken binnen een Azure-Virtual Network (vnet). Zie deze [netwerk vereisten](how-to-enable-virtual-network.md#compute-instance) voor vnet voor meer informatie.        |

U kunt ook een exemplaar maken
* Rechtstreeks vanuit de [ervaring met geïntegreerde notebooks](tutorial-1st-experiment-sdk-setup.md#azure)
* In Azure Portal
* Van Azure Resource Manager sjabloon. Zie de [sjabloon Create a Azure machine learning Compute instance](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)voor een voorbeeld sjabloon.
* Met [Azure machine learning SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb)
* Vanuit de [cli-uitbrei ding voor Azure machine learning](reference-azure-machine-learning-cli.md#computeinstance)

De toegewezen kernen per regio per VM-serie quota en het totale regionale quotum, die van toepassing zijn op het maken van een reken instantie. is Unified en gedeeld met Azure Machine Learning-quotum voor het trainings berekenings cluster. Wanneer het reken exemplaar wordt gestopt, wordt er geen quotum vrijgegeven om ervoor te zorgen dat u het reken exemplaar opnieuw kunt starten.

## <a name="compute-target"></a>Rekendoel

Reken instanties kunnen worden gebruikt als een [trainings berekenings doel](concept-compute-target.md#train) vergelijkbaar met Azure machine learning Compute-trainings clusters. 

Een reken instantie:
* Bevat een taak wachtrij.
* Voert taken veilig uit in een virtuele netwerk omgeving, zonder dat ondernemingen de SSH-poort hoeven te openen. De taak wordt uitgevoerd in een omgeving met containers en verpakt uw model afhankelijkheden in een docker-container.
* Kan meerdere kleine taken parallel uitvoeren (preview).  Twee taken per kernen kunnen parallel worden uitgevoerd terwijl de rest van de taken in de wachtrij worden geplaatst.

U kunt reken instantie gebruiken als een lokaal doel voor het afwijzen van de implementatie voor scenario's voor testen en fout opsporing.

> [!NOTE]
> Gedistribueerde trainings taken worden niet ondersteund voor reken instanties.  Gebruik (reken clusters] (How-to-set-up-training-targets. MD # amlcompute) voor gedistribueerde training.

Zie voor meer informatie de notebook [Train-on-computeinstance](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb). Dit notitie blok is ook beschikbaar in de map Studio **samples** in *training/Train-on-computeinstance*.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Wat is er gebeurd met de VM van de notebook?

Reken instanties vervangen de VM van de notebook.  

Alle notitieblok bestanden die zijn opgeslagen in de werkruimte bestands share en gegevens in werkruimte gegevens archieven, zijn toegankelijk vanuit een reken instantie. Alle aangepaste pakketten die eerder op een notebook-VM zijn geïnstalleerd, moeten echter opnieuw worden geïnstalleerd op de reken instantie. Quota beperkingen die van toepassing zijn op het maken van reken clusters, zijn ook van toepassing op het maken van reken instanties.

Er kunnen geen nieuwe Vm's voor het notitie blok worden gemaakt. U kunt echter nog steeds toegang krijgen tot en gebruikmaken van de laptop-Vm's die u hebt gemaakt, met volledige functionaliteit. Reken instanties kunnen worden gemaakt in dezelfde werk ruimte als de bestaande virtuele machines van het werk blok.


## <a name="next-steps"></a>Volgende stappen

 * [Zelf studie: uw eerste ml-model trainen](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een reken instantie met een geïntegreerde notebook kunt gebruiken.
