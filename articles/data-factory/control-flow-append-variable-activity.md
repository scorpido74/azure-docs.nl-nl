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
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679982"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Variabele activity toevoegen in Azure Data Factory

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

- [Variabele activiteit instellen](control-flow-set-variable-activity.md)
