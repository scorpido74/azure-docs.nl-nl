---
title: Python-toepassingen controleren met Azure Monitor (voorbeeld) | Microsoft Documenten
description: Geeft instructies om OpenCensus Python te verbinden met Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537105"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Azure Monitor instellen voor uw Python-toepassing

Azure Monitor ondersteunt gedistribueerde tracering, metrische verzameling en logboekregistratie van Python-toepassingen door integratie met [OpenCensus.](https://opencensus.io) In dit artikel wordt u door het proces van het instellen van OpenCensus voor Python en het verzenden van uw bewakingsgegevens naar Azure Monitor meegestouw.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Python installatie. Dit artikel maakt gebruik van [Python 3.7.0,](https://www.python.org/downloads/)hoewel eerdere versies waarschijnlijk zullen werken met kleine wijzigingen.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Een Application Insights-bron maken in Azure Monitor

Eerst moet u een Application Insights-bron maken in Azure Monitor, waarmee een instrumentatiesleutel (ikey) wordt gegenereerd. De ikey wordt vervolgens gebruikt om de OpenCensus SDK te configureren om telemetriegegevens naar Azure Monitor te verzenden.

1. Selecteer De**toepassingsinzichten van hulpprogramma's** >  **voor resourceontwikkelaars** > **maken**.

   ![Een Application Insights-bron toevoegen](./media/opencensus-python/0001-create-resource.png)

1. Er verschijnt een configuratievak. Gebruik de volgende tabel om de invoervelden in te vullen.

   | Instelling        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Wereldwijd unieke waarde | Naam die de app identificeert die u controleert |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resourcegroep om Application Insights-gegevens te hosten |
   | **Locatie** | VS - oost | Een locatie bij u in de buurt of in de buurt van waar uw app wordt gehost |

1. Selecteer **Maken**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument met OpenCensus Python SDK voor Azure Monitor

Installeer de Exporteurs van de OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

Zie [OpenCensus-pakketten voor](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)een volledige lijst met pakketten en integraties.

> [!NOTE]
> De `python -m pip install opencensus-ext-azure` opdracht gaat ervan `PATH` uit dat u een omgevingsvariabele hebt ingesteld voor uw Python-installatie. Als u deze variabele niet hebt geconfigureerd, moet u het volledige mappad geven waar uw Python uitvoerbaar is. Het resultaat is een `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`commando als volgt: .

De SDK gebruikt drie Azure Monitor-exporteurs om verschillende soorten telemetrie naar Azure Monitor te verzenden: traceering, metrische gegevens en logboeken. Zie het overzicht van het [dataplatform](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)voor meer informatie over deze telemetrietypen. Gebruik de volgende instructies om deze telemetrietypes via de drie exporteurs te verzenden.

## <a name="telemetry-type-mappings"></a>Toewijzingen van telemetrietypen

Hier volgen de exporteurs die OpenCensus biedt, toegewezen aan de typen telemetrie die u in Azure Monitor ziet.

![Schermafbeelding van toewijzing van telemetrietypen van OpenCensus naar Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Tracering

> [!NOTE]
> `Trace`in OpenCensus verwijst naar [distributed tracing](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). De `AzureExporter` `requests` verzendingen en `dependency` telemetrie naar Azure Monitor.

1. Laten we eerst lokaal wat traceergegevens genereren. Voer in Python IDLE of uw editor naar keuze de volgende code in.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. Als u de code uitvoert, wordt u herhaaldelijk gevraagd een waarde in te voeren. Bij elk item wordt de waarde afgedrukt op de shell en genereert de `SpanData`OpenCensus Python Module een overeenkomstig stuk . Het OpenCensus-project definieert een [spoor als een reeks overspanningen.](https://opencensus.io/core-concepts/tracing/)
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Hoewel het invoeren van waarden nuttig is voor `SpanData` demonstratiedoeleinden, willen we uiteindelijk de naar Azure Monitor uitzenden. Geef uw verbindingstekenreeks rechtstreeks door aan de exporteur, `APPLICATIONINSIGHTS_CONNECTION_STRING`of u deze opgeven in een omgevingsvariabele. Wijzig uw code uit de vorige stap op basis van het volgende codevoorbeeld:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Wanneer u nu het Python-script uitvoert, moet u nog steeds worden gevraagd om waarden in te voeren, maar alleen de waarde wordt afgedrukt in de shell. De `SpanData` gemaakte wordt verzonden naar Azure Monitor. U vindt de uitgestraalde `dependencies`spangegevens onder . Zie [Afhankelijkheden](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)van OpenCensus Python voor meer informatie over uitgaande aanvragen.
Zie OpenCensus [Python-aanvragen](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)voor meer informatie over binnenkomende aanvragen.

#### <a name="sampling"></a>Steekproeven

Voor informatie over de bemonstering in OpenCensus, neem een kijkje op [bemonstering in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlatie traceren

Voor meer informatie over telemetrie correlatie in uw trace gegevens, neem een kijkje op OpenCensus Python [telemetrie correlatie](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python).

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie OpenCensus [Python-telemetrieprocessors](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)voor meer informatie over het wijzigen van bijgehouden telemetrie voordat deze naar Azure Monitor wordt verzonden.

### <a name="metrics"></a>Metrische gegevens

1. Laten we eerst lokale metrische gegevens genereren. We maken een eenvoudige statistiek om bij te houden hoe vaak de gebruiker op Enter drukt.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. Als u de code uitvoert, wordt u herhaaldelijk gevraagd op Enter te drukken. Er wordt een statistiek gemaakt om bij te houden hoe vaak Enter wordt ingedrukt. Bij elk item wordt de waarde verhoogd en wordt de metrische informatie weergegeven in de console. De informatie bevat de huidige waarde en de huidige tijdstempel wanneer de statistiek werd bijgewerkt.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Hoewel het invoeren van waarden nuttig is voor demonstratiedoeleinden, willen we de metrische gegevens uiteindelijk uitzenden naar Azure Monitor. Geef uw verbindingstekenreeks rechtstreeks door aan de exporteur, `APPLICATIONINSIGHTS_CONNECTION_STRING`of u deze opgeven in een omgevingsvariabele. Wijzig uw code uit de vorige stap op basis van het volgende codevoorbeeld:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. De exporteur stuurt metrische gegevens naar Azure Monitor met een vast interval. De standaardinstelling is elke 15 seconden. We volgen één statistiek, dus deze metrische gegevens, met welke waarde en tijdstempel het ook bevat, worden elk interval verzonden. U de `customMetrics`gegevens vinden onder.

#### <a name="standard-metrics"></a>Standaardstatistieken

Standaard stuurt de metrische exporteur een reeks standaardstatistieken naar Azure Monitor. U dit uitschakelen `enable_standard_metrics` door `False` de vlag in te stellen in de constructeur van de metrische exporteur.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Hieronder vindt u een lijst met standaardstatistieken die momenteel worden verzonden:

- Beschikbaar geheugen (bytes)
- CPU-processortijd (percentage)
- Inkomende aanvraagsnelheid (per seconde)
- Gemiddelde uitvoeringstijd voor binnenkomende aanvraag (milliseconden)
- Percentage uitgaande aanvragen (per seconde)
- CPU-gebruik verwerken (percentage)
- Privébytes verwerken (bytes)

U moet deze statistieken kunnen `performanceCounters`zien in . Het inkomende aanvraagtarief `customMetrics`zou onder . Zie [prestatiemeteritems voor](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)meer informatie .

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie OpenCensus [Python-telemetrieprocessors](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)voor meer informatie over het wijzigen van bijgehouden telemetrie voordat deze naar Azure Monitor wordt verzonden.

### <a name="logs"></a>Logboeken

1. Laten we eerst wat lokale loggegevens genereren.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  De code zal voortdurend vragen om een waarde in te voert. Voor elke ingevoerde waarde wordt een logboekvermelding uitgezonden.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Hoewel het invoeren van waarden nuttig is voor demonstratiedoeleinden, willen we de loggegevens uiteindelijk uitzenden naar Azure Monitor. Geef uw verbindingstekenreeks rechtstreeks door aan de exporteur, `APPLICATIONINSIGHTS_CONNECTION_STRING`of u deze opgeven in een omgevingsvariabele. Wijzig uw code uit de vorige stap op basis van het volgende codevoorbeeld:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. De exporteur stuurt loggegevens naar Azure Monitor. U de `traces`gegevens vinden onder. 

> [!NOTE]
> `traces`in deze context is `Tracing`niet hetzelfde als . `traces`verwijst naar het type telemetrie dat u in `AzureLogHandler`Azure Monitor ziet wanneer u de . `Tracing`verwijst naar een concept in OpenCensus en heeft betrekking op [distributed tracing](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Als u uw logboekberichten wilt `formatters` opmaken, u deze gebruiken in de ingebouwde Python-logboekregistratie-API. [logging API](https://docs.python.org/3/library/logging.html#formatter-objects)

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. U ook aangepaste eigenschappen toevoegen aan uw logboekberichten in het argument *extra* trefwoord met behulp van het veld custom_dimensions. Deze worden weergegeven als sleutelwaardeparen in `customDimensions` Azure Monitor.
> [!NOTE]
> Als u deze functie wilt gebruiken, moet u een woordenboek doorgeven aan het veld custom_dimensions. Als u argumenten van een ander type doorgeeft, zal de logger ze negeren.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="sending-exceptions"></a>Uitzonderingen verzenden

OpenCensus Python volgt en verzendt `exception` telemetrie niet automatisch. Ze worden verzonden via de `AzureLogHandler` door middel van uitzonderingen via de Python logging bibliotheek. U aangepaste eigenschappen toevoegen, net als bij normale logboekregistratie.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
        logger.exception('Captured an exception.', extra=properties)
    ```
Aangezien u uitzonderingen expliciet moet registreren, is het aan de gebruiker om onverwerkte uitzonderingen te registreren. OpenCensus stelt geen beperkingen op in hoe een gebruiker dit wil doen, zolang deze expliciet een uitzonderingstelemetrie registreert.

#### <a name="sampling"></a>Steekproeven

Voor informatie over de bemonstering in OpenCensus, neem een kijkje op [bemonstering in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Logboekcorrelatie

Zie OpenCensus Python [logs integration](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)voor meer informatie over het verrijken van uw logboeken met traceringscontextgegevens.

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie OpenCensus [Python-telemetrieprocessors](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)voor meer informatie over het wijzigen van bijgehouden telemetrie voordat deze naar Azure Monitor wordt verzonden.

## <a name="view-your-data-with-queries"></a>Uw gegevens weergeven met query's

U de telemetriegegevens die vanuit uw toepassing zijn verzonden, bekijken via het tabblad **Logboeken (Analytics).**

![Schermafbeelding van het overzichtsvenster met 'Logboeken (Analytics)' geselecteerd in een rood vak](./media/opencensus-python/0010-logs-query.png)

In de lijst onder **Actief**:

- Voor telemetrie die wordt verzonden met de `requests`Azure Monitor trace-exporteur, worden binnenkomende aanvragen weergegeven onder . Uitgaande of in-process `dependencies`aanvragen worden weergegeven onder .
- Voor telemetrie die wordt verzonden met `customMetrics`de Azure Monitor-statistiekenexporteur, worden verzonden statistieken weergegeven onder .
- Voor telemetrie die wordt verzonden met `traces`de Azure Monitor-logboekenexporteur, worden logboeken weergegeven onder . Uitzonderingen worden `exceptions`weergegeven onder .

Zie [Logboeken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)voor meer gedetailleerde informatie over het gebruik van query's en logboeken.

## <a name="learn-more-about-opencensus-for-python"></a>Meer informatie over OpenCensus voor Python

* [OpenCensus Python op GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Aanpassing](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor-exporteurs op GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus-integraties](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Voorbeeldtoepassingen voor Azure-monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Volgende stappen

* [Binnenkomende aanvragen bijhouden](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Out-going verzoeken bijhouden](./../../azure-monitor/app/opencensus-python-request.md)
* [Toepassingskaart](./../../azure-monitor/app/app-map.md)
* [End-to-end prestatiebewaking](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Waarschuwingen

* [Beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md): maak tests om ervoor te zorgen dat uw site zichtbaar is op internet.
* [Slimme diagnostische gegevens](../../azure-monitor/app/proactive-diagnostics.md): deze tests worden automatisch uitgevoerd, zodat u niets hoeft te doen om ze in te stellen. Deze geeft aan of een app een ongebruikelijk aantal mislukte aanvragen heeft.
* [Metrische waarschuwingen:](../../azure-monitor/app/alerts.md)stel waarschuwingen in om u te waarschuwen als een statistiek een drempelwaarde overschrijdt. U kunt deze instellen op aangepaste metrische gegevens die u in uw app codeert.
