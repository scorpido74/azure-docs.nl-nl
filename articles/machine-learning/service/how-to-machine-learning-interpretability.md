---
title: De interpretatie van modellen in de Azure Machine Learning-service
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw model voorspellingen maakt met behulp van de Azure Machine Learning SDK. Het kan worden gebruikt tijdens de training en om te begrijpen hoe uw model voor spellingen doet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 14118098e5d476dc07b21462180673b942a6224d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672559"
---
# <a name="model-interpretability-in-azure-machine-learning-service"></a>De interpretatie van modellen in de Azure Machine Learning-service
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Overzicht van de interpretatie van modellen

Interpretbaarheid is van cruciaal belang voor gegevens wetenschappers en besluit vormers van de onderneming om ervoor te zorgen dat de beleids regels van het bedrijf, industrie normen en overheids voorschriften worden nageleefd:
+ Gegevens wetenschappers hebben de mogelijkheid nodig om hun modellen te uitleggen aan leidinggevenden en belanghebbenden, zodat ze de waarde en nauw keurigheid van hun bevindingen kunnen begrijpen 
+ Besluit vormers hebben gemoeds rust op de mogelijkheid om te voorzien in transparantie voor eind gebruikers om hun vertrouwen te krijgen en te onderhouden

Het inschakelen van de mogelijkheid om een machine learning model uit te leggen, is belang rijk tijdens twee belang rijke fasen van model ontwikkeling:
+ Tijdens de trainings fase van de ontwikkelings cyclus van het machine learning model. Model ontwerpers en evaluatoren kunnen gebruikmaken van de interpretatieve uitvoer van een model om hypo Thesen te controleren en om vertrouwen te bouwen met belanghebbenden. Ze gebruiken ook de inzichten in het model voor het opsporen van fouten, het valideren van het model gedrag aan de doel stellingen en het controleren op bias of onbeduidende functies.
+ Tijdens de versterkings fase is het met transparantie rond geïmplementeerde modellen het mogelijk dat leidinggevenden begrijpen "wanneer geïmplementeerd" hoe het model werkt en hoe de beslissingen in werkelijkheid worden behandeld en wat van invloed is op mensen in het leven. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretiteit met Azure Machine Learning

In dit artikel leert u hoe concepten voor de interpretatie van modellen worden geïmplementeerd in de SDK.

Met de klassen en methoden in de SDK kunt u het volgende doen:
+ Belang rijke waarden van de functie voor zowel onbewerkte als ontworpen functies
+ Interpreteer baarheid voor de gegevens sets op schaal tijdens de training en de interferentie.
+ Interactieve visualisaties die u helpen bij het ontdekken van patronen in gegevens en uitleg tijdens de trainings tijd


In machine learning zijn **functies** de gegevens velden die worden gebruikt om een doel gegevens punt te voors pellen. Bijvoorbeeld, om het krediet risico te voors pellen, kunnen gegevens velden voor leeftijd, account grootte en account leeftijd worden gebruikt. In dit geval zijn de leeftijd, de account grootte en de account duur **functies**. Functie belang vertelt u hoe elk gegevens veld de voor spellingen van het model beïnvloedt. Leeftijd kan bijvoorbeeld intensief worden gebruikt in de voor spelling, terwijl de grootte van het account en de leeftijd geen invloed hebben op de nauw keurigheid van de voor spelling. Met dit proces kunnen gegevens wetenschappers de resulterende voor spellingen uitleggen, zodat de belanghebbenden inzicht hebben in welke gegevens punten het belangrijkst zijn in het model.

Met deze hulpprogram ma's kunt u machine learning modellen **globaal op alle gegevens**of **lokaal op een specifiek gegevens punt** uitleggen met behulp van de geavanceerde technologieën in een gemakkelijk te gebruiken en schaal bare manier.

De interpreters klassen worden beschikbaar gesteld via meerdere SDK-pakketten. Meer informatie over het [installeren van SDK-pakketten voor Azure machine learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, het hoofd pakket, dat functionaliteiten bevat die door micro soft worden ondersteund.

* `azureml.contrib.interpret`, de preview-versie en experimentele functionaliteiten die u kunt proberen.

* `azureml.train.automl.automlexplainer`-pakket voor het interpreteren van geautomatiseerde machine learning modellen.

> [!IMPORTANT]
> Inhoud in de naam ruimte van de `contrib` wordt niet volledig ondersteund. Naarmate de experimentele functionele onderdelen rijp worden, worden ze geleidelijk verplaatst naar de hoofd naam ruimte.

## <a name="how-to-interpret-your-model"></a>Het model interpreteren

U kunt de klassen voor interpretaties en methoden Toep assen om inzicht te krijgen in het globale gedrag van het model of specifieke voor spellingen. De eerste wordt globale uitleg genoemd en de laatste wordt een lokale uitleg genoemd.

De methoden kunnen ook worden gecategoriseerd op basis van het feit of de methode model neutraal of model specifiek is. Sommige methoden richten zich op een bepaald type modellen. De boom uitleg van SHAP is bijvoorbeeld alleen van toepassing op op structuur gebaseerde modellen. Sommige methoden behandelen het model als een zwart vak, zoals nabootsen of de kernel-uitleg van SHAP. Het `interpret`-pakket maakt gebruik van deze verschillende benaderingen op basis van gegevens sets, model typen en use cases.

De uitvoer is een set informatie over hoe een bepaald model de voor spelling maakt, zoals:
* Globaal/lokaal relatief belang rijk onderdeel
* Globale/lokale functie en Voorspellings relatie

### <a name="explainers"></a>Uitleg

Dit pakket maakt gebruik van de technieken voor interpretaties die zijn ontwikkeld in [interprete-Community](https://github.com/interpretml/interpret-community/), een op open source python-pakket voor het interpreteren van modellen en voor het uitleggen van blackbox AI-systemen. [Interpreteren: Community](https://github.com/interpretml/interpret-community/) fungeert als host voor de ondersteunde uitlegers van deze SDK en ondersteunt momenteel de volgende methoden voor interpretatie:

* **Shap tree-uitleg**: de boom structuur van [Shap](https://github.com/slundberg/shap), die gericht is op een polynomiale, snelle Shap waarde schattings algoritme die specifiek is voor bomen en ensembles van structuren.
* **Shap diepe uitleg**: op basis van de uitleg van [Shap](https://github.com/slundberg/shap), dieper uitleg is een algoritme voor hoge snelheids benadering voor Shap waarden in diepe leer modellen die zijn gebaseerd op een verbinding met DeepLIFT die wordt beschreven in het [Shap NIPS-papier](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Tensor flow-modellen en Keras-modellen met behulp van de tensor flow-backend worden ondersteund (er is ook voorlopige ondersteuning voor PyTorch) '.
* **Shap lineaire uitleg**: de lineaire uitleg van [Shap](https://github.com/slundberg/shap)berekent Shap-waarden voor een lineair model, eventueel administratief voor interfunctie-correlaties.

* **Shap kernel-uitleg**: de kernel-uitleg van [Shap](https://github.com/slundberg/shap)maakt gebruik van een speciaal gewogen lokale lineaire regressie om Shap-waarden voor elk model te schatten.
* **Nabootte uitleg**: nabooter is gebaseerd op het idee van [globale surrogaat modellen](https://christophm.github.io/interpretable-ml-book/global.html) met trainingen om blackbox modellen te simuleren. Een globaal surrogaat model is een intrinsiek interpretable model dat is getraind om de voor spellingen van een Black Box-model zo nauw keurig mogelijk te benaderen. Data wetenschapper kan het surrogaat model interpreteren om conclusies over het zwarte box-model te tekenen. U kunt een van de volgende verwerkte modellen gebruiken als surrogaat model: LightGBM (LGBMExplainableModel), lineaire regressie (LinearExplainableModel), stochastische Gradient Daal verklarend model (SGDExplainableModel) en beslissings structuur ( DecisionTreeExplainableModel).


* De **belang rijke uitleg van de permutatie functie**: permutatie functie is een techniek die wordt gebruikt om classificatie-en regressie modellen te verklaren die zijn geïnspireerd op [het breiman van een wille keurige bossen](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (zie sectie 10). Op hoog niveau kan de manier waarop het werkt, worden uitgevoerd door in wille keurige volg orde de gegevens per functie op te nemen voor de hele gegevensset en te berekenen hoeveel de prestatie metriek van de interesses is gewijzigd. Hoe groter de verandering, des te belang rijker deze functie is.

* **Kalk lichter** (`contrib`): op basis van [kalk](https://github.com/marcotcr/lime), gebruikt kalk lichter de geavanceerde status van het algoritme voor het gebruik van lokale vertolkingen (licht-neutraal uitleg) om lokale surrogaat modellen te maken. In tegens telling tot de globale surrogaat modellen, is kalk gericht op het trainen van lokale surrogaat modellen om afzonderlijke voor spellingen te verklaren.
* **Han-tekst uitleg** (`contrib`): Han-tekst uitleg maakt gebruik van een hiërarchische attentie netwerk voor het verkrijgen van model verklaringen van tekst gegevens voor een gegeven zwart-Box-tekst model. Het surrogaat model van het HAN wordt op een gegeven voor speld Black Box-model getraind. Na de wereld wijde training over de tekst verzameling, voegt het een nauw keurige stap voor een specifiek document toe om de nauw keurigheid van de uitleg te verbeteren. HAN maakt gebruik van een bidirectionele RNN met twee attentie lagen, voor de aandacht van zinnen en woorden. Zodra de DNN is getraind op het zwarte box-model en op een specifiek document is afgestemd, kan de gebruiker de woord urgentie uit de attentie lagen extra heren. HAN wordt weer gegeven om nauw keuriger te zijn dan kalk-of SHAP voor tekst gegevens, maar ook meer kosten in het kader van de trainings tijd. Er zijn verbeteringen aangebracht om gebruikers de mogelijkheid te geven het netwerk te initialiseren met ondersteunt woord insluitingen om de trainings tijd te verminderen. De trainings tijd kan aanzienlijk worden verbeterd door een HAN uit te voeren op een externe Azure GPU-VM. De implementatie van HAN wordt beschreven in [' hiërarchische aandacht netwerken voor document classificatie (Yang et al., 2016) '](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Tabellaire uitleg**: `TabularExplainer` maakt gebruik van de volgende logica voor het aanroepen van de directe [Shap](https://github.com/slundberg/shap) -uitleg:

    1. Als het een structuur model is, past u SHAP toe `TreeExplainer`, else
    2. Als het een DNN-model is, past u SHAP `DeepExplainer`toe, else
    3. Als het een lineair model is, past u SHAP toe `LinearExplainer`, else
    3. Behandel het als een zwart-box model en pas SHAP toe `KernelExplainer`


`TabularExplainer` heeft ook aanzienlijke verbeteringen aangebracht in de functie en prestaties ten opzichte van de directe SHAP-uitleg:

* **Samen vatting van de initialisatie-gegevensset**. In gevallen waarin de snelheid van uitleg het belangrijkst is, vatten we de initialisatie-gegevensset samen en genereren ze een kleine set representatieve voor beelden, waardoor zowel globale als lokale uitleg wordt versneld.
* **Bemonstert u de set evaluatie gegevens**. Als de gebruiker een grote set evaluatie voorbeelden heeft door gegeven, maar niet alle moet worden geëvalueerd, kan de para meter sample worden ingesteld op True om de globale uitleg te versnellen.

In het volgende diagram ziet u de huidige structuur van directe en meta-uitleg.

[Architectuur van Machine Learning interpretatie ![](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Ondersteunde modellen

Modellen die zijn getraind op gegevens sets in python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`of `scipy.sparse.csr_matrix` indeling, worden ondersteund door het `explain` pakket met interpreters van de SDK.

De uitleg functies accepteren zowel modellen als pijp lijnen als invoer. Als er een model wordt gegeven, moet het model de Voorspellings functie implementeren `predict` of `predict_proba` die voldoet aan de Scikit-Conventie. Als er een pijp lijn (naam van het pijplijn script) wordt gegeven, wordt ervan uitgegaan dat het actieve pijplijn script een voor spelling retourneert. We ondersteunen modellen die zijn getraind via PyTorch, tensor flow en Keras diepe leer frameworks.

### <a name="local-and-remote-compute-target"></a>Lokaal en extern Compute-doel

Het `explain`-pakket is ontworpen om te werken met zowel lokale als externe Compute-doelen. Als de SDK-functies lokaal worden uitgevoerd, worden er geen contact opgenomen met Azure-Services. U kunt de uitleg op afstand uitvoeren op Azure Machine Learning Compute en de uitleg informatie vastleggen in Azure Machine Learning uitvoerings geschiedenis Services. Zodra deze informatie is geregistreerd, zijn rapporten en visualisaties van de uitleg beschikbaar op Azure Machine Learning-werkruimte portal voor gebruikers analyse.


## <a name="next-steps"></a>Volgende stappen

Zie de [procedure](how-to-machine-learning-interpretability-aml.md) voor het inschakelen van de functie voor interpretering voor model training zowel lokaal als op Azure machine learning externe Compute-resources. Raadpleeg de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) voor aanvullende scenario's.
