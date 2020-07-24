---
title: Azure-beveiligings basislijn voor Azure DevTest Labs
description: Azure-beveiligings basislijn voor Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097095"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure-beveiligings basislijn voor Azure DevTest Labs

De Azure-beveiligings basislijn voor Azure DevTest Labs bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in Azure.

Zie [overzicht van Azure Security-basis lijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken
**Hulp:** Micro soft onderhoudt tijd bronnen voor Azure-resources. U kunt echter tijd synchronisatie-instellingen voor uw reken resources beheren.

Zie het volgende artikel voor meer informatie over het configureren van tijd synchronisatie voor Azure Compute-resources: [tijd synchronisatie voor Windows-vm's in azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync). 

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren
**Hulp:** Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een Log Analytics-werk ruimte, een Azure Event Hub of een Azure-opslag account voor archivering. Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure DevTest Labs-instanties op het niveau van het beheer vlak. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het beheer vlak voor uw DevTest Labs-instanties.

Zie voor meer informatie [Diagnostische instellingen maken voor het verzenden van platform logboeken en-metrische gegevens naar verschillende bestemmingen](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources
**Hulp:** Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een Log Analytics-werk ruimte, een Azure Event Hub of een Azure-opslag account voor archivering. Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure DevTest Labs-instanties op het niveau van het beheer vlak. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het beheer vlak voor uw DevTest Labs-instanties.

Zie voor meer informatie [Diagnostische instellingen maken voor het verzenden van platform logboeken en-metrische gegevens naar verschillende bestemmingen](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen
**Hulp:** Azure DevTest Labs virtuele machines worden gemaakt en het eigendom zijn van de klant. Het is dus de verantwoordelijkheid van de organisatie om deze te bewaken. U kunt Azure Security Center gebruiken om het Compute-besturings systeem te bewaken. Gegevens die worden verzameld door Security Center van het besturings systeem zijn onder andere besturingssysteem type en-versie, besturings systeem (Windows-gebeurtenis Logboeken), actieve processen, computer naam, IP-adressen en aangemelde gebruiker. De Log Analytics-agent verzamelt ook crash dump bestanden.

Raadpleeg voor meer informatie de volgende artikelen: 

- [Interne host-logboeken van de virtuele machine van Azure verzamelen met Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Meer informatie over het verzamelen van Azure Security Center gegevens](../security-center/security-center-enable-data-collection.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren
***Hulp:** Stel in Azure Monitor de Bewaar periode voor logboek registratie in voor Log Analytics werk ruimten die zijn gekoppeld aan uw Azure DevTest Labs-instanties volgens de nalevings voorschriften van uw organisatie.

Zie het volgende artikel: [para meters voor het bewaren van Logboeken instellen](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) voor meer informatie.

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren
**Hulp:** Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een Log Analytics-werk ruimte. Voer query's uit in Log Analytics om zoek termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de activiteiten logboek gegevens die mogelijk zijn verzameld voor Azure DevTest Labs.

Raadpleeg voor meer informatie de volgende artikelen:

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/diagnostic-settings.md)
- [Azure-activiteiten logboeken verzamelen en analyseren in Log Analytics werk ruimte in Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit
**Hulp:** Gebruik Azure Log Analytics-werk ruimte voor het bewaken en waarschuwen over afwijkende activiteiten in beveiligings logboeken en gebeurtenissen met betrekking tot uw Azure DevTest Labs.

Zie het volgende artikel voor meer informatie: een [waarschuwing geven over logboek gegevens van log Analytics](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren
**Hulp:** Niet van toepassing. Azure DevTest Labs geen aan anti-malware gerelateerde logboeken verwerkt of produceert.

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen
**Hulp:** Niet van toepassing. Azure DevTest Labs geen aan DNS gerelateerde logboeken verwerkt of produceert.

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen
**Hulp:** Azure DevTest Labs maakt Azure Compute-machines die eigendom zijn van en worden beheerd door de klant. Gebruik micro soft Monitoring Agent op alle ondersteunde virtuele machines van Azure Windows om de gebeurtenis voor het maken van processen en het veld CommandLine te registreren. Voor ondersteunde virtuele machines van Azure Linux kunt u hand matig console logboek registratie configureren per knoop punt en syslog gebruiken om de gegevens op te slaan. U kunt ook de werk ruimte Log Analytics van Azure Monitor gebruiken om logboeken te controleren en query's uit te voeren op geregistreerde gegevens van virtuele Azure-machines.

- [Gegevensverzameling in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Syslog-gegevensbronnen in Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel:

- [Beveiligings waarschuwingen voor omgevingen in Azure DevTest Labs](environment-security-alerts.md)