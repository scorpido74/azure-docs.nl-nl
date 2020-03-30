---
title: Inkomende aanvraagtracking in Azure-toepassingsinzichten met OpenCensus Python | Microsoft Documenten
description: Controleer aanvragen voor uw Python-apps via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669944"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Binnenkomende aanvragen bijhouden met OpenCensus Python

Inkomende aanvraaggegevens worden verzameld met OpenCensus Python en de verschillende integraties. Volg binnenkomende aanvraaggegevens die naar uw webtoepassingen worden `django` `flask` verzonden en die bovenop de populaire webframeworks zijn gebouwd, en `pyramid`. De gegevens worden vervolgens verzonden naar `requests` Application Insights onder Azure Monitor als telemetrie.

Ten eerste, instrument uw Python applicatie met de nieuwste [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Django-toepassingen bijhouden

1. Download en `opencensus-ext-django` installeer van [PyPI](https://pypi.org/project/opencensus-ext-django/) en `django` instrument uw applicatie met de middleware. Inkomende aanvragen die `django` naar uw aanvraag worden verzonden, worden bijgehouden.

2. Opnemen `opencensus.ext.django.middleware.OpencensusMiddleware` in `settings.py` uw `MIDDLEWARE`bestand onder .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Controleer of AzureExporter correct is `settings.py` `OPENCENSUS`geconfigureerd in uw onder .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. U ook url's toevoegen aan `settings.py` onder `BLACKLIST_PATHS` voor verzoeken die u niet wilt bijhouden.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Toepassingen voor het bijhouden van kolf

1. Download en `opencensus-ext-flask` installeer van [PyPI](https://pypi.org/project/opencensus-ext-flask/) en `flask` instrument uw applicatie met de middleware. Inkomende aanvragen die `flask` naar uw aanvraag worden verzonden, worden bijgehouden.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. U `flask` uw middleware rechtstreeks in de code configureren. Voor verzoeken van url's die u niet `BLACKLIST_PATHS`wilt volgen, voegt u deze toe aan .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Piramidetoepassingen bijhouden

1. Download en `opencensus-ext-django` installeer van [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) en `pyramid` instrument uw applicatie met de tween. Inkomende aanvragen die `pyramid` naar uw aanvraag worden verzonden, worden bijgehouden.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. U `pyramid` uw tween rechtstreeks in de code configureren. Voor verzoeken van url's die u niet `BLACKLIST_PATHS`wilt volgen, voegt u deze toe aan .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Volgende stappen

* [Toepassingskaart](../../azure-monitor/app/app-map.md)
* [Beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)
* [Zoeken](../../azure-monitor/app/diagnostic-search.md)
* [Query log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostische gegevens voor transacties](../../azure-monitor/app/transaction-diagnostics.md)
