---
title: Azure Event Grid - Diagnostische logboeken inschakelen voor een onderwerp
description: In dit artikel vindt u stapsgewijze instructies voor het inschakelen van diagnostische logboeken voor een Azure-gebeurtenisrasteronderwerp.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960501"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Diagnostische logboeken voor een Azure-gebeurtenisrasteronderwerp
Diagnostische instellingen stellen gebruikers van eventgrid in staat om publicatie- en leveringsfoutlogboeken vast te leggen en weer te geven op een van de volgende plaatsen: een Azure-opslagaccount, een gebeurtenishub of een log-analysewerkruimte. In dit artikel worden stapsgewijze instructies gegeven om diagnostische logboeken voor een gebeurtenisrasteronderwerp in te schakelen.

## <a name="prerequisites"></a>Vereisten

- Een ingerichte gebeurtenisrasteronderwerp
- Een ingerichte bestemming voor het vastleggen van diagnostische logboeken. Het kan een van de volgende bestemmingen:
    - Azure Storage-account
    - Event Hub
    - Log Analytics-werkruimte


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Stappen voor het inschakelen van diagnostische logboeken voor een onderwerp

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer naar het gebeurtenisrasteronderwerp waarvoor u diagnostische logboekinstellingen wilt inschakelen. 
3. Selecteer **Diagnostische instellingen** onder **Controleren** in het linkermenu.
4. Selecteer **op** de pagina Diagnostische instellingen de optie **Nieuwe diagnostische instelling toevoegen**. 
    
    ![Knop Diagnostische instelling toevoegen](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Geef een **naam** op voor de diagnostische instelling. 

    ![Diagnostische instellingen - naam](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Schakel een of meer van de opnamebestemmingen voor de logboeken in en configureer deze door een eerder gemaakte opnamebron te selecteren. 
    - Als u **Archiveren selecteert in een opslagaccount,** selecteert u **Opslagaccount - Configureren**en selecteert u vervolgens het opslagaccount in uw Azure-abonnement. 

        ![Archiveren naar een Azure-opslagaccount](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Als u **Streamen naar een gebeurtenishub**selecteert, selecteert u **Gebeurtenishub - Configureren**en selecteert u vervolgens de naamruimte van gebeurtenishubs, de gebeurtenishub en het toegangsbeleid. 
        ![Streamen naar een gebeurtenishub](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Als u **Verzenden naar logboekanalyses**selecteert, selecteert u de werkruimte Log Analytics.
        ![Verzenden naar Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Selecteer de opties **DeliveryFailures** en **PublishFailures** in de sectie **Logboek.** 
    ![Selecteer de fouten](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Selecteer **Opslaan**. Selecteer **X** in de rechterhoek om de pagina te sluiten. 
9. Bevestig nu op de pagina **Diagnostische instellingen** of u een nieuw item ziet in de tabel **Diagnostische instellingen.** 
    ![Diagnostische instelling in de lijst](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     U ook het verzamelen van alle statistieken voor het onderwerp inschakelen. 

## <a name="next-steps"></a>Volgende stappen
Als je meer hulp nodig hebt, plaats je je probleem op het [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) of open je een [ondersteuningsticket.](https://azure.microsoft.com/support/options/) 
