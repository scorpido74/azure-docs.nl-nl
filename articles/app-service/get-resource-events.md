---
title: Resource gebeurtenissen in Azure App Service ophalen
description: Meer informatie over het ophalen van bron gebeurtenissen via activiteiten logboeken en Event Grid op uw App Service-app.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 1fd283f95823a67319dc467a3a1d6251193182da
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124734"
---
# <a name="get-resource-events-in-azure-app-service"></a>Resource gebeurtenissen in Azure App Service ophalen

Azure App Service biedt ingebouwde hulpprogram ma's voor het bewaken van de status en status van uw resources. Resource gebeurtenissen helpt u bij het begrijpen van alle wijzigingen die zijn aangebracht in uw onderliggende web-app-resources en om indien nodig actie te ondernemen. Voor beelden van gebeurtenissen zijn: schalen van instanties, updates van toepassings instellingen, opnieuw starten van de web-app en nog veel meer. In dit artikel leert u hoe u [Azure-activiteiten logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) kunt weer geven en [Event grid](https://docs.microsoft.com/azure/event-grid/) om bron gebeurtenissen te bewaken die betrekking hebben op uw app service Web-app.

> [!NOTE]
> App Service integratie met Event Grid is in **Preview**. [Bekijk de aankondiging voor meer informatie.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Activiteiten logboeken van Azure weer geven
Activiteiten logboeken van Azure bevatten bron gebeurtenissen die worden verzonden door bewerkingen die zijn uitgevoerd op de resources in uw abonnement. Zowel de gebruikers acties in de Azure Portal als Azure Resource Manager sjablonen dragen bij aan de gebeurtenissen die worden vastgelegd door het activiteiten logboek. 

Azure-activiteiten logboeken voor App Service Details, zoals:
- welke bewerkingen zijn uitgevoerd voor de resources (bijvoorbeeld: App Service-abonnementen)
- wie de bewerking heeft gestart
- Wanneer de bewerking is uitgevoerd
- de status van de bewerking
- eigenschaps waarden die u helpen bij het onderzoeken van de bewerking

### <a name="what-can-you-do-with-azure-activity-logs"></a>Wat kunt u doen met Azure-activiteiten logboeken?

De activiteiten logboeken van Azure kunnen worden opgevraagd met behulp van de Azure Portal, Power shell, REST API of CLI. U kunt de logboeken verzenden naar een opslag account, Event hub en Log Analytics. U kunt ze ook analyseren in Power BI of waarschuwingen maken om de resource gebeurtenissen bijgewerkt te houden.

[Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>Activiteiten logboeken verzenden naar Event Grid

Hoewel activiteiten logboeken op basis van een gebruiker zijn gebaseerd, is er een nieuwe [Event grid](https://docs.microsoft.com/azure/event-grid/) integratie met app service (preview) waarmee zowel gebruikers acties als geautomatiseerde gebeurtenissen worden geregistreerd. Met Event Grid kunt u een handler configureren om te reageren op de genoemde gebeurtenissen. Gebruik Event Grid bijvoorbeeld om direct een serverloze functie te triggeren voor het uitvoeren van beeldanalyse zodra er een nieuwe foto wordt toegevoegd aan de container voor blob-opslag.

U kunt Event Grid ook gebruiken met Logic Apps om op elke locatie gegevens te verwerken, zonder dat u hiervoor code hoeft te schrijven. Event Grid verbindt gegevensbronnen en gebeurtenis-handlers. Gebruik Event Grid bijvoorbeeld om direct een serverloze functie te triggeren voor het uitvoeren van beeldanalyse zodra er een nieuwe foto wordt toegevoegd aan de container voor blob-opslag.

### <a name="supported-event-types"></a>Ondersteunde gebeurtenis typen
| Gebeurtenistype |Beschrijving|
| -----------| ------------- |
| Micro soft. web/sites | Ding |
| BackupOperationCompleted |De back-up van de webapp is voltooid|
| BackupOperationFailed | Kan geen back-up maken van de webapp|
| RestoreOperationStarted |Het terugzetten vanuit de back-up is gestart|
| RestoreOperationCompleted |Het terugzetten van de back-up is voltooid|
| RestoreOperationFailed |Herstellen vanaf back-up is mislukt|
| SlotSwapStarted |Wisseling van sleuven is gestart|
| SlotSwapCompleted |Het wisselen van de sleuf is voltooid|
| SlotSwapFailed |Wisseling van sleuf is mislukt|
| SlotSwapWithPreviewStarted |Sleuf wisseling met preview is gestart|
| SlotSwapWithPreviewCancelled |Wisseling van sleuven met preview is mislukt|
| AppUpdated | |
| Gestart | De webapp is opnieuw gestart |
| Gestopt | De webapp is gestopt |
| ChangedAppSettings | App-instellingen op de webapp zijn gewijzigd |
| - | - |
| Micro soft. web/server farms | (App Service plan) |
| AspUpdated | Het app service-plan is bijgewerkt. Het gebeurtenis object bevat details over de eigenschappen die zijn gewijzigd. |
| Omhoog/omlaag schalen | Het app service-plan is omhoog of omlaag geschaald. Het gebeurtenis object bevat het aantal instanties.|


## <a name="next-steps"></a><a name="nextsteps"></a>Volgende stappen
* [Query's uitvoeren op Logboeken met Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Azure App Service bewaken](web-sites-monitor.md)
* [Problemen met Azure App Service oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [App-Logboeken in HDInsight analyseren](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
