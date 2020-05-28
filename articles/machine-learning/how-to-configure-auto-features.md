---
title: Parametrisatie in AutoML experimenten
titleSuffix: Azure Machine Learning
description: Meer informatie over parametrisatie-instellingen Azure Machine Learning aanbiedingen en hoe functie techniek wordt ondersteund in geautomatiseerde ml experimenten.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 93e18a95e30c21a44f9ca7df92925323930a9ce8
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122428"
---
# <a name="featurization-with-automated-machine-learning"></a>Parametrisatie met geautomatiseerde machine learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze hand leiding leest u welke parametrisatie-instellingen worden aangeboden en hoe u deze kunt aanpassen voor uw [geautomatiseerde machine learning experimenten](concept-automated-ml.md).

Feature engineering is het proces van het gebruik van domein kennis van de gegevens voor het maken van functies waarmee u beter ML-algoritmen kunt leren. In Azure Machine Learning worden technieken voor het schalen van gegevens en normalisatie toegepast om functie techniek te vergemakkelijken. Deze technieken en functie techniek worden gezamenlijk aangeduid als parametrisatie in geautomatiseerde machine learning, AutoML, experimenten.

In dit artikel wordt ervan uitgegaan dat u al bekend bent met het configureren van een AutoML-experiment. Raadpleeg de volgende artikelen voor meer informatie:

* Voor een code-eerste ervaring: [Configureer geautomatiseerde ml experimenten met de python-SDK](how-to-configure-auto-train.md).
* Voor een weinig of geen code-ervaring: [Maak, Bekijk en implementeer automatische machine learning modellen met de Azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md)

## <a name="configure-featurization"></a>Parametrisatie configureren

In elk automatisch machine learning experiment worden standaard [technieken voor automatisch schalen en normaliseren](#featurization) toegepast op uw gegevens. Deze schaal-en normalisatie technieken zijn soorten parametrisatie die *bepaalde* algoritmen helpen die gevoelig zijn voor functies op verschillende schalen. U kunt echter ook extra parametrisatie inschakelen, zoals **ontbrekende waarden, code ring** en **trans formaties**.

> [!NOTE]
> Automatische machine learning parametrisatie stappen (functie normalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarde, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde parametrisatie-stappen die tijdens de training worden toegepast, automatisch toegepast op de invoer gegevens.

Voor experimenten die zijn geconfigureerd met de SDK kunt u de instelling in-of uitschakelen `featurization` en de parametrisatie-stappen voor het experiment opgeven. Als u de Azure Machine Learning Studio gebruikt, raadpleegt u parametrisatie inschakelen met de [volgende stappen](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

De volgende tabel bevat de geaccepteerde instellingen voor `featurization` in de [AutoMLConfig-klasse](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Parametrisatie-configuratie | Beschrijving|
------------- | ------------- |
|**`"featurization": 'auto'`**| Geeft aan dat als onderdeel van de voor verwerking, [gegevens Guardrails en parametrisatie-stappen](#featurization) automatisch worden uitgevoerd. **Standaard instelling**.|
|**`"featurization": 'off'`**| Hiermee wordt aangegeven dat parametrisatie-stappen niet automatisch moeten worden uitgevoerd.|
|**`"featurization":`&nbsp;`'FeaturizationConfig'`**| Hiermee wordt aangegeven dat er een aangepaste parametrisatie-stap moet worden gebruikt. [Meer informatie over het aanpassen van parametrisatie](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatische parametrisatie

De volgende tabel bevat een overzicht van de technieken die automatisch worden toegepast op uw gegevens. Dit gebeurt voor experimenten die zijn geconfigureerd via de SDK of de Studio. Als u dit gedrag wilt uitschakelen, stelt u `"featurization": 'off'` in uw `AutoMLConfig` object in.

> [!NOTE]
> Als u van plan bent om uw automatisch gegenereerde modellen te exporteren naar een [ONNX-model](concept-onnx.md), worden alleen de parametrisatie-opties aangeduid met een * ondersteund in de ONNX-indeling. Meer informatie over [het converteren van modellen naar ONNX](concept-automated-ml.md#use-with-onnx). 

|Parametrisatie- &nbsp; stappen| Beschrijving |
| ------------- | ------------- |
|**Hoge kardinaliteit of geen variantie-functies verwijderen*** |Deze verwijderen uit trainingen en validatie sets, met inbegrip van functies waarbij alle waarden ontbreken, dezelfde waarde over alle rijen of met hoge kardinaliteit (bijvoorbeeld hashes, Id's of GUID'S).|
|**Ontbrekende waarden toegerekend*** |Voor numerieke functies toegerekend met het gemiddelde van de waarden in de kolom.<br/><br/>Voor categorische-functies toegerekend met de meest frequente waarde.|
|**Aanvullende functies genereren*** |Voor DateTime-functies: jaar, maand, dag, dag van de week, dag van jaar, kwar taal, week van het jaar, uur, minuut, seconde.<br/><br/>Voor tekst functies: term frequentie op basis van unigrams, bi-gram en Tri-Character-gram.|
|**Transformeren en coderen***|Numerieke functies met weinig unieke waarden worden omgezet in categorische-functies.<br/><br/>Er wordt één Hot encoding uitgevoerd voor lage kardinaliteit categorische; voor hoge kardinaliteit, een-hot-hash-code ring.|
|**Woord insluitingen**|Tekst featurizer waarmee vectoren van tekst tokens worden geconverteerd naar sentence vectoren met behulp van een vooraf getraind model. De insluitings vector van elk woord in een document wordt samen geaggregeerd om een document functie Vector te maken.|
|**Doel codering**|Voor categorische-functies wijst elke categorie toe met de gemiddelde doel waarde voor regressie problemen en op basis van de klasse-kans voor elke klasse voor classificatie problemen. De Kruis validatie op basis van frequentie en k-vouwen wordt toegepast om de toewijzing en het lawaai dat door sparse gegevens categorieën wordt veroorzaakt, te verminderen.|
|**Coderen van tekst doel**|Voor tekst invoer wordt een gestapeld lineair model met Bag-of-woorden gebruikt om de kans van elke klasse te genereren.|
|**Gewicht van bewijs (WoE)**|Hiermee wordt WoE berekend als een maat eenheid van de correlatie tussen categorische-kolommen en de doel kolom. Het wordt berekend als het logboek van de verhouding van de in-class versus van de cursus kansen. Met deze stap wordt één numerieke functie kolom per klasse uitgevoerd en wordt de nood zaak om ontbrekende waarden en uitschieter-behandeling expliciet te toegerekend.|
|**Cluster afstand**|Treinen a k: cluster model op alle numerieke kolommen.  Hiermee worden k nieuwe functies, een nieuwe numerieke functie per cluster, met de afstand van elk voor beeld naar de massa middelpunt van elk cluster uitgevoerd.|

## <a name="data-guardrails"></a>Gegevens Guardrails

Data Guardrails helpt u bij het identificeren van mogelijke problemen met uw gegevens (bijvoorbeeld ontbrekende waarden, [klasse onevenwicht](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)) en het nemen van corrigerende maat regelen voor betere resultaten. 

De gegevens Guardrails worden toegepast 

* **Voor SDK-experimenten**, wanneer de para meters `"featurization": 'auto'` of `validation=auto` worden opgegeven in uw `AutoMLConfig` object.
* **Voor Studio experimenten**, wanneer *automatische parametrisatie* is ingeschakeld.  

U kunt de gegevens Guardrails die betrekking hebben op uw experiment bekijken

* Door in te stellen `show_output=True` bij het verzenden van een experiment met de python-SDK.

* In de studio op het tabblad **gegevens Guardrails** van kunt u een geautomatiseerd ml-programma uitvoeren.

### <a name="data-guardrail-states"></a>Status gegevens guardrail

In data Guardrails worden een van de volgende drie statussen weer gegeven: **door gegeven**, **voltooid**of **gewaarschuwd**.

|Staat| Beschrijving |
|----|---- |
|**Buffer**| Er zijn geen gegevens problemen gedetecteerd en er is geen gebruikers actie vereist. |
|**Gereed**| Er zijn wijzigingen toegepast op uw gegevens. We moedigen gebruikers aan om de corrigerende maat regelen te controleren om ervoor te zorgen dat de wijzigingen worden uitgelijnd met de verwachte resultaten. |
|**Gewaarschuwd**| Er is een gegevens probleem gedetecteerd dat niet kan worden opgelost. We raden gebruikers aan om het probleem te herzien en op te lossen.| 

In de volgende tabel worden de gegevens Guardrails beschreven die momenteel worden ondersteund, en de bijbehorende statussen die gebruikers kunnen krijgen bij het verzenden van hun experiment.

Guardrail|Status|Voor waarde &nbsp; voor &nbsp; trigger
---|---|---
**Ontbrekende functie waarden toerekening** |*Buffer* <br><br><br> *Gereed*| Er zijn geen ontbrekende onderdeel waarden gedetecteerd in uw trainings gegevens. Meer informatie over [ontbrekende waarden toegerekend.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Er zijn ontbrekende onderdeel waarden gedetecteerd in uw trainings gegevens en toegerekend.
**Functie verwerking met hoge kardinaliteit** |*Buffer* <br><br><br> *Gereed*| Uw invoer is geanalyseerd en er zijn geen functies met een hoge kardinaliteit gedetecteerd. Meer informatie over de [detectie van hoge functies voor kardinaliteit.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Er zijn functies met een hoge kardinaliteit gedetecteerd in uw invoer en zijn afgehandeld.
**Verwerking van splitsing van validatie** |*Gereed*| De validatie configuratie is ingesteld op auto en de trainings gegevens bevatten **minder dan 20.000 rijen**. <br> Elke iteratie van het getrainde model is door Kruis validatie gevalideerd. Meer informatie over [validatie gegevens.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> De validatie configuratie is ingesteld op ' auto ' en de trainings gegevens bevatten **meer dan 20.000 rijen**. <br> De invoer gegevens zijn gesplitst in een trainings gegevensset en een validatie gegevensset voor validatie van het model.
**Detectie van klasse-verdeling** |*Buffer* <br><br><br><br><br> *Gewaarschuwd* | Uw invoer is geanalyseerd en alle klassen zijn evenwichtig in uw trainings gegevens. Een gegevensset wordt als evenwichtig beschouwd als elke klasse een goede representatie in de gegevensset heeft, gemeten op basis van het aantal en de verhouding van steek proeven. <br><br><br> Er zijn niet-sluitende klassen gedetecteerd in uw invoer. Los het probleem met de oplossing op om model afwijking te herstellen. Meer informatie over [gegevens](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data) die niet in balans zijn.
**Detectie van geheugen problemen** |*Buffer* <br><br><br><br> *Gereed* |<br> De geselecteerde {horizon, vertraging, rollend venster} waarde (n) zijn geanalyseerd en er zijn geen mogelijke problemen met de geheugen detectie gedetecteerd. Meer informatie over [prognose configuraties](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) voor time series. <br><br><br>De geselecteerde waarden voor {Horizon, lag, Rolling venster} zijn geanalyseerd en kunnen ertoe leiden dat uw experiment te weinig geheugen beschikbaar heeft. De configuratie van de vertraging of het uitrollen venster is uitgeschakeld.
**Frequentie detectie** |*Buffer* <br><br><br><br> *Gereed* |<br> De tijd reeks is geanalyseerd en alle gegevens punten zijn afgestemd op de gedetecteerde frequentie. <br> <br> De tijd reeks is geanalyseerd en gegevens punten die niet zijn uitgelijnd met de gedetecteerde frequentie, zijn gedetecteerd. Deze gegevens punten zijn verwijderd uit de gegevensset. Meer informatie over het [voorbereiden van gegevens voor time series-prognoses.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="customize-featurization"></a>Parametrisatie aanpassen

U kunt uw parametrisatie-instellingen aanpassen om ervoor te zorgen dat de gegevens en functies die worden gebruikt om uw ML-model te trainen, in relevante voor spellingen resulteren. 

Geef in uw object op om featurizations aan te passen  `"featurization": FeaturizationConfig` `AutoMLConfig` . Als u de Azure Machine Learning Studio voor uw experiment gebruikt, raadpleegt u de [instructies](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Ondersteunde aanpassingen zijn onder andere:

|Aanpassing|Definitie|
|--|--|
|**Update van het kolom doel**|Het functie type voor de opgegeven kolom overschrijven.|
|**Para meter bijwerken van trans formatie** |De para meters voor de opgegeven transformator bijwerken. Biedt momenteel ondersteuning voor toerekening (gemiddelde, meest frequente & mediaan) en HashOneHotEncoder.|
|**Kolommen neerzetten** |Kolommen die moeten worden verwijderd uit de featurized.|
|**Trans formaties blok keren**| Blok keren dat trans formaties moeten worden gebruikt in het parametrisatie-proces.|

Het FeaturizationConfig-object maken met API-aanroepen:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```
## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het instellen van uw geautomatiseerde ML experimenten,

    * Voor code-ervarings gebruikers: [Configureer Automatische ml experimenten met de Azure machine learning SDK](how-to-configure-auto-train.md).
    * Voor klanten met weinig of geen code ervaring: [Maak uw geautomatiseerde machine learning experimenten in azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Meer informatie over [hoe en waar een model moet worden geïmplementeerd](how-to-deploy-and-where.md).

* Meer informatie over [het trainen van een regressie model met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [hoe u het gebruik van geautomatiseerde machine learning op een externe bron kunt trainen](how-to-auto-train-remote.md).
