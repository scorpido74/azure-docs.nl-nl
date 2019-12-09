---
title: Variabele activiteit instellen in Azure Data Factory
description: Meer informatie over het gebruik van de activiteit variabele instellen om de waarde in te stellen van een bestaande variabele die in een Data Factory pijp lijn is gedefinieerd
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 88500ecbc56b34551a0cbd3ca94727ba4bbcda9f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930649"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Variabele activiteit instellen in Azure Data Factory

Gebruik de activiteit variabele instellen om de waarde in te stellen van een bestaande variabele van het type teken reeks, BOOL of matrix die in een Data Factory pijp lijn is gedefinieerd.

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Verplicht
-------- | ----------- | --------
name | De naam van de activiteit in de pijp lijn | Ja
description | Tekst die beschrijft wat de activiteit doet | nee
type | Type activiteit is SetVariable | ja
waarde | Waarde van de teken reeks of expressie object die wordt gebruikt om de opgegeven variabele in te stellen | ja
variableName | Naam van de variabele die wordt ingesteld door deze activiteit | ja


## <a name="next-steps"></a>Volgende stappen
Meer informatie over een gerelateerde controle stroom activiteit die wordt ondersteund door Data Factory: 

- [Variabele activiteit toevoegen](control-flow-append-variable-activity.md)
