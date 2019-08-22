---
title: Afstemmen van hyperparameters voor uw model
titleSuffix: Azure Machine Learning service
description: Efficiënt afstemmen van hyperparameters voor uw deep learning / machine learning-model met behulp van Azure Machine Learning-service. U leert hoe u de zoek ruimte voor de para meters definieert, een primaire metriek opgeeft die u wilt optimaliseren, en vervroegde uitvoeringen worden uitgevoerd.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2019
ms.custom: seodec18
ms.openlocfilehash: 5a6f7c6de005112578cc29865574e5e255c99a8e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873073"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning-service"></a>Afstemmen van hyperparameters voor uw model met Azure Machine Learning-service

Efficiënt afstemmen van hyperparameters voor uw model met behulp van Azure Machine Learning-service.  Hyperparameter afstemmen bevat de volgende stappen uit:

* De parameter zoeken ruimte definiëren
* Geef een primaire metrische waarde om te optimaliseren  
* Geef criteria op voor vroegtijdige beëindiging voor het uitvoeren van slecht wordt uitgevoerd
* Bronnen voor het afstemmen van hyperparameter toewijzen
* Een experiment met de bovenstaande configuratie starten
* De trainingsuitvoeringen visualiseren
* Selecteer de best presterende configuratie voor uw model

## <a name="what-are-hyperparameters"></a>Wat zijn hyperparameters?

Hyperparameters zijn aanpasbare parameters die u wilt een model te trainen, die het trainingsproces zelf bepalen. Bijvoorbeeld, het trainen van een deep neural network, besluit u het aantal verborgen lagen in het netwerk en het aantal knooppunten in elke laag voorafgaand aan het model te trainen. Deze waarden blijven doorgaans constant tijdens de training.

In uitgebreide machineleren / machine learning-scenario's modelprestaties is afhankelijk van sterk op basis van de waarden van de hyperparameter geselecteerd. Het doel van hyperparameter verkenning is om te zoeken in configuraties met verschillende hyperparameter te vinden van een configuratie die in de beste prestaties resulteert. Het proces voor het verkennen van hyperparameter is meestal desktoptoepassingen handmatige, gezien het feit dat de search-ruimte vast is en evaluatie van elke configuratie kan duur zijn.

Azure Machine Learning kunt u hyperparameter verkennen automatiseren op een efficiënte manier, waardoor u veel tijd en resources. U het bereik van waarden hyperparameter opgeven en een maximum aantal training wordt uitgevoerd. Het systeem vervolgens automatisch meerdere gelijktijdige uitvoeringen met verschillende configuraties worden gestart en de configuratie die in de beste prestaties resulteert, gemeten door de metrische gegevens die u zoekt. Trainingsuitvoeringen van slecht presterende zijn automatisch vroege beëindigd, verspilling van compute-resources beperken. Deze resources worden in plaats daarvan gebruikt om andere configuraties hyperparameter verkennen.


## <a name="define-search-space"></a>Search ruimte definiëren

Automatisch afstemmen van hyperparameters door het bereik van waarden die zijn gedefinieerd voor elke hyperparameter verkennen.

### <a name="types-of-hyperparameters"></a>Typen hyperparameters

Elke afstemming kan afzonderlijk of doorlopend zijn en heeft een distributie van waarden die worden beschreven door een [parameter expressie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Discrete hyperparameters 

Discrete hyperparameters zijn opgegeven als een `choice` tot discrete waarden. `choice` kan zijn:

* een of meer door komma's gescheiden waarden
* een `range` object
* een willekeurige `list` object


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In dit geval `batch_size` neemt een van de waarden [16, 32, 64, 128] en `number_of_hidden_layers` neemt een van de waarden [1, 2, 3, 4].

Geavanceerde discrete hyperparameters kan ook worden opgegeven met behulp van een distributiepunt. De volgende distributies worden ondersteund:

* `quniform(low, high, q)` -Retourneert een waarde, zoals round (uniform (laag, hoog) / q) * q
* `qloguniform(low, high, q)` -Retourneert een waarde, zoals round (exp (uniform (laag, hoog)) / q) * q
* `qnormal(mu, sigma, q)` -Retourneert een waarde, zoals round (normaal (mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` -Retourneert een waarde, zoals round (exp (normaal (mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Continue hyperparameters 

Continue hyperparameters worden opgegeven als een distributiepunt via een continue reeks waarden. Ondersteunde distributies zijn onder andere:

* `uniform(low, high)` -Retourneert een gelijkmatig verdeeld over lage en hoge waarde
* `loguniform(low, high)` -Retourneert een waarde getekend op basis van exp (uniform (laag, hoog)), zodat de logaritme van de geretourneerde waarde is gelijkmatig verdeeld.
* `normal(mu, sigma)` -Een werkelijke waarde retourneert die normaal gesproken wordt gedistribueerd met gemiddelde mu en de standaarddeviatie sigma
* `lognormal(mu, sigma)` -Retourneert een waarde getekend op basis van exp (normaal (mu, sigma)), zodat de logaritme van de geretourneerde waarde wordt normaal gesproken gedistribueerd

Een voorbeeld van een parameterdefinitie ruimte:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Deze code definieert een spatie zoeken met twee parameters - `learning_rate` en `keep_probability`. `learning_rate` heeft een normale distributie met een gemiddelde waarde 10 en de standaarddeviatie van 3. `keep_probability` een uniforme distributie met een minimale waarde van 0,05 en een maximumwaarde van 0,1 heeft.

### <a name="sampling-the-hyperparameter-space"></a>De ruimte hyperparameter steekproeven

U kunt ook de parameter methode te gebruiken via de definitie van de ruimte hyperparameter opgeven. Azure Machine Learning-service ondersteunt steekproeven, raster steekproeven en Bayesiaanse steekproeven.

#### <a name="random-sampling"></a>Steekproeven

In een willekeurige steekproef hyperparameter waarden willekeurig geselecteerd uit de gedefinieerde search-ruimte. Met een [wille keurige steek proef](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) kan de zoek ruimte zowel discrete als doorlopende Hyper parameters bevatten.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Raster steekproeven

[Raster sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) voert een eenvoudige raster zoekactie uit op alle bruikbare waarden in de gedefinieerde zoek ruimte. Kan alleen worden gebruikt met de opgegeven met behulp van hyperparameters `choice`. De volgende ruimte heeft bijvoorbeeld een totaal van zes voorbeelden:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesiaanse steekproeven

[Bayesiaanse-steek proeven](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) zijn gebaseerd op het Bayesiaanse-optimalisatie algoritme en maken intelligente keuzes voor de afstemming-waarden voor het volgende voor beeld. Deze kiest de steekproef op basis van hoe de vorige voorbeelden uitgevoerd, zoals dat het nieuwe voorbeeld verbetert de gerapporteerde primaire metrische gegevens.

Wanneer u Bayesiaanse steekproeven gebruikt, heeft het aantal gelijktijdige uitvoeringen een invloed op de effectiviteit van het proces voor afstemmen. Normaal gesproken worden een kleiner aantal gelijktijdige uitvoeringen kan leiden tot betere steekproeven convergentie, omdat de kleinere graad van parallelle uitvoering verhoogt het aantal uitvoeringen die baat bij eerder voltooid wordt uitgevoerd hebben.

Bayesiaanse-steek proeven `choice`bieden `uniform`alleen ondersteuning `quniform` voor, en distributies in de zoek ruimte.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesiaanse steekproeven biedt geen ondersteuning voor vroegtijdige beëindiging beleid (Zie [opgeven van een beleid voor vroegtijdige beëindiging](#specify-early-termination-policy)). Wanneer u Bayesiaanse parameter steekproeven, stelt `early_termination_policy = None`, of laat het veld uit de `early_termination_policy` parameter.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Primaire metrische gegevens opgeven

Geef de [primaire meet waarde](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) op voor het afstemming-afstemmings experiment dat u wilt optimaliseren. Elke uitvoering training wordt geëvalueerd voor de primaire metrische gegevens. Slecht presterend wordt uitgevoerd (waarbij de primaire metrische waarde voldoet niet aan criteria die zijn ingesteld door het beleid voor vroegtijdige beëindiging) wordt beëindigd. Naast de naam van het primaire metrische, u ook het doel van de optimalisatie - opgeven of u wilt maximaliseren of beperken van de primaire metrische gegevens.

* `primary_metric_name`: De naam van de primaire metriek die moet worden geoptimaliseerd. De naam van de primaire metriek moet exact overeenkomen met de naam van de metrische gegevens vastgelegd door het trainingsscript. Zie [Meld u metrische gegevens voor het afstemmen van hyperparameter](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Dit kan ofwel `PrimaryMetricGoal.MAXIMIZE` of `PrimaryMetricGoal.MINIMIZE` en bepalen of de primaire metriek wordt gemaximaliseerd of geminimaliseerd bij het evalueren van de uitvoeringen. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimaliseer de wordt uitgevoerd voor maximale 'nauwkeurigheid'.  Zorg ervoor dat u deze waarde in uw trainingsscript aanmelden.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Logboek metrische gegevens voor het afstemmen van hyperparameter

Het trainingsscript voor het model moet zich aanmelden van de relevante metrische gegevens tijdens het trainen van het model. Wanneer u de hyperparameter afstemmen configureert, geeft u de primaire metriek moet worden gebruikt voor het evalueren van prestaties uitvoeren. (Zie [opgeven van een primaire metrische waarde voor het Optimaliseer](#specify-primary-metric-to-optimize).)  U moet zich aanmelden met deze metriek, zodat deze beschikbaar is om het proces afstemmen hyperparameter in uw trainingsscript.

Meld u met deze metriek in uw trainingsscript met het volgende voorbeeld-fragment:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Het trainingsscript berekent de `val_accuracy` en registreert u deze als 'nauwkeurig', die wordt gebruikt als de primaire metrische gegevens. Telkens wanneer die de metriek wordt vastgelegd wordt die door de hyperparameter afstemmen van de service ontvangen. Het is aan de ontwikkelaar model om te bepalen hoe vaak deze metrische gegevens rapporteren.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Beleid voor vroegtijdige beëindiging opgeven

Beëindig slecht presterend wordt uitgevoerd automatisch met een beleid voor vroegtijdige beëindiging. Beëindiging minder verspilling van resources en in plaats daarvan deze resources worden gebruikt voor het verkennen van de andere parameterconfiguraties.

Wanneer u een beleid voor vroegtijdige beëindiging gebruikt, kunt u de volgende parameters die bepalen wanneer een beleid wordt toegepast:

* `evaluation_interval`: de frequentie voor het toepassen van het beleid. Telkens wanneer de training de primaire metriek scriptlogboeken telt als één interval. Dus een `evaluation_interval` van 1 wordt het beleid wordt toegepast wanneer het trainingsscript de primaire metrische gegevens rapporteert. Een `evaluation_interval` van 2 wordt het beleid toepassen andere wanneer het trainingsscript de primaire metrische gegevens rapporteert. Indien niet opgegeven, `evaluation_interval` is standaard ingesteld op 1.
* `delay_evaluation`: de eerste evaluatie van het beleid voor een opgegeven aantal intervallen uitstelt. Het is een optionele parameter waarmee alle configuraties voor een initiële minimum aantal intervallen, vermijden voortijdige beëindiging van de training uit te voeren wordt uitgevoerd. Als u opgeeft, is het beleid van toepassing is elke meervoud van evaluation_interval die groter zijn dan of gelijk is aan delay_evaluation.

Azure Machine Learning-service ondersteunt de volgende beleidsregels voor vroegtijdige beëindiging.

### <a name="bandit-policy"></a>Bandieten beleid

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) is een afsluitings beleid op basis van een toegestane factor en een toegestane vertragings hoeveelheid en evaluatie-interval. Het beleid beëindigt vroeg geen runs worden gestart wanneer de primaire metriek valt niet binnen het opgegeven slack factor / slack bedrag met betrekking tot de best presterende training run. Het duurt de volgende configuratieparameters:

* `slack_factor` of `slack_amount`: de toegestane vertraging toegestaan met betrekking tot de best presterende training uitvoeren. `slack_factor` Hiermee geeft u de toegestane vertraging als een verhouding. `slack_amount` Hiermee geeft u de toegestane vertraging als absoluut bedrag, in plaats van een verhouding.

    Bijvoorbeeld, kunt u overwegen een bandieten-beleid wordt toegepast op basis van interval 10. Wordt ervan uitgegaan dat een primaire metric 0,8 met als doel om te maximaliseren van de primaire metrische gegevens het beste uitvoeren van uitvoering op interval 10 gerapporteerd. Als het beleid is opgegeven met een `slack_factor` van 0,2, een training wordt uitgevoerd, waarvan aanbevolen metrische gegevens op basis van interval 10 is kleiner dan 0.66 (0,8 / (1 +`slack_factor`)) wordt beëindigd. Als er in plaats daarvan het beleid is opgegeven met een `slack_amount` van 0,2, een training wordt uitgevoerd, waarvan aanbevolen metrische gegevens op basis van interval 10 is kleiner dan 0,6 (0,8 - `slack_amount`) wordt beëindigd.
* `evaluation_interval`: de frequentie voor het toepassen van het beleid (optionele parameter).
* `delay_evaluation`: de eerste evaluatie van het beleid voor een opgegeven aantal intervallen (optionele parameter) uitstelt.


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In dit voorbeeld wordt het beleid voor vroegtijdige beëindiging toegepast op elke interval wanneer metrische gegevens worden gerapporteerd, beginnend bij de evaluatie-interval 5. Elke run waarvan aanbevolen waarde is kleiner is dan (1/(1+0.1) of 91% van de best presterende uitvoeren wordt beëindigd.

### <a name="median-stopping-policy"></a>Beleid van de mediaan moet worden gestopt

[Mediaan stoppen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) is een beleid voor vroegtijdige beëindiging op basis van het actieve gemiddelde van primaire metrische gegevens die door de uitvoeringen worden gerapporteerd. Dit beleid uitgevoerd gemiddelden berekent voor alle trainingsuitvoeringen en wordt uitgevoerd waarvan de prestaties slechter dan de mediaan van de actieve gemiddelden is beëindigd. Dit beleid voert de volgende configuratieparameters:
* `evaluation_interval`: de frequentie voor het toepassen van het beleid (optionele parameter).
* `delay_evaluation`: de eerste evaluatie van het beleid voor een opgegeven aantal intervallen (optionele parameter) uitstelt.


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In dit voorbeeld wordt het beleid voor vroegtijdige beëindiging toegepast op elke interval dat begint bij de evaluatie-interval 5. Een uitvoering worden op basis van interval 5 beëindigd als de beste primaire metriek slechter dan de mediaan van de actieve gemiddelden boven intervallen 1:5 tussen alle trainingsuitvoeringen.

### <a name="truncation-selection-policy"></a>Beleid voor afkapping van selectie

Met de selectie van de [Afkap ping](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) wordt een bepaald percentage van de laagste uitvoeringen bij elk evaluatie-interval geannuleerd. Uitvoeringen worden vergeleken op basis van hun prestaties op de primaire metrische gegevens en de laagste X % worden beëindigd. Het duurt de volgende configuratieparameters:

* `truncation_percentage`: het percentage van de uitvoering van de laagste uitgevoerd om te beëindigen elke evaluatie-interval. Geef een geheel getal tussen 1 en 99.
* `evaluation_interval`: de frequentie voor het toepassen van het beleid (optionele parameter).
* `delay_evaluation`: de eerste evaluatie van het beleid voor een opgegeven aantal intervallen (optionele parameter) uitstelt.


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In dit voorbeeld wordt het beleid voor vroegtijdige beëindiging toegepast op elke interval dat begint bij de evaluatie-interval 5. Een run wordt beëindigd bij interval 5 als de prestaties bij interval 5 in de laagste 20% van de prestaties van alle uitvoeringen in interval 5 liggen.

### <a name="no-termination-policy"></a>Er is geen beleid beëindiging

Als u wilt dat alle trainingsuitvoeringen beleid ingesteld op None om uit te voeren om te voltooien. Dit is het effect van een vroegtijdige beëindiging-beleid niet wordt toegepast.

```Python
policy=None
```

### <a name="default-policy"></a>Standaardbeleid

Als er geen beleid is opgegeven, kan de afstemming-afstemmings service alle trainings uitvoeringen uitvoeren tot voltooiing.

>[!NOTE] 
>Als u een conservatieve beleid dat zorgt voor besparingen zonder afsluitende veelbelovende taken zoekt, kunt u een beleid mediaan stoppen met `evaluation_interval` 1 en `delay_evaluation` 5. Dit zijn conservatieve instellingen, die ongeveer 25% - 35% lager zonder verlies van primaire metrische gegevens (op basis van onze evaluatiegegevens) kunnen bieden.

## <a name="allocate-resources"></a>Resources toewijzen

Budget voor de resource voor uw experiment afstemmen door op te geven het maximum aantal trainingsuitvoeringen hyperparameter beheren.  (Optioneel) Geef de maximale duur voor uw hyperparameter experiment afstemmen.

* `max_total_runs`: Maximum aantal trainings uitvoeringen dat wordt gemaakt. Bovengrens - mogelijk zijn er minder wordt uitgevoerd, bijvoorbeeld, als de ruimte hyperparameter beperkt is en minder voorbeelden. Moet een getal tussen 1 en 1000.
* `max_duration_minutes`: Maximale duur in minuten van het experiment voor het afstemmen van afstemming. Parameter is optioneel en als deze aanwezig is, geen runs worden gestart dat zou worden uitgevoerd na deze periode automatisch worden geannuleerd.

>[!NOTE] 
>Als beide `max_total_runs` en `max_duration_minutes` zijn opgegeven, wordt de hyperparameter afstemmen experiment wordt beëindigd wanneer de eerste dag van deze twee drempelwaarden is bereikt.

Daarnaast Geef dat het maximum aantal training wordt uitgevoerd om uit te voeren gelijktijdig tijdens uw hyperparameter afstemmen zoeken.

* `max_concurrent_runs`: Het maximum aantal uitvoeringen dat gelijktijdig kan worden uitgevoerd. Als er niets opgegeven, alle `max_total_runs` parallel wordt gestart. Als u opgeeft, moet een getal tussen 1 en 100 liggen.

>[!NOTE] 
>Het aantal gelijktijdige uitvoeringen worden geregeld op de beschikbare resources in de opgegeven compute-doel. Daarom moet u ervoor zorgen dat de compute-doel de beschikbare resources voor de gewenste gelijktijdigheid is.

Resources voor het afstemmen van hyperparameter toewijzen:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Met deze code wordt het afstemming-afstemmings experiment geconfigureerd voor gebruik van Maxi maal 20 volledige uitvoeringen, waarbij vier configuraties tegelijk worden uitgevoerd.

## <a name="configure-experiment"></a>Configureren van experiment

[Configureer uw afstemming](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) -afstemmings experiment met behulp van de gedefinieerde afstemming Zoek ruimte, het beleid voor vroegtijdige beëindiging, de primaire metriek en de resource toewijzing van de bovenstaande secties. Daarnaast bieden een `estimator` die wordt aangeroepen met de sample hyperparameters. De `estimator` beschrijft de trainingsscript die u uitvoert, de resources per taak (één of meerdere gpu) en de compute-doel te gebruiken. Sinds de gelijktijdigheid van taken voor uw experiment afstemmen hyperparameter wordt geregeld op de resources die beschikbaar is, zorg ervoor dat de compute-doel opgegeven de `estimator` voldoende bronnen voor uw gewenste gelijktijdigheid heeft. (Zie voor meer informatie over loopt [het trainen van modellen](how-to-train-ml-models.md).)

Uw experiment afstemmen hyperparameter configureren:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Experiment verzenden

Wanneer u de afstemming-afstemmings configuratie hebt gedefinieerd, moet u [een experiment verzenden](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` de naam die u toewijst aan uw hyperparameter experiment, afstemmen en `workspace` is de werkruimte waarin u wilt maken van het experiment (Zie voor meer informatie over experimenten [hoe werkt Azure Machine Learning-service?](concept-azure-machine-learning-architecture.md))

## <a name="visualize-experiment"></a>Experiment visualiseren

De Azure Machine Learning SDK bevat een [notebook-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) waarmee de voortgang van uw trainings uitvoeringen wordt gevisualiseerd. Het volgende codefragment worden gevisualiseerd met alle uw hyperparameter wordt uitgevoerd op één locatie in een Jupyter-notebook afstemmen:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Deze code wordt een tabel met details over de trainingsuitvoeringen weergegeven voor elk van de hyperparameter-configuraties.

![hyperparameter afstemmen tabel](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

U kunt ook de prestaties van elk van de wordt uitgevoerd als de voortgang van de training visualiseren. 

![hyperparameter afstemmen tekengebied](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Bovendien kunt u de correlatie tussen prestaties en de waarden van afzonderlijke hyperparameters met behulp van een parallelle coördineert tekenen visueel identificeren. 

[![parallelle coördinaten voor afstemming afstemmen](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

U kunt alle uw hyperparameter afstemmen wordt uitgevoerd in de Azure-web-portal kunt visualiseren. Zie voor meer informatie over het weergeven van een experiment in de webportal [bijhouden experimenten](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Het beste model zoeken

Wanneer alle afstemmings uitvoeringen van de afstemming zijn voltooid, identificeert u [de best presterende configuratie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true-) en de bijbehorende afstemming-waarden:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Voorbeeld-notebook
Raadpleeg Train-afstemming-*-notebooks in deze map:
* [How-to-use-azureml/training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen
* [Bijhouden van een experiment](how-to-track-experiments.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
