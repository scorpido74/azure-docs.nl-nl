---
title: Monitorpijplijn voert Azure Synapse Studio uit (voorbeeld)
description: Gebruik de Azure Synapse Studio om de pijplijn van uw werkruimte te controleren.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430784"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Azure Synapse Studio gebruiken om de pijplijn van werkruimtes te controleren

Met Azure Synapse Analytics u complexe pijplijnen maken die uw gegevensverplaatsing, gegevenstransformatie en rekenactiviteiten binnen uw oplossing kunnen automatiseren en orkestreren. U deze pijplijnen maken en controleren met Azure Synapse Studio (voorbeeld).

In dit artikel wordt uitgelegd hoe u uw pijplijnuitvoeringen controleren, zodat u de laatste status, problemen en voortgang van uw pijplijnen in de gaten houden.

## <a name="access-the-list-of-pipeline-runs"></a>Toegang tot de lijst met pijplijnuitvoeringen

Als u de lijst met pijplijnuitvoeringen in uw werkruimte wilt bekijken, opent u eerst [de Azure Synapse Studio](https://web.azuresynapse.net/) en selecteert u uw werkruimte.

![Inloggen bij werkruimte](./media/common/login-workspace.png)

Nadat u uw werkruimte hebt geopend, selecteert u de sectie **Monitor** aan de linkerkant.

![Monitorhub selecteren](./media/common/left-nav.png)

Selecteer **Pijplijnuitvoeringen** om de lijst met pijplijnuitvoeringen weer te geven.

![Pijplijnuitvoeringen selecteren](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Het filteren van uw pijplijnloopt

U de lijst met pijplijnuitvoeringen filteren op de pijplijnuitvoeringen waarin u geïnteresseerd bent. Met de filters boven aan het scherm u een veld opgeven waarop u wilt filteren.

U bijvoorbeeld de weergave filteren om alleen de pijplijnuitvoeringen voor de pijplijn met de naam 'vakantie' te zien:

![Knop Filter](./media/common/filter-button.png)

![Voorbeeldfilter](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Details weergeven over een specifieke pijplijnrun

Als u details over uw pijplijnrun wilt weergeven, selecteert u de pijplijnuitvoering. Bekijk vervolgens de activiteitsruns die zijn gekoppeld aan de pijplijnrun. Als de pijplijn nog steeds actief is, u de voortgang controleren. 
  
## <a name="next-steps"></a>Volgende stappen

Zie het artikel Over het monitoren van toepassingen voor meer informatie over het monitoren van [toepassingen.](how-to-monitor-spark-applications.md) 
