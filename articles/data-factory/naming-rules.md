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
ms.openlocfilehash: f922ada663391cf65a61f4e18bba53668f9c4a1a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419405"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - naamgevingsregels

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In de volgende tabel vindt u naamgevingsregels voor artefacten van Gegevensfabriek.

| Naam | Naam uniciteit | Validatiecontroles |
|:--- |:--- |:--- |
| Data Factory |Uniek in Microsoft Azure. Namen zijn case-ongevoelig, `MyDF` dat `mydf` wil zeggen, en verwijzen naar dezelfde gegevensfabriek. |<ul><li>Elke gegevensfabriek is gebonden aan precies één Azure-abonnement.</li><li>Objectnamen moeten beginnen met een letter of een getal en mogen alleen letters, cijfers en het streepje (-) teken bevatten.</li><li>Elk streepje (-) teken moet onmiddellijk worden voorafgegaan en gevolgd door een letter of een nummer. Opeenvolgende streepjes zijn niet toegestaan in containernamen.</li><li>Naam kan 3-63 tekens lang.</li></ul> |
| Gekoppelde services/gegevenssets/pijplijnen |Uniek met in een datafabriek. Namen zijn ongevoelig. |<ul><li>Objectnamen moeten beginnen met een letter, getal of een underscore (_).</li><li>Volgende tekens zijn niet toegestaan: "",+,',',','/',<",>',"",",""&,"""""""","","","","\\</li><li>Streepjes ("-") zijn niet toegestaan in de namen van gekoppelde services en alleen van gegevenssets.</li></ul>  |
| Resourcegroep |Uniek in Microsoft Azure. Namen zijn ongevoelig. | Zie [Azure-naamgevingsregels en -beperkingen voor](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)meer informatie. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het maken van gegevensfabrieken door stapsgewijze instructies te volgen in [Quickstart: maak een artikel in de gegevensfabriek.](quickstart-create-data-factory-powershell.md) 
