---
title: Variabele activiteit toevoegen in Azure Data Factory
description: Meer informatie over het instellen van de variabele activiteit toevoegen om een waarde toe te voegen aan een bestaande matrixvariabele die is gedefinieerd in een datafabriekpijplijn
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414200"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Variabele activiteit toevoegen in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Gebruik de activiteit Variabele toevoegen om een waarde toe te voegen aan een bestaande matrixvariabele die is gedefinieerd in een pijplijn voor gegevensfabrieken.

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
name | Naam van de activiteit in de pijplijn | Ja
description | Tekst waarin wordt beschreven wat de activiteit doet | nee
type | Activiteitstype is AppendVariable | ja
waarde | Tekenreeksletterlijke of expressieobjectwaarde die wordt gebruikt om toe te nemen aan opgegeven variabele | ja
variableName | Naam van de variabele die wordt gewijzigd door activiteit, moet de variabele van het type 'Array' zijn | ja

## <a name="next-steps"></a>Volgende stappen
Meer informatie over een gerelateerde controlestroomactiviteit die wordt ondersteund door Data Factory: 

- [Activiteit variabele instellen](control-flow-set-variable-activity.md)
