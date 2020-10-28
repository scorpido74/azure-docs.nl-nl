---
title: 'Zelfstudie: Aan de slag met het organiseren van pijplijnen'
description: In deze zelfstudie leert u hoe u pijplijnen en activiteiten kunt indelen met behulp van Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329880"
---
# <a name="orchestrate-with-pipelines"></a>Organiseren met pijplijnen

In deze zelfstudie leert u hoe u pijplijnen en activiteiten kunt indelen met behulp van Synapse Studio. 

## <a name="overview"></a>Overzicht

U kunt een groot aantal verschillende taken in Azure Synapse organiseren.

1. Ga in Synapse Studio naar de hub **Integreren** .
1. Selecteer **+**  > **Pijplijn** om een nieuwe pijplijn te maken.
1. Ga naar de hub **Ontwikkelen** en selecteer een van de notebooks die u eerder hebt gemaakt.
1. Sleep de notebook naar de pijplijn ( **Opmerking** : De stap importmodules toevoegen aan notebook, zoals aangegeven in het [document](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace), die vereist zijn tijdens de uitvoering vanaf de pijplijn)
1. Selecteer in de pijplijn **Trigger toevoegen** > **Nieuw/bewerken** .
1. In **Trigger kiezen** selecteert u **Nieuw** en stelt u **Terugkeerpatroon** in op Elk uur.
1. Selecteer **OK** . 
1. Selecteer **Alles publiceren** .
1. Als u de pijplijn onmiddellijk wilt uitvoeren zonder te wachten op het volgende uur, selecteert u **Trigger toevoegen** > **Nu activeren** .



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens visualiseren met Power BI](get-started-visualize-power-bi.md)
                                 
