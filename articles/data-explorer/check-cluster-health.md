---
title: De status van een Azure Data Explorer-cluster controleren
description: In dit artikel worden stappen beschreven om de status van uw Azure Data Explorer-cluster te controleren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60861291"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>De status van een Azure Data Explorer-cluster controleren

Er zijn verschillende factoren die van invloed zijn op de status van een Azure Data Explorer-cluster, waaronder CPU, geheugen en het subsysteem schijf. In dit artikel ziet u enkele basisstappen die u nemen om de status van een cluster te meten.

1. Log hier [https://dataexplorer.azure.com](https://dataexplorer.azure.com)in

1. Selecteer in het linkerdeelvenster het cluster en voer de volgende opdracht uit.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Een output van 1 is gezond; een uitvoer van 0 is ongezond.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en navigeer naar uw cluster.

1. Selecteer **onder Controleren**de optie **Metriek**en selecteer In leven **houden,** zoals weergegeven in de volgende afbeelding. Een uitvoer dicht bij 1 betekent een gezonde cluster.

    ![Cluster Keep Alive- statistiek](media/check-cluster-health/portal-metrics.png)

1. Het is mogelijk om andere statistieken toe te voegen aan de grafiek. Selecteer de grafiek en **voeg metrische gegevens toe**. Selecteer een andere statistiek - in dit voorbeeld wordt **CPU**weergegeven .

    ![Waarde toevoegen](media/check-cluster-health/add-metric.png)

1. Als u hulp nodig hebt bij het diagnosticeren van problemen met de status van een cluster, opent u een ondersteuningsaanvraag in de [Azure-portal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)