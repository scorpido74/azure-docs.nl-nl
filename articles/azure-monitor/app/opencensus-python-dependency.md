---
title: Afhankelijkheidstracking in Azure-toepassingsinzichten met OpenCensus Python | Microsoft Documenten
description: Monitor afhankelijkheidsoproepen voor uw Python-apps via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669927"
---
# <a name="track-dependencies-with-opencensus-python"></a>Afhankelijkheden bijhouden met OpenCensus Python

Een afhankelijkheid is een externe component die door uw toepassing wordt aangeroepen. Afhankelijkheidsgegevens worden verzameld met OpenCensus Python en de verschillende integraties. De gegevens worden vervolgens verzonden naar `dependencies` Application Insights onder Azure Monitor als telemetrie.

Ten eerste, instrument uw Python applicatie met de nieuwste [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Afhankelijkheden in het proces

Met OpenCensus Python SDK voor Azure Monitor u 'in-process' afhankelijkheidstelemetrie verzenden (informatie en logica die binnen uw toepassing plaatsvindt). In-process afhankelijkheden hebben `type` het `INPROC` veld zoals in analytics.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Afhankelijkheden met integratie met "aanvragen"

Volg uw uitgaande verzoeken `requests` met de OpenCensus-integratie.

Download en `opencensus-ext-requests` installeer van [PyPI](https://pypi.org/project/opencensus-ext-requests/) en voeg het toe aan de trace-integraties. Aanvragen die worden verzonden via de [Python-aanvragenbibliotheek,](https://pypi.org/project/requests/) worden bijgehouden.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Afhankelijkheden met "httplib" integratie

Houd uw uitgaande aanvragen `httplib` bij met OpenCensus-integratie.

Download en `opencensus-ext-httplib` installeer van [PyPI](https://pypi.org/project/opencensus-ext-httplib/) en voeg het toe aan de trace-integraties. Aanvragen die via [http.client](https://docs.python.org/3.7/library/http.client.html) voor Python3 of [httplib](https://docs.python.org/2/library/httplib.html) voor Python2 worden verzonden, worden bijgehouden.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Afhankelijkheden met "django" integratie

Volg uw uitgaande Django-verzoeken `django` met de OpenCensus-integratie.

Download en `opencensus-ext-django` installeer van [PyPI](https://pypi.org/project/opencensus-ext-django/) en `MIDDLEWARE` voeg de `settings.py` volgende regel toe aan de sectie in het Django-bestand.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Extra configuratie kan worden verstrekt, lees [aanpassingen](https://github.com/census-instrumentation/opencensus-python#customization) voor een volledige referentie.

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

## <a name="dependencies-with-mysql-integration"></a>Afhankelijkheden met "mysql" integratie

Houd uw MYSQL-afhankelijkheden `mysql` bij met de OpenCensus-integratie. Deze integratie ondersteunt de [mysql-connector](https://pypi.org/project/mysql-connector-python/) bibliotheek.

Download en `opencensus-ext-mysql` installeer van [PyPI](https://pypi.org/project/opencensus-ext-mysql/) en voeg de volgende regels toe aan uw code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Afhankelijkheden met "pymysql" integratie

Houd uw PyMySQL-afhankelijkheden `pymysql` bij met de OpenCensus-integratie.

Download en `opencensus-ext-pymysql` installeer van [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) en voeg de volgende regels toe aan uw code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Afhankelijkheden met "postgresql" integratie

Houd uw PostgreSQL-afhankelijkheden `postgresql` bij met de OpenCensus-integratie. Deze integratie ondersteunt de [psycopg2](https://pypi.org/project/psycopg2/) bibliotheek.

Download en `opencensus-ext-postgresql` installeer van [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) en voeg de volgende regels toe aan uw code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Afhankelijkheden met "pymongo" integratie

Houd uw MongoDB-afhankelijkheden `pymongo` bij met de OpenCensus-integratie. Deze integratie ondersteunt de [pymongo](https://pypi.org/project/pymongo/) bibliotheek.

Download en `opencensus-ext-pymongo` installeer van [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) en voeg de volgende regels toe aan uw code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Afhankelijkheden met "sqlalchemie" integratie

Houd uw afhankelijkheden bij met SQLAlchemy met opencensus-integratie. `sqlalchemy` Deze integratie houdt het gebruik van het [sqlalchemy-pakket](https://pypi.org/project/SQLAlchemy/) bij, ongeacht de onderliggende database.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Volgende stappen

* [Toepassingskaart](../../azure-monitor/app/app-map.md)
* [Beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)
* [Zoeken](../../azure-monitor/app/diagnostic-search.md)
* [Query log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostische gegevens voor transacties](../../azure-monitor/app/transaction-diagnostics.md)
