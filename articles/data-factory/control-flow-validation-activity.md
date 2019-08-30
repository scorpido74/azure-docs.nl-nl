---
title: Validatie activiteit in Azure Data Factory | Microsoft Docs
description: De validatie activiteit blijft de uitvoering van de pijp lijn voortzetten totdat de gekoppelde gegevensset wordt gevalideerd met bepaalde criteria die de gebruiker opgeeft.
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
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141611"
---
# <a name="validation-activity-in-azure-data-factory"></a>Validatie activiteit in Azure Data Factory
U kunt een validatie in een pijp lijn gebruiken om ervoor te zorgen dat de pijp lijn alleen de uitvoering voortzet wanneer het de gekoppelde gegevensset bevat, die aan de opgegeven criteria voldoet, of er is een time-out opgetreden.


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


## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Description | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van de activiteit validatie | Tekenreeks | Ja |
Type | Moet worden ingesteld op **validatie**. | Tekenreeks | Ja |
sets | De uitvoering van de activiteit wordt geblokkeerd totdat deze de gegevensset bevat die aan de opgegeven criteria voldoet, of er is een time-out opgetreden. De gegeven gegevensset moet de eigenschap MinimumSize of ChildItems ondersteunen. | Verwijzing naar gegevensset | Ja |
timeout | Hiermee geeft u de time-out op voor de activiteit die moet worden uitgevoerd. Als er geen waarde is opgegeven, is de standaard waarde 7 dagen ("7.00:00:00"). De notatie is d. uu: mm: SS | Tekenreeks | Nee |
dwars | Een vertraging in seconden tussen de validatie pogingen. Als er geen waarde is opgegeven, is de standaard waarde 10 seconden. | Integer | Nee |
childItems | Hiermee wordt gecontroleerd of de map onderliggende items heeft. Kan worden ingesteld op-True: Controleer of de map bestaat en of deze items bevat. Blokken tot ten minste één item aanwezig is in de map of de time-outwaarde is bereikt.-onwaar: Controleer of de map bestaat en of deze leeg is. Blokken totdat de map leeg is of totdat de time-outwaarde is bereikt. Als er geen waarde is opgegeven, wordt de activiteit geblokkeerd totdat de map bestaat of totdat de time-out is bereikt. | Boolean-waarde | Nee |
minimumSize | Minimale grootte van een bestand in bytes. Als er geen waarde is opgegeven, is de standaard waarde 0 bytes | Integer | Nee |


## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund:

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until Activity](control-flow-until-activity.md)
