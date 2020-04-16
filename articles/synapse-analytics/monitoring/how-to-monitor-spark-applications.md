---
title: Apache Spark-toepassingen controleren
description: Gebruik de Azure Synapse Studio om uw Apache Spark-toepassingen te controleren.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430745"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>De Azure Synapse Studio (preview) gebruiken om uw Apache Spark-toepassingen te controleren

Met Azure Synapse Analytics u Spark gebruiken om notitieblokken, taken en andere soorten toepassingen uit te voeren op uw Spark-pools in uw werkruimte.

In dit artikel wordt uitgelegd hoe u uw Spark-toepassingen controleren, zodat u de laatste status, problemen en voortgang in de gaten houden.

## <a name="accessing-the-list-of-spark-applications"></a>Toegang tot de lijst met Spark-toepassingen

Als u de lijst met Spark-toepassingen in uw werkruimte wilt bekijken, opent u eerst [de Azure Synapse Studio](https://web.azuresynapse.net/) en selecteert u uw werkruimte.

  > [!div class="mx-imgBorder"]
  > ![Inloggen bij werkruimte](./media/common/login-workspace.png)

Nadat u uw werkruimte hebt geopend, selecteert u de sectie **Monitor** aan de linkerkant.

  > [!div class="mx-imgBorder"]
  > ![Monitorhub selecteren](./media/common/left-nav.png)

Selecteer **Spark-toepassingen** om de lijst met Spark-toepassingen weer te geven.

  > [!div class="mx-imgBorder"]
  > ![Spark-toepassingen selecteren](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Uw Spark-toepassingen filteren

U de lijst met Spark-toepassingen filteren op de toepassingen waarin u geïnteresseerd bent. Met de filters boven aan het scherm u een veld opgeven waarop u wilt filteren.

U bijvoorbeeld de weergave filteren om alleen de Spark-toepassingen te zien die de naam 'verkoop' bevatten:

  > [!div class="mx-imgBorder"]
  > ![Knop Filter](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Voorbeeldfilter](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Details weergeven over een specifieke Spark-toepassing

Als u de details van een van uw Spark-toepassingen wilt bekijken, selecteert u de Spark-toepassing en bekijkt u de details. Als de Spark-toepassing nog steeds actief is, u de voortgang controleren.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel Monitor pipeline [runs Azure Synapse Studio](how-to-monitor-pipeline-runs.md) voor meer informatie over het bewaken van pijplijnuitvoeringen.  
