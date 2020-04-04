---
title: Interpreteerbaarheid modelleren in Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uitleggen waarom uw model voorspellingen doet met de Azure Machine Learning SDK. Het kan worden gebruikt tijdens de training en gevolgtrekking om te begrijpen hoe uw model voorspellingen maakt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631393"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpreteerbaarheid modelleren in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Overzicht van de interpreteerbaarheid van het model

Interpreteerbaarheid is van cruciaal belang voor gegevenswetenschappers, auditors en besluitvormers van bedrijven om ervoor te zorgen dat het bedrijfsbeleid, de industrienormen en overheidsvoorschriften worden nageleefd:

+ Gegevenswetenschappers hebben de mogelijkheid nodig om hun modellen uit te leggen aan leidinggevenden en belanghebbenden, zodat ze de waarde en nauwkeurigheid van hun bevindingen kunnen begrijpen. Ze vereisen ook interpreteerbaarheid om hun modellen te debuggen en weloverwogen beslissingen te nemen over hoe ze te verbeteren. 

+ Juridische auditors hebben tools nodig om modellen te valideren met betrekking tot naleving van de regelgeving en te controleren hoe de beslissingen van modellen van invloed zijn op mensen. 

+ Zakelijke besluitvormers hebben gemoedsrust nodig door de mogelijkheid te hebben om eindgebruikers transparantie te bieden. Dit stelt hen in staat om vertrouwen te verdienen en te behouden.


Het inschakelen van de mogelijkheid om een machine learning-model uit te leggen is belangrijk tijdens twee hoofdfasen van modelontwikkeling:
+ Tijdens de trainingsfase kunnen modelontwerpers en beoordelaars de interpretatievan een model gebruiken om hypothesen te verifiëren en vertrouwen op te bouwen met belanghebbenden. Ze gebruiken ook de inzichten in het model voor het debuggen, valideren van modelgedrag komt overeen met hun doelstellingen en om te controleren op modeloneerlijkheid of onbelangrijke functies.

+ Tijdens de inferencing fase, als het hebben van transparantie rond geïmplementeerde modellen stelt leidinggevenden in staat om te begrijpen "wanneer ingezet" hoe het model werkt en hoe de beslissingen zijn de behandeling en impact van mensen in het echte leven. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpreteerbaarheid met Azure Machine Learning

De interpreteerbaarheidsklassen worden beschikbaar gesteld via meerdere SDK-pakketten: (Meer informatie over het [installeren van SDK-pakketten voor Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py))

* `azureml.interpret`, het hoofdpakket, met functionaliteiten die door Microsoft worden ondersteund.

* `azureml.contrib.interpret`, voorbeeld en experimentele functionaliteiten die u uitproberen.

* `azureml.train.automl.automlexplainer`pakket voor het interpreteren van geautomatiseerde machine learning-modellen.

Gebruik `pip install azureml-interpret` `pip install azureml-interpret-contrib` en voor algemeen `pip install azureml-interpret-contrib` gebruik, en voor AutoML gebruik om de interpreteerbaarheid pakketten te krijgen.


> [!IMPORTANT]
> Inhoud in `contrib` de naamruimte wordt niet volledig ondersteund. Naarmate de experimentele functionaliteiten volwassen worden, worden ze geleidelijk verplaatst naar de hoofdnaamruimte.
.



## <a name="how-to-interpret-your-model"></a>Hoe interpreteer je je model?

Met behulp van de klassen en methoden in de SDK u het nieuwe volgen van het nieuwe:
+ Leg modelvoorspelling uit door functiebelangwaarden te genereren voor het hele model en/of afzonderlijke datapunten. 
+ Bereik modelinterpreteerbaarheid op real-world datasets op schaal, tijdens training en gevolgtrekking.
+ Gebruik een interactief visualisatiedashboard om patronen in gegevens en uitleg te ontdekken tijdens de training


In machine learning zijn **functies** de gegevensvelden die worden gebruikt om een doelgegevenspunt te voorspellen. Om bijvoorbeeld kredietrisico's te voorspellen, kunnen gegevensvelden voor leeftijd, accountgrootte en accountleeftijd worden gebruikt. In dit geval zijn leeftijd, accountgrootte en accountleeftijd **kenmerken.** Functiebelang vertelt u hoe elk gegevensveld de voorspellingen van het model beïnvloedde. Leeftijd kan bijvoorbeeld intensief worden gebruikt in de voorspelling, terwijl accountgrootte en leeftijd geen invloed hebben op de voorspellingswaarden. Dit proces stelt gegevenswetenschappers in staat om resulterende voorspellingen uit te leggen, zodat belanghebbenden inzicht hebben in welke functies het belangrijkst zijn in het model.

Lees hier meer over ondersteunde interpreteerbaarheidstechnieken, ondersteunde machine learning-modellen en ondersteunde runomgevingen.


## <a name="supported-interpretability-techniques"></a>Ondersteunde interpreteerbaarheidstechnieken

 `azureml-interpret`maakt gebruik van de interpreteerbaarheidstechnieken die zijn ontwikkeld in [Interpret-Community](https://github.com/interpretml/interpret-community/), een open source python-pakket voor het trainen van interpreteerbare modellen en het helpen verklaren van blackbox AI-systemen. [Interpret-Community](https://github.com/interpretml/interpret-community/) fungeert als gastheer voor de ondersteunde explainers van deze SDK en ondersteunt momenteel de volgende interpreteerbaarheidstechnieken:

|Techniek voor interpreteerbaarheid|Beschrijving|Type|
|--|--|--------------------|
|1. SHAP Tree Explainer| [SHAP](https://github.com/slundberg/shap)'s tree explainer, die zich richt op polynomial tijd snel SHAP waarde schatting algoritme specifiek voor **bomen en ensembles van bomen**.|Modelspecifiek|
|2. SHAP Deep Explainer| Gebaseerd op de uitleg van [SHAP](https://github.com/slundberg/shap), Deep Explainer "is een high-speed benadering algoritme voor SHAP waarden in deep learning modellen die voortbouwt op een verbinding met DeepLIFT beschreven in de [SHAP NIPS papier](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). **TensorFlow-modellen** en **Keras-modellen** met de TensorFlow-backend worden ondersteund (er is ook voorlopige ondersteuning voor PyTorch)".|Modelspecifiek|
|3. SHAP Linear Explainer| [SHAP](https://github.com/slundberg/shap)'s Linear explainer berekent SHAP-waarden voor een **lineair model**, waarbij optioneel rekening wordt gehouden met correlaties tussen functies.|Modelspecifiek|
|4. SHAP Kernel Explainer| [SHAP](https://github.com/slundberg/shap)'s Kernel explainer gebruikt een speciaal gewogen lokale lineaire regressie om SHAP-waarden voor **elk model**te schatten.|Modelagtisch|
|5. Mimic Explainer (Global Surrogate)| Mimic explainer is gebaseerd op het idee van de opleiding van [wereldwijde surrogaat modellen](https://christophm.github.io/interpretable-ml-book/global.html) om blackbox modellen na te bootsen. Een globaal surrogaatmodel is een intrinsiek interpreteerbaar model dat is getraind om de voorspellingen van **een black box-model** zo nauwkeurig mogelijk te benaderen. Gegevenswetenschappers kunnen het surrogaatmodel interpreteren om conclusies te trekken over het black box-model. U een van de volgende interpreteerbare modellen gebruiken als surrogaatmodel: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) en Decision Tree (DecisionTreeExplainableModel).|Modelagtisch|
|6. Permutatie Feature Importance Explainer (PFI)| Permutatie Feature Importance is een techniek die wordt gebruikt om classificatie- en regressiemodellen uit te leggen die is geïnspireerd op [Breiman's Random Forests paper](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (zie sectie 10). Op een hoog niveau, de manier waarop het werkt is door willekeurig schuifelen gegevens een functie tegelijk voor de hele gegevensset en het berekenen van hoeveel de prestaties metrische van belang verandert. Hoe groter de verandering, hoe belangrijker die functie is. PFI kan het algehele gedrag van **elk onderliggend model** verklaren, maar verklaart geen individuele voorspellingen. |Modelagtisch|




Naast de hierboven beschreven interpreteerbaarheidstechnieken ondersteunen we een `TabularExplainer`andere [SHAP-gebaseerde explainer](https://github.com/slundberg/shap), genaamd . Afhankelijk van het `TabularExplainer` model, maakt gebruik van een van de ondersteunde SHAP explainers:

* TreeExplainer voor alle boommodellen
* DeepExplainer voor DNN-modellen
* LinearExplainer voor lineaire modellen
* KernelExplainer voor alle andere modellen

`TabularExplainer`heeft ook aanzienlijke functie- en prestatieverbeteringen aangebracht ten opzichte van de directe SHAP Explainers:

* **Samenvatting van de initialisatiegegevensset**. In gevallen waar de snelheid van uitleg het belangrijkst is, vatten we de initialisatiegegevensset samen en genereren we een kleine set representatieve monsters, die het genereren van algemene en individuele functiewaardewaarden versnellen.
* **Bemonstering van de evaluatiegegevensset**. Als de gebruiker in een grote reeks evaluatiemonsters slaagt, maar ze niet allemaal hoeft te evalueren, kan de parameter voor de bemonstering worden ingesteld op true om de berekening van de algemene modeluitleg te versnellen.

Het volgende diagram toont de huidige structuur van ondersteunde explainers.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Ondersteunde machine learning-modellen

Het `azureml.interpret` pakket van de SDK ondersteunt modellen die zijn getraind met de volgende gegevenssetindelingen:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

De uitlegfuncties accepteren zowel modellen als pijplijnen als invoer. Als een model wordt geleverd, moet `predict` het `predict_proba` model de voorspellingsfunctie implementeren of die voldoet aan het Scikit-verdrag. Als uw model dit niet ondersteunt, u uw model verpakken `predict` `predict_proba` in een functie die hetzelfde resultaat genereert als of in Scikit en die wrapperfunctie gebruiken met de geselecteerde uitleg. Als een pijplijn wordt geleverd, gaat de uitlegfunctie ervan uit dat het lopende pijplijnscript een voorspelling retourneert. Met behulp van `azureml.interpret` deze verpakkingstechniek kunnen modellen die zijn getraind via PyTorch, TensorFlow en Keras deep learning-frameworks en klassieke machine learning-modellen worden ondersteund.

## <a name="local-and-remote-compute-target"></a>Lokaal en extern rekendoel

Het `azureml.interpret` pakket is ontworpen om te werken met zowel lokale als externe rekendoelen. Als de SDK-functies lokaal worden uitgevoerd, worden geen contact opgenomen met Azure-services. 

U uitleg op afstand uitvoeren op Azure Machine Learning Compute en de uitleggegevens aanmelden bij de Azure Machine Learning Run History Service. Zodra deze informatie is vastgelegd, zijn rapporten en visualisaties uit de uitleg direct beschikbaar in Azure Machine Learning-studio voor gebruikersanalyse.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [how-to](how-to-machine-learning-interpretability-aml.md) voor het inschakelen van interpreteerbaarheid voor modellen die zowel lokaal als op Azure Machine Learning remote compute resources trainen. Zie de [voorbeeldnotitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) voor extra scenario's.
