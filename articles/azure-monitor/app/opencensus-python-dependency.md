---
title: Het bijhouden van afhankelijkheden in Azure-toepassing Insights met opentellingen python | Microsoft Docs
description: Afhankelijkheids aanroepen voor uw python-apps bewaken via opentellingen python.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 6217798f8175e7ecc1c1ec4068d7765444e4d2a2
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368287"
---
# <a name="track-dependencies-with-opencensus-python"></a>Afhankelijkheden bijhouden met opentellingen python

Een afhankelijkheid is een extern onderdeel dat wordt aangeroepen door uw toepassing. Afhankelijkheids gegevens worden verzameld met opentellingen python en de verschillende integraties. De gegevens worden vervolgens naar Application Insights onder Azure Monitor verzonden als `dependencies` telemetrie.

Eerst moet u uw python-toepassing instrumenteren met de nieuwste [Opentellingen PYTHON SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>In-process afhankelijkheden

Met opentellingen python SDK voor Azure Monitor kunt u ' in-process ' telemetrie (informatie en logica die in uw toepassing plaatsvinden) verzenden. In-process afhankelijkheden hebben het `type` veld als `INPROC` in Analytics.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Afhankelijkheden met de integratie van ' aanvragen '

Volg uw uitgaande aanvragen met de opentellings `requests`-integratie.

Down load en installeer `opencensus-ext-requests` van [PyPI](https://pypi.org/project/opencensus-ext-requests/) en voeg deze toe aan de traceer integraties. Aanvragen die worden verzonden via de python- [aanvragen](https://pypi.org/project/requests/) bibliotheek, worden bijgehouden.

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

## <a name="dependencies-with-httplib-integration"></a>Afhankelijkheden met ' httplib-integratie

Volg uw uitgaande aanvragen met opentellingen `httplib` integratie.

Down load en installeer `opencensus-ext-httplib` van [PyPI](https://pypi.org/project/opencensus-ext-httplib/) en voeg deze toe aan de traceer integraties. Aanvragen die worden verzonden via [http. client](https://docs.python.org/3.7/library/http.client.html) voor Python3 of [httplib](https://docs.python.org/2/library/httplib.html) voor Python2 worden bijgehouden.

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

## <a name="dependencies-with-django-integration"></a>Afhankelijkheden met ' Django-integratie

Volg uw uitgaande Django-aanvragen met de opentellings `django`-integratie.

Down load en installeer `opencensus-ext-django` van [PyPI](https://pypi.org/project/opencensus-ext-django/) en voeg de volgende regel toe aan de sectie `MIDDLEWARE` in het bestand Django `settings.py`.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Aanvullende configuratie kan worden gegeven, Lees [aanpassingen](https://github.com/census-instrumentation/opencensus-python#customization) voor een volledige referentie.

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

## <a name="dependencies-with-mysql-integration"></a>Afhankelijkheden met MySQL-integratie

Volg uw MYSQL-afhankelijkheden met de opentellings `mysql` integratie. Deze integratie ondersteunt de [mysql-connector](https://pypi.org/project/mysql-connector-python/) bibliotheek.

Down load en installeer `opencensus-ext-mysql` van [PyPI](https://pypi.org/project/opencensus-ext-mysql/) en voeg de volgende regels toe aan uw code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Afhankelijkheden met ' pymysql-integratie

Volg uw PyMySQL-afhankelijkheden met de opentellings `pymysql` integratie.

Down load en installeer `opencensus-ext-pymysql` van [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) en voeg de volgende regels toe aan uw code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Afhankelijkheden met ' postgresql-integratie

Volg uw PostgreSQL-afhankelijkheden met de opentellings `postgresql` integratie. Deze integratie biedt ondersteuning voor de [psycopg2](https://pypi.org/project/psycopg2/) -bibliotheek.

Down load en installeer `opencensus-ext-postgresql` van [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) en voeg de volgende regels toe aan uw code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Afhankelijkheden met ' pymongo-integratie

Volg uw MongoDB-afhankelijkheden met de opentellings `pymongo` integratie. Deze integratie biedt ondersteuning voor de [pymongo](https://pypi.org/project/pymongo/) -bibliotheek.

Down load en installeer `opencensus-ext-pymongo` van [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) en voeg de volgende regels toe aan uw code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Afhankelijkheden met ' sqlalchemy-integratie

Houd uw afhankelijkheden bij met SQLAlchemy met opentellingen `sqlalchemy` integratie. Deze integratie houdt het gebruik van het [sqlalchemy](https://pypi.org/project/SQLAlchemy/) -pakket bij, ongeacht de onderliggende data base.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Volgende stappen

* [Toepassingskaart](../../azure-monitor/app/app-map.md)
* [Beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)
* [Zoeken](../../azure-monitor/app/diagnostic-search.md)
* [Logboek query (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Transactie diagnostiek](../../azure-monitor/app/transaction-diagnostics.md)
