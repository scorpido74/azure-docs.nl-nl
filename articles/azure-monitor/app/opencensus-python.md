---
title: Python-toepassingen bewaken met Azure Monitor | Microsoft Docs
description: Biedt instructies om opentellingen python te bekabelen met Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294001"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Azure Monitor instellen voor uw python-toepassing

Azure Monitor ondersteunt gedistribueerde tracering, metrische verzameling en logboek registratie van python-toepassingen via integratie met [Opentellingen](https://opencensus.io). Dit artikel begeleidt u stapsgewijs door het proces voor het instellen van opentellingen voor python en het ophalen van uw bewakings gegevens voor Azure Monitor.

## <a name="prerequisites"></a>Vereisten

- U hebt een Azure-abonnement nodig.
- Python moet worden geïnstalleerd. in dit artikel wordt [python 3.7.0](https://www.python.org/downloads/)gebruikt, hoewel eerdere versies waarschijnlijk zullen werken met kleine aanpassingen.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Application Insights resource in Azure Monitor maken

Eerst moet u een Application Insights-resource in Azure Monitor maken, waarmee een instrumentatie sleutel (Ikey) wordt gegenereerd. De iKey wordt vervolgens gebruikt voor het configureren van de opentellings-SDK voor het verzenden van telemetriegegevens naar Azure Monitor.

1. Selecteer **een resource maken** > **Ontwikkelhulpprogramma's** > **Application Insights**.

   ![Application Insights-resource toevoegen](./media/opencensus-python/0001-create-resource.png)

   Er wordt een configuratiescherm weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen.

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app beschrijft die u wilt controleren |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resourcegroep waarin App Insights-gegevens worden gehost |
   | **Locatie** | US - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

2. Klik op **Maken**.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentatie met opentellingen python SDK voor Azure Monitor

1. Installeer de Azure Monitor Exporters van opentellingen:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` ervan uitgaan dat u een omgevings variabele PATH hebt ingesteld voor uw python-installatie. Als u dit niet hebt geconfigureerd, moet u het volledige mappad opgeven waar uw python-uitvoer bare bestand zich bevindt, wat resulteert in een opdracht als: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. De SDK maakt gebruik van drie Azure Monitor-Exporters voor het verzenden van verschillende soorten telemetrie naar Azure Monitor: tracering, metrische gegevens en Logboeken. Bekijk [het overzicht van het gegevens platform](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) voor meer informatie over de verschillende typen. Volg de onderstaande instructies om te zien hoe u deze verschillende typen via de drie Exporters kunt verzenden.

### <a name="trace"></a>Tracering

1. Eerst gaan we een aantal tracerings gegevens lokaal genereren. Voer de volgende code in python niet-actief of uw editor van keuze in.

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

2. Als u de code uitvoert, wordt u herhaaldelijk gevraagd om een waarde in te voeren. Bij elke vermelding wordt de waarde afgedrukt op de shell en wordt een bijbehorend **SpanData** gegenereerd door de module opentellingen python. Het project opentelling definieert een [_tracering als een boom structuur van reeksen_](https://opencensus.io/core-concepts/tracing/).
    
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

3. Hoewel het nuttig is voor demonstratie doeleinden, willen we uiteindelijk het `SpanData` verzenden naar Azure Monitor. Wijzig uw code uit de vorige stap op basis van het volgende code voorbeeld:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
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

4. Wanneer u nu het python-script uitvoert, moet u nog steeds worden gevraagd om waarden in te voeren, maar nu wordt alleen de waarde in de shell afgedrukt. Het gemaakte `SpanData` wordt naar Azure Monitor verzonden. U kunt de verzonden gegevens over het bereik vinden onder `dependencies`.

### <a name="metrics"></a>Metrische gegevens

1. Eerst gaan we een aantal lokale metrische gegevens genereren. We gaan een eenvoudige metriek maken om het aantal keren dat de gebruiker op ENTER drukt, te volgen.

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
2. Als u de code uitvoert, wordt u herhaaldelijk gevraagd om op ENTER te drukken. Er wordt een metriek gemaakt om het aantal ingedrukt te houden. Bij elke vermelding wordt de waarde verhoogd en de metrische gegevens worden weer gegeven in de-console, met de huidige waarde en het huidige tijds tempel wanneer de metriek is bijgewerkt.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Hoewel het nuttig is voor demonstratie doeleinden, willen we uiteindelijk de metrische gegevens naar Azure Monitor verzenden. Wijzig uw code uit de vorige stap op basis van het volgende code voorbeeld:

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
    )
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

4. De exporteur verzendt metrische gegevens naar Azure Monitor met een vast interval, de standaard waarde is elke 15 seconden. We volgen een enkele metriek zodat deze metrische gegevens, met een wille keurige waarde en tijds tempel daarin, worden verzonden elk interval. U vindt de gegevens onder `customMetrics`.

### <a name="logs"></a>Logboeken

1. Eerst gaan we een aantal lokale logboek gegevens genereren.

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

2.  De code vraagt voortdurend om een waarde die moet worden ingevoerd. Een logboek vermelding wordt verzonden voor elke waarde die wordt ingevoerd met daarin de genoemde waarde.

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

3. Hoewel het nuttig is voor demonstratie doeleinden, willen we uiteindelijk de metrische gegevens naar Azure Monitor verzenden. Wijzig uw code uit de vorige stap op basis van het volgende code voorbeeld:

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

4. De export functie stuurt logboek gegevens naar Azure Monitor. U vindt de gegevens onder `traces`.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. U kunt de pagina **overzicht** van Application Insights nu opnieuw openen in het Azure Portal om details weer te geven over de toepassing die momenteel wordt uitgevoerd. Selecteer **Live metrische stroom**.

   ![Scherm opname van het deel venster overzicht met een live metrieke stroom die is geselecteerd in het rode vak](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Ga terug naar de pagina **overzicht** en selecteer **toepassings overzicht** voor een visuele indeling van de afhankelijkheids relaties en roep timing tussen de onderdelen van uw toepassing aan.

    ![Scherm opname van basis toepassings toewijzing](./media/opencensus-python/0007-application-map.png)

    Omdat we slechts één methode aanroep traceren, is onze toepassings toewijzing niet zo interessant. Het is echter mogelijk dat toepassings overzicht kan worden geschaald om veel meer gedistribueerde toepassingen te visualiseren:

   ![Toepassingskaart](media/opencensus-python/application-map.png)

3. Selecteer **prestaties onderzoeken** om gedetailleerde prestatie analyses uit te voeren en de hoofd oorzaak van trage prestaties te bepalen.

    ![Scherm afbeelding van het deel venster prestaties](./media/opencensus-python/0008-performance.png)

4. Door voor **beelden** te selecteren en vervolgens te klikken op een van de voor beelden die in het rechterdeel venster worden weer gegeven, wordt de end-to-end-transactie Details geopend. Hoewel in onze voor beeld-app slechts één gebeurtenis wordt weer gegeven, zou een complexere toepassing u in staat stellen de end-to-end-trans actie te verkennen tot het niveau van de aanroep stack van een afzonderlijke gebeurtenis.

     ![Scherm opname van end-to-end trans actie-interface](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Uw gegevens weer geven met query's

1. U kunt de telemetriegegevens weer geven die vanuit uw toepassing zijn verzonden via het tabblad Logboeken (analyse).

    ![Scherm opname van het deel venster overzicht met Logboeken (Analytics) geselecteerd in het rood vak](./media/opencensus-python/0010-logs-query.png)

2. Voor telemetrie die met de Azure Monitor Trace-export functie worden verzonden, worden binnenkomende aanvragen weer gegeven onder de `requests` en de out-of-process-aanvragen worden weer gegeven onder `dependencies`.

3. Voor telemetrie die met de export functie voor metrische gegevens van Azure Monitor worden verzonden, worden de metrische gegevens die worden verzonden weer gegeven onder `customMetrics`.

4. Voor telemetrie die wordt verzonden met de export functie van de Azure Monitor logboeken, worden logboeken weer gegeven onder `traces` en uitzonde ringen worden weer gegeven onder `exceptions`.

5. Bekijk de [Logboeken in azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) voor meer gedetailleerde informatie over het gebruik van query's en Logboeken.

## <a name="opencensus-for-python"></a>Opentelling voor python

* [Opentellingen python op GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Aanpassing](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Erlenmeyer integratie](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django-integratie](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL-integratie](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Volgende stappen

* [API-samen vatting](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Toepassings overzicht](./../../azure-monitor/app/app-map.md)
* [End-to-end prestatie bewaking](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Waarschuwingen

* [Beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md): maak tests om ervoor te zorgen dat uw site zichtbaar is op internet.
* [Slimme diagnostische gegevens](../../azure-monitor/app/proactive-diagnostics.md): deze tests worden automatisch uitgevoerd, zodat u niets hoeft te doen om ze in te stellen. Deze geeft aan of een app een ongebruikelijk aantal mislukte aanvragen heeft.
* [Metrische waarschuwingen](../../azure-monitor/app/alerts.md): Stel waarschuwingen in om u te waarschuwen als een metriek een drempel waarde overschrijdt. U kunt deze instellen op aangepaste metrische gegevens die u in uw app codeert.