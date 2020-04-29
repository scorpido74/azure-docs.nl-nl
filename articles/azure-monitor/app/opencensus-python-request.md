---
title: Inkomende aanvraag tracering in Azure-toepassing Insights met opentellingen python | Microsoft Docs
description: Bewaak aanvraag aanroepen voor uw python-apps via opentellingen python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669944"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Inkomende aanvragen bijhouden met opentellingen python

Gegevens van binnenkomende aanvragen worden verzameld met opentellingen python en de verschillende integraties. Spoor gegevens op die worden verzonden naar uw webtoepassingen die zijn `django` `flask` gemaakt boven op de populaire web `pyramid`frameworks en. De gegevens worden vervolgens naar Application Insights onder Azure Monitor als `requests` telemetrie verzonden.

Eerst moet u uw python-toepassing instrumenteren met de nieuwste [Opentellingen PYTHON SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Django-toepassingen bijhouden

1. Down load en `opencensus-ext-django` Installeer van [PyPI](https://pypi.org/project/opencensus-ext-django/) en instrumenteer uw toepassing `django` met de middleware. Binnenkomende aanvragen die naar uw `django` toepassing worden verzonden, worden bijgehouden.

2. Vermeld `opencensus.ext.django.middleware.OpencensusMiddleware` in uw `settings.py` bestand onder `MIDDLEWARE`.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Zorg ervoor dat AzureExporter op de `settings.py` juiste wijze is `OPENCENSUS`geconfigureerd.

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

4. U kunt ook url's toevoegen `settings.py` onder `BLACKLIST_PATHS` voor aanvragen die u niet wilt bijhouden.

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

## <a name="tracking-flask-applications"></a>Fles toepassingen bijhouden

1. Down load en `opencensus-ext-flask` Installeer van [PyPI](https://pypi.org/project/opencensus-ext-flask/) en instrumenteer uw toepassing `flask` met de middleware. Binnenkomende aanvragen die naar uw `flask` toepassing worden verzonden, worden bijgehouden.

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

2. U kunt uw `flask` middleware rechtstreeks in de code configureren. Voor aanvragen van url's die u niet wilt bijhouden, voegt u deze toe aan `BLACKLIST_PATHS`.

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

## <a name="tracking-pyramid-applications"></a>Piramide toepassingen bijhouden

1. Down load en `opencensus-ext-django` Installeer van [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) en instrumenteer uw toepassing `pyramid` met de tween. Binnenkomende aanvragen die naar uw `pyramid` toepassing worden verzonden, worden bijgehouden.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. U kunt uw `pyramid` tween rechtstreeks in de code configureren. Voor aanvragen van url's die u niet wilt bijhouden, voegt u deze toe aan `BLACKLIST_PATHS`.

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
* [Logboek query (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostische gegevens voor transacties](../../azure-monitor/app/transaction-diagnostics.md)
