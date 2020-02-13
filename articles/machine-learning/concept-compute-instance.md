---
title: Wat is een Azure Machine Learning Compute-instantie?
titleSuffix: Azure Machine Learning
description: Meer informatie over de Azure Machine Learning Compute-instantie, een volledig beheerd werk station in de Cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: b65b7a9fd38b690729fafd86fe213ff56760a1bb
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169838"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Wat is een Azure Machine Learning Compute-instantie?

Een Azure Machine Learning Compute-instantie (preview) is een volledig beheerd werk station in de Cloud voor gegevens wetenschappers. 

Reken instanties maken het eenvoudig om aan de slag te gaan met Azure Machine Learning-ontwikkeling en bieden mogelijkheden voor het beheer en de bedrijfs voorbereiding voor IT-beheerders.  

Gebruik een reken instantie als uw volledig geconfigureerde en beheerde ontwikkel omgeving in de Cloud.

Reken instanties worden meestal gebruikt als ontwikkel omgevingen.  Ze kunnen ook worden gebruikt als een reken doel voor training en voor het afleiden van ontwikkel-en test doeleinden.  Voor grote taken is een [Azure machine learning Compute-Cluster](how-to-set-up-training-targets.md#amlcompute) met schaal mogelijkheden voor meerdere knoop punten een betere reken doel keuze.


## <a name="why-use-a-compute-instance"></a>Waarom een reken instantie gebruiken?

Een reken instantie is een volledig beheerd werk station in de cloud dat is geoptimaliseerd voor uw machine learning-ontwikkel omgeving. Het biedt de volgende voor delen:

|Belangrijkste voordelen||
|----|----|
|Productiviteit|Gegevens wetenschappers kunnen modellen bouwen en implementeren met behulp van geïntegreerde notebooks en de volgende hulpprogram ma's in hun webbrowser:<br/>-Jupyter<br/>-Jjupyterlab<br/>-RStudio|
|Beheerde & beveiligd|Verminder uw beveiligings footprint en voeg naleving toe met beveiligings vereisten voor ondernemingen. Reken instanties bieden robuust beheer beleid en beveiligde netwerk configuraties zoals:<br/><br/>-Automatisch inrichten vanuit Resource Manager-sjablonen of Azure Machine Learning SDK<br/>- [op rollen gebaseerd toegangs beheer (RBAC)](/azure/role-based-access-control/overview)<br/>[ondersteuning voor virtuele netwerken](how-to-enable-virtual-network.md#compute-instance) - <br/>-SSH-beleid voor het inschakelen/uitschakelen van SSH-toegang|
|Vooraf geconfigureerde&nbsp;of&nbsp;ML|Bespaar tijd bij het instellen van taken met vooraf geconfigureerde en up-to-date ML-pakketten, diepe leer frameworks, GPU-Stuur Programma's.|
|Volledig aanpasbaar|Uitgebreide ondersteuning voor Azure VM-typen, waaronder Gpu's en persistente aanpassing op laag niveau, zoals het installeren van pakketten en stuur Programma's, maakt een koud probleem van geavanceerde scenario's. |

## <a name="contents"></a>Hulpprogram ma's en omgevingen

Met Azure Machine Learning Compute-instantie kunt u modellen ontwerpen, trainen en implementeren in een volledig geïntegreerde laptop ervaring in uw werk ruimte.


Deze hulpprogram ma's en omgevingen zijn geïnstalleerd op het reken exemplaar: 

|Algemene hulpprogram ma's & omgevingen|Details|
|----|:----:|
|Stuurprogramma's|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotheek||
|Azure CLI ||
|Azure Machine Learning-voor beelden ||
|EDAT-engine Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|**R** -hulpprogram ma's & omgevingen|Details|
|----|:----:|
|Open source-editie van RStudio-server||
|R-kernel||
|Azure Machine Learning SDK voor R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-voorbeelden|

|**PYTHON** -hulpprogram ma's & omgevingen|Details|
|----|----|
|Anaconda python||
|Jupyter en-extensies||
|Jjupyterlab en-extensies||
|Visual Studio Code ||
[Azure Machine Learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>van PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Andere PyPI-pakketten|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-pakketten|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Uitgebreide leer pakketten|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-pakketten|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning python & R SDK-voor beelden||

Python-pakketten zijn allemaal geïnstalleerd in de **Python 3,6-AzureML-** omgeving.  

Reken instanties worden meestal gebruikt als ontwikkel omgevingen.  Ze kunnen ook worden gebruikt als een reken doel voor training en voor het afleiden van ontwikkel-en test doeleinden.  Voor grote taken is een [Azure machine learning Compute-Cluster](how-to-set-up-training-targets.md#amlcompute) met schaal mogelijkheden voor meerdere knoop punten een betere reken doel keuze.

### <a name="installing-packages"></a>Pakketten installeren

U kunt pakketten rechtstreeks in een Jupyter-notebook of Rstudio installeren:

* RStudio gebruik het tabblad **pakketten** aan de rechter kant of op het tabblad **console** linksboven.  
* Python: Voeg installatie code toe en voer uit in een Jupyter notebook-cel.

Of u kunt op een van de volgende manieren toegang krijgen tot een Terminal venster:

* RStudio: Selecteer het tabblad **Terminal** aan de rechter bovenhoek.
* Jupyter Lab: Selecteer de tegel **Terminal** onder de **andere** kop op het tabblad Start.
* Jupyter: Selecteer **nieuwe > Terminal** rechtsboven op het tabblad bestanden.
* SSH naar de computer.  Installeer Python-pakketten vervolgens in de **Python 3,6-AzureML-** omgeving.  R-pakketten installeren in de **R** -omgeving.

## <a name="accessing-files"></a>Toegang tot bestanden

Notebooks en R-scripts worden opgeslagen in het standaard opslag account van uw werk ruimte in de Azure-bestands share.  Deze bestanden bevinden zich in de map gebruikers bestanden. Met deze opslag kunt u eenvoudig notitie blokken delen tussen Compute-exemplaren. Het opslag account zorgt er ook voor dat uw notitie blokken veilig blijven behouden wanneer u een reken instantie stopt of verwijdert.

Het account van de Azure-bestands share van uw werk ruimte is gekoppeld als een station op het reken exemplaar. Dit station is de standaard werkmap voor Jupyter, Jupyter Labs en RStudio.

De bestanden in de bestands share zijn toegankelijk vanuit alle reken instanties in dezelfde werk ruimte. Wijzigingen in deze bestanden op het reken exemplaar worden betrouwbaar weer gegeven in de bestands share.

U kunt de meest recente Azure Machine Learning-voor beelden ook klonen naar uw map in de map gebruikers bestanden in de werkruimte bestands share.

Het schrijven van kleine bestanden kan langzamer zijn op netwerk stations dan het schrijven naar de virtuele machine zelf.  Als u veel kleine bestanden schrijft, kunt u een map rechtstreeks op het reken exemplaar gebruiken, zoals een `/tmp` Directory. Houd er rekening mee dat deze bestanden niet toegankelijk zijn vanuit andere compute-instanties in de werk ruimte.

## <a name="managing-a-compute-instance"></a>Een reken instantie beheren

Selecteer in uw werk ruimte in Azure Machine Learning Studio **Compute**en selecteer vervolgens **Compute instance** bovenin.

![Een reken instantie beheren](./media/concept-compute-instance/manage-compute-instance.png)

U kunt de volgende acties uitvoeren:

* Maak een reken instantie. Geef de naam, het type van de Azure-VM op, inclusief Gpu's (het VM-type kan niet worden gewijzigd na het maken), schakel SSH-toegang in/uit en configureer de instellingen voor het virtuele netwerk optioneel. U kunt ook rechtstreeks vanuit geïntegreerde notebooks, Azure Portal, Resource Manager-sjabloon of Azure Machine Learning SDK een exemplaar maken. Het quotum toegewezen kernen per regio dat van toepassing is op het maken van een reken instantie is Unified en gedeeld met Azure Machine Learning quotum voor reken clusters.
* Het tabblad Compute instances vernieuwen
* Een reken instantie starten, stoppen en opnieuw starten
* Een reken instantie verwijderen

Voor elk reken exemplaar in uw werk ruimte kunt u het volgende doen:

* Toegang tot Jupyter, Jjupyterlab, RStudio op het reken exemplaar
* SSH naar Compute-instantie. SSH-toegang is standaard uitgeschakeld, maar kan worden ingeschakeld op het moment dat het reken proces wordt gemaakt. SSH-toegang is via het mechanisme voor open bare/persoonlijke sleutels. Op het tabblad krijgt u details over SSH-verbindingen zoals IP-adres, gebruikers naam en poort nummer.
* Details ophalen over een specifiek reken exemplaar, zoals het IP-adres en de regio.

Met [RBAC](/azure/role-based-access-control/overview) kunt u bepalen welke gebruikers in de werk ruimte een reken instantie kunnen maken, verwijderen, starten, stoppen en opnieuw starten. Alle gebruikers in de rol Inzender en eigenaar van de werk ruimte kunnen reken instanties maken, verwijderen, starten, stoppen en opnieuw starten in de werk ruimte. Alleen de maker van een specifiek reken exemplaar heeft echter toegang tot Jupyter, Jjupyterlab en RStudio op die reken instantie. De maker van het reken exemplaar heeft het reken exemplaar dat eraan is toegewezen, heeft toegang tot het hoofd, en kan Terminal in via Jupyter. Reken instantie heeft een aanmelding voor één gebruiker van de maker gebruiker en alle acties gebruiken de identiteit van die gebruiker voor RBAC en de toewijzing van experimenten. SSH-toegang wordt beheerd via het mechanisme voor open bare/persoonlijke sleutels.

U kunt ook een exemplaar maken
* Rechtstreeks vanuit de ervaring met geïntegreerde notebooks
* In Azure Portal
* Van Azure Resource Manager sjabloon
* Met Azure Machine Learning SDK

Het quotum toegewezen kernen per regio, dat van toepassing is op het maken van een reken instantie, wordt gecombineerd en gedeeld met Azure Machine Learning trainings cluster quota. 

## <a name="compute-target"></a>Reken doel

Reken instanties kunnen worden gebruikt als een [trainings berekenings doel](concept-compute-target.md#train) vergelijkbaar met Azure machine learning Compute-trainings clusters. Richt een multi-GPU-VM in om gedistribueerde trainings taken uit te voeren met tensor flow/PyTorch-schattingen. U kunt ook een uitvoerings configuratie maken en deze gebruiken om uw experiment uit te voeren op reken exemplaar. U kunt reken instantie gebruiken als een lokaal doel voor het afwijzen van de implementatie voor scenario's voor testen en fout opsporing.

## <a name="notebookvm"></a>Wat is er gebeurd met de VM van de notebook?

Reken instanties vervangen de VM van de notebook.  In regio's waar reken instanties nog niet beschikbaar zijn, kunt u de VM-Vm's blijven gebruiken met de volledige functionaliteit en nieuwe VM-Vm's maken.

Alle notitieblok bestanden die zijn opgeslagen in de werkruimte bestands share en gegevens in werkruimte gegevens archieven, zijn toegankelijk vanuit een reken instantie. Alle aangepaste pakketten die eerder op een notebook-VM zijn geïnstalleerd, moeten echter opnieuw worden geïnstalleerd op de reken instantie. Quota beperkingen die van toepassing zijn op het maken van reken clusters, zijn ook van toepassing op het maken van reken instanties. 

In regio's waar reken instanties beschikbaar zijn, kunnen geen nieuwe virtuele machines van de werk plek worden gemaakt. U kunt echter nog steeds toegang krijgen tot en gebruikmaken van de laptop-Vm's die u hebt gemaakt, met volledige functionaliteit. Reken instanties kunnen worden gemaakt in dezelfde werk ruimte als de bestaande virtuele machines van het werk blok. 


## <a name="next-steps"></a>Volgende stappen

 * [Zelf studie: uw eerste ml-model trainen](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een reken instantie met een geïntegreerde notebook kunt gebruiken.
