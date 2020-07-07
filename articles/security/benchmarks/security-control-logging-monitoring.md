---
title: Azure-beveiligings beheer-logboek registratie en controle
description: Logboek registratie en controle van Azure-beveiligings beheer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e58df0997c62a131a3c9987369f8e076a62d6654
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408347"
---
# <a name="security-control-logging-and-monitoring"></a>Beveiligings beheer: logboek registratie en controle

Beveiligings logboeken en-bewaking zijn gericht op activiteiten met betrekking tot het inschakelen, ophalen en opslaan van audit logboeken voor Azure-Services.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Micro soft onderhoudt tijd bronnen voor Azure-resources, maar u hebt de mogelijkheid om de tijd synchronisatie-instellingen voor uw reken resources te beheren.

- [Tijd synchronisatie configureren voor Azure Windows Compute-resources](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [Tijd synchronisatie configureren voor Azure Linux-reken resources](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2.2 | 6,5, 6,6 | Klant |

Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. In Azure Monitor kunt u Log Analytics werk ruimte (n) gebruiken om een query uit te voeren en een Analytics-account te gebruiken, en Azure Storage accounts voor lange termijn/archiverings opslag.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Interne host-logboeken van de virtuele machine van Azure verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2.3 | 6,2, 6,3 | Klant |

Diagnostische instellingen op Azure-resources inschakelen voor toegang tot controle-, beveiligings-en Diagnostische logboeken. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Logboek registratie en verschillende logboek typen in azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2,4 | 6,2, 6,3 | Klant |

Als de reken resource eigendom is van micro soft, is micro soft verantwoordelijk voor het bewaken van de bron. Als de reken resource eigendom is van uw organisatie, is het uw verantwoordelijkheid om deze te controleren. U kunt Azure Security Center gebruiken om het besturings systeem te bewaken. Gegevens die worden verzameld door Security Center van het besturings systeem zijn onder andere besturingssysteem type en-versie, besturings systeem (Windows-gebeurtenis Logboeken), actieve processen, computer naam, IP-adressen en aangemelde gebruiker. De Log Analytics-agent verzamelt ook crash dump bestanden.

- [Interne host-logboeken van de virtuele machine van Azure verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Meer informatie over het verzamelen van Azure Security Center gegevens](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2.5 | 6.4 | Klant |

Stel binnen Azure Monitor uw Bewaar periode voor Log Analytics werk ruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2,6 | 6.7 | Klant |

Analyseer en bewaak logboeken voor afwijkend gedrag en controleer regel matig de resultaten. Gebruik de Log Analytics werk ruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Log Analytics-werk ruimte begrijpen](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Aangepaste query's uitvoeren in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2.7 | 6.8 | Klant |

Gebruik Azure Security Center met Log Analytics werk ruimte voor bewaking en waarschuwingen over afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden.

U kunt ook gegevens naar Azure-Sentinel inschakelen en op het bord zetten.

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Waarschuwingen beheren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Een waarschuwing over logboek gegevens van log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2,8 | 8,6 | Klant |

Schakel de verzameling van antimalware-gebeurtenissen in voor Azure Virtual Machines en Cloud Services.

- [Micro soft antimalware configureren voor Virtual Machines](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [Micro soft antimalware configureren voor Cloud Services](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Micro soft antimalware begrijpen](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2.9 | 8.7 | Klant |

Implementeer een oplossing van derden van Azure Marketplace voor de DNS-registratie van oplossingen conform uw organisatie.  

## <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 2,10 | 8,8 | Klant |

Gebruik micro soft Monitoring Agent op alle ondersteunde virtuele machines van Azure Windows om de gebeurtenis voor het maken van processen en het veld CommandLine te registreren.   Voor ondersteunde virtuele machines van Azure Linux kunt u hand matig console logboek registratie configureren per knoop punt en syslog gebruiken om de gegevens op te slaan.  U kunt ook de werk ruimte Log Analytics van Azure Monitor gebruiken om logboeken te controleren en query's uit te voeren op geregistreerde gegevens van virtuele Azure-machines. 

- [Gegevensverzameling in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Aangepaste query's uitvoeren in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Syslog-gegevensbronnen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)


## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer: [identiteit en Access Control](security-control-identity-access-control.md)