---
title: Validatieactiviteit in Azure Data Factory
description: De validatieactiviteit gaat niet door met de uitvoering van de pijplijn totdat de gekoppelde gegevensset is gevalideerd met bepaalde criteria die de gebruiker opgeeft.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 764b41d1823e8edce134c5099e066486f4f08acc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417926"
---
# <a name="validation-activity-in-azure-data-factory"></a>Validatieactiviteit in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U een validatie in een pijplijn gebruiken om ervoor te zorgen dat de pijplijn alleen wordt uitgevoerd nadat de gekoppelde gegevenssetverwijzing is gevalideerd, dat deze voldoet aan de opgegeven criteria of dat een time-out is bereikt.


## <a name="syntax"></a>Syntaxis

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van de activiteit 'Validatie' | Tekenreeks | Ja |
type | Moet zijn ingesteld op **Validatie**. | Tekenreeks | Ja |
Dataset | Activiteit blokkeert de uitvoering totdat deze verwijzing naar de gegevensset is gevalideerd en dat deze voldoet aan de opgegeven criteria of een time-out is bereikt. De meegeleverde gegevensset moet de eigenschap 'MinimumSize' of 'ChildItems' ondersteunen. | Gegevenssetverwijzing | Ja |
timeout | Hiermee geeft u de time-out op voor de activiteit die moet worden uitgevoerd. Als er geen waarde is opgegeven, is de standaardwaarde 7 dagen ('7.00:00:00'). Formaat is d.hh:mm:ss | Tekenreeks | Nee |
Slaap | Een vertraging in seconden tussen validatiepogingen. Als er geen waarde is opgegeven, is de standaardwaarde 10 seconden. | Geheel getal | Nee |
onderliggendE items | Hiermee wordt gecontroleerd of de map onderliggende items bevat. Kan worden ingesteld op true: valideren dat de map bestaat en dat deze items bevat. Blokken totdat ten minste één item aanwezig is in de map of time-out waarde is bereikt.-false: Controleer of de map bestaat en dat deze leeg is. Blokkeert totdat de map leeg is of totdat de time-outwaarde is bereikt. Als er geen waarde is opgegeven, wordt de activiteit geblokkeerd totdat de map bestaat of totdat een time-out is bereikt. | Booleaans | Nee |
minimumGrootte | Minimale grootte van een bestand in bytes. Als er geen waarde is opgegeven, is de standaardwaarde 0 bytes | Geheel getal | Nee |


## <a name="next-steps"></a>Volgende stappen
Bekijk andere controlestroomactiviteiten die worden ondersteund door Data Factory:

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Activiteit uitvoeren van pijplijn](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Web Activity](control-flow-web-activity.md)
- [Until-activiteit](control-flow-until-activity.md)
