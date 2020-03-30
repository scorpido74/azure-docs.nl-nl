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
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063988"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpreteerbaarheid modelleren in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Overzicht van de interpreteerbaarheid van het model

Interpreteerbaarheid is van cruciaal belang voor gegevenswetenschappers en zakelijke besluitvormers om ervoor te zorgen dat het bedrijfsbeleid, de industrienormen en overheidsvoorschriften worden nageleefd:
+ Gegevenswetenschappers hebben de mogelijkheid nodig om hun modellen uit te leggen aan leidinggevenden en belanghebbenden, zodat ze de waarde en nauwkeurigheid van hun bevindingen kunnen begrijpen 
+ Zakelijke besluitvormers hebben behoefte aan gemoedsrust van de mogelijkheid om transparantie te bieden voor eindgebruikers om hun vertrouwen te winnen en te behouden

Het inschakelen van de mogelijkheid om een machine learning-model uit te leggen is belangrijk tijdens twee hoofdfasen van modelontwikkeling:
+ Tijdens de trainingsfase van de machine learning model ontwikkelingscyclus. Modelontwerpers en beoordelaars kunnen de interpretatievan een model gebruiken om hypothesen te verifiëren en vertrouwen op te bouwen met belanghebbenden. Ze gebruiken ook de inzichten in het model voor het debuggen, valideren van modelgedrag komt overeen met hun doelstellingen en om te controleren op bias of onbelangrijke functies.
+ Tijdens de inferencing fase, als het hebben van transparantie rond geïmplementeerde modellen stelt leidinggevenden in staat om te begrijpen "wanneer ingezet" hoe het model werkt en hoe de beslissingen zijn de behandeling en impact van mensen in het echte leven. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpreteerbaarheid met Azure Machine Learning

In dit artikel leert u hoe modelinterpreteerbaarheidsconcepten worden geïmplementeerd in de SDK.

Met behulp van de klassen en methoden in de SDK, u het:
+ Functiebelangwaarden voor zowel ruwe als gemanipuleerde functies
+ Interpreteerbaarheid op real-world datasets op schaal, tijdens training en gevolgtrekking.
+ Interactieve visualisaties om u te helpen bij het ontdekken van patronen in gegevens en uitleg tijdens de training


In machine learning zijn **functies** de gegevensvelden die worden gebruikt om een doelgegevenspunt te voorspellen. Om bijvoorbeeld kredietrisico's te voorspellen, kunnen gegevensvelden voor leeftijd, accountgrootte en accountleeftijd worden gebruikt. In dit geval zijn leeftijd, accountgrootte en accountleeftijd **kenmerken.** Functiebelang vertelt u hoe elk gegevensveld de voorspellingen van het model beïnvloedde. Leeftijd kan bijvoorbeeld intensief worden gebruikt in de voorspelling, terwijl accountgrootte en leeftijd de nauwkeurigheid van de voorspelling niet significant beïnvloeden. Dit proces stelt data scientists in staat om resulterende voorspellingen uit te leggen, zodat belanghebbenden inzicht hebben in welke gegevenspunten het belangrijkst zijn in het model.

Met behulp van deze tools u machine learning-modellen wereldwijd uitleggen **op alle gegevens**of lokaal op een specifiek **gegevenspunt** met behulp van de state-of-art technologieën op een gebruiksvriendelijke en schaalbare manier.

De interpreteerbaarheidsklassen worden beschikbaar gesteld via meerdere SDK-pakketten. Meer informatie over het [installeren van SDK-pakketten voor Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, het hoofdpakket, met functionaliteiten die door Microsoft worden ondersteund.

* `azureml.contrib.interpret`, voorbeeld en experimentele functionaliteiten die u uitproberen.

* `azureml.train.automl.automlexplainer`pakket voor het interpreteren van geautomatiseerde machine learning-modellen.

> [!IMPORTANT]
> Inhoud in `contrib` de naamruimte wordt niet volledig ondersteund. Naarmate de experimentele functionaliteiten volwassen worden, worden ze geleidelijk verplaatst naar de hoofdnaamruimte.

## <a name="how-to-interpret-your-model"></a>Hoe interpreteer je je model?

U de interpreteerbaarheidsklassen en -methoden toepassen om inzicht te krijgen in het globale gedrag of specifieke voorspellingen van het model. De eerste heet globale verklaring en de laatste heet lokale uitleg.

De methoden kunnen ook worden gecategoriseerd op basis van de vraag of de methode modelagnostisch of modelspecifiek is. Sommige methoden zijn gericht op bepaalde soorten modellen. De tree explainer van SHAP is bijvoorbeeld alleen van toepassing op op bomen gebaseerde modellen. Sommige methoden behandelen het model als een zwarte doos, zoals mimic explainer of SHAP's kernel explainer. Het `interpret` pakket maakt gebruik van deze verschillende benaderingen op basis van gegevenssets, modeltypen en use cases.

De uitvoer is een set van informatie over hoe een bepaald model maakt zijn voorspelling, zoals:
* Globaal/lokaal relatief functiebelang
* Globale/lokale functie- en voorspellingsrelatie

### <a name="explainers"></a>Uitleg

Dit pakket maakt gebruik van de interpreteerbaarheidstechnieken die zijn ontwikkeld in [Interpret-Community](https://github.com/interpretml/interpret-community/), een open source python-pakket voor het trainen van interpreteerbare modellen en het helpen verklaren van blackbox AI-systemen. [Interpret-Community](https://github.com/interpretml/interpret-community/) fungeert als gastheer voor de ondersteunde explainers van deze SDK en ondersteunt momenteel de volgende interpreteerbaarheidstechnieken:

* **SHAP Tree Explainer**: [SHAP](https://github.com/slundberg/shap)'s tree explainer, die zich richt op polynomial eitijd snel SHAP waarde schatting algoritme specifiek voor bomen en ensembles van bomen.
* **SHAP Deep Explainer**: Gebaseerd op de uitleg van [SHAP](https://github.com/slundberg/shap), Deep Explainer "is een high-speed benadering algoritme voor SHAP waarden in deep learning modellen die voortbouwt op een verbinding met DeepLIFT beschreven in de [SHAP NIPS papier](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). TensorFlow-modellen en Keras-modellen met de TensorFlow-backend worden ondersteund (er is ook voorlopige ondersteuning voor PyTorch)".
* **SHAP Linear Explainer**: [SHAP](https://github.com/slundberg/shap)'s Linear explainer berekent SHAP-waarden voor een lineair model, waarbij optioneel rekening wordt gehouden met correlaties tussen functies.

* **SHAP Kernel Explainer**: [SHAP](https://github.com/slundberg/shap)'s Kernel explainer gebruikt een speciaal gewogen lokale lineaire regressie om SHAP-waarden voor elk model te schatten.
* **Mimic Explainer:** Mimic explainer is gebaseerd op het idee van de opleiding van [wereldwijde surrogaat modellen](https://christophm.github.io/interpretable-ml-book/global.html) om blackbox modellen na te bootsen. Een globaal surrogaatmodel is een intrinsiek interpreteerbaar model dat is getraind om de voorspellingen van een black box-model zo nauwkeurig mogelijk te benaderen. Data scientist kan interpreteren het surrogaat model om conclusies te trekken over de zwarte doos model. U een van de volgende interpreteerbare modellen gebruiken als surrogaatmodel: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) en Decision Tree ( DecisionTreeExplainableModel).


* **Permutatie Feature Importance Explainer**: Permutation Feature Importance is een techniek die wordt gebruikt om classificatie- en regressiemodellen uit te leggen die is geïnspireerd op [Breiman's Random Forests paper](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (zie sectie 10). Op een hoog niveau, de manier waarop het werkt is door willekeurig schuifelen gegevens een functie tegelijk voor de hele gegevensset en het berekenen van hoeveel de prestaties metrische van belang verandert. Hoe groter de verandering, hoe belangrijker die functie is.

* **LIME Explainer** (`contrib`): Op basis van [LIME](https://github.com/marcotcr/lime)gebruikt LIME Explainer het state-of-the-art Local interpretable model-agnostisch uitlegalgoritme (LIME) om lokale surrogaatmodellen te maken. In tegenstelling tot de wereldwijde surrogaatmodellen richt LIME zich op het trainen van lokale surrogaatmodellen om individuele voorspellingen uit te leggen.
* **HAN Text Explainer** (`contrib`): HAN Text Explainer maakt gebruik van een hiërarchisch aandachtsnetwerk voor het verkrijgen van modeluitleg uit tekstgegevens voor een bepaald tekstmodel voor zwarte vakken. Het traint de HAN surrogaat model op een bepaalde zwarte doos model voorspelde uitgangen. Na een wereldwijde training in het tekstcorpus, voegt het een fine-tune stap toe voor een specifiek document om de nauwkeurigheid van de uitleg te verbeteren. HAN gebruikt een bidirectioneel RNN met twee aandachtslagen, voor zin en woordaandacht. Zodra de DNN is getraind op het black box model en afgestemd op een specifiek document, kan de gebruiker het woord belang uit de aandachtslagen halen. HAN blijkt nauwkeuriger te zijn dan LIME of SHAP voor tekstgegevens, maar ook in termen van trainingstijd. Er zijn verbeteringen aangebracht om de gebruiker de mogelijkheid te geven om het netwerk te initialiseren met GloVe-woordinbeddingen om de trainingstijd te verkorten. De trainingstijd kan aanzienlijk worden verbeterd door HAN uit te voeren op een externe Azure GPU VM. De implementatie van de HAN wordt beschreven in ['Hierarchical Attention Networks for Document Classification (Yang et al., 2016)'](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Tabeluitleg :** `TabularExplainer` gebruikt de volgende logica om de Direct [SHAP](https://github.com/slundberg/shap) Explainers aan te roepen:

    1. Als het een boomgebaseerd model `TreeExplainer`is, pas dan SHAP toe, anders
    2. Als het een DNN-model `DeepExplainer`is, pas dan SHAP toe, anders
    3. Als het een lineair model `LinearExplainer`is, pas dan SHAP toe, anders
    3. Behandel het als een black-box model en breng SHAP toe`KernelExplainer`


`TabularExplainer`heeft ook aanzienlijke functie- en prestatieverbeteringen aangebracht ten opzichte van de directe SHAP Explainers:

* **Samenvatting van de initialisatiegegevensset**. In gevallen waar de snelheid van uitleg het belangrijkst is, vatten we de initialisatiegegevensset samen en genereren we een kleine set representatieve monsters, die zowel de globale als lokale uitleg versnellen.
* **Bemonstering van de evaluatiegegevensset**. Als de gebruiker een grote set evaluatiemonsters passeert, maar ze eigenlijk niet allemaal nodig heeft om te worden geëvalueerd, kan de parameter voor de bemonstering op true worden ingesteld om de globale uitleg te versnellen.

Het volgende diagram toont de huidige structuur van directe en meta explainers.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Ondersteunde modellen

Alle modellen die zijn getraind `numpy.array`op `pandas.DataFrame` `iml.datatypes.DenseData`gegevenssets `scipy.sparse.csr_matrix` in Python , of `explain` formaat worden ondersteund door het interpreteerbaarheidspakket van de SDK.

De uitlegfuncties accepteren zowel modellen als pijplijnen als invoer. Als een model wordt geleverd, moet `predict` het `predict_proba` model de voorspellingsfunctie implementeren of die voldoet aan het Scikit-verdrag. Als een pijplijn (naam van het pijplijnscript) wordt opgegeven, gaat de uitlegfunctie ervan uit dat het lopende pijplijnscript een voorspelling retourneert. We ondersteunen modellen die zijn getraind via PyTorch, TensorFlow en Keras deep learning frameworks.

### <a name="local-and-remote-compute-target"></a>Lokaal en extern rekendoel

Het `explain` pakket is ontworpen om te werken met zowel lokale als externe rekendoelen. Als de SDK-functies lokaal worden uitgevoerd, worden geen contact opgenomen met Azure-services. U uitleg op afstand uitvoeren op Azure Machine Learning Compute en de uitleggegevens aanmelden bij Azure Machine Learning Run History Services. Zodra deze informatie is vastgelegd, zijn rapporten en visualisaties uit de uitleg direct beschikbaar op de Azure Machine Learning-werkruimte voor gebruikersanalyse.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [how-to](how-to-machine-learning-interpretability-aml.md) voor het inschakelen van interpreteerbaarheid voor modellen die zowel lokaal als op Azure Machine Learning remote compute resources trainen. Zie de [voorbeeldnotitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) voor extra scenario's.
