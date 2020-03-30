---
title: Een Azure-bron bewaken met Azure Monitor
description: Meer informatie over het verzamelen en analyseren van gegevens voor een Azure-bron in Azure Monitor.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: c4b80e62d3800392b847a411dfc66c3278e72bba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77661861"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Snelstart: een Azure-bron controleren met Azure Monitor
[Azure Monitor](../overview.md) begint met het verzamelen van gegevens uit Azure-bronnen op het moment dat ze worden gemaakt. Deze quickstart biedt een korte doorloop van de gegevens die automatisch worden verzameld voor een resource en hoe deze te bekijken in de Azure-portal voor een bepaalde bron. Later u configuratie toevoegen om extra gegevens te verzamelen en u naar het menu Azure Monitor gaan om dezelfde hulpprogramma's te gebruiken om toegang te krijgen tot gegevens die zijn verzameld voor alle bronnen in uw abonnement.

Zie [Azure-bronnen bewaken met Azure Monitor](../insights/monitor-azure-resource.md)voor meer gedetailleerde beschrijvingen van bewakingsgegevens die zijn verzameld uit Azure-bronnen.


## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op . 


## <a name="overview-page"></a>Overzichtspagina
Veel services bevatten monitoringgegevens op hun **overzichtspagina** als een snelle blik op hun werking. Dit is meestal gebaseerd op een subset van platformstatistieken die zijn opgeslagen in Azure Monitor Metrics.

1. Zoek een Azure-bron in uw abonnement.
2. Ga naar de **overzichtspagina** en noteer of er prestatiegegevens worden weergegeven. Deze gegevens worden geleverd door Azure Monitor. Het voorbeeld hieronder is de **overzichtspagina** voor een Azure-opslagaccount en u zien dat er meerdere statistieken worden weergegeven.

    ![Overzichtspagina](media/quick-monitor-azure-resource/overview.png)

3. U op een van de grafieken klikken om de gegevens te openen in metrics explorer die hieronder wordt beschreven.

## <a name="view-the-activity-log"></a>Het activiteitenlogboek weergeven
Het logboek Activiteit biedt inzicht in de bewerkingen van elke Azure-bron in het abonnement. Dit omvat informatie zoals wanneer een resource wordt gemaakt of gewijzigd, wanneer een taak wordt gestart of wanneer een bepaalde bewerking plaatsvindt.

1. Selecteer **Activiteitslogboek**boven aan het menu voor uw resource .
2. Het huidige filter is ingesteld op gebeurtenissen die betrekking hebben op uw resource. Als u geen gebeurtenissen ziet, probeert u de **tijdspanne** te wijzigen om het tijdsbereik te vergroten.

    ![Activiteitenlogboek](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Als u gebeurtenissen uit andere bronnen in uw abonnement wilt zien, wijzigt u de criteria in het filter of verwijdert u zelfs filtereigenschappen.

    ![Activiteitenlogboek](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Metrische gegevens bekijken
Statistieken zijn numerieke waarden die een bepaald aspect van uw resource op een bepaald moment beschrijven. Azure Monitor verzamelt automatisch platformstatistieken met intervallen van één minuut uit alle Azure-bronnen. U deze statistieken bekijken met behulp van metrics explorer.

1. Selecteer **statistieken**onder het gedeelte **Controle** van het menu van uw resource . Hiermee wordt metrics explorer geopend met de scope die is ingesteld op uw resource.
2. Klik **op Statistiek toevoegen** om een statistiek aan de grafiek toe te voegen.
   
   ![Statistieken explorer](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Selecteer een **statistiek** in de vervolgkeuzelijst en vervolgens een **aggregatie**. Dit definieert hoe de verzamelde waarden worden bemonsterd over elk tijdsinterval.

    ![Statistieken explorer](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Klik **op Statistiek toevoegen** om extra metrische en aggregatiecombinaties aan de grafiek toe te voegen.

    ![Statistieken explorer](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u het activiteitenlogboek en de statistieken voor een Azure-bron bekeken die automatisch worden verzameld door Azure Monitor. Resourcelogboeken geven inzicht in de gedetailleerde werking van de resource, maar moeten worden geconfigureerd om te worden verzameld. Ga verder naar de zelfstudie voor het verzamelen van bronlogboeken in een werkruimte log Analytics waar ze kunnen worden geanalyseerd met behulp van logboekquery's.

> [!div class="nextstepaction"]
> [Bronlogboeken verzamelen en analyseren met Azure Monitor](tutorial-resource-logs.md)
