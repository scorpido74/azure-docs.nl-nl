---
title: Regels voor het benoemen van Azure Data Factory-entiteiten
description: Beschrijft naamgevingsregels voor entiteiten in Gegevensfabriek.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 59cddf04493333b441dcf130d1d99d4fa946748c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837837"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - naamgevingsregels
In de volgende tabel vindt u naamgevingsregels voor artefacten van Gegevensfabriek.

| Name | Naam uniciteit | Validatiecontroles |
|:--- |:--- |:--- |
| Data Factory |Uniek in Microsoft Azure. Namen zijn case-ongevoelig, `MyDF` dat `mydf` wil zeggen, en verwijzen naar dezelfde gegevensfabriek. |<ul><li>Elke gegevensfabriek is gebonden aan precies één Azure-abonnement.</li><li>Objectnamen moeten beginnen met een letter of een getal en mogen alleen letters, cijfers en het streepje (-) teken bevatten.</li><li>Elk streepje (-) teken moet onmiddellijk worden voorafgegaan en gevolgd door een letter of een nummer. Opeenvolgende streepjes zijn niet toegestaan in containernamen.</li><li>Naam kan 3-63 tekens lang.</li></ul> |
| Gekoppelde services/gegevenssets/pijplijnen |Uniek met in een datafabriek. Namen zijn ongevoelig. |<ul><li>Objectnamen moeten beginnen met een letter, getal of een underscore (_).</li><li>Volgende tekens zijn niet toegestaan: "",+,',',','/',<",>',"",",""&,"""""""","","","","\\</li><li>Streepjes ("-") zijn niet toegestaan in de namen van gekoppelde services en alleen van gegevenssets.</li></ul>  |
| Resourcegroep |Uniek in Microsoft Azure. Namen zijn ongevoelig. | Zie [Azure-naamgevingsregels en -beperkingen voor](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)meer informatie. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het maken van gegevensfabrieken door stapsgewijze instructies te volgen in [Quickstart: maak een artikel in de gegevensfabriek.](quickstart-create-data-factory-powershell.md) 
