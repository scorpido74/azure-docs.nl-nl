---
title: Azure-beveiligingslogboekregistratie en -controle | Microsoft Documenten
description: Meer informatie over de logboeken die beschikbaar zijn in Azure en de beveiligingsinzichten die u verkrijgen.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750785"
---
# <a name="azure-security-logging-and-auditing"></a>Logboekregistratie en controle van Azure-beveiliging

Azure biedt een breed scala aan configureerbare beveiligingscontrole- en logboekregistratieopties om u te helpen hiaten in uw beveiligingsbeleid en -mechanismen te identificeren. In dit artikel wordt gesproken over het genereren, verzamelen en analyseren van beveiligingslogboeken van services die op Azure worden gehost.

> [!Note]
> Bepaalde aanbevelingen in dit artikel kunnen leiden tot meer gegevens, netwerk- of rekenbronnen en uw licentie- of abonnementskosten verhogen.

## <a name="types-of-logs-in-azure"></a>Typen logboeken in Azure

Cloudtoepassingen zijn complex met veel bewegende delen. Logboekregistratiegegevens kunnen inzichten bieden over uw toepassingen en u helpen:

- Problemen uit het verleden oplossen of potentiële problemen voorkomen
- De prestaties of onderhoudbaarheid van toepassingen verbeteren
- Acties automatiseren waarvoor anders handmatige interventie nodig zou zijn

Azure-logboeken zijn ingedeeld in de volgende typen:
* **Controle-/beheerlogboeken** bevatten informatie over bewerkingen voor het maken, bijwerken en verwijderen van Azure Resource Manager. Zie [Azure-activiteitslogboeken](../../azure-monitor/platform/platform-logs-overview.md)voor meer informatie .

* **Gegevensvlaklogboeken** bevatten informatie over gebeurtenissen die zijn opgehaald als onderdeel van azure-brongebruik. Voorbeelden van dit type logboek zijn het Windows-gebeurtenissysteem, beveiliging en toepassingslogboeken in een virtuele machine (VM) en de [diagnostische logboeken](../../azure-monitor/platform/platform-logs-overview.md) die zijn geconfigureerd via Azure Monitor.

* **Verwerkte gebeurtenissen** bevatten informatie over geanalyseerde gebeurtenissen/waarschuwingen die namens u zijn verwerkt. Voorbeelden van dit type zijn [Azure Security Center-waarschuwingen](../../security-center/security-center-managing-and-responding-alerts.md) waarbij [Azure Security Center](../../security-center/security-center-intro.md) uw abonnement heeft verwerkt en geanalyseerd en beknopte beveiligingswaarschuwingen biedt.

In de volgende tabel worden de belangrijkste typen logboeken weergegeven die beschikbaar zijn in Azure:

| Logboekcategorie | Logboektype | Gebruik | Integratie |
| ------------ | -------- | ------ | ----------- |
|[Activiteitenlogboeken](../../azure-monitor/platform/platform-logs-overview.md)|Gebeurtenissen in beheervlak op Azure Resource Manager-resources|  Geeft inzicht in de bewerkingen die zijn uitgevoerd op resources in uw abonnement.|    Rest API, [Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Azure Resource-logboeken](../../azure-monitor/platform/platform-logs-overview.md)|Frequente gegevens over de werking van Azure Resource Manager-resources in abonnement|   Geeft inzicht in bewerkingen die uw resource zelf heeft uitgevoerd.| Azure Monitor|
|[Azure Active Directory-rapportage](../../active-directory/reports-monitoring/overview-reports.md)|Logboeken en rapporten | Rapporteert aanmeldingsactiviteiten van gebruikers en informatie over systeemactiviteit over gebruikers en groepsbeheer.|[Grafische API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtuele machines en cloudservices](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows Event Log-service en Linux Syslog|  Legt systeemgegevens vast en registreert gegevens op de virtuele machines en brengt die gegevens over naar een opslagaccount van uw keuze.|   Windows (met Windows Azure Diagnostics [[WAD]](../../monitoring-and-diagnostics/azure-diagnostics.md)opslag) en Linux in Azure Monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Opslaglogboekregistratie, biedt metrische gegevens voor een opslagaccount|Geeft inzicht in traceeraanvragen, analyseert gebruikstrends en diagnosticeert problemen met uw opslagaccount.|   REST API of de [clientbibliotheek](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[NSG-stroomlogboeken (Network Security Group)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON-indeling, toont uitgaande en inkomende stromen per regel|Hiermee geeft u informatie weer over het binnendringen en het binnendringen van IP-verkeer via een netwerkbeveiligingsgroep.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Inzicht in toepassingen](../../azure-monitor/app/app-insights-overview.md)|Logboeken, uitzonderingen en aangepaste diagnoses|  Biedt een APM-service (Application Performance Monitoring) voor webontwikkelaars op meerdere platforms.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Procesgegevens / beveiligingswaarschuwingen](../../security-center/security-center-intro.md)|  Azure Security Center-waarschuwingen, Azure Monitor-logboeken waarschuwingen|    Biedt beveiligingsinformatie en waarschuwingen.|  REST API's, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Logintegratie met on-premises SIEM-systemen
[Bij het integreren van waarschuwingen voor beveiligingscentrum](../../security-center/security-center-export-data-to-siem.md) wordt besproken hoe u beveiligingswaarschuwingen voor beveiligingssystemen voor virtuele machines synchroniseren die zijn verzameld door Azure-diagnostische logboeken en Azure-controlelogboeken met uw Azure Monitor-logboeken of SIEM-oplossing.

## <a name="next-steps"></a>Volgende stappen

- [Controle en logboekregistratie:](management-monitoring-overview.md)bescherm gegevens door de zichtbaarheid te behouden en snel te reageren op tijdige beveiligingswaarschuwingen.

- [Logboekregistratie en controlelogboekverzameling binnen Azure:](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)Dwing deze instellingen af om ervoor te zorgen dat uw Azure-instanties de juiste beveiligings- en controlelogboeken verzamelen.

- [Controle-instellingen configureren voor een siteverzameling:](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)Als u beheerder bent van een siteverzameling, haalt u de geschiedenis op van de acties van individuele gebruikers en de geschiedenis van de acties die tijdens een bepaald datumbereik zijn uitgevoerd.

- [Zoek in het controlelogboek in het Office 365 Security & Compliance Center:](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)gebruik het Office 365 Security & Compliance Center om in het uniforme controlelogboek te zoeken en de gebruikers- en beheerdersactiviteit in uw Office 365-organisatie weer te geven.
