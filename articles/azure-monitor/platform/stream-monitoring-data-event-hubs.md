---
title: Azure-bewakingsgegevens streamen naar gebeurtenishub
description: Meer informatie over het streamen van uw Azure-bewakingsgegevens naar een gebeurtenishub om de gegevens in een SIEM- of analysetool van een partner te krijgen.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 32bc90cc069ac82641c3aa7692c900c60db7ba87
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733106"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Bewakingsgegevens van Azure naar een event hub
Azure Monitor biedt een complete oplossing voor volledige stackbewaking voor toepassingen en services in Azure, in andere clouds en on-premises. Naast het gebruik van Azure Monitor voor het analyseren van die gegevens en het gebruik ervan voor verschillende bewakingsscenario's, moet u deze mogelijk ook naar andere bewakingshulpprogramma's in uw omgeving verzenden. De meest effectieve methode om bewakingsgegevens in de meeste gevallen naar externe hulpprogramma's te streamen, is het gebruik van [Azure Event Hubs.](/azure/event-hubs/) In dit artikel vindt u een korte beschrijving van hoe u bewakingsgegevens van verschillende bronnen streamen naar een gebeurtenishub en koppelingen naar gedetailleerde richtlijnen.


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Voordat u streaming configureert voor een gegevensbron, moet u [een naamruimte voor gebeurtenishubs en gebeurtenishub maken.](../../event-hubs/event-hubs-create.md) Deze naamruimte en gebeurtenishub is de bestemming voor al uw bewakingsgegevens. Een naamruimte voor gebeurtenishubs is een logische groepering van gebeurtenishubs die hetzelfde toegangsbeleid delen, net zoals een opslagaccount afzonderlijke blobs binnen dat opslagaccount heeft. Houd rekening met de volgende details over de naamruimte van gebeurtenishubs en gebeurtenishubs die u gebruikt voor streamingmonitoringsgegevens:

* Met het aantal doorvoereenheden u de doorvoerschaal voor uw gebeurtenishubs verhogen. Er is doorgaans slechts één doorvoereenheid nodig. Als u moet opschalen naarmate het gebruik van uw logboek toeneemt, u handmatig het aantal doorvoereenheden voor de naamruimte verhogen of automatische inflatie inschakelen.
* Met het aantal partities u het verbruik over veel consumenten parallel lopen. Een enkele partitie kan maximaal 20 MBps of ongeveer 20.000 berichten per seconde ondersteunen. Afhankelijk van het gereedschap dat de gegevens verbruikt, kan het al dan niet worden ondersteund consumeren van meerdere partities. Vier partities is redelijk om te beginnen als u niet zeker bent over als u niet zeker bent over het aantal partities in te stellen.
* U stelt het behoud van berichten op uw gebeurtenishub in op ten minste 7 dagen. Als uw verbruikende tool meer dan een dag uitvalt, zorgt dit ervoor dat de tool verder kan gaan waar het was gebleven voor gebeurtenissen tot 7 dagen oud.
* U moet de standaardconsumentengroep gebruiken voor uw gebeurtenishub. Het is niet nodig om andere consumentengroepen te maken of een aparte consumentengroep te gebruiken, tenzij u van plan bent om twee verschillende hulpprogramma's dezelfde gegevens van dezelfde gebeurtenishub te laten verbruiken.
* Voor het azure-activiteitenlogboek kiest u een naamruimte voor gebeurtenishubs en Azure Monitor maakt een gebeurtenishub binnen die naamruimte die _insights-logs-operational-logs_wordt genoemd. Voor andere logboektypen u een bestaande gebeurtenishub kiezen of Azure Monitor per gebeurteniscategorie een gebeurtenishub laten maken.
* Uitgaande poort 5671 en 5672 moeten doorgaans worden geopend op de computer of VNET die gegevens van de gebeurtenishub verbruikt.

## <a name="monitoring-data-available"></a>Beschikbare monitoringgegevens
[Bronnen van bewakingsgegevens voor Azure Monitor](data-sources.md) beschrijven de verschillende gegevenslagen voor Azure-toepassingen en de soorten bewakingsgegevens die voor elk van deze gegevens beschikbaar zijn. In de volgende tabel worden elk van deze lagen weergegeven en een beschrijving van hoe die gegevens naar een gebeurtenishub kunnen worden gestreamd. Volg de links voor meer details.

| Laag | Gegevens | Methode |
|:---|:---|:---|
| [Azure-tenant](data-sources.md#azure-tenant) | Azure Active Directory-controlelogboeken | Configureer een tenantdiagnostische instelling op uw AAD-tenant. Zie [Zelfstudie: Azure Active Directory-logboeken streamen naar een Azure-gebeurtenishub](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) voor meer informatie. |
| [Azure-abonnement](data-sources.md#azure-subscription) | Azure-activiteitenlogboek | Maak een logboekprofiel om activiteitenlogboekgebeurtenissen te exporteren naar gebeurtenishubs.  Zie [Azure-platformlogboeken streamen naar Azure Event Hubs](resource-logs-stream-event-hubs.md) voor meer informatie. |
| [Azure-resources](data-sources.md#azure-resources) | Platformstatistieken<br> Resourcelogboeken |Beide typen gegevens worden naar een gebeurtenishub verzonden met behulp van een diagnostische instelling voor resources. Zie [Azure-bronlogboeken streamen naar een gebeurtenishub](resource-logs-stream-event-hubs.md) voor meer informatie. |
| [Besturingssysteem (gast)](data-sources.md#operating-system-guest) | Virtuele machines van Azure | Installeer de [Azure Diagnostics Extension](diagnostics-extension-overview.md) op virtuele Windows- en Linux-machines in Azure. Zie [Azure Diagnostics-gegevens streamen in het hot pad door Gebeurtenishubs te gebruiken](diagnostics-extension-stream-event-hubs.md) voor meer informatie over Windows VM's en Linux Diagnostische extensie gebruiken om statistieken en [logboeken te controleren](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) voor meer informatie over Linux-VM's. |
| [Toepassingscode](data-sources.md#application-code) | Application Insights | Application Insights biedt geen directe methode om gegevens naar gebeurtenishubs te streamen. U [continue export](../../azure-monitor/app/export-telemetry.md) van de Toepassingsstatistieken-gegevens naar een opslagaccount instellen en vervolgens een Logische App gebruiken om de gegevens naar een gebeurtenishub te verzenden, zoals beschreven in Handmatig streamen met Logic [App.](#manual-streaming-with-logic-app) |

## <a name="manual-streaming-with-logic-app"></a>Handmatig streamen met Logic App
Voor gegevens die u niet rechtstreeks naar een gebeurtenishub streamen, u naar Azure-opslag schrijven en vervolgens een logische app met tijdgeactiveerde gebruiken die [gegevens uit blob-opslag haalt](../../connectors/connectors-create-api-azureblobstorage.md#add-action) en deze als bericht naar de [gebeurtenishub duwt.](../../connectors/connectors-create-api-azure-event-hubs.md#add-action) 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partnerhulpprogramma's met Azure Monitor-integratie

Als u uw bewakingsgegevens routert naar een gebeurtenishub met Azure Monitor, u eenvoudig integreren met externe SIEM- en bewakingstools. Voorbeelden van hulpprogramma's met Azure Monitor-integratie zijn de volgende:

| Hulpprogramma | Gehost in Azure | Beschrijving |
|:---|:---| :---|
|  IBM QRadar | Nee | Het Microsoft Azure DSM- en Microsoft Azure Event Hub Protocol zijn beschikbaar om te downloaden vanaf [de ibm-ondersteuningswebsite.](https://www.ibm.com/support) Meer informatie over de integratie met Azure vindt u bij [QRadar DSM-configuratie.](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) |
| Splunk | Nee | [De Azure Monitor Add-On voor Splunk](https://splunkbase.splunk.com/app/3534/) is een open source-project dat beschikbaar is in Splunkbase. De documentatie is beschikbaar bij [Azure Monitor Addon For Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Als u een add-on niet installeren in uw Splunk-exemplaar, als u bijvoorbeeld een proxy gebruikt of op Splunk Cloud wordt uitgevoerd, u deze gebeurtenissen doorsturen naar de Splunk HTTP-gebeurtenisverzamelaar met [Azure-functie voor Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), die wordt geactiveerd door nieuwe berichten in de gebeurtenishub. |
| SumoLogic | Nee | Instructies voor het instellen van SumoLogic om gegevens uit een gebeurtenishub te gebruiken, zijn beschikbaar bij [Logboeken verzamelen voor de Azure Audit App van Gebeurtenishub.](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) |
| ArcSight | Nee | De ArcSight Azure Event Hub smart connector is beschikbaar als onderdeel van [de ArcSight smart connector collection.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Syslog-server | Nee | Als u Azure Monitor-gegevens rechtstreeks naar een syslog-server wilt streamen, u een oplossing gebruiken [op basis van een Azure-functie.](https://github.com/miguelangelopereira/azuremonitor2syslog/)
| LogRhythm | Nee| Instructies voor het instellen van LogRhythm om logboeken te verzamelen van een gebeurtenishub zijn [hier](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)beschikbaar. 
|Logz.io | Ja | Zie [Aan de slag met het monitoren en loggen met Logz.io voor Java-apps die op Azure worden uitgevoerd,](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen
* [Het activiteitenlogboek archiveren naar een opslagaccount](../../azure-monitor/platform/archive-activity-log.md)
* [Lees het overzicht van het Azure Activity-logboek](../../azure-monitor/platform/platform-logs-overview.md)
* [Een waarschuwing instellen op basis van een gebeurtenis activiteitslogboek](../../azure-monitor/platform/alerts-log-webhook.md)


