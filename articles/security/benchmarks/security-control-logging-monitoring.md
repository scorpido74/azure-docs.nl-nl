---
title: Azure-beveiligingsbeheer - Logboekregistratie en -bewaking
description: Logboekregistratie en bewaking van beveiligingscontrole
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545497"
---
# <a name="security-control-logging-and-monitoring"></a>Beveiligingscontrole: logboekregistratie en -controle

Beveiliging logboekregistratie en -bewaking is gericht op activiteiten die betrekking hebben op het inschakelen, aanschaffen en opslaan van controlelogboeken voor Azure-services.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft onderhoudt tijdbronnen voor Azure-resources, maar u hebt de optie om de tijdsynchronisatie-instellingen voor uw rekenbronnen te beheren.

Tijdsynchronisatie configureren voor Azure-rekenbronnen:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2,2 | 6.5, 6.6 | Klant |

Inname wordt via Azure Monitor opgeslagen om beveiligingsgegevens te verzamelen die worden gegenereerd door eindpuntapparaten, netwerkbronnen en andere beveiligingssystemen. Gebruik in Azure Monitor Log Analytics Workspace(s) om analyses op te vragen en uit te voeren en Azure Storage Accounts te gebruiken voor opslag op lange termijn/archiverings.

U ook gegevens aan boord inschakelen en aan boord maken voor Azure Sentinel of een SIEM van derden. Azure Sentinel aan boord:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Platformlogboeken en -statistieken verzamelen met Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Interne hostlogboeken van Azure Virtual Machine verzamelen met Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Aan de slag met Azure Monitor en SIEM-integratie van derden:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2.3 | 6.2, 6.3 | Klant |

Diagnostische instellingen in Azure-bronnen inschakelen voor toegang tot controle-, beveiligings- en diagnostische logboeken. Activiteitenlogboeken, die automatisch beschikbaar zijn, bevatten gebeurtenisbron, datum, gebruiker, tijdstempel, bronadressen, bestemmingsadressen en andere nuttige elementen.

Platformlogboeken en -statistieken verzamelen met Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Inzicht in logboekregistratie en verschillende logboektypen in Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2.4 | 6.2, 6.3 | Klant |

Als de compute resource eigendom is van Microsoft, is Microsoft verantwoordelijk voor het bewaken ervan. Als de compute resource eigendom is van uw organisatie, is het uw verantwoordelijkheid om deze te controleren. U Azure Security Center gebruiken om het besturingssysteem te controleren. Gegevens die door Security Center van het besturingssysteem worden verzameld, omvatten het type en de versie van het besturingssysteem, OS-logboeken (Windows-gebeurtenislogboeken), het uitvoeren van processen, machinenaam, IP-adressen en ingelogde gebruiker. De Log Analytics Agent verzamelt ook crashdumpbestanden.

Interne hostlogboeken van Azure Virtual Machine verzamelen met Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Informatie over azure security center-gegevensverzameling:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2,5 | 6.4 | Klant |

Stel in Azure Monitor de bewaartermijn van uw Log Analytics Workspace in volgens de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage Accounts voor opslag op lange termijn/archiverings.

Logboekretentieparameters instellen voor Logboekanalysewerkruimten:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2,6 | 6.7 | Klant |

Analyseer en monitor logboeken op afwijkend gedrag en bekijk regelmatig de resultaten. Gebruik de Log Analytics Workspace van Azure Monitor om logboeken te controleren en query's uit te voeren op logboekgegevens.

U ook gegevens aan boord inschakelen en aan boord maken voor Azure Sentinel of een SIEM van derden. 

Azure Sentinel aan boord:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Inzicht in logboekanalysewerkruimte:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2.7 | 6.8 | Klant |

Gebruik Azure Security Center met Log Analytics Workspace voor het bewaken en waarschuwen van afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen.

U ook gegevens aan Azure Sentinel inschakelen en aan boord maken.

Azure Sentinel aan boord:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Waarschuwingen beheren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Hoe u logboekgegevens waarschuwen voor logboekanalyse:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2,8 | 8.6 | Klant |

Antimalware-gebeurtenisverzameling inschakelen voor Azure Virtual Machines en Cloud Services.

Microsoft Antimalware configureren voor virtuele machines:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Microsoft Antimalware configureren voor Cloud Services:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Microsoft Antimalware begrijpen:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2.9 | 8.7 | Klant |

Implementeer een oplossing van derden voor DNS-logboekregistratie.

## <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 2.1 | 8.8 | Klant |

Configureer handmatig consolelogboekregistratie en PowerShell-transcriptie per knooppunt.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende beveiligingsbesturingselement: [Identiteits- en toegangsbeheer](security-control-identity-access-control.md)
