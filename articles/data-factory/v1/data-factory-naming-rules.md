---
title: Regels voor het benoemen van Azure Data Factory-entiteiten
description: Beschrijft naamgevingsregels voor entiteiten in Gegevensfabriek.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8fa1340b586434bf98d51437d4dc6b08594f0afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931894"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - naamgevingsregels
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de gegevensfabriekservice gebruikt, raadpleegt u [naamgevingsregels in Gegevensfabriek](../naming-rules.md).

In de volgende tabel vindt u naamgevingsregels voor artefacten van Gegevensfabriek.

| Name | Naam uniciteit | Validatiecontroles |
|:--- |:--- |:--- |
| Data Factory |Uniek in Microsoft Azure. Namen zijn case-ongevoelig, `MyDF` dat `mydf` wil zeggen, en verwijzen naar dezelfde gegevensfabriek. |<ul><li>Elke gegevensfabriek is gebonden aan precies één Azure-abonnement.</li><li>Objectnamen moeten beginnen met een letter of een getal en mogen alleen letters, cijfers en het streepje (-) teken bevatten.</li><li>Elk streepje (-) teken moet onmiddellijk worden voorafgegaan en gevolgd door een letter of een nummer. Opeenvolgende streepjes zijn niet toegestaan in containernamen.</li><li>Naam kan 3-63 tekens lang.</li></ul> |
| Gekoppelde services/tabellen/pijplijnen |Uniek met in een datafabriek. Namen zijn ongevoelig. |<ul><li>Maximum aantal tekens in een tabelnaam: 260.</li><li>Objectnamen moeten beginnen met een letter, getal of een underscore (_).</li><li>Volgende tekens zijn niet toegestaan: "",+,',',','/',<",>',"",",""&,"""""""","","","","\\</li></ul> |
| Resourcegroep |Uniek in Microsoft Azure. Namen zijn ongevoelig. |<ul><li>Maximum aantal tekens: 1000.</li><li>De naam kan letters, cijfers en de volgende tekens bevatten: '-','''' en ''.</li></ul> |

