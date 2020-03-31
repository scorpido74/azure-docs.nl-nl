---
title: Wat is een Azure Machine Learning-rekenexemplaar?
titleSuffix: Azure Machine Learning
description: Meer informatie over het rekenexemplaar Azure Machine Learning, een volledig beheerd cloudwerkstation.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283925"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Wat is een Azure Machine Learning-rekenexemplaar?

Een Azure Machine Learning-rekeninstantie (preview) is een volledig beheerd cloudwerkstation voor gegevenswetenschappers. 

Compute-exemplaren maken het eenvoudig om aan de slag te gaan met azure machine learning-ontwikkeling en bieden beheer- en bedrijfsgereedheidsmogelijkheden voor IT-beheerders.  

Gebruik een rekeninstantie als uw volledig geconfigureerde en beheerde ontwikkelomgeving in de cloud.

Compute-exemplaren worden meestal gebruikt als ontwikkelomgevingen.  Ze kunnen ook worden gebruikt als een rekendoel voor training en inferencing voor ontwikkeling en testen.  Voor grote taken is een [Azure Machine Learning-compute cluster](how-to-set-up-training-targets.md#amlcompute) met multi-node scaling-mogelijkheden een betere rekendoelkeuze.


## <a name="why-use-a-compute-instance"></a>Waarom een rekeninstantie gebruiken?

Een rekeninstantie is een volledig beheerd cloudgebaseerd werkstation dat is geoptimaliseerd voor uw ontwikkelomgeving voor machine learning. Het biedt de volgende voordelen:

|Belangrijkste voordelen||
|----|----|
|Productiviteit|Gegevenswetenschappers kunnen modellen bouwen en implementeren met geïntegreerde notitieblokken en de volgende tools in hun webbrowser:<br/>- Jupyter<br/>- JupyterLab<br/>- RStudio|
|Beheerd & veilig|Verminder uw beveiligingsvoetafdruk en voeg naleving toe aan de beveiligingsvereisten van de onderneming. Compute-exemplaren bieden robuust beheerbeleid en veilige netwerkconfiguraties, zoals:<br/><br/>- Automatisch inrichten vanuit Resource Manager-sjablonen of Azure Machine Learning SDK<br/>- [Op rollen gebaseerde toegangscontrole (RBAC)](/azure/role-based-access-control/overview)<br/>- [Ondersteuning voor virtueel netwerk](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH-beleid om SSH-toegang in te schakelen/uit te schakelen|
|Vooraf geconfigureerd&nbsp;&nbsp;of ML|Bespaar tijd op installatietaken met vooraf geconfigureerde en up-to-date ML-pakketten, deep learning-frameworks, GPU-stuurprogramma's.|
|Volledig aanpasbaar|Brede ondersteuning voor Azure VM-typen, waaronder GPU's en aanhoudende aanpassingen op laag niveau, zoals het installeren van pakketten en stuurprogramma's, maken geavanceerde scenario's een fluitje van een cent. |

## <a name="tools-and-environments"></a><a name="contents"></a>Tools en omgevingen

Met de rekeninstantie Azure Machine Learning u modellen maken, trainen en implementeren in een volledig geïntegreerde notebookervaring in uw werkruimte.


Deze hulpprogramma's en omgevingen zijn geïnstalleerd op de compute instance: 

|Algemene hulpmiddelen & omgevingen|Details|
|----|:----:|
|Stuurprogramma's|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotheek||
|Azure-CLI ||
|Azure Machine Learning-voorbeelden ||
|Azure Machine Learning EDAT-engine ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R-tools** & omgevingen|Details|
|----|:----:|
|RStudio Server Open Source Edition||
|R-kernel||
|Azure Machine Learning SDK voor R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-voorbeelden|

|**PYTHON-tools** & omgevingen|Details|
|----|----|
|Anaconda Python||
|Jupyter en uitbreidingen||
|Jupyterlab en uitbreidingen||
|Visual Studio Code ||
[Azure Machine Learning-SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>van PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Andere PyPI-pakketten|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda pakketten|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Deep learning pakketten|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-pakketten|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & R SDK-voorbeelden||

Python-pakketten zijn allemaal geïnstalleerd in de **Python 3.6 - AzureML-omgeving.**  

Compute-exemplaren worden meestal gebruikt als ontwikkelomgevingen.  Ze kunnen ook worden gebruikt als een rekendoel voor training en inferencing voor ontwikkeling en testen.  Voor grote taken is een [Azure Machine Learning-compute cluster](how-to-set-up-training-targets.md#amlcompute) met multi-node scaling-mogelijkheden een betere rekendoelkeuze.

### <a name="installing-packages"></a>Pakketten installeren

U pakketten direct installeren in een Jupyter-laptop of Rstudio:

* RStudio Gebruik het tabblad **Pakketten** rechtsonder of het tabblad **Console** linksboven.  
* Python: Voeg installatiecode toe en voer deze uit in een Jupyter-notitieblokcel.

Of u op een van de volgende manieren toegang krijgen tot een terminalvenster:

* RStudio: Selecteer het tabblad **Terminal** linksboven.
* Jupyter Lab: Selecteer de **tegel Terminal** onder de **andere** kop op het tabblad Launcher.
* Jupyter: Selecteer **Nieuw>Terminal** rechtsboven op het tabblad Bestanden.
* SSH naar de machine.  Installeer vervolgens Python-pakketten in de **Python 3.6 - AzureML-omgeving.**  Installeer R-pakketten in de **R-omgeving.**

## <a name="accessing-files"></a>Toegang tot bestanden

Notitieblokken en R-scripts worden opgeslagen in het standaardopslagaccount van uw werkruimte in Azure-bestandsshare.  Deze bestanden bevinden zich onder uw map 'Gebruikersbestanden'. Deze opslag maakt het eenvoudig om notitieblokken te delen tussen compute-exemplaren. Het opslagaccount houdt uw notitieblokken ook veilig bewaard wanneer u een rekeninstantie stopt of verwijdert.

Het Azure-account voor bestandsshare van uw werkruimte is gemonteerd als een station op de rekeninstantie. Dit station is de standaard werkende directory voor Jupyter, Jupyter Labs en RStudio.

De bestanden in de bestandsshare zijn toegankelijk vanuit alle compute-instanties in dezelfde werkruimte. Eventuele wijzigingen in deze bestanden op de compute instance worden betrouwbaar gehandhaafd terug naar het bestandsaandeel.

U ook de nieuwste Azure Machine Learning-voorbeelden klonen naar uw map onder de map gebruikersbestanden in het delen van werkruimtebestanden.

Het schrijven van kleine bestanden kan langzamer op netwerkstations dan schrijven naar de VM zelf.  Als u veel kleine bestanden schrijft, probeert u een map `/tmp` rechtstreeks op de rekeninstantie te gebruiken, zoals een map. Houd er rekening mee dat deze bestanden niet toegankelijk zijn vanaf andere compute-instanties in de werkruimte.

## <a name="managing-a-compute-instance"></a>Een rekeninstantie beheren

Selecteer in uw werkruimte in Azure Machine Learning-studio **Compute**en selecteer **Vervolgens Compute Instance** bovenaan.

![Een rekeninstantie beheren](./media/concept-compute-instance/manage-compute-instance.png)

U de volgende acties uitvoeren:

* Een rekeninstantie maken. Geef de naam op, het Type Azure VM inclusief GPU's (let op vm-type kan niet worden gewijzigd na het maken), ssh-toegang inschakelen/uitschakelen en configureren van virtuele netwerkinstellingen optioneel. U ook een exemplaar rechtstreeks maken vanuit geïntegreerde notitieblokken, Azure-portal, Resource Manager-sjabloon of Azure Machine Learning SDK. De toegewezen kernen per regioquotum dat van toepassing is op het maken van rekeninstanties, worden samengevoegd en gedeeld met azure Machine Learning-compute clusterquota.
* Het tabblad Rekeninstanties vernieuwen
* Een rekeninstantie starten, stoppen en opnieuw starten
* Een rekeninstantie verwijderen

Voor elke rekeninstantie in uw werkruimte u:

* Toegang tot Jupyter, JupyterLab, RStudio op de compute instance
* SSH in compute instance. SSH-toegang is standaard uitgeschakeld, maar kan worden ingeschakeld op de tijd van het maken van compute-instanties. SSH-toegang is via publiek/private sleutelmechanisme. Op het tabblad vindt u details voor ssh-verbinding, zoals IP-adres, gebruikersnaam en poortnummer.
* Informatie over een specifieke rekeninstantie zoals IP-adres en regio.

[Met RBAC](/azure/role-based-access-control/overview) u bepalen welke gebruikers in de werkruimte een rekeninstantie kunnen maken, verwijderen, starten, stoppen, opnieuw opstarten. Alle gebruikers in de rol van werkruimtebijdrage en eigenaar kunnen rekeninstanties in de werkruimte maken, verwijderen, starten, stoppen en opnieuw starten. Alleen de maker van een specifieke compute instance heeft echter toegang tot Jupyter, JupyterLab en RStudio op die rekeninstantie. De maker van de compute instance heeft de compute instance gewijd aan hen, hebben root-toegang, en kan terminal in via Jupyter. Compute-instantie heeft eenmalige gebruikerslogin van de makergebruiker en alle acties gebruiken de identiteit van die gebruiker voor RBAC en attributie van experimentuitvoeringen. SSH-toegang wordt gecontroleerd via een publiek/privaat sleutelmechanisme.

U ook een instantie maken
* Rechtstreeks vanuit de geïntegreerde notebooks-ervaring
* In Azure-portal
* Vanuit azure resource manager-sjabloon
* Met Azure Machine Learning SDK

De toegewezen kernen per regioquotum, dat van toepassing is op het maken van rekeninstanties, worden samengevoegd en gedeeld met het azure Machine Learning-trainingsclusterquotum. 

## <a name="compute-target"></a>Rekendoel

Compute-exemplaren kunnen worden gebruikt als een [trainingsdoel dat](concept-compute-target.md#train) vergelijkbaar is met azure Machine Learning-trainingstrainingsclusters voor gegevens. Inrichten van een multi-GPU VM om gedistribueerde trainingstaken uit te voeren met TensorFlow/PyTorch schatters. U ook een run-configuratie maken en deze gebruiken om uw experiment uit te voeren op rekeninstantie. U rekeninstantie gebruiken als een lokaal doel voor implementatie van gebruikers voor het testen/debuggen van scenario's.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Wat is er gebeurd met Notebook VM?

Compute-exemplaren vervangen de vm notebook.  

Alle notitieblokbestanden die zijn opgeslagen in de bestandsshare en gegevens in werkruimtegegevensopslag, zijn toegankelijk via een rekeninstantie. Alle aangepaste pakketten die eerder op een notebook-vm zijn geïnstalleerd, moeten echter opnieuw worden geïnstalleerd op de rekeninstantie. Quotabeperkingen die van toepassing zijn op het maken van rekenclusters zijn ook van toepassing op het maken van rekeninstanties. 

Er kunnen geen nieuwe notebook-VM's worden gemaakt. U echter nog steeds toegang krijgen tot notebookvm's die u hebt gemaakt, met volledige functionaliteit. Compute-exemplaren kunnen worden gemaakt in dezelfde werkruimte als de bestaande notebook-VM's. 


## <a name="next-steps"></a>Volgende stappen

 * [Zelfstudie: Train uw eerste ML-model](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een rekeninstantie gebruiken met een geïntegreerd notitieblok.
