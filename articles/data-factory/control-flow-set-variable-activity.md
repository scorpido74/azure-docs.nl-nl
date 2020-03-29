---
title: Variabele activiteit instellen in Azure Data Factory
description: Meer informatie over het gebruik van de activiteit Variabele instellen om de waarde in te stellen van een bestaande variabele die is gedefinieerd in een datafabriekpijplijn
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930649"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Variabele activiteit instellen in Azure Data Factory

Gebruik de variabele activiteit instellen om de waarde in te stellen van een bestaande variabele van het type String, Bool of Array die is gedefinieerd in een pijplijn gegevensfabriek.

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
name | Naam van de activiteit in de pijplijn | Ja
description | Tekst waarin wordt beschreven wat de activiteit doet | nee
type | Activiteitstype is SetVariable | ja
waarde | Tekenreeksletterlijke of expressieobjectwaarde die wordt gebruikt om opgegeven variabele in te stellen | ja
variableName | Naam van de variabele die door deze activiteit wordt ingesteld | ja


## <a name="next-steps"></a>Volgende stappen
Meer informatie over een gerelateerde controlestroomactiviteit die wordt ondersteund door Data Factory: 

- [Activiteit variabele toevoegen](control-flow-append-variable-activity.md)
