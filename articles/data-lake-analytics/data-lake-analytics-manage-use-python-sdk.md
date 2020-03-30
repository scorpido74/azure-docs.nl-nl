---
title: Azure Data Lake Analytics beheren met Python
description: In dit artikel wordt beschreven hoe u Python gebruiken om Data Lake Analytics-accounts, gegevensbronnen, gebruikers, & taken te beheren.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d40658e1510c9ae8a2e3e1f865df7ac95f61abfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68355974"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Azure Data Lake Analytics beheren met Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In dit artikel wordt beschreven hoe u Azure Data Lake Analytics-accounts, gegevensbronnen, gebruikers en taken beheert met Python.

## <a name="supported-python-versions"></a>Ondersteunde Python-versies

* Gebruik een 64-bits versie van Python.
* U de standaard Python-distributie gebruiken die bij Python.org downloads wordt **[gevonden.](https://www.python.org/downloads/)** 
* Veel ontwikkelaars vinden het handig om de **[Anaconda Python-distributie](https://www.anaconda.com/download/)** te gebruiken.  
* Dit artikel is geschreven met Python versie 3.6 van de standaard Python-distributie

## <a name="install-azure-python-sdk"></a>Azure Python-SDK installeren

Installeer de volgende modules:

* De **azure-mgmt-resourcemodule** bevat andere Azure-modules voor Active Directory, enz.
* De **azure-datalake-store-module** bevat de azure Data Lake Store-bestandssysteembewerkingen. 
* De **azure-mgmt-datalake-store-module** bevat de Azure Data Lake Store-accountbeheerbewerkingen.
* De **azure-mgmt-datalake-analytics-module** bevat de Azure Data Lake Analytics-bewerkingen. 

Zorg er eerst voor `pip` dat u het laatste nieuws hebt door de volgende opdracht uit te voeren:

```
python -m pip install --upgrade pip
```

Dit document is `pip version 9.0.1`geschreven met behulp van .

Gebruik de `pip` volgende opdrachten om de modules vanaf de opdrachtregel te installeren:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Een nieuw Python-script maken

Plak de volgende code in het script:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Voer dit script uit om te controleren of de modules kunnen worden geïmporteerd.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interactieve gebruikersverificatie met een pop-up

Deze methode wordt niet ondersteund.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interactieve gebruikersverificatie met een apparaatcode

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Niet-interactieve authenticatie met SPI en een geheim

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Niet-interactieve verificatie met API en een certificaat

Deze methode wordt niet ondersteund.

## <a name="common-script-variables"></a>Algemene scriptvariabelen

Deze variabelen worden gebruikt in de monsters.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>De clients maken

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Een Azure-resourcegroep maken

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Een Data Lake Analytics-account maken

Maak eerst een winkelaccount aan.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Maak vervolgens een ADLA-account dat die winkel gebruikt.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Een taak indienen

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Wachten tot een taak is beëindigd

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Pijplijnen en herhalingen weergeven
Afhankelijk van of er pijplijn- of herhalingsmetagegevens zijn gekoppeld aan uw taken, u pijplijnen en herhalingen weergeven.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Rekenbeleid beheren

Het object DataLakeAnalyticsAccountManagementClient biedt methoden voor het beheren van het rekenbeleid voor een Data Lake Analytics-account.

### <a name="list-compute-policies"></a>Rekenbeleid weergeven

Met de volgende code wordt een lijst met rekenbeleid opgehaald voor een Data Lake Analytics-account.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Een nieuw rekenbeleid maken

Met de volgende code wordt een nieuw rekenbeleid gemaakt voor een Data Lake Analytics-account, waarbij de maximale AU's die beschikbaar zijn voor de opgegeven gebruiker worden ingesteld op 50 en de minimale taakprioriteit op 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Volgende stappen

- Als u dezelfde zelfstudie wilt bekijken met een ander hulpprogramma, klikt u op de tabselectors boven aan de pagina.
- Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
- Zie [Azure Data Lake Analytics beheren met Azure-portal](data-lake-analytics-manage-use-portal.md)voor beheertaken.

