---
title: Azure Event Grid-Diagnostische logboeken inschakelen voor een onderwerp
description: In dit artikel vindt u stapsgewijze instructies voor het inschakelen van Diagnostische logboeken voor een Azure Event grid-onderwerp.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960501"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Diagnostische logboeken voor een Azure Event grid-onderwerp
Met Diagnostische instellingen kunnen Event Grid gebruikers publicatie-en bezorg fout logboeken vastleggen en weer geven op een van de volgende locaties: een Azure-opslag account, een Event Hub of een Log Analytics-werk ruimte. In dit artikel vindt u stapsgewijze instructies voor het inschakelen van Diagnostische logboeken voor een event grid-onderwerp.

## <a name="prerequisites"></a>Vereisten

- Een onderwerp over een ingericht gebeurtenis raster
- Een ingerichte bestemming voor het vastleggen van Diagnostische logboeken. Dit kan een van de volgende bestemmingen hebben:
    - Azure Storage-account
    - Event Hub
    - Log Analytics-werkruimte


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Stappen voor het inschakelen van Diagnostische logboeken voor een onderwerp

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Ga naar het event grid-onderwerp waarvoor u de instellingen voor Diagnostische logboeken wilt inschakelen. 
3. Selecteer **Diagnostische instellingen** onder **bewaking** in het menu links.
4. Selecteer op de pagina **Diagnostische instellingen** de optie **nieuwe diagnostische instelling toevoegen**. 
    
    ![Knop diagnostische instelling toevoegen](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Geef een **naam** op voor de diagnostische instelling. 

    ![Diagnostische instellingen-naam](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Schakel een of meer van de vastleg doelen in voor de logboeken en configureer ze vervolgens door een eerder gemaakte Capture-bron te selecteren. 
    - Als u **archiveren naar een opslag account**selecteert, selecteert u **opslag account-configureren**en selecteert u vervolgens het opslag account in uw Azure-abonnement. 

        ![Archiveren naar een Azure Storage-account](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Als u **Stream naar een event hub**selecteert, selecteert u **Event hub-configure**en selecteert u vervolgens de Event Hubs naam ruimte, Event hub en het toegangs beleid. 
        ![streamen naar een Event Hub](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Als u **verzenden naar log Analytics**selecteert, selecteert u de werk ruimte log Analytics.
        ![verzenden naar Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Selecteer de opties **DeliveryFailures** en **PublishFailures** in de sectie **logboek** . 
    ![de fouten te selecteren](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Selecteer **Opslaan**. Selecteer **X** in de rechter bovenhoek om de pagina te sluiten. 
9. Ga nu terug naar de pagina **Diagnostische instellingen** en controleer of er een nieuwe vermelding wordt weer gegeven in de tabel **Diagnostische instellingen** . 
    ![diagnostische instelling in de lijst](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     U kunt ook het verzamelen van alle metrische gegevens inschakelen voor het onderwerp. 

## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een [ondersteunings ticket](https://azure.microsoft.com/support/options/)openen. 
