---
title: Hyper parameters voor uw model afstemmen
titleSuffix: Azure Machine Learning
description: Hyper parameters efficiënt afstemmen op uw diepe leer-en machine learning model met behulp van Azure Machine Learning. U leert hoe u de zoek ruimte voor de para meters definieert, een primaire metriek opgeeft die u wilt optimaliseren, en vervroegde uitvoeringen worden uitgevoerd.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: a7b0276ca41e1b9342b3602a67dea0517c60f66a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489343"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Hyper parameters voor uw model afstemmen met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Hyper parameters efficiënt afstemmen voor uw model met behulp van Azure Machine Learning.  Afstemming tuning omvat de volgende stappen:

* De zoek ruimte voor de para meter definiëren
* Geef een primaire metriek op om te optimaliseren  
* Criteria voor vroegtijdige beëindiging opgeven voor slecht uitgevoerde uitvoeringen
* Resources toewijzen voor afstemming-afstemming
* Een experiment met de bovenstaande configuratie starten
* De trainings uitvoeringen visualiseren
* Selecteer de best presterende configuratie voor uw model

## <a name="what-are-hyperparameters"></a>Wat zijn Hyper parameters?

Hyper parameters zijn aanpas bare para meters die u hebt gekozen voor het trainen van een model dat het trainings proces zelf bepaalt. Als u bijvoorbeeld een diep Neural-netwerk wilt trainen, bepaalt u het aantal verborgen lagen in het netwerk en het aantal knoop punten in elke laag voordat het model wordt getraind. Deze waarden blijven doorgaans constant tijdens het trainings proces.

In diepe leer-en machine learning scenario's is model prestaties sterk afhankelijk van de geselecteerde afstemming-waarden. Het doel van afstemming verkennen is om in verschillende afstemming-configuraties te zoeken naar een configuratie die de beste prestaties levert. Normaal gesp roken is het afstemming-onderzoek proces erg hand matig, gezien de duur van de zoek ruimte en de evaluatie van elke configuratie kan kostbaar zijn.

Met Azure Machine Learning kunt u afstemming-exploratie op een efficiënte manier automatiseren, waardoor u veel tijd en bronnen bespaart. U geeft het bereik van afstemming-waarden en een maximum aantal trainings runs op. Het systeem start vervolgens automatisch meerdere gelijktijdige uitvoeringen met verschillende parameter configuraties en zoekt de configuratie die de beste prestaties levert, gemeten op basis van de metrische gegevens die u kiest. Slecht uitgevoerde trainings uitvoeringen worden automatisch afgesloten, waardoor er minder verspilling van reken bronnen kan worden uitgevoerd. Deze resources worden in plaats daarvan gebruikt om andere afstemming-configuraties te verkennen.


## <a name="define-search-space"></a>Zoek ruimte definiëren

Hyper parameters automatisch afstemmen door het bereik van waarden te verkennen dat voor elke afstemming is gedefinieerd.

### <a name="types-of-hyperparameters"></a>Typen Hyper parameters

Elke afstemming kan afzonderlijk of doorlopend zijn en heeft een distributie van waarden die worden beschreven door een [parameter expressie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Discrete Hyper parameters 

Discrete Hyper parameters zijn opgegeven als een `choice` onder discrete waarden. `choice` kan zijn:

* een of meer door komma's gescheiden waarden
* een `range`-object
* wille keurig `list` object


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In dit geval heeft `batch_size` een van de waarden [16, 32, 64, 128] en `number_of_hidden_layers` een van de waarden [1, 2, 3, 4] in beslag neemt.

Geavanceerde discrete Hyper parameters kunnen ook worden opgegeven met behulp van een distributie. De volgende distributies worden ondersteund:

* `quniform(low, high, q)`: retourneert een waarde zoals Round (Uniform (laag, hoog)/q) * q
* `qloguniform(low, high, q)`: retourneert een waarde zoals Round (exp (Uniform (laag, hoog)/q) * q
* `qnormal(mu, sigma, q)`: retourneert een waarde zoals Round (normaal (mu, Sigma)/q) * q
* `qlognormal(mu, sigma, q)`: retourneert een waarde zoals Round (exp (normaal (mu, Sigma)/q) * q

#### <a name="continuous-hyperparameters"></a>Doorlopend Hyper parameters 

Doorlopende Hyper parameters worden opgegeven als een distributie over een doorlopend bereik van waarden. Ondersteunde distributies zijn:

* `uniform(low, high)`: retourneert een waarde die gelijkmatig wordt verdeeld tussen laag en hoog
* `loguniform(low, high)`: retourneert een waarde die is getekend op basis van exp (Uniform (Low, High)), zodat de logaritme van de geretourneerde waarde gelijkmatig wordt gedistribueerd
* `normal(mu, sigma)`: retourneert een echte waarde die normaal gesp roken wordt gedistribueerd met het gemiddelde MU en de standaard deviatie Sigma
* `lognormal(mu, sigma)`: retourneert een waarde die is getekend op basis van exp (normaal (mu, Sigma)), zodat de logaritme van de geretourneerde waarde normaal gesp roken wordt gedistribueerd

Een voor beeld van een definitie van een parameter ruimte:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Met deze code wordt een zoek ruimte gedefinieerd met twee para meters: `learning_rate` en `keep_probability`. `learning_rate` heeft een normale verdeling met gemiddelde waarde 10 en een standaard afwijking van 3. `keep_probability` heeft een uniforme distributie met een minimum waarde van 0,05 en een maximum waarde van 0,1.

### <a name="sampling-the-hyperparameter-space"></a>De afstemming ruimte bemonsteren

U kunt ook de bemonsterings methode voor para meters opgeven die u wilt gebruiken voor de definitie van de afstemming-ruimte. Azure Machine Learning ondersteunt wille keurige steek proeven, Raster sampling en Bayesiaanse-steek proeven.

#### <a name="picking-a-sampling-method"></a>Een steekproef methode kiezen

* Raster sampling kan worden gebruikt als uw afstemming ruimte kan worden gedefinieerd als keuze tussen discrete waarden en als u voldoende budget hebt om uitgebreid te zoeken naar alle waarden in de gedefinieerde zoek ruimte. Daarnaast kan een automatische vroegtijdige beëindiging van slecht uitgevoerde uitvoeringen worden gebruikt, waardoor er minder verspilling van resources is.
* Met wille keurige steek proeven kan de afstemming ruimte zowel discrete als doorlopende Hyper parameters bevatten. In de praktijk levert de meeste tijd de meeste tijden op en biedt het gebruik van automatische vroegtijdige beëindiging van slechtere uitvoeringen. Sommige gebruikers voeren een eerste zoek opdracht uit met wille keurige steek proeven en verfijnen de zoek ruimte iteratief om de resultaten te verbeteren.
* Bayesiaanse-bemonstering maakt gebruik van kennis van voor gaande voor beelden bij het kiezen van afstemming-waarden, waardoor de gerapporteerde primaire metriek daad werkelijk wordt verbeterd. Bayesiaanse-steek proeven worden aanbevolen wanneer u voldoende budget hebt om de afstemming-ruimte te verkennen. voor de beste resultaten met Bayesiaanse-steek proeven wordt het aanbevolen een maximum aantal uitvoeringen te gebruiken dat groter is dan of gelijk is aan 20 maal het aantal Hyper parameters dat is afgestemd. Houd er rekening mee dat Bayesiaanse-steek proeven momenteel geen beleid voor vroegtijdige beëindiging ondersteunt.

#### <a name="random-sampling"></a>Wille keurige steek proef

In wille keurige steek proeven worden afstemming waarden wille keurig geselecteerd uit de gedefinieerde zoek ruimte. Met een [wille keurige steek proef](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) kan de zoek ruimte zowel discrete als doorlopende Hyper parameters bevatten.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Raster sampling

[Raster sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) voert een eenvoudige raster zoekactie uit op alle bruikbare waarden in de gedefinieerde zoek ruimte. Deze kan alleen worden gebruikt met Hyper parameters die is opgegeven met behulp van `choice`. De volgende ruimte bevat bijvoorbeeld in totaal zes voor beelden:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesiaanse-steek proeven

[Bayesiaanse-steek proeven](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) zijn gebaseerd op het Bayesiaanse-optimalisatie algoritme en maken intelligente keuzes voor de afstemming-waarden voor het volgende voor beeld. Hiermee wordt het voor beeld gekozen op basis van de manier waarop de vorige voor beelden zijn uitgevoerd, zodat de gerapporteerde primaire metriek door het nieuwe voor beeld wordt verbeterd.

Wanneer u Bayesiaanse-steek proeven gebruikt, heeft het aantal gelijktijdige uitvoeringen gevolgen voor de effectiviteit van het afstemmings proces. Normaal gesp roken kan een kleiner aantal gelijktijdige uitvoeringen leiden tot een betere steek proef van de convergentie, omdat de kleinere mate van parallellisme het aantal uitvoeringen verhoogt dat van eerder voltooide uitvoeringen voor komt.

Bayesiaanse-steek proeven bieden alleen ondersteuning voor `choice`, `uniform`en `quniform` distributies over de zoek ruimte.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesiaanse-steek proeven bieden geen ondersteuning voor het beleid voor vroegtijdige beëindiging (Zie [een beleid voor vroegtijdige beëindiging opgeven](#specify-early-termination-policy)). Wanneer u Bayesiaanse parameter sampling gebruikt, stelt u `early_termination_policy = None`in of verlaat u de para meter `early_termination_policy`.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Primaire metriek opgeven

Geef de [primaire meet waarde](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) op voor het afstemming-afstemmings experiment dat u wilt optimaliseren. Elke trainings uitvoering wordt geëvalueerd voor de primaire metriek. Slecht uitgevoerde uitvoeringen (waarbij de primaire metriek niet voldoet aan de criteria die zijn ingesteld door het beleid voor vroegtijdige beëindiging) wordt beëindigd. Naast de primaire metrische naam moet u ook het doel van de optimalisatie opgeven, of u de primaire metriek wilt maximaliseren of minimaliseren.

* `primary_metric_name`: de naam van de primaire metriek die moet worden geoptimaliseerd. De naam van de primaire metriek moet exact overeenkomen met de naam van de metrische gegevens die door het trainings script worden vastgelegd. Zie [metrische logboek gegevens voor afstemming-afstemming](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: het kan `PrimaryMetricGoal.MAXIMIZE` of `PrimaryMetricGoal.MINIMIZE` zijn en bepaalt of de primaire metriek wordt gemaximaliseerd of geminimaliseerd wanneer de uitvoeringen worden geëvalueerd. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimaliseer de uitvoeringen om de nauw keurigheid te maximaliseren.  Zorg ervoor dat u deze waarde in uw trainings script aanmeldt.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Metrische logboek gegevens voor afstemming-afstemming

Het trainings script voor uw model moet de relevante metrische gegevens registreren tijdens de model training. Wanneer u de afstemming-afstemming configureert, geeft u de primaire meet waarde op die moet worden gebruikt voor het evalueren van de prestaties van de uitvoering. (Zie [een primaire metriek opgeven om te optimaliseren](#specify-primary-metric-to-optimize).)  In uw trainings script moet u deze metriek vastleggen zodat deze beschikbaar is voor het afstemmings proces van afstemming.

Registreer deze metrische gegevens in uw trainings script met het volgende voorbeeld fragment:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Het trainings script berekent de `val_accuracy` en registreert dit als ' nauwkeurigheid ', die wordt gebruikt als primaire metriek. Telkens wanneer de metriek wordt geregistreerd, wordt deze ontvangen door de afstemming tuning service. Het is de ontwikkelaar van het model om te bepalen hoe vaak deze metrische gegevens moeten worden gerapporteerd.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Beleid voor vroegtijdige beëindiging opgeven

Het afsluiten van de uitvoering van het programma wordt niet goed uitgevoerd met een beleid voor vroegtijdige beëindiging. Beëindiging vermindert de verspilling van resources en maakt in plaats daarvan gebruik van deze bronnen voor het verkennen van andere parameter configuraties.

Wanneer u een beleid voor vroegtijdige beëindiging gebruikt, kunt u de volgende para meters configureren die bepalen wanneer een beleid wordt toegepast:

* `evaluation_interval`: de frequentie waarmee het beleid wordt toegepast. Telkens wanneer het trainings script de primaire metriek registreert als één interval. Een `evaluation_interval` van 1 past daarom het beleid toe telkens wanneer het trainings script de primaire metriek rapporteert. Met een `evaluation_interval` van 2 wordt het beleid telkens toegepast wanneer het trainings script de primaire metriek rapporteert. Als u niets opgeeft, wordt `evaluation_interval` standaard ingesteld op 1.
* `delay_evaluation`: de eerste beleids evaluatie voor een opgegeven aantal intervallen wordt uitgesteld. Het is een optionele para meter waarmee alle configuraties kunnen worden uitgevoerd voor een eerste minimum aantal intervallen, waardoor het voor tijdig beëindigen van de trainings uitvoeringen wordt voor komen. Indien opgegeven, wordt het beleid toegepast op elk veelvoud van evaluation_interval dat groter is dan of gelijk is aan delay_evaluation.

Azure Machine Learning ondersteunt de volgende beleids regels voor vroegtijdige beëindiging.

### <a name="bandit-policy"></a>Bandit-beleid

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) is een afsluitings beleid op basis van een toegestane factor en een toegestane vertragings hoeveelheid en evaluatie-interval. Het beleid verbreekt alle uitvoeringen waarbij de primaire metriek zich niet binnen het opgegeven aantal toegestane vertragings factoren bevindt met betrekking tot de best presterende uitvoering van de training. Hierbij worden de volgende configuratie parameters gebruikt:

* `slack_factor` of `slack_amount`: de toegestane vertraging met betrekking tot de best presterende trainings uitvoering. `slack_factor` geeft u de toegestane vertraging als een ratio op. `slack_amount` geeft u de toegestane vertraging als een absoluut bedrag op in plaats van een ratio.

    Stel dat er een Bandit-beleid wordt toegepast tijdens interval 10. Stel dat de best presterende uitvoering bij interval 10 een primaire metriek 0,8 heeft gerapporteerd met een doel om de primaire metriek te maximaliseren. Als het beleid is opgegeven met een `slack_factor` van 0,2, worden alle trainingen uitgevoerd, waarvan de beste metrische waarde bij interval 10 kleiner is dan 0,66 (0,8/(1 +`slack_factor`)). Als u in plaats daarvan het beleid hebt opgegeven met een `slack_amount` van 0,2, worden alle trainingen uitgevoerd, waarvan de aanbevolen metrische waarde bij interval 10 kleiner is dan 0,6 (0,8-`slack_amount`).
* `evaluation_interval`: de frequentie voor het Toep assen van het beleid (optionele para meter).
* `delay_evaluation`: de eerste beleids evaluatie wordt uitgesteld voor een opgegeven aantal intervallen (optionele para meter).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In dit voor beeld wordt het beleid voor vroegtijdige beëindiging toegepast op elk interval wanneer metrische gegevens worden gerapporteerd, beginnend bij de evaluatie-interval 5. Een uitvoering waarvan de beste metriek kleiner is dan (1/(1 + 0,1) of 91% van de best presterende uitvoering wordt beëindigd.

### <a name="median-stopping-policy"></a>Beleid voor mediaan stoppen

[Mediaan stoppen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) is een beleid voor vroegtijdige beëindiging op basis van het actieve gemiddelde van primaire metrische gegevens die door de uitvoeringen worden gerapporteerd. Met deze beleids regel berekent u de gemiddelden van alle trainings runs en beëindigt u de uitvoeringen waarvan de prestaties slechter zijn dan de mediaan van de lopende gemiddelden. Voor dit beleid worden de volgende configuratie parameters gebruikt:
* `evaluation_interval`: de frequentie voor het Toep assen van het beleid (optionele para meter).
* `delay_evaluation`: de eerste beleids evaluatie wordt uitgesteld voor een opgegeven aantal intervallen (optionele para meter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In dit voor beeld wordt het beleid voor vroegtijdige beëindiging toegepast op elk interval, te beginnen bij de evaluatie-interval 5. Een run wordt beëindigd bij interval 5 als de beste primaire gegevens slechter zijn dan de mediaan van de lopende gemiddelden over intervallen 1:5 voor alle trainings uitvoeringen.

### <a name="truncation-selection-policy"></a>Selectie beleid voor afkap ping

Met de selectie van de [Afkap ping](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) wordt een bepaald percentage van de laagste uitvoeringen bij elk evaluatie-interval geannuleerd. Uitvoeringen worden vergeleken op basis van de prestaties van de primaire metriek en de laagste X% wordt beëindigd. Hierbij worden de volgende configuratie parameters gebruikt:

* `truncation_percentage`: het percentage van de laagste uitvoeringen dat bij elk evaluatie-interval wordt beëindigd. Geef een geheel getal op tussen 1 en 99.
* `evaluation_interval`: de frequentie voor het Toep assen van het beleid (optionele para meter).
* `delay_evaluation`: de eerste beleids evaluatie wordt uitgesteld voor een opgegeven aantal intervallen (optionele para meter).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In dit voor beeld wordt het beleid voor vroegtijdige beëindiging toegepast op elk interval, te beginnen bij de evaluatie-interval 5. Een run wordt beëindigd bij interval 5 als de prestaties bij interval 5 in de laagste 20% van de prestaties van alle uitvoeringen in interval 5 liggen.

### <a name="no-termination-policy"></a>Geen beleid voor beëindiging

Stel beleid in op geen als u wilt dat alle trainings uitvoeringen worden uitgevoerd om te worden voltooid. Dit heeft tot gevolg dat er geen beleid voor vroegtijdige beëindiging wordt toegepast.

```Python
policy=None
```

### <a name="default-policy"></a>Standaard beleid

Als er geen beleid is opgegeven, kan de afstemming-afstemmings service alle trainings uitvoeringen uitvoeren tot voltooiing.

### <a name="picking-an-early-termination-policy"></a>Een beleid voor vroegtijdige beëindiging kiezen

* Als u op zoek bent naar een conservatief beleid dat besparingen oplevert zonder dat toezeggings taken worden beëindigd, kunt u een beleid voor mediaan stoppen gebruiken met `evaluation_interval` 1 en `delay_evaluation` 5. Dit zijn voorzichtige instellingen, die ongeveer 25%-35% besparingen kunnen bieden zonder verlies van primaire metriek (op basis van de evaluatie gegevens).
* Als u op zoek bent naar meer agressieve besparingen van vroegtijdige beëindiging, kunt u Bandit-beleid gebruiken met een striktere (kleinere) toegestane vertraging of selectie beleid voor Afkap ping met een grotere Afbrekings percentage.

## <a name="allocate-resources"></a>Resources toewijzen

Beheer uw resource budget voor uw afstemming-afstemmings experiment door het maximum aantal trainings runs op te geven.  Geef desgewenst de maximale duur op voor uw afstemming-afstemmings experiment.

* `max_total_runs`: het maximale aantal trainings runs dat wordt gemaakt. Bovengrens: er kunnen minder uitvoeringen zijn, bijvoorbeeld als de afstemming ruimte eindig is en minder voor beelden heeft. Moet een getal tussen 1 en 1000 zijn.
* `max_duration_minutes`: de maximale duur in minuten van het experiment voor het afstemmen van afstemming. De para meter is optioneel, en indien aanwezig, alle uitvoeringen die worden uitgevoerd na deze duur, worden automatisch geannuleerd.

>[!NOTE] 
>Als zowel `max_total_runs` als `max_duration_minutes` zijn opgegeven, wordt het afstemming-afstemmings experiment beëindigd wanneer de eerste van deze twee drempel waarden wordt bereikt.

Daarnaast geeft u het maximum aantal trainings uitvoeringen op dat gelijktijdig moet worden uitgevoerd tijdens uw afstemming-afstemmings zoek opdracht.

* `max_concurrent_runs`: het maximum aantal uitvoeringen kan op een gegeven moment gelijktijdig worden uitgevoerd. Als niets is opgegeven, worden alle `max_total_runs` parallel gestart. Indien opgegeven, moet een getal tussen 1 en 100 zijn.

>[!NOTE] 
>Het aantal gelijktijdige uitvoeringen wordt gegatedd op de resources die beschikbaar zijn in het opgegeven Compute-doel. Daarom moet u ervoor zorgen dat het Compute-doel de beschik bare resources voor de gewenste gelijktijdigheid heeft.

Resources toewijzen voor afstemming tuning:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Met deze code wordt het afstemming-afstemmings experiment geconfigureerd voor gebruik van Maxi maal 20 volledige uitvoeringen, waarbij vier configuraties tegelijk worden uitgevoerd.

## <a name="configure-experiment"></a>Experiment configureren

[Configureer uw afstemming-afstemmings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) experiment met behulp van de gedefinieerde afstemming Zoek ruimte, het beleid voor vroegtijdige beëindiging, de primaire metriek en de resource toewijzing van de bovenstaande secties. Geef daarnaast een `estimator` op die wordt aangeroepen met de bemonsterde Hyper parameters. De `estimator` beschrijft het trainings script dat u uitvoert, de resources per taak (single of multi-GPU) en het reken doel dat moet worden gebruikt. Omdat gelijktijdigheid voor uw afstemming-afstemmings experiment wordt gegatedeerd op de beschik bare resources, zorgt u ervoor dat het berekenings doel dat is opgegeven in de `estimator` voldoende resources heeft voor de gewenste gelijktijdigheid. (Zie [modellen trainen](how-to-train-ml-models.md)voor meer informatie over schattingen.)

Uw afstemming-afstemmings experiment configureren:

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

`experiment_name` is de naam die u toewijst aan uw afstemming-afstemmings experiment en `workspace` is de werk ruimte waarin u het experiment wilt maken (Zie [Hoe werkt Azure machine learning?](concept-azure-machine-learning-architecture.md)) voor meer informatie over experimenten.

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Start uw afstemming tuning-experiment warme (optioneel)

Het zoeken naar de beste afstemming-waarden voor uw model kan vaak een iteratief proces zijn, maar er zijn meerdere afstemmings uitvoeringen nodig die van vorige afstemming-afstemmings uitvoeringen leren. Door de kennis van deze vorige uitvoeringen te hergebruiken, wordt het afstemming-tuning-proces versneld, waardoor de kosten voor het afstemmen van het model worden verminderd en de primaire metriek van het resulterende model mogelijk wordt verbeterd. Wanneer u een afstemming-afstemmings experiment met Bayesiaanse-steek proeven hebt gestart, worden tests van de vorige uitvoering gebruikt als eerdere kennis om op intelligente wijze nieuwe voor beelden te kiezen om de primaire metriek te verbeteren. Wanneer u een wille keurige of raster bemonstering gebruikt, worden er bovendien metrische gegevens uit de vorige uitvoeringen in rekening worden opgevolgd om te bepalen of er slecht uitgevoerde trainings uitvoeringen worden uitgevoerd. 

Met Azure Machine Learning kunt u uw afstemming-afstemmings proces warm starten door kennis te maken van Maxi maal 5 eerder voltooide/geannuleerde afstemming tuning-afstemming van bovenliggende uitvoeringen. U kunt de lijst met bovenliggende uitvoeringen opgeven die u wilt warmen om te beginnen met het gebruik van dit fragment:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Het is ook mogelijk dat er situaties zijn waarin het experiment voor het afstemmen van een afstemming wordt geannuleerd vanwege budget beperkingen of dat er een fout is opgetreden vanwege andere redenen. Het is nu mogelijk om dergelijke afzonderlijke trainingen uit het laatste controle punt uit te voeren (ervan uitgaande dat uw trainings script controle punten afhandelt). Als u een afzonderlijke training hervat, wordt dezelfde afstemming-configuratie gebruikt en wordt de map outputs gekoppeld die wordt gebruikt voor die uitvoering. Het trainings script moet het argument `resume-from` accepteren, dat het controle punt of de model bestanden bevat waaruit de trainings uitvoering moet worden hervat. U kunt afzonderlijke trainings uitvoeringen hervatten met het volgende code fragment:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

U kunt uw afstemming-afstemmings experiment configureren om aan de slag te gaan met een vorig experiment of door de uitvoering van afzonderlijke trainingen te hervatten met behulp van de optionele para meters `resume_from` en `resume_child_runs` in de configuratie:

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

## <a name="visualize-experiment"></a>Experimenteren met visualiseren

De Azure Machine Learning SDK bevat een [notebook-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) waarmee de voortgang van uw trainings uitvoeringen wordt gevisualiseerd. In het volgende code fragment worden alle afstemming-afstemmings uitvoeringen op één plek in een Jupyter-notebook gevisualiseerd:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Met deze code wordt een tabel weer gegeven met informatie over de trainings uitvoeringen voor elk van de afstemming-configuraties.

![afstemming tuning Table](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

U kunt ook de prestaties van elk van de uitvoeringen visualiseren als de voortgang van de training. 

![afstemming-afstemmings plot](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Daarnaast kunt u de correlatie tussen de prestaties en waarden van afzonderlijke Hyper parameters visueel identificeren met behulp van een parallelle coördinaten tekening. 

[parallelle coördinaten ![afstemming afstemmen](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

U kunt ook visualiseren van al uw afstemming-afstemmings uitvoeringen in de Azure-webportal. Zie [experimenten volgen](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)voor meer informatie over het weer geven van een experiment in de webportal.

## <a name="find-the-best-model"></a>Het beste model zoeken

Wanneer alle afstemmings uitvoeringen van de afstemming zijn voltooid, [identificeert u de best presterende configuratie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true-) en de bijbehorende afstemming-waarden:

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

## <a name="sample-notebook"></a>Voorbeeld notitieblok
Raadpleeg Train-afstemming-*-notebooks in deze map:
* [procedures voor het gebruik van azureml/training-met-diep leren](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen
* [Een experiment volgen](how-to-track-experiments.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
