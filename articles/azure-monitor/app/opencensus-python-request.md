---
title: Inkomende aanvraag tracering in Azure-toepassing Insights met opentellingen python | Microsoft Docs
description: Bewaak aanvraag aanroepen voor uw python-apps via opentellingen python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: tracking-python
ms.openlocfilehash: 6eae11e2157904b47582f78a601c8d2900e61247
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324587"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Inkomende aanvragen bijhouden met opentellingen python

Gegevens van binnenkomende aanvragen worden verzameld met opentellingen python en de verschillende integraties. Spoor gegevens op die worden verzonden naar uw webtoepassingen die zijn gemaakt boven op de populaire web Frameworks `django` `flask` en `pyramid` . De gegevens worden vervolgens naar Application Insights onder Azure Monitor als `requests` telemetrie verzonden.

Eerst moet u uw python-toepassing instrumenteren met de nieuwste [Opentellingen PYTHON SDK](./opencensus-python.md).

## <a name="tracking-django-applications"></a>Django-toepassingen bijhouden

1. Down load en Installeer `opencensus-ext-django` van [PyPI](https://pypi.org/project/opencensus-ext-django/) en instrumenteer uw toepassing met de `django` middleware. Binnenkomende aanvragen die naar uw `django` toepassing worden verzonden, worden bijgehouden.

2. Vermeld `opencensus.ext.django.middleware.OpencensusMiddleware` in uw `settings.py` bestand onder `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Zorg ervoor dat AzureExporter op de juiste wijze is geconfigureerd `settings.py` `OPENCENSUS` . Voor aanvragen van url's die u niet wilt bijhouden, voegt u deze toe aan `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Fles toepassingen bijhouden

1. Down load en Installeer `opencensus-ext-flask` van [PyPI](https://pypi.org/project/opencensus-ext-flask/) en instrumenteer uw toepassing met de `flask` middleware. Binnenkomende aanvragen die naar uw `flask` toepassing worden verzonden, worden bijgehouden.

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

2. U kunt uw toepassing ook configureren `flask` via `app.config` . Voor aanvragen van url's die u niet wilt bijhouden, voegt u deze toe aan `BLACKLIST_PATHS` .

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

1. Down load en Installeer `opencensus-ext-django` van [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) en instrumenteer uw toepassing met de `pyramid` tween. Binnenkomende aanvragen die naar uw `pyramid` toepassing worden verzonden, worden bijgehouden.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. U kunt uw `pyramid` tween rechtstreeks in de code configureren. Voor aanvragen van url's die u niet wilt bijhouden, voegt u deze toe aan `BLACKLIST_PATHS` .

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

* [Toepassingskaart](./app-map.md)
* [Beschikbaarheid](./monitor-web-app-availability.md)
* [Zoeken](./diagnostic-search.md)
* [Logboek query (Analytics)](../log-query/log-query-overview.md)
* [Diagnostische gegevens voor transacties](./transaction-diagnostics.md)

