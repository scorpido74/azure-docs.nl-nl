---
title: Overzicht van Azure-platformlogboeken | Microsoft Documenten
description: Overzicht van logboeken in Azure Monitor die uitgebreide, frequente gegevens bevatten over de werking van een Azure-bron.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659317"
---
# <a name="overview-of-azure-platform-logs"></a>Overzicht van Azure-platformlogboeken
Platformlogboeken bieden gedetailleerde diagnostische en controle-informatie voor Azure-resources en het Azure-platform waarop ze afhankelijk zijn. Ze worden automatisch gegenereerd, hoewel u bepaalde platformlogboeken moet configureren om te worden doorgestuurd naar een of meer bestemmingen die moeten worden bewaard. In dit artikel vindt u een overzicht van platformlogboeken, inclusief welke informatie ze verstrekken en hoe u ze configureren voor verzameling en analyse.

## <a name="types-of-platform-logs"></a>Typen platformlogboeken
In de volgende tabel worden de specifieke platformlogboeken weergegeven die beschikbaar zijn op verschillende lagen Azure.

| Logboek | Laag | Beschrijving |
|:---|:---|:---|
| Resourcelogboeken | Azure-bronnen | Geef inzicht in bewerkingen die zijn uitgevoerd binnen een Azure-bron (het *gegevensvlak),* bijvoorbeeld het verkrijgen van een geheim uit een Key Vault of het indienen van een verzoek naar een database. De inhoud van resourcelogboeken verschilt per Azure-service en resourcetype.<br><br>*Resourcelogboeken werden voorheen diagnostische logboeken genoemd.*  |
| Activiteitenlogboek | Azure-abonnement | Biedt inzicht in de bewerkingen op elke Azure-bron in het abonnement van buitenaf *(het beheervlak)* naast updates voor servicestatusgebeurtenissen. Gebruik het activiteitenlogboek om te bepalen _wat,_ _wie_en _wanneer_ voor schrijfbewerkingen (PUT, POST, DELETE) die zijn genomen op basis van de bronnen in uw abonnement. U ook de status van de bewerking en andere relevante eigenschappen begrijpen.  Er is één activiteitslogboek voor elk Azure-abonnement. |
| Azure Active Directory-logboeken | Azure-tenant |  Bevat de geschiedenis van aanmeldingsactiviteit en controlespoor van wijzigingen die zijn aangebracht in de Azure Active Directory voor een bepaalde tenant. Zie [Wat zijn Azure Active Directory-rapporten?](../../active-directory/reports-monitoring/overview-reports.md) Voor een volledige beschrijving van Azure Active Directory-logboeken.   |

> [!NOTE]
> Het Azure-activiteitenlogboek is voornamelijk bedoeld voor activiteiten die plaatsvinden in Azure Resource Manager. Het houdt geen resources bij met behulp van het Classic/RDFE-model. Sommige klassieke resourcetypen hebben een proxyresourceprovider in Azure Resource Manager (bijvoorbeeld Microsoft.ClassicCompute). Als u interactie hebt met een klassiek resourcetype via Azure Resource Manager met behulp van deze proxyresourceproviders, worden de bewerkingen weergegeven in het activiteitenlogboek. Als u interactie hebt met een klassiek resourcetype buiten de proxy's van Azure Resource Manager, worden uw acties alleen opgenomen in het bewerkingslogboek. Het operationeel logboek kan worden doorzocht in een apart gedeelte van de portal.

![Overzicht van platformlogboeken](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Logboeken van platforms weergeven
Er zijn verschillende opties voor het bekijken en analyseren van de verschillende Azure-platformlogboeken.

- Bekijk het activiteitenlogboek in de Azure-portal en krijg toegang tot gebeurtenissen vanuit PowerShell en CLI. Zie [Azure Activity-logboekgebeurtenissen weergeven en ophalen](activity-log-view.md) voor meer informatie. 
- Bekijk Azure Active Directory Security and Activity-rapporten in de Azure-portal. Zie [Wat zijn Azure Active Directory-rapporten?](../../active-directory/reports-monitoring/overview-reports.md)  voor meer informatie.
- Resourcelogboeken worden automatisch gegenereerd door ondersteunde Azure-bronnen, maar ze zijn niet beschikbaar om te worden bekeken, tenzij u ze naar een [bestemming](#destinations)verzendt. 

## <a name="destinations"></a>Bestemmingen
U platformlogboeken naar een of meer van de bestemmingen in de volgende tabel verzenden, afhankelijk van uw bewakingsvereisten. Configureer bestemmingen voor platformlogboeken door [een diagnostische instelling te maken.](diagnostic-settings.md)

| Doel | Scenario | Verwijzingen |
|:---|:---|:---|:---|
| Log Analytics-werkruimte | Analyseer de logboeken met andere bewakingsgegevens en maak gebruik van Azure Monitor-functies, zoals logboekquery's en waarschuwingen. | [Logboeken voor activiteit en bronlogboeken](resource-logs-collect-workspace.md)<br>[Azure Activity Directory-logboeken](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Archiveer de logboeken voor controle, statische analyse of back-up. |[Logboeken voor activiteit en bronlogboeken](archive-diagnostic-logs.md)<br>[Azure Activity Directory-logboeken](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | Stream de logboeken naar logboekregistratie- en telemetriesystemen van derden.  |[Logboeken voor activiteit en bronlogboeken](resource-logs-stream-event-hubs.md)<br>[Azure Activity Directory-logboeken](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Volgende stappen

* [Het programma van het activiteitenlogboek voor verschillende categorieën weergeven](activity-log-schema.md)
* [Het resourcelogboekschema voor verschillende Azure-services weergeven](diagnostic-logs-schema.md)
