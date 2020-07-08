---
title: Verbinding maken met GitHub
description: Gebruik GitHub om uw algemene gegevens model entiteit verwijzingen op te geven
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771033"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Gebruik GitHub om veelvoorkomende gegevens model entiteit verwijzingen te lezen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De GitHub-connector in Azure Data Factory wordt alleen gebruikt voor het ontvangen van het verwijzings schema van de entiteit voor de [gemeen schappelijke gegevens model](format-common-data-model.md) indeling in gegevens stroom toewijzen.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde GitHub-service.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **github**. | ja
| userName | GitHub-gebruikers naam | ja |
| wachtwoord | GitHub-wacht woord | ja |

## <a name="next-steps"></a>Volgende stappen

Maak een [bron-gegevensset](data-flow-source.md) in gegevens stroom toewijzen.