---
title: Hyperparameters afstemmen voor uw model
titleSuffix: Azure Machine Learning
description: Stem hyperparameters efficiënt af voor uw deep learning/ machine learning-model met Azure Machine Learning. U leert hoe u de zoekruimte van de parameter definieert, een primaire statistiek opgeeft om te optimaliseren en slecht presterende uitvoeringen vroegtijdig beëindigen.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: seodec18
ms.openlocfilehash: 74fa6949716119d85eac5b142ac9e3c651a0a5d0
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398265"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Hyperparameters voor uw model afstemmen met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Stem hyperparameters voor uw model efficiënt af met Azure Machine Learning.  Hyperparameter tuning bevat de volgende stappen:

* De zoekruimte voor parameterdefiniëren
* Een primaire statistiek opgeven om te optimaliseren  
* Criteria voor vroegtijdige beëindiging opgeven voor slecht presterende uitvoeringen
* Resources toewijzen voor hyperparametertuning
* Een experiment starten met de bovenstaande configuratie
* Visualiseer de trainingsruns
* Selecteer de best presterende configuratie voor uw model

## <a name="what-are-hyperparameters"></a>Wat zijn hyperparameters?

Hyperparameters zijn aanpasbare parameters die u kiest om een model te trainen dat het trainingsproces zelf regelt. Als u bijvoorbeeld een diep neuraal netwerk wilt trainen, bepaalt u het aantal verborgen lagen in het netwerk en het aantal knooppunten in elke laag voordat u het model traint. Deze waarden blijven meestal constant tijdens het trainingsproces.

In deep learning/ machine learning-scenario's zijn de modelprestaties sterk afhankelijk van de geselecteerde hyperparameterwaarden. Het doel van hyperparameter exploratie is om te zoeken in verschillende hyperparameter configuraties om een configuratie die resulteert in de beste prestaties te vinden. Typisch, de hyperparameter exploratie proces is nauwgezet handmatig, gezien het feit dat de zoekruimte is enorm en evaluatie van elke configuratie kan duur zijn.

Azure Machine Learning stelt u in staat om hyperparameter-exploratie op een efficiënte manier te automatiseren, waardoor u veel tijd en middelen bespaart. U geeft het bereik van hyperparameterwaarden en een maximum aantal trainingsruns op. Het systeem start vervolgens automatisch meerdere gelijktijdige uitvoeringen met verschillende parameterconfiguraties en vindt de configuratie die resulteert in de beste prestaties, gemeten aan de basis van de statistiek die u kiest. Slecht presterende trainingsruns worden automatisch vroegtijdig beëindigd, waardoor verspilling van rekenresources wordt verminderd. Deze bronnen worden in plaats daarvan gebruikt om andere hyperparameterconfiguraties te verkennen.


## <a name="define-search-space"></a>Zoekruimte definiëren

Stem hyperparameters automatisch af door het bereik van waarden te verkennen dat voor elke hyperparameter is gedefinieerd.

### <a name="types-of-hyperparameters"></a>Typen hyperparameters

Elke hyperparameter kan discreet of continu zijn en heeft een verdeling van waarden die worden beschreven door een [parameterexpressie.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py)

#### <a name="discrete-hyperparameters"></a>Discrete hyperparameters 

Discrete hyperparameters worden `choice` opgegeven als een tussen afzonderlijke waarden. `choice`kan zijn:

* een of meer door komma's gescheiden waarden
* een `range` object
* willekeurig `list` object


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In dit `batch_size` geval neemt een van de waarden [16, 32, `number_of_hidden_layers` 64, 128] en neemt een van de waarden [1, 2, 3, 4].

Geavanceerde discrete hyperparameters kunnen ook worden opgegeven met behulp van een distributie. De volgende distributies worden ondersteund:

* `quniform(low, high, q)`- Geeft als resultaat een waarde als rond(uniform(laag, hoog) / q) * q
* `qloguniform(low, high, q)`- Geeft als resultaat een waarde als rond(exp(uniform(laag, hoog)) / q) * q
* `qnormal(mu, sigma, q)`- Geeft als resultaat een waarde als rond (normaal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)`- Geeft als resultaat een waarde als rond(exp(normaal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Continue hyperparameters 

Continue hyperparameters worden opgegeven als een verdeling over een continu bereik van waarden. Ondersteunde distributies zijn onder andere:

* `uniform(low, high)`- Geeft als resultaat een waarde die gelijkmatig wordt verdeeld over laag en hoog
* `loguniform(low, high)`- Geeft als resultaat een waarde die is getrokken op basis van exp(uniform(laag, hoog)) zodat de logaritme van de retourwaarde gelijkmatig wordt verdeeld
* `normal(mu, sigma)`- Geeft als resultaat een reële waarde die normaal gesproken wordt verdeeld met gemiddelde mu en standaarddeviatie sigma
* `lognormal(mu, sigma)`- Geeft als resultaat een waarde die is getrokken volgens exp(normal(mu, sigma)) zodat de logaritme van de retourwaarde normaal wordt verdeeld

Een voorbeeld van een definitie van parameterruimte:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Deze code definieert een zoekruimte met twee parameters - `learning_rate` en `keep_probability`. `learning_rate`heeft een normale verdeling met gemiddelde waarde 10 en een standaarddeviatie van 3. `keep_probability`heeft een uniforme verdeling met een minimumwaarde van 0,05 en een maximale waarde van 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Bemonstering van de hyperparameterruimte

U ook de parameterbemonsteringsmethode opgeven die u wilt gebruiken via de definitie van hyperparameterruimte. Azure Machine Learning ondersteunt steekproeven, rasterbemonstering en Bayesiaanse steekproeven.

#### <a name="picking-a-sampling-method"></a>Een bemonsteringsmethode kiezen

* Rasterbemonstering kan worden gebruikt als uw hyperparameterruimte kan worden gedefinieerd als een keuze tussen afzonderlijke waarden en als u voldoende budget hebt om alle waarden in de gedefinieerde zoekruimte volledig te doorzoeken. Bovendien kan men gebruik maken van geautomatiseerde vroegtijdige beëindiging van slecht presterende runs, waardoor verspilling van resources wordt verminderd.
* Willekeurige bemonstering maakt het mogelijk de hyperparameter ruimte om zowel discrete als continue hyperparameters op te nemen. In de praktijk levert het meestal goede resultaten op en maakt het ook het gebruik van geautomatiseerde vroegtijdige beëindiging van slecht presterende runs mogelijk. Sommige gebruikers voeren een eerste zoekopdracht uit met behulp van steekproeven en verfijnen vervolgens iteratief de zoekruimte om de resultaten te verbeteren.
* Bayesiaanse bemonstering maakt gebruik van kennis van eerdere monsters bij het kiezen van hyperparameterwaarden, waarbij effectief wordt geprobeerd de gerapporteerde primaire statistiek te verbeteren. Bayesiaanse bemonstering wordt aanbevolen wanneer u voldoende budget hebt om de hyperparameterruimte te verkennen - voor de beste resultaten met Bayesiaanse sampling raden we u aan een maximum aantal runs te gebruiken dat groter is dan of gelijk is aan 20 keer het aantal hyperparameters dat wordt afgestemd. Houd er rekening mee dat Bayesiaanse steekproeven momenteel geen beleid voor vroegtijdige beëindiging ondersteunen.

#### <a name="random-sampling"></a>Steekproef

In willekeurige steekproeven worden hyperparameterwaarden willekeurig geselecteerd uit de gedefinieerde zoekruimte. [Willekeurige bemonstering](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) maakt het mogelijk de zoekruimte om zowel discrete als continue hyperparameters op te nemen.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Bemonstering van het raster

[Grid sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) voert een eenvoudige grid search uit over alle haalbare waarden in de gedefinieerde zoekruimte. Het kan alleen worden gebruikt `choice`met hyperparameters opgegeven met behulp van . De volgende spatie heeft bijvoorbeeld in totaal zes voorbeelden:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesiaanse steekproeven

[Bayesiaanse sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) is gebaseerd op het Bayesiaanse optimalisatiealgoritme en maakt intelligente keuzes over de hyperparameterwaarden om vervolgens te samplen. Het kiest het monster op basis van hoe de vorige monsters hebben gepresteerd, zodat het nieuwe monster de gerapporteerde primaire statistiek verbetert.

Wanneer u Bayesiaanse steekproeven gebruikt, heeft het aantal gelijktijdige uitvoeringen een impact op de effectiviteit van het stemproces. Doorgaans kan een kleiner aantal gelijktijdige runs leiden tot een betere convergentie van steekproeven, omdat de kleinere mate van parallellisme het aantal runs verhoogt dat profiteert van eerder voltooide runs.

Bayesiaanse sampling `choice`ondersteunt `uniform`alleen `quniform` , en distributies over de zoekruimte.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesiaanse steekproeven ondersteunen geen beleid voor vroegtijdige beëindiging (Zie [Een beleid voor vroegtijdige beëindiging opgeven).](#specify-early-termination-policy) Stel de parameter in of `early_termination_policy = None`laat de `early_termination_policy` parameter bij het gebruik van Bayesiaanse parametersampling in.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Primaire statistiek opgeven

Geef de [primaire statistiek](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) op die het hyperparametertuning-experiment moet optimaliseren. Elke trainingsrun wordt geëvalueerd op basis van de primaire statistiek. Slecht presterende uitvoeringen (waarbij de primaire statistiek niet voldoet aan de criteria die zijn vastgesteld door het beleid voor vroegtijdige beëindiging) worden beëindigd. Naast de primaire metrische naam geeft u ook het doel van de optimalisatie op - of u de primaire statistiek wilt maximaliseren of minimaliseren.

* `primary_metric_name`: De naam van de primaire statistiek die moet worden geoptimaliseerd. De naam van de primaire statistiek moet exact overeenkomen met de naam van de statistiek die door het trainingsscript is vastgelegd. Zie [Logboekstatistieken voor hyperparametertuning](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Het kan `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` zijn of en bepaalt of de primaire statistiek zal worden gemaximaliseerd of geminimaliseerd bij de evaluatie van de runs. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimaliseer de runs om de "nauwkeurigheid" te maximaliseren.  Zorg ervoor dat u deze waarde in uw trainingsscript registreert.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Logboekstatistieken voor hyperparametertuning

Het trainingsscript voor uw model moet de relevante statistieken registreren tijdens de modeltraining. Wanneer u de hyperparametertuning configureert, geeft u de primaire statistiek op die moet worden gebruikt voor het evalueren van de uitvoeringsprestaties. (Zie [Een primaire statistiek opgeven om te optimaliseren](#specify-primary-metric-to-optimize).)  In uw trainingsscript moet u deze statistiek registreren, zodat deze beschikbaar is voor het hyperparametertuningproces.

Log deze statistiek in in uw trainingsscript met het volgende voorbeeld:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Het trainingsscript berekent de `val_accuracy` en registreert het als "nauwkeurigheid", die wordt gebruikt als de primaire statistiek. Elke keer dat de statistiek wordt geregistreerd, wordt deze ontvangen door de hyperparameter-tuningservice. Het is aan de modelontwikkelaar om te bepalen hoe vaak deze statistiek moet worden gemeld.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Beleid voor vroegtijdige beëindiging opgeven

Slecht presterende uitvoeringen automatisch beëindigen met een beleid voor vroegtijdige beëindiging. Beëindiging vermindert verspilling van resources en gebruikt deze resources in plaats daarvan voor het verkennen van andere parameterconfiguraties.

Wanneer u een beleid voor vroegtijdige beëindiging gebruikt, u de volgende parameters configureren die bepalen wanneer een beleid wordt toegepast:

* `evaluation_interval`: de frequentie voor de toepassing van het beleid. Telkens wanneer het trainingsscript de primaire metrische gegevens registreert als één interval. Zo `evaluation_interval` past een van 1 het beleid toe telkens wanneer het trainingsscript de primaire statistiek rapporteert. Een `evaluation_interval` van 2 past het beleid elke andere keer toe wanneer het trainingsscript de primaire statistiek rapporteert. Als dit `evaluation_interval` niet is opgegeven, wordt dit standaard ingesteld op 1.
* `delay_evaluation`: vertraagt de eerste beleidsevaluatie voor een bepaald aantal intervallen. Het is een optionele parameter waarmee alle configuraties kunnen worden uitgevoerd voor een eerste minimum aantal intervallen, het vermijden van voortijdige beëindiging van de training loopt. Indien opgegeven, past het beleid elk veelvoud van evaluation_interval toe dat groter is dan of gelijk is aan delay_evaluation.

Azure Machine Learning ondersteunt het volgende beleid voor vroegtijdige beëindiging.

### <a name="bandit-policy"></a>Bandietbeleid

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) is een beëindigingsbeleid op basis van slack factor/slack bedrag en evaluatie interval. Het beleid beëindigt alle runs waarbij de primaire statistiek niet binnen de opgegeven slackfactor / spelingshoeveelheid valt met betrekking tot de best presterende trainingsrun. Er zijn de volgende configuratieparameters nodig:

* `slack_factor`of `slack_amount`: de speling toegestaan met betrekking tot de best presterende training run. `slack_factor`geeft de toegestane speling als een verhouding. `slack_amount`geeft de toegestane speling op als een absoluut bedrag, in plaats van een verhouding.

    Overweeg bijvoorbeeld een Bandietbeleid dat wordt toegepast bij interval 10. Stel dat de best presterende run op interval 10 een primaire statistiek 0,8 heeft gerapporteerd met als doel de primaire statistiek te maximaliseren. Als het beleid is `slack_factor` opgegeven met een van 0,2, worden alle trainingsruns uitgevoerd, waarvan de beste statistiek`slack_factor`bij interval 10 minder dan 0,66 (0,8/(1+ )) is, worden beëindigd. Als in plaats daarvan het `slack_amount` beleid is opgegeven met een van 0,2, worden alle trainingsruns, waarvan `slack_amount`de beste statistiek bij interval 10 minder dan 0,6 (0,8 - ) is, beëindigd.
* `evaluation_interval`: de frequentie voor de toepassing van het beleid (facultatieve parameter).
* `delay_evaluation`: vertraagt de eerste beleidsevaluatie voor een bepaald aantal intervallen (optionele parameter).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In dit voorbeeld wordt het beleid voor vroegtijdige beëindiging toegepast bij elk interval wanneer statistieken worden gerapporteerd, te beginnen bij evaluatieinterval 5. Elke run waarvan de beste statistiek kleiner is dan (1/(1+0,1) of 91% van de best presterende run, wordt beëindigd.

### <a name="median-stopping-policy"></a>Mediaan stopbeleid

[Mediane stoppen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) is een beleid voor vroegtijdige beëindiging op basis van lopende gemiddelden van primaire statistieken gerapporteerd door de runs. Dit beleid berekent lopende gemiddelden voor alle trainingsuitvoeringen en eindigt uitvoeringen waarvan de prestaties slechter zijn dan de mediaan van de lopende gemiddelden. In dit beleid worden de volgende configuratieparameters weergegeven:
* `evaluation_interval`: de frequentie voor de toepassing van het beleid (facultatieve parameter).
* `delay_evaluation`: vertraagt de eerste beleidsevaluatie voor een bepaald aantal intervallen (optionele parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In dit voorbeeld wordt het beleid voor vroegtijdige beëindiging toegepast bij elk interval vanaf evaluatieinterval 5. Een run wordt beëindigd met interval 5 als de beste primaire statistiek slechter is dan de mediaan van de lopende gemiddelden over intervallen 1:5 voor alle trainingsruns.

### <a name="truncation-selection-policy"></a>Selectiebeleid voor afkappen

[Afgekapte selectie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) annuleert een bepaald percentage van de laagst presterende uitvoeringen bij elk evaluatieinterval. Runs worden vergeleken op basis van hun prestaties op de primaire statistiek en de laagste X% worden beëindigd. Er zijn de volgende configuratieparameters nodig:

* `truncation_percentage`: het percentage van de laagst presterende uitvoeringen dat bij elk evaluatieinterval moet worden beëindigd. Geef een gehele waarde op tussen 1 en 99.
* `evaluation_interval`: de frequentie voor de toepassing van het beleid (facultatieve parameter).
* `delay_evaluation`: vertraagt de eerste beleidsevaluatie voor een bepaald aantal intervallen (optionele parameter).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In dit voorbeeld wordt het beleid voor vroegtijdige beëindiging toegepast bij elk interval vanaf evaluatieinterval 5. Een run wordt beëindigd met interval 5 als de prestaties bij interval 5 in de laagste 20% van de prestaties van alle runs bij interval 5 zijn.

### <a name="no-termination-policy"></a>Geen beëindigingsbeleid

Als u wilt dat alle trainingsruns worden voltooid, stelt u het beleid in op Geen. Dit zal tot gevolg hebben dat er geen beleid voor vroegtijdige beëindiging wordt toegepast.

```Python
policy=None
```

### <a name="default-policy"></a>Standaardbeleid

Als er geen beleid is opgegeven, laat de hyperparametertuningservice alle trainingsuitvoeringen uitvoeren tot voltooiing.

### <a name="picking-an-early-termination-policy"></a>Kiezen voor een beleid voor vroegtijdige beëindiging

* Als u op zoek bent naar een conservatief beleid dat besparingen biedt zonder `evaluation_interval` veelbelovende `delay_evaluation` banen te beëindigen, u een Mediaan Stop beleid gebruiken met 1 en 5. Dit zijn conservatieve instellingen, die ongeveer 25%-35% besparingen kunnen opleveren zonder verlies op primaire metrische gegevens (op basis van onze evaluatiegegevens).
* Als u op zoek bent naar agressievere besparingen door vroegtijdige beëindiging, u banditbeleid gebruiken met een strengere (kleinere) toegestane speling of truncationselectiebeleid met een groter afgekapt percentage.

## <a name="allocate-resources"></a>Resources toewijzen

Beheer uw resourcebudget voor uw hyperparametertuning-experiment door het maximale totale aantal trainingsruns op te geven.  Geef eventueel de maximale duur op voor uw hyperparametertuning-experiment.

* `max_total_runs`: Maximaal totaal aantal trainingsruns dat wordt gemaakt. Bovengrens - er kunnen bijvoorbeeld minder runs zijn als de hyperparameterruimte eindig is en minder monsters heeft. Moet een getal tussen 1 en 1000 zijn.
* `max_duration_minutes`: Maximale duur in minuten van het hyperparameter tuning experiment. Parameter is optioneel en als deze aanwezig is, worden alle uitvoeringen die na deze duur worden uitgevoerd, automatisch geannuleerd.

>[!NOTE] 
>Als `max_total_runs` beide `max_duration_minutes` en zijn opgegeven, eindigt het hyperparametertuning-experiment wanneer de eerste van deze twee drempelwaarden wordt bereikt.

Geef bovendien het maximum aantal trainingsruns op dat gelijktijdig moet worden uitgevoerd tijdens het afstemmen van hyperparameter.

* `max_concurrent_runs`: Maximaal aantal runs dat gelijktijdig op een bepaald moment moet worden uitgevoerd. Als dit niet `max_total_runs` is opgegeven, wordt alles parallel gestart. Indien gespecificeerd, moet een getal tussen 1 en 100.

>[!NOTE] 
>Het aantal gelijktijdige uitvoeringen wordt geboekt op de resources die beschikbaar zijn in het opgegeven rekendoel. Daarom moet u ervoor zorgen dat het rekendoel de beschikbare resources voor de gewenste gelijktijdigheid heeft.

Resources toewijzen voor hyperparametertuning:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Deze code configureert het hyperparameter tuning-experiment om maximaal 20 runs te gebruiken, waarbij vier configuraties tegelijk worden uitgevoerd.

## <a name="configure-experiment"></a>Experiment configureren

[Configureer uw hyperparametertuning-experiment](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) met behulp van de gedefinieerde hyperparameterzoekruimte, beleid voor vroegtijdige beëindiging, primaire statistiek en toewijzing van resources uit de bovenstaande secties. Bovendien, bieden `estimator` een die zal worden aangeroepen met de bemonsterde hyperparameters. Het `estimator` beschrijft het trainingsscript dat u uitvoert, de resources per taak (single of multi-gpu) en het te gebruiken rekendoel. Aangezien gelijktijdigheid voor uw hyperparametertuning-experiment is gated op de beschikbare `estimator` resources, moet u ervoor zorgen dat het rekendoel dat is opgegeven in de voldoende resources voor uw gewenste gelijktijdigheid heeft. (Voor meer informatie over schatters, zie [hoe modellen te trainen](how-to-train-ml-models.md).)

Configureer uw hyperparameter tuning experiment:

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

Zodra u uw hyperparametertuningconfiguratie [definieert, dient u een experiment in:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`is de naam die u toewijst `workspace` aan uw hyperparametertuning-experiment en is de werkruimte waarin u het experiment wilt maken (Zie Hoe werkt Azure Machine Learning voor meer informatie over [experimenten?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Warme start uw hyperparameter tuning experiment (optioneel)

Vaak kan het vinden van de beste hyperparameterwaarden voor uw model een iteratief proces zijn, waarbij meerdere tuningruns nodig zijn die leren van eerdere hyperparametertuningruns. Hergebruik van kennis van deze vorige runs zal het hyperparameter tuning proces versnellen, waardoor de kosten van het afstemmen van het model worden verlaagd en de primaire statistiek van het resulterende model mogelijk zal verbeteren. Bij warm starten van een hyperparameter tuning experiment met Bayesiaanse bemonstering, proeven van de vorige run zal worden gebruikt als voorkennis om intelligent te kiezen nieuwe monsters, om de primaire metrische te verbeteren. Bovendien, bij het gebruik van Random of Grid sampling, zullen alle beslissingen over vroegtijdige beëindiging gebruik maken van statistieken van de vorige runs om slecht presterende trainingsruns te bepalen. 

Azure Machine Learning stelt u in staat om uw hyperparametertuning te verwarmen door gebruik te maken van kennis van maximaal 5 eerder voltooide / geannuleerde hyperparametertuning-bovenliggende uitvoeringen. U de lijst met bovenliggende uitvoeringen opgeven die u wilt opwarmen vanaf dit fragment:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Bovendien kunnen er gevallen zijn wanneer individuele trainingsruns van een hyperparametertuning-experiment worden geannuleerd vanwege budgetbeperkingen of mislukken vanwege andere redenen. Het is nu mogelijk om dergelijke individuele trainingsruns vanaf het laatste controlepunt te hervatten (ervan uitgaande dat je trainingsscript controlepunten afhandelt). Als u een individuele trainingsrun hervat, wordt dezelfde hyperparameterconfiguratie gebruikt en wordt de uitvoermap die voor die uitvoering wordt gebruikt, gemonteerd. Het trainingsscript moet `resume-from` het argument accepteren, dat het controlepunt of modelbestanden bevat van waaruit de trainingsrun moet worden hervat. U de afzonderlijke trainingsruns hervatten via het volgende fragment:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

U uw hyperparametertuning-experiment zo configureren dat een eerder experiment `resume_from` wordt `resume_child_runs` gestart of dat u afzonderlijke trainingsuitvoeringen hervat met behulp van de optionele parameters en in de config:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Experiment visualiseren

De Azure Machine Learning SDK biedt een [notebookwidget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) die de voortgang van uw trainingsuitvoeringen visualiseert. Het volgende fragment visualiseert al uw hyperparameter tuning draait op een plaats in een Jupyter notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Deze code geeft een tabel weer met details over de trainingsuitvoeringen voor elk van de hyperparameterconfiguraties.

![hyperparameter stemtabel](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

U ook de prestaties van elk van de runs visualiseren naarmate de training vordert. 

![hyperparameter tuning plot](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Bovendien u de correlatie tussen prestaties en waarden van afzonderlijke hyperparameters visueel identificeren met behulp van een parallel coördinatenplot. 

[![hyperparameter tuning parallelle coördinaten](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

U al uw hyperparametertuning-uitvoeringen ook visualiseren in de Azure-webportal. Voor meer informatie over het bekijken van een experiment in het webportaal, raadpleegt u [hoe u experimenten bijhouden.](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)

## <a name="find-the-best-model"></a>Vind het beste model

Zodra alle hyperparametertuningruns zijn voltooid, [identificeert u de best presterende configuratie](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) en de bijbehorende hyperparameterwaarden:

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

## <a name="sample-notebook"></a>Voorbeeldnotitieblok
Raadpleeg de notebooks van train-hyperparameter-* in deze map:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen
* [Een experiment bijhouden](how-to-track-experiments.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
