---
title: Regels voor het benoemen van Azure Data Factory entiteiten
description: Hierin worden de naamgevings regels voor Data Factory entiteiten beschreven.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: e8bfc5fe9a63ba54e055fe86af6a9bb88d7348d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666690"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory naamgevings regels
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [naamgevings regels in Data Factory](../naming-rules.md).

De volgende tabel bevat naamgevings regels voor Data Factory artefacten.

| Naam | Unieke naam | Validatie controles |
|:--- |:--- |:--- |
| Data Factory |Uniek in Microsoft Azure. Namen zijn niet hoofdletter gevoelig, dat wil zeggen, `MyDF` en `mydf` verwijzen naar dezelfde data factory. |<ul><li>Elk data factory is gekoppeld aan precies één Azure-abonnement.</li><li>Object namen moeten beginnen met een letter of een cijfer en mogen alleen letters, cijfers en het koppel teken (-) bevatten.</li><li>Elk streepje (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. Opeenvolgende streepjes zijn niet toegestaan in container namen.</li><li>De naam kan 3-63 tekens lang zijn.</li></ul> |
| Gekoppelde services/tabellen/pijp lijnen |Uniek met in een data factory. Namen zijn niet hoofdletter gevoelig. |<ul><li>Maximum aantal tekens in de naam van de tabel: 260.</li><li>Object namen moeten beginnen met een letter, cijfer of onderstrepings teken (_).</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ', ': ', '\\'</li></ul> |
| Resourcegroep |Uniek in Microsoft Azure. Namen zijn niet hoofdletter gevoelig. |<ul><li>Maximum aantal tekens: 1000.</li><li>De naam mag letters, cijfers en de volgende tekens bevatten: '-', ' _ ', ', ' en '. '</li></ul> |

