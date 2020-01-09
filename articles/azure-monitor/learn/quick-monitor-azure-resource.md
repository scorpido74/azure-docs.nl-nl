---
title: Gegevens verzamelen van een virtuele machine van Azure met Azure Monitor | Microsoft Docs
description: Hier vindt u informatie over hoe u de extensie van de Log Analytics-agent-VM en het verzamelen van gegevens van de Azure-VM's met Log Analytics inschakelt.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 85c953c4acdc31cc6d79600951ba745346771b0c
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533970"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Quick Start: een Azure-resource bewaken met Azure Monitor
[Azure monitor](../overview.md) begint met het verzamelen van gegevens uit Azure-resources op het moment dat ze worden gemaakt. Deze Snelstartgids bevat een kort overzicht van de gegevens die automatisch worden verzameld voor een resource en hoe u deze kunt weer geven in de Azure Portal voor een bepaalde resource. U kunt later een configuratie toevoegen voor het verzamelen van aanvullende gegevens en u kunt naar het Azure Monitor menu gaan om dezelfde hulpprogram ma's te gebruiken voor toegang tot gegevens die zijn verzameld voor alle resources in uw abonnement.

Zie [Azure-resources bewaken met Azure monitor](../insights/monitor-azure-resource.md)voor meer gedetailleerde beschrijvingen van de bewakings gegevens die worden verzameld van Azure-resources.


## <a name="sign-in-to-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Overzichts pagina
Veel services bevatten bewakings gegevens op de **overzichts** pagina als een kort overzicht van de werking ervan. Dit is doorgaans gebaseerd op een subset van platform metrieken die zijn opgeslagen in Azure Monitor metrische gegevens.

1. Zoek een Azure-resource in uw abonnement.
2. Ga naar de pagina **overzicht** en kijk of er prestatie gegevens worden weer gegeven. Deze gegevens worden verzorgd door Azure Monitor. Het onderstaande voor beeld is de pagina **overzicht** voor een Azure Storage-account en u kunt zien dat er meerdere metrische gegevens worden weer gegeven.

    ![Overzichts pagina](media/quick-monitor-azure-resource/overview.png)

3. U kunt op een van de grafieken klikken om de gegevens in Metrics Explorer te openen, die hieronder worden beschreven.

## <a name="view-the-activity-log"></a>Het activiteiten logboek weer geven
Het activiteiten logboek biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement. Dit omvat gegevens over het moment waarop een resource wordt gemaakt of gewijzigd, wanneer een taak wordt gestart of wanneer een bepaalde bewerking plaatsvindt.

1. Selecteer aan de bovenkant van het menu voor uw resource het **activiteiten logboek**.
2. Het huidige filter is ingesteld op gebeurtenissen die betrekking hebben op uw resource. Als er geen gebeurtenissen worden weer geven, wijzigt u de **time span** om het tijds bereik te verg Roten.

    ![Activiteitenlogboek](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Als u gebeurtenissen van andere resources in uw abonnement wilt weer geven, wijzigt u de criteria in de filter of zelfs filter eigenschappen verwijderen.

    ![Activiteitenlogboek](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Metrische gegevens bekijken
Metrische gegevens zijn numerieke waarden die een aspect van uw resource op een bepaald moment beschrijven. Azure Monitor worden automatisch platform metrieken met een interval van één minuut van alle Azure-resources verzameld. U kunt deze metrische gegevens weer geven met metrische gegevens Verkenner.

1. Selecteer **metrische gegevens**onder het gedeelte **bewaking** van het menu van de resource. Hiermee opent u de metrische gegevens Verkenner met het bereik dat is ingesteld op uw resource.
2. Klik op **metrische gegevens toevoegen** om een metriek toe te voegen aan de grafiek.
   
   ![Metrics Explorer](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Selecteer een **waarde** in de vervolg keuzelijst en vervolgens een **aggregatie**. Hiermee definieert u hoe de verzamelde waarden voor elk tijds interval worden gesampled.

    ![Metrics Explorer](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Klik op **metrische gegevens toevoegen** om extra metrische en aggregatie combinaties toe te voegen aan de grafiek.

    ![Metrics Explorer](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u het activiteiten logboek en de metrische gegevens weer gegeven voor een Azure-resource die automatisch wordt verzameld door Azure Monitor. Bron logboeken bieden inzicht in de gedetailleerde werking van de resource, maar moeten worden geconfigureerd om te worden verzameld. Ga door naar de zelf studie voor het verzamelen van resource Logboeken in een Log Analytics-werk ruimte, waar ze kunnen worden geanalyseerd met behulp van logboek query's.

> [!div class="nextstepaction"]
> [Resource logboeken verzamelen en analyseren met Azure Monitor](tutorial-resource-logs.md)
