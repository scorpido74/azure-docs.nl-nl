---
title: Verbinding maken met GitHub
description: Gebruik GitHub om uw algemene gegevens model entiteit verwijzingen op te geven
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 3ad666a477595b8367b388c4343df8aaed561a87
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435441"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Gebruik GitHub om veelvoorkomende gegevens model entiteit verwijzingen te lezen

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