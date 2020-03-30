---
title: Veelgestelde vragen over Azure Site Recovery-controle
description: Antwoorden op veelgestelde vragen over Azure Site Recovery monitoring, met ingebouwde monitoring en Azure Monitor (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718260"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Veelgestelde vragen over siteherstelbewaking

In dit artikel worden veelgestelde vragen beantwoord over het bewaken van Azure [Site Recovery](site-recovery-overview.md), met behulp van ingebouwde siteherstelbewaking en Azure Monitor (Log Analytics).

## <a name="general"></a>Algemeen

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Hoe verschilt de RPO-waarde van het laatst beschikbare herstelpunt?

Siteherstel maakt gebruik van een asynchrone proces in meerdere stappen om machines te repliceren naar Azure.

- In de voorlaatste stap van replicatie worden recente wijzigingen op de machine, samen met metagegevens, gekopieerd naar een opslagaccount in het logboek/cache.
- Deze wijzigingen, samen met de tag die een herstelbaar punt identificeert, worden geschreven naar de opslagaccount /beheerde schijf in het doelgebied.
- Site Recovery kan nu een herstelbaar punt voor de machine genereren.
- Op dit punt, de RPO is voldaan voor de wijzigingen geüpload naar de opslag account tot nu toe. Met andere woorden, de machine RPO is op dit punt gelijk aan de hoeveelheid tijd die is verstreken vanaf de tijdstempel die overeenkomt met het herstelbare punt.
- Nu kiest Site recovery de geüploade gegevens uit het opslagaccount en past deze toe op de replicaschijven die voor de machine zijn gemaakt.
- Site Recovery genereert vervolgens een herstelpunt en maakt dit punt beschikbaar voor herstel bij failover.
- Het meest recente beschikbare herstelpunt geeft dus de tijdstempel aan die overeenkomt met het laatste herstelpunt dat al is verwerkt en toegepast op de replicaschijven.


Als u een onjuiste systeemtijd op de replicerende bronmachine of op on-premises infrastructuurservers hebt, wordt de berekende RPO-waarde scheefgesteld. Voor nauwkeurige RPO-rapportage moet u ervoor zorgen dat de systeemklok nauwkeurig is op alle servers en machines.



## <a name="inbuilt-site-recovery-logging"></a>Ingebouwde logboekregistratie voor siteherstel


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Waarom verschilt het aantal vm's in de weergave van de kluisinfrastructuur van het totale aantal dat wordt weergegeven in gerepliceerde items?

De weergave kluisinfrastructuur wordt beperkt door replicatiescenario's. Alleen machines in het geselecteerde replicatiescenario zijn opgenomen in de telling voor de weergave. Bovendien tellen we alleen VM's die zijn geconfigureerd om te repliceren naar Azure. Mislukt ten opzichte van machines of machines die worden gerepliceerd naar een on-premises site, worden niet meegeteld in de weergave.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Waarom verschilt het aantal gerepliceerde items in Essentials van het totale aantal gerepliceerde items op het dashboard?

Alleen machines waarvoor de eerste replicatie is voltooid, worden opgenomen in de telling die in Essentials wordt weergegeven. Het totaal van de gerepliceerde items omvat alle machines in de kluis, inclusief de machines waarvoor momenteel de eerste replicatie wordt uitgevoerd.

## <a name="azure-monitor-logging"></a>Logboekregistratie van Azure Monitor


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Hoe vaak verzendt Site Recovery diagnostische logboeken naar Azure Monitor Log? 

- AzureSiteRecoveryReplicationStats en AzureSiteRecoveryRecoveryPoints worden elke 15 minuten verzonden.  
- AzureSiteRecoveryReplicatieDataUploadRate en AzureSiteRecoveryProtectedDiskDataChurn worden elke vijf minuten verzonden. 
- AzureSiteRecoveryJobs wordt verzonden bij de trigger en voltooiing van een taak.
- AzureSiteRecoveryEvents wordt verzonden wanneer een gebeurtenis wordt gegenereerd. 
- AzureSiteRecoveryReplicatedItems wordt verzonden wanneer er een omgevingswijziging is. Doorgaans is de vernieuwingstijd van gegevens 15 minuten na een wijziging. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Hoe lang worden gegevens bewaard in Azure Monitor-logboeken? 

Standaard geldt de retentie voor 31 dagen. U de periode in de sectie **Gebruik en Geschatte kosten** in de werkruimte Log Analytics verlengen. Klik op **Gegevensbehoud**en kies het bereik.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Wat is de grootte van de diagnostische logboeken? 

Meestal is de grootte van een logboek 15-20 KB. 


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken met [ingebouwde monitoring van SiteHerstel](site-recovery-monitor-and-troubleshoot.md)of [Azure Monitor](monitor-log-analytics.md).


