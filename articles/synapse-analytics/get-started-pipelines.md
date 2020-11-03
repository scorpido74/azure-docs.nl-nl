---
title: 'Zelfstudie: aan de slag met het integreren met pijplijnen'
description: In deze zelfstudie leert u hoe u pijplijnen en activiteiten kunt integreren met behulp van Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744926"
---
# <a name="integrate-with-pipelines"></a>Integreren met pijplijnen

In deze zelfstudie leert u hoe u pijplijnen en activiteiten kunt integreren met behulp van Synapse Studio. 

## <a name="overview"></a>Overzicht

U kunt een groot aantal verschillende taken in Azure Synapse integreren.

1. Ga in Synapse Studio naar de hub **Integreren**.
1. Selecteer **+**  > **Pijplijn** om een nieuwe pijplijn te maken.
1. Ga naar de hub **Ontwikkelen** en selecteer een van de notebooks die u eerder hebt gemaakt.
1. Sleep de notebook naar de pijplijn ( **Opmerking** : De stap Importmodules toevoegen aan notebook, zoals aangegeven in het [document](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace), die vereist zijn tijdens de uitvoering vanaf de pijplijn)
1. Selecteer in de pijplijn **Trigger toevoegen** > **Nieuw/bewerken**.
1. In **Trigger kiezen** selecteert u **Nieuw** en stelt u **Terugkeerpatroon** in op Elk uur.
1. Selecteer **OK**. 
1. Selecteer **Alles publiceren**.
1. Als u de pijplijn onmiddellijk wilt uitvoeren zonder te wachten op het volgende uur, selecteert u **Trigger toevoegen** > **Nu activeren**.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens visualiseren met Power BI](get-started-visualize-power-bi.md)
                                 
