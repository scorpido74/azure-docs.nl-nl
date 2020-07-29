---
title: Parametrisatie in AutoML experimenten
titleSuffix: Azure Machine Learning
description: Meer informatie over parametrisatie-instellingen Azure Machine Learning aanbiedingen en hoe functie techniek wordt ondersteund in geautomatiseerde ML experimenten.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 950f258e7380d7fbd25e1a5fe2dd4673ba122c52
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321579"
---
# <a name="featurization-in-automated-machine-learning"></a>Parametrisatie in automatische machine learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze hand leiding leert u het volgende:

- Welke parametrisatie-instellingen Azure Machine Learning Voorst Ellen.
- Hoe u deze functies kunt aanpassen voor uw [geautomatiseerde machine learning experimenten](concept-automated-ml.md).

*Functie techniek* is het proces van het gebruik van domein kennis van de gegevens voor het maken van functies die machine learning (ml) algoritmen voor meer informatie. In Azure Machine Learning worden technieken voor het schalen van gegevens en normalisatie toegepast om functie techniek gemakkelijker te maken. Deze technieken en deze functie techniek worden gezamenlijk *parametrisatie* genoemd in geautomatiseerde machine learning, of *AutoML*, experimenten.

In dit artikel wordt ervan uitgegaan dat u al weet hoe u een AutoML-experiment kunt configureren. Raadpleeg de volgende artikelen voor meer informatie over configuratie:

- Voor een code-eerste ervaring: [Configureer geautomatiseerde ml experimenten met behulp van de Azure machine learning SDK voor python](how-to-configure-auto-train.md).
- Voor een code ring met weinig code of zonder codes: [u kunt geautomatiseerde machine learning modellen maken, controleren en implementeren met behulp van de Azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Parametrisatie configureren

In elk automatisch machine learning experiment worden standaard [technieken voor automatisch schalen en normaliseren](#featurization) toegepast op uw gegevens. Deze technieken zijn soorten parametrisatie die *bepaalde* algoritmen helpen die gevoelig zijn voor functies op verschillende schalen. U kunt echter ook extra parametrisatie inschakelen, zoals gegevens over *ontbrekende waarden*, *code ring*en *trans formaties*.

> [!NOTE]
> Stappen voor automatische machine learning parametrisatie (zoals functie normalisatie, het verwerken van ontbrekende gegevens of het converteren van tekst naar numerieke) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde parametrisatie-stappen die tijdens de training worden toegepast, automatisch toegepast op de invoer gegevens.

Voor experimenten die u configureert met de python-SDK, kunt u de instelling parametrisatie in-of uitschakelen en de parametrisatie-stappen die u voor uw experiment wilt gebruiken verder opgeven. Als u de Azure Machine Learning Studio gebruikt, raadpleegt u de [stappen om parametrisatie in te scha kelen](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

De volgende tabel bevat de geaccepteerde instellingen voor `featurization` in de [AutoMLConfig-klasse](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Parametrisatie-configuratie | Beschrijving|
------------- | ------------- |
|`"featurization": 'auto'`| Hiermee geeft u op dat, als onderdeel van preverwerking, de [stappen voor gegevens Guardrails en parametrisatie](#featurization) automatisch moeten worden uitgevoerd. Dit is de standaardinstelling.|
|`"featurization": 'off'`| Hiermee geeft u op dat parametrisatie stappen niet automatisch moeten worden uitgevoerd.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Hiermee geeft u op dat aangepaste parametrisatie-stappen moeten worden gebruikt. [Meer informatie over het aanpassen van parametrisatie](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatische featurization

De volgende tabel bevat een overzicht van de technieken die automatisch worden toegepast op uw gegevens. Deze technieken worden toegepast op experimenten die zijn geconfigureerd met behulp van de SDK of Studio. Als u dit gedrag wilt uitschakelen, stelt u `"featurization": 'off'` in uw `AutoMLConfig` object in.

> [!NOTE]
> Als u van plan bent om uw door AutoML gemaakte modellen te exporteren naar een [ONNX-model](concept-onnx.md), worden alleen de parametrisatie-opties aangegeven met een asterisk (*) ondersteund in de ONNX-indeling. Meer informatie over [het converteren van modellen naar ONNX](concept-automated-ml.md#use-with-onnx).

|Parametrisatie- &nbsp; stappen| Beschrijving |
| ------------- | ------------- |
|**Hoge kardinaliteit of geen variantie-functies verwijderen*** |Verwijder deze functies uit de trainings-en validatie sets. Is van toepassing op functies waarbij alle waarden ontbreken, met dezelfde waarde in alle rijen of met een hoge kardinaliteit (bijvoorbeeld hashes, Id's of GUID'S).|
|**Ontbrekende waarden toegerekend*** |Voor numerieke functies toegerekend met het gemiddelde van de waarden in de kolom.<br/><br/>Voor categorische-functies toegerekend met de meest frequente waarde.|
|**Aanvullende functies genereren*** |Voor DateTime-functies: jaar, maand, dag, dag van de week, dag van jaar, kwar taal, week van het jaar, uur, minuut, seconde.<br/><br/>Voor tekst functies: term frequentie op basis van unigrams, bigrams en trigrams. Meer informatie over [hoe dit wordt gedaan met Bert.](#bert-integration)|
|**Transformeren en coderen***|Numerieke functies met weinig unieke waarden transformeren in categorische-functies.<br/><br/>Code ring met één Hot-categorische wordt gebruikt voor functies met weinig kardinaliteit. Een hot-hash-code ring wordt gebruikt voor categorische-functies met een hoge kardinaliteit.|
|**Woord insluitingen**|Met een tekst featurizer worden vectoren van tekst tokens geconverteerd naar zinnen vectoren met behulp van een voortraind model. De insluitings vector van elk woord in een document wordt samengevoegd met de rest om een document functie Vector te maken.|
|**Doel codering**|Voor categorische-functies wordt met deze stap elke categorie toegewezen aan een gemiddelde doel waarde voor regressie problemen en aan de klasse-kans voor elke klasse voor classificatie problemen. Op frequentie gebaseerde weging en kruis validatie met k-vouwen worden toegepast om het overschrijden van de toewijzing en lawaai door sparse gegevens categorieën te verminderen.|
|**Coderen van tekst doel**|Voor tekst invoer wordt een gestapeld lineair model met Bag-of-woorden gebruikt om de kans van elke klasse te genereren.|
|**Gewicht van bewijs (WoE)**|Hiermee wordt WoE berekend als een maat eenheid van de correlatie tussen categorische-kolommen en de doel kolom. WoE wordt berekend als het logboek van de ratio van in-class versus out-of-the-Class-kansen. Met deze stap wordt één numerieke functie kolom per klasse gegenereerd en wordt de nood zaak om ontbrekende waarden en uitschieter-behandeling expliciet te verduidelijkt.|
|**Cluster afstand**|Treinen a k: cluster model op alle numerieke kolommen. Produceert *k* nieuwe functies (één nieuwe numerieke functie per cluster) die de afstand van elk voor beeld naar de massa middelpunt van elk cluster bevatten.|

## <a name="data-guardrails"></a>Gegevens Guardrails

*Data Guardrails* helpt u bij het identificeren van mogelijke problemen met uw gegevens (bijvoorbeeld ontbrekende waarden of [onevenwicht in klasse](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Ze helpen u ook om corrigerende maat regelen te nemen voor betere resultaten.

De gegevens Guardrails worden toegepast:

- **Voor SDK-experimenten**: wanneer de para meters `"featurization": 'auto'` of `validation=auto` in uw object zijn opgegeven `AutoMLConfig` .
- **Voor Studio experimenten**: wanneer automatische parametrisatie is ingeschakeld.

U kunt de gegevens Guardrails voor uw experiment bekijken:

- Door in te stellen `show_output=True` Wanneer u een experiment verzendt met behulp van de SDK.

- In de Studio, op het tabblad **gegevens Guardrails** van uw automatische ml run.

### <a name="data-guardrail-states"></a>Status gegevens guardrail

Gegevens Guardrails worden weer gegeven in een van de volgende drie statussen:

|Status| Beschrijving |
|----|---- |
|**Buffer**| Er zijn geen gegevens problemen gedetecteerd en er is geen actie vereist voor u. |
|**Gereed**| Er zijn wijzigingen toegepast op uw gegevens. We raden u aan de corrigerende maat regelen te controleren die AutoML hebben genomen, om ervoor te zorgen dat de wijzigingen worden uitgelijnd met de verwachte resultaten. |
|**Gewaarschuwd**| Er is een gegevens probleem gedetecteerd, maar dit kan niet worden opgelost. We raden u aan het probleem te herzien en op te lossen.|

### <a name="supported-data-guardrails"></a>Guardrails voor ondersteunde gegevens

In de volgende tabel worden de gegevens Guardrails beschreven die momenteel worden ondersteund en de bijbehorende statussen die u kunt zien wanneer u uw experiment verzendt:

Guardrail|Status|Voor waarde &nbsp; voor &nbsp; trigger
---|---|---
**Ontbrekende functie waarden toerekening** |Buffer <br><br><br> Gereed| Er zijn geen ontbrekende onderdeel waarden gedetecteerd in uw trainings gegevens. Meer informatie over de toerekening van [ontbrekende waarden.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Er zijn ontbrekende functie waarden gedetecteerd in uw trainings gegevens en deze zijn toegerekend.
**Functie verwerking met hoge kardinaliteit** |Buffer <br><br><br> Gereed| Uw invoer is geanalyseerd en er zijn geen functies met een hoge kardinaliteit gedetecteerd. <br><br> Er zijn functies met een hoge kardinaliteit gedetecteerd in uw invoer en zijn afgehandeld.
**Verwerking van splitsing van validatie** |Gereed| De validatie configuratie is ingesteld op `'auto'` en de trainings gegevens bevatten *minder dan 20.000 rijen*. <br> Elke iteratie van het getrainde model is gevalideerd door Kruis validatie te gebruiken. Meer informatie over [validatie gegevens](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data). <br><br> De validatie configuratie is ingesteld op `'auto'` en de trainings gegevens bevatten *meer dan 20.000 rijen*. <br> De invoer gegevens zijn gesplitst in een trainings gegevensset en een validatie gegevensset voor validatie van het model.
**Detectie van klasse-verdeling** |Buffer <br><br><br><br><br> Gewaarschuwd | Uw invoer is geanalyseerd en alle klassen zijn evenwichtig in uw trainings gegevens. Een gegevensset wordt beschouwd als evenwichtig als elke klasse een goede representatie heeft in de gegevensset, gemeten op basis van het aantal en de verhouding van steek proeven. <br><br><br> Er zijn niet-sluitende klassen gedetecteerd in uw invoer. Los het probleem met de oplossing op om model afwijking te herstellen. Meer informatie over [gegevens](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)die niet in balans zijn.
**Detectie van geheugen problemen** |Buffer <br><br><br><br> Gereed |<br> De geselecteerde waarden (horizon, vertraging, doorlopend venster) zijn geanalyseerd en er zijn geen mogelijke problemen met de geheugen detectie gedetecteerd. Meer informatie over [prognose configuraties](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)voor time series. <br><br><br>De geselecteerde waarden (horizon, vertraging, doorlopend venster) zijn geanalyseerd en kunnen ervoor zorgen dat het experiment te weinig geheugen heeft. De vertragings-of venster configuraties zijn uitgeschakeld.
**Frequentie detectie** |Buffer <br><br><br><br> Gereed |<br> De tijd reeks is geanalyseerd en alle gegevens punten zijn afgestemd op de gedetecteerde frequentie. <br> <br> De tijd reeks is geanalyseerd en gegevens punten die niet zijn uitgelijnd met de gedetecteerde frequentie, zijn gedetecteerd. Deze gegevens punten zijn verwijderd uit de gegevensset. Meer informatie over het [voorbereiden van gegevens voor time series-prognoses](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Parametrisatie aanpassen

U kunt uw parametrisatie-instellingen aanpassen om ervoor te zorgen dat de gegevens en functies die worden gebruikt om uw ML-model te trainen, in relevante voor spellingen resulteren.

Geef in uw object op om featurizations aan te passen  `"featurization": FeaturizationConfig` `AutoMLConfig` . Als u de Azure Machine Learning Studio gebruikt voor uw experiment, raadpleegt u het [artikel](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Ondersteunde aanpassingen zijn onder andere:

|Aanpassing|Definitie|
|--|--|
|**Update van het kolom doel**|Het functie type voor de opgegeven kolom overschrijven.|
|**Para meter bijwerken van trans formatie** |De para meters voor de opgegeven transformator bijwerken. *Biedt momenteel* ondersteuning voor toerekening (gemiddelde, meest frequente en gemiddelde) en *HashOneHotEncoder*.|
|**Kolommen neerzetten** |Hiermee geeft u kolommen op die moeten worden featurized.|
|**Trans formaties blok keren**| Hiermee geeft u de Block-trans formaties op die moeten worden gebruikt in het parametrisatie-proces.|

Het object maken met `FeaturizationConfig` API-aanroepen:

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

## <a name="bert-integration"></a>BERT-integratie 
[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) wordt gebruikt in de parametrisatie-laag van automatische milliliters. In deze laag detecteren we of een kolom vrije tekst of andere typen gegevens bevat, zoals tijds tempels of eenvoudige getallen en we parametriseer dienovereenkomstig. Voor BERT kunnen we het model verfijnen/trainen door gebruik te maken van de door de gebruiker geleverde labels. vervolgens worden de document insluitingen uitgevoerd (voor BERT deze zijn de laatste verborgen status die is gekoppeld aan het speciale [CLS]-token) als functies naast andere functies zoals op tijds tempel gebaseerde functies (bijvoorbeeld dag van de week) of getallen die veel typische gegevens sets bevatten 

Als u BERT wilt inschakelen, moet u GPU Compute for training gebruiken. Als er een CPU-berekening wordt gebruikt, wordt in plaats van BERT AutoML BiLSTM DNN featurizer. Voor het aanroepen van BERT moet u ' enable_dnn: True ' instellen in automl_settings en GPU Compute gebruiken (bijvoorbeeld vm_size = "STANDARD_NC6" of een hogere GPU). Raadpleeg [Dit notitie blok voor een voor beeld](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb).

AutoML voert de volgende stappen uit, voor het geval van BERT (u moet enable_dnn: true instellen in automl_settings voor deze items die moeten worden uitgevoerd):

1. Preverwerking met inbegrip van tokenisatie van alle tekst kolommen (in de parametrisatie-samen vatting van het uiteindelijke model ziet u de StringCast-transformator. Ga naar [Dit notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) om een voor beeld te zien van het maken van de parametrisatie-samen vatting van het model met behulp van de- `get_featurization_summary()` methode.

```python
text_transformations_used = []
for column_group in fitted_model.named_steps['datatransformer'].get_featurization_summary():
    text_transformations_used.extend(column_group['Transformations'])
text_transformations_used
```

2. Voegt alle tekst kolommen toe aan een enkele tekst kolom, dus u ziet "StringConcatTransformer" in het uiteindelijke model. 

> [!NOTE]
> Onze implementatie van BERT beperkt de totale tekst lengte van een trainings voorbeeld tot 128-tokens. Dit betekent dat alle tekst kolommen bij het samen voegen de meeste 128-tokens lang moeten zijn. In het ideale geval, als er meerdere kolommen aanwezig zijn, moet elke kolom worden verwijderd, zodat aan deze voor waarde wordt voldaan. Als er bijvoorbeeld twee tekst kolommen in de gegevens zijn, moeten beide tekst kolommen worden verwijderd naar de 64-tokens (ervan uitgaande dat u wilt dat beide kolommen gelijkmatig worden weer gegeven in de laatste samengevoegde tekst kolom) voordat de gegevens worden gevoederd in AutoML. Voor samengevoegde kolommen met een lengte >128-tokens, wordt deze invoer door de Tokenizer-laag van BERT afgekapt op 128-tokens.

3. In de stap voor het opruimen van functies vergelijkt AutoML BERT met de basis lijn (een verzameling woorden en getrainde woord insluitingen) op een voor beeld van de gegevens en bepaalt u of BERT nauw keurigere verbeteringen oplevert. Als wordt vastgesteld dat BERT beter presteert dan de basis lijn, gebruikt AutoML vervolgens BERT voor Text parametrisatie als de optimale parametrisatie-strategie en gaat over op de volledige gegevens van featurizing. In dat geval wordt de "PretrainedTextDNNTransformer" in het uiteindelijke model weer geven.

AutoML ondersteunt momenteel ongeveer 100 talen en afhankelijk van de taal van de gegevensset, kiest AutoML het juiste BERT-model. Voor Duitse gegevens gebruiken we het Duitse BERT-model. Voor het Engels gebruiken we het Engelse BERT-model. Voor alle andere talen gebruiken we het Multilingual BERT-model.

In de volgende code wordt het Duitse BERT-model geactiveerd, omdat de taal van de gegevensset is opgegeven in deu, de taal code van drie letters voor Duits volgens de [ISO-classificatie](https://iso639-3.sil.org/code/hbs):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het instellen van uw geautomatiseerde ML experimenten:

    * Voor een code-eerste ervaring: [Configureer geautomatiseerde ml experimenten met behulp van de Azure machine learning SDK](how-to-configure-auto-train.md).
    * Voor een code ring met weinig code of voor [uw eigen ervaring: Maak uw automatische ml-experimenten in de Azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Meer informatie over [hoe en waar een model moet worden geïmplementeerd](how-to-deploy-and-where.md).

* Meer informatie over [het trainen van een regressie model met behulp van geautomatiseerde machine learning](tutorial-auto-train-models.md) of [hoe u kunt trainen met behulp van geautomatiseerde machine learning op een externe bron](how-to-auto-train-remote.md).
