---
title: Azure-bewakings gegevens streamen naar Event Hub
description: Meer informatie over het streamen van uw Azure-bewakings gegevens naar een Event Hub om de gegevens op te halen in een partner SIEM of een analyse programma.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: b22f779d616751ebaa3dad853d5aa23ec4969f23
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658860"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Azure-bewakings gegevens streamen naar een Event Hub
Azure Monitor biedt een volledige stack bewakings oplossing voor toepassingen en services in azure, in andere Clouds en on-premises. Naast het gebruik van Azure Monitor voor het analyseren van die gegevens en het gebruiken voor verschillende bewakings scenario's, moet u deze mogelijk verzenden naar andere controle hulpprogramma's in uw omgeving. De meest efficiënte methode voor het streamen van bewakings gegevens naar externe hulpprogram ma's is in de meeste gevallen het gebruik van [Azure Event hubs](/azure/event-hubs/). In dit artikel vindt u een korte beschrijving van de manier waarop u bewakings gegevens uit verschillende bronnen kunt streamen naar een Event Hub en koppelingen naar gedetailleerde richt lijnen.


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Voordat u streaming voor een gegevens bron configureert, moet u [een event hubs naam ruimte en Event hub maken](../../event-hubs/event-hubs-create.md). Deze naam ruimte en Event Hub is het doel voor al uw bewakings gegevens. Een Event Hubs naam ruimte is een logische groepering van Event hubs die hetzelfde toegangs beleid delen, net zoals een opslag account afzonderlijke blobs in dat opslag account heeft. Houd rekening met de volgende details over de Event hubs-naam ruimte en Event hubs die u gebruikt voor het streamen van bewakings gegevens:

* Met het aantal doorvoer eenheden kunt u de doorvoer schaal voor uw event hubs verhogen. Normaal gesp roken is er slechts één doorvoer eenheid nodig. Als u de schaal van het logboek gebruik wilt verg Roten, kunt u het aantal doorvoer eenheden voor de naam ruimte hand matig verhogen of de automatische inflatie inschakelen.
* Met het aantal partities kunt u het verbruik van verschillende gebruikers parallelliseren. Eén partitie kan Maxi maal 20MBps of ongeveer 20.000 berichten per seconde ondersteunen. Afhankelijk van het hulp programma dat de gegevens gebruikt, kan het al dan niet voor komen dat er meerdere partities worden verbruikt. Het kan zijn dat vier partities worden gestart als u niet zeker weet of u niet zeker weet of het aantal partities moet worden ingesteld.
* U stelt de Bewaar periode voor berichten in op uw Event Hub ten minste 7 dagen. Als uw verbruikte hulp programma meer dan een dag uitvalt, zorgt u ervoor dat het hulp programma kan ophalen waar het niet langer dan zeven dagen oud was.
* U moet de standaard Consumer groep voor uw Event Hub gebruiken. U hoeft geen andere consumenten groepen te maken of een afzonderlijke consumenten groep te gebruiken, tenzij u van plan bent om twee verschillende hulpprogram ma's dezelfde gegevens van dezelfde Event Hub te laten gebruiken.
* Voor het Azure-activiteiten logboek kiest u een Event Hubs naam ruimte en Azure Monitor maakt u een Event Hub binnen die naam ruimte met de naam _Insights-logboeken: operationele logboeken_. Voor andere logboek typen kunt u een bestaande Event Hub kiezen of Azure Monitor een Event Hub per logboek categorie maken.
* De uitgaande poort 5671 en 5672 moeten normaal gesp roken worden geopend op de computer of het VNET dat gegevens uit het Event Hub gebruiken.

## <a name="monitoring-data-available"></a>Beschik bare bewakings gegevens
[Bronnen van bewakings gegevens voor Azure monitor](data-sources.md) beschrijven de verschillende gegevens lagen voor Azure-toepassingen en de soorten beschik bare bewakings gegevens voor elk. De volgende tabel geeft een lijst van elk van deze lagen en een beschrijving van hoe deze gegevens naar een Event Hub kunnen worden gestreamd. Volg de onderstaande koppelingen voor meer informatie.

| Laag | Gegevens | Methode |
|:---|:---|:---|
| [Azure-Tenant](data-sources.md#azure-tenant) | Controle logboeken Azure Active Directory | Configureer een diagnostische instelling voor tenants op uw AAD-Tenant. Zie [zelf studie: Stream Azure Active Directory logboeken naar een Azure Event hub](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) voor meer informatie. |
| [Azure-abonnement](data-sources.md#azure-subscription) | Azure-activiteitenlogboek | Maak een logboek profiel om gebeurtenissen in het activiteiten logboek te exporteren naar Event Hubs.  Zie [Azure-activiteiten logboek exporteren naar opslag of azure Event hubs](activity-log-export.md) voor meer informatie. |
| [Azure-resources](data-sources.md#azure-resources) | Metrische platform gegevens<br> Resourcelogboeken |Beide typen gegevens worden verzonden naar een Event Hub met behulp van de diagnostische instelling van de resource. Zie [Azure-resource logboeken streamen naar een event hub](resource-logs-stream-event-hubs.md) voor meer informatie. |
| [Besturings systeem (gast)](data-sources.md#operating-system-guest) | Virtuele machines van Azure | Installeer de [Azure Diagnostics-extensie](diagnostics-extension-overview.md) op virtuele Windows-en Linux-machines in Azure. Zie [Azure Diagnostics gegevens streamen in het warme pad met behulp van Event hubs](diagnostics-extension-stream-event-hubs.md) voor meer informatie over virtuele Windows-machines en [Linux diagnostische uitbrei ding voor het bewaken van gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) voor meer informatie over Linux-vm's. |
| [Toepassings code](data-sources.md#application-code) | Application Insights | Application Insights biedt geen directe methode voor het streamen van gegevens naar Event hubs. U kunt [continue export](../../azure-monitor/app/export-telemetry.md) van de Application Insights gegevens naar een opslag account instellen en vervolgens een logische app gebruiken om de gegevens naar een event hub te verzenden zoals beschreven in [hand matige streaming met logische app](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Hand matige streaming met logische app
Voor gegevens die u niet rechtstreeks naar een Event Hub kunt streamen, schrijft u naar Azure Storage en gebruikt u vervolgens een met tijd geactiveerde logische app die [gegevens uit de Blob-opslag ophaalt](../../connectors/connectors-create-api-azureblobstorage.md#add-action) en [deze als een bericht naar de Event hub pusht](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partner hulpprogramma's met Azure Monitor-integratie

Door uw bewakings gegevens te routeren naar een Event Hub met Azure Monitor kunt u eenvoudig integreren met externe hulpprogram ma's voor SIEM en controle. Voor beelden van hulpprogram ma's met Azure Monitor-integratie zijn onder andere:

| Hulpprogramma | Gehost in azure | Beschrijving |
|:---|:---| :---|
|  IBM QRadar | Nee | Het Microsoft Azure DSM en Microsoft Azure Event hub-protocol kunnen worden gedownload via [de website van IBM-ondersteuning](https://www.ibm.com/support). Meer informatie over de integratie met Azure vindt u in de [configuratie van QRADAR DSM](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Nee | [De Azure monitor-invoeg toepassing voor Splunk](https://splunkbase.splunk.com/app/3534/) is een open-source project dat beschikbaar is in Splunkbase. De documentatie is beschikbaar op [Azure monitor addon voor Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Als u een invoeg toepassing in uw Splunk-exemplaar niet kunt installeren, als u bijvoorbeeld een proxy gebruikt of wordt uitgevoerd op Splunk Cloud, kunt u deze gebeurtenissen door sturen naar de Splunk HTTP-gebeurtenis verzamelaar met de [Azure-functie voor Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), die wordt geactiveerd door nieuwe berichten in de Event hub. |
| SumoLogic | Nee | Instructies voor het instellen van SumoLogic om gegevens van een Event Hub te gebruiken, zijn beschikbaar in [Logboeken verzamelen voor de Azure audit-app vanuit Event hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Nee | De ArcSight Azure Event hub Smart connector is beschikbaar als onderdeel van [de verzameling van de ArcSight slimme connector](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Syslog-server | Nee | Als u Azure Monitor gegevens rechtstreeks naar een syslog-server wilt streamen, kunt u een [oplossing gebruiken op basis van een Azure-functie](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | Nee| Instructies voor het instellen van LogRhythm voor het verzamelen van logboeken van een Event Hub zijn [hier](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)beschikbaar. 
|Logz.io | Ja | Zie voor meer informatie aan de slag [met bewaking en logboek registratie met behulp van Logz.io voor java-apps die worden uitgevoerd op Azure](https://docs.microsoft.com/azure/java/java-get-started-with-logzio)


## <a name="next-steps"></a>Volgende stappen
* [Het activiteiten logboek archiveren in een opslag account](../../azure-monitor/platform/archive-activity-log.md)
* [Lees het overzicht van het Azure-activiteiten logboek](../../azure-monitor/platform/platform-logs-overview.md)
* [Een waarschuwing instellen op basis van een gebeurtenis in een activiteiten logboek](../../azure-monitor/platform/alerts-log-webhook.md)


