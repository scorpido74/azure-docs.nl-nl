---
title: Apache Spark toepassingen bewaken
description: Gebruik Azure Synapse Studio om uw Apache Spark-toepassingen te bewaken.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430745"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Azure Synapse Studio (preview) gebruiken om uw Apache Spark-toepassingen te bewaken

Met Azure Synapse Analytics kunt u Spark gebruiken om notitie blokken, taken en andere soorten toepassingen uit te voeren in uw Spark-Pools in uw werk ruimte.

In dit artikel wordt uitgelegd hoe u uw Spark-toepassingen kunt bewaken, zodat u op de hoogte kunt blijven van de meest recente status, problemen en voortgang.

## <a name="accessing-the-list-of-spark-applications"></a>Toegang tot de lijst met Spark-toepassingen

Als u de lijst met Spark-toepassingen in uw werk ruimte wilt zien, opent u eerst [Azure Synapse Studio](https://web.azuresynapse.net/) en selecteert u uw werk ruimte.

  > [!div class="mx-imgBorder"]
  > ![Aanmelden bij werk ruimte](./media/common/login-workspace.png)

Wanneer u uw werk ruimte hebt geopend, selecteert u de sectie **monitor** aan de linkerkant.

  > [!div class="mx-imgBorder"]
  > ![Hub bewaken selecteren](./media/common/left-nav.png)

Selecteer **Spark-toepassingen** om de lijst met Spark-toepassingen weer te geven.

  > [!div class="mx-imgBorder"]
  > ![Spark-toepassingen selecteren](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Uw Spark-toepassingen filteren

U kunt de lijst met Spark-toepassingen filteren op de items waarin u bent geïnteresseerd. Met de filters boven aan het scherm kunt u een veld opgeven waarop u wilt filteren.

U kunt bijvoorbeeld de weer gave filteren om alleen de Spark-toepassingen weer te geven die de naam ' Sales ' bevatten:

  > [!div class="mx-imgBorder"]
  > ![Knop filteren](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Voorbeeld filter](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Details over een specifieke Spark-toepassing weer geven

Als u de details van een van uw Spark-toepassingen wilt weer geven, selecteert u de Spark-toepassing en bekijkt u de details. Als de Spark-toepassing nog steeds wordt uitgevoerd, kunt u de voortgang bewaken.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [Azure Synapse Studio](how-to-monitor-pipeline-runs.md) voor meer informatie over het bewaken van pijplijn uitvoeringen.  
