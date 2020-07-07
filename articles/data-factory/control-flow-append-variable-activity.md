---
title: Variabele activity toevoegen in Azure Data Factory
description: Meer informatie over het instellen van de activiteit variabele toevoegen om een waarde toe te voegen aan een bestaande matrix variabele die in een Data Factory pijp lijn is gedefinieerd
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414200"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Variabele activity toevoegen in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Gebruik de activiteit variabele toevoegen om een waarde toe te voegen aan een bestaande matrix variabele die in een Data Factory pijp lijn is gedefinieerd.

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
naam | De naam van de activiteit in de pijp lijn | Ja
description | Tekst die beschrijft wat de activiteit doet | nee
type | Type activiteit is AppendVariable | ja
waarde | Letterlijke teken reeks of expressie object waarde die wordt gebruikt om toe te voegen aan de opgegeven variabele | ja
variableName | Naam van de variabele die wordt gewijzigd door activiteit, de variabele moet van het type matrix zijn | ja

## <a name="next-steps"></a>Volgende stappen
Meer informatie over een gerelateerde controle stroom activiteit die wordt ondersteund door Data Factory: 

- [Activiteit variabele instellen](control-flow-set-variable-activity.md)
