---
title: Azure-siteherstel controleren met Azure-monitorlogboeken
description: Meer informatie over het bewaken van Azure Site Recovery met Azure Monitor Logs (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133419"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Site Recovery bewaken met Azure Monitor-logboeken

In dit artikel wordt beschreven hoe u machines controleren die zijn gerepliceerd door Azure [Site Recovery](site-recovery-overview.md), met behulp van Azure [Monitor Logs](../azure-monitor/platform/data-platform-logs.md)en [Logboekanalyse](../azure-monitor/log-query/log-query-overview.md).

Azure Monitor Logs bieden een logdataplatform dat activiteits- en diagnostische logboeken verzamelt, samen met andere bewakingsgegevens. Binnen Azure Monitor Logs gebruikt u Log Analytics om logboekquery's te schrijven en te testen en om logboekgegevens interactief te analyseren. U logboekresultaten visualiseren en query's configureren om acties uit te voeren op basis van bewaakte gegevens.

Voor Siteherstel u Azure Monitor-logboeken gebruiken om u te helpen het volgende te doen:

- **Status en status siteherstel bewaken.** U bijvoorbeeld de replicatiestatus controleren, failoverstatus testen, siteherstelgebeurtenissen, herstelpuntendoelstellingen (RTO's) voor beveiligde machines en schijf-/gegevenswijzigingspercentages.
- **Waarschuwingen instellen voor siteherstel**. U bijvoorbeeld waarschuwingen configureren voor machinestatus, failoverstatus of taakstatus Siterecovery.

Het gebruik van Azure Monitor Logs with Site Recovery wordt ondersteund voor **Azure naar Azure-replicatie** en **VMware VM/physical server naar** Azure-replicatie.

> [!NOTE]
> Als u de logboeken voor churn-gegevens en logboeken voor uploadsnelheid voor VMware- en fysieke machines wilt downloaden, moet u een Microsoft-bewakingsagent op de processerver installeren. Deze agent stuurt de logboeken van de replicerende machines naar de werkruimte. Deze mogelijkheid is alleen beschikbaar voor 9,30 mobility agent versie verder.

## <a name="before-you-start"></a>Voordat u begint

U hebt het volgende nodig:

- Ten minste één machine beschermd in een vault van Recovery Services.
- Een werkruimte log Analytics om logboeken voor siteherstel op te slaan. [Meer informatie over](../azure-monitor/learn/quick-create-workspace.md) het instellen van een werkruimte.
- Een basiskennis van het schrijven, uitvoeren en analyseren van logboekquery's in Log Analytics. [Meer informatie](../azure-monitor/log-query/get-started-portal.md).

We raden u aan [veelvoorkomende bewakingsvragen te](monitoring-common-questions.md) bekijken voordat u begint.

## <a name="configure-site-recovery-to-send-logs"></a>Siteherstel configureren om logboeken te verzenden

1. Klik in de kluis op **Diagnostische instellingen** > **Diagnostische instellingen toevoegen**.

    ![Diagnostische logboekregistratie selecteren](./media/monitoring-log-analytics/add-diagnostic.png)

2. Geef **in diagnostische instellingen**een naam op en schakel het selectievakje Verzenden naar **logboekanalyse in**.
3. Selecteer het Azure Monitor Logs-abonnement en de werkruimte Log Analytics.
4. Selecteer **Azure Diagnostics** in de schakelfunctie.
5. Selecteer in de logboeklijst alle logboeken met het voorvoegsel **AzureSiteRecovery**. Klik vervolgens op **OK**.

    ![Werkruimte selecteren](./media/monitoring-log-analytics/select-workspace.png)

De logboeken siteherstel beginnen te worden ingevoerd in een tabel **(AzureDiagnostics)** in de geselecteerde werkruimte.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Microsoft-bewakingsagent op de processerver configureren om logboeken met verloop- en uploadsnelheid te verzenden

U de informatie over de gegevensverloopsnelheid en informatie over de uploadsnelheid van brongegevens voor uw VMware/fysieke machines on-premises vastleggen. Om dit mogelijk te maken, moet een Microsoft-bewakingsagent op de processerver worden geïnstalleerd.

1. Ga naar de werkruimte Log Analytics en klik op **Geavanceerde instellingen**.
2. Klik op de pagina **Verbonden bronnen** en selecteer verder **Windows-servers**.
3. Download de Windows Agent (64-bits) op de processerver. 
4. [De werkruimte-id en -sleutel verkrijgen](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Agent configureren om TLS 1.2 te gebruiken](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Voltooi de installatie van de agent](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) door de verkregen werkruimte-ID en -sleutel te verstrekken.
7. Zodra de installatie is voltooid, gaat u naar de werkruimte Log Analytics en klikt u op **Geavanceerde instellingen**. Ga naar de **pagina Gegevens** en klik verder op **Windows Prestatiemeteritems**. 
8. Klik op **'+'** om de volgende twee tellers toe te voegen met een monsterinterval van 300 seconden:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

De churn- en uploadsnelheidgegevens worden in de werkruimte ingevoerd.


## <a name="query-the-logs---examples"></a>De logboeken opvragen - voorbeelden

U haalt gegevens op uit logboeken met logboekquery's die zijn geschreven met de [kusto-querytaal.](../azure-monitor/log-query/get-started-queries.md) In deze sectie vindt u een paar voorbeelden van veelvoorkomende query's die u mogelijk gebruikt voor siteherstelbewaking.

> [!NOTE]
> Sommige voorbeelden gebruiken **replicationProviderName_s** ingesteld op **A2A**. Hiermee worden Azure VM's opgehaald die worden gerepliceerd naar een secundair Azure-gebied met behulp van Siteherstel. In deze voorbeelden u **A2A** vervangen door **InMageAzureV2**, als u on-premises VMware VM's of fysieke servers wilt ophalen die met Site Recovery naar Azure worden gerepliceerd.


### <a name="query-replication-health"></a>Queryreplicatiestatus

Met deze query wordt een cirkeldiagram voor de huidige replicatiestatus van alle beveiligde Azure VM's, onderverdeeld in drie statussen, in kaart gebracht: Normaal, Waarschuwing of Kritiek.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Query Mobility-serviceversie

Met deze query wordt een cirkeldiagram voor Azure VM's weergegeven dat is gerepliceerd met Siteherstel, uitgesplitst naar de versie van de mobiliteitsagent die ze uitvoeren.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Query RPO-tijd

Deze query brengt een staafdiagram met Azure VM's in kaart dat is gerepliceerd met Siterecovery, uitgesplitst naar doel herstelpunt (RPO): Minder dan 15 minuten, tussen 15-30 minuten, meer dan 30 minuten.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Query RPO](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Querysitehersteltaken

Met deze query worden alle sitehersteltaken opgehaald (voor alle scenario's voor noodherstel), die in de afgelopen 72 uur zijn geactiveerd en de voltooiingsstatus ervan.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Querysiteherstelgebeurtenissen

Met deze query worden alle gebeurtenissen voor siteherstel (voor alle scenario's voor noodherstel) opgehaald die in de afgelopen 72 uur zijn opgehaald, samen met de ernst ervan. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Failoverstatus querytest (cirkeldiagram)

Met deze query wordt een cirkeldiagram voor de failoverstatus van Azure VM's weergegeven die zijn gerepliceerd met Siteherstel.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Failoverstatus querytest (tabel)

Met deze query wordt een tabel voor de failoverstatus van Azure VM's gerepd met Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Querymachine RPO

Met deze query wordt een trendgrafiek weergegeven die de RPO van een specifieke Azure VM (ContosoVM123) van de afgelopen 72 uur bijhoudt.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Querymachine RPO](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Querygegevenswijzigingssnelheid (verloop) en uploadsnelheid voor een Azure-vm

Met deze query wordt een trendgrafiek voor een specifieke Azure VM (ContosoVM123) weergegeven, die de snelheid van gegevenswijziging (Schrijfbytes per seconde) en de snelheid voor het uploaden van gegevens vertegenwoordigt. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Wijziging van querygegevens](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Querygegevenswijzigingssnelheid (churn) en uploadsnelheid voor een VMware- of fysieke machine

> [!Note]
> Zorg ervoor dat u de bewakingsagent op de processerver instelt om deze logboeken op te halen. Stappen [doorverwijzen om de bewakingsagent te configureren](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Deze query brengt een trendgrafiek voor een specifieke **schijfvan** een gerepliceerd item **win-9r7sfh9qlru**uit, dat de gegevenswijzigingssnelheid (Schrijfbytes per seconde) en de snelheid voor gegevensupload vertegenwoordigt. U de schijfnaam vinden op **het schijvenblad** van het gerepliceerde item in de kluis van herstelservices. Instantienaam die in de query moet worden gebruikt, is de DNS-naam van de machine, gevolgd door _ en de naam van de schijf, zoals in dit voorbeeld.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Process Server pusht deze gegevens elke 5 minuten naar de Log Analytics-werkruimte. Deze gegevenspunten vertegenwoordigen het gemiddelde berekend gedurende 5 minuten.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Querydisaster recovery summary (Azure to Azure)

Met deze query wordt een overzichtstabel voor Azure VM's gerepliceerd naar een secundair Azure-gebied.  Het toont VM naam, replicatie en bescherming status, RPO, test failover status, Mobility agent versie, eventuele actieve replicatie fouten, en de bronlocatie.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Querydisaster recovery summary (VMware/physical servers)

Met deze query wordt een overzichtstabel voor VMware VM's en fysieke servers die naar Azure zijn gerepliceerd, weergegeven.  Het toont machinenaam, replicatie en beveiligingsstatus, RPO, testfailoverstatus, Mobility agent-versie, actieve replicatiefouten en de relevante processerver.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Waarschuwingen instellen - voorbeelden

U waarschuwingen voor siteherstel instellen op basis van Azure Monitor-gegevens. [Meer informatie](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) over het instellen van logboekwaarschuwingen. 

> [!NOTE]
> Sommige voorbeelden gebruiken **replicationProviderName_s** ingesteld op **A2A**. Hiermee worden waarschuwingen ingesteld voor Azure VM's die worden gerepliceerd naar een secundair Azure-gebied. In deze voorbeelden u **A2A** vervangen door **InMageAzureV2** als u waarschuwingen wilt instellen voor on-premises Vm's vMware of fysieke servers die zijn gerepliceerd naar Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Meerdere machines in kritieke toestand

Stel een waarschuwing in als meer dan 20 gerepliceerde Azure VM's in kritieke status worden opgenomen.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Stel voor de waarschuwing **de drempelwaarde** in op 20.

### <a name="single-machine-in-a-critical-state"></a>Eén machine in kritieke toestand

Stel een waarschuwing in als een specifieke gerepliceerde Azure VM in kritieke toestand terechtkomt.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Stel voor de waarschuwing **drempelwaarde** in op 1.

### <a name="multiple-machines-exceed-rpo"></a>Meerdere machines overschrijden RPO

Stel een waarschuwing in als de RPO meer dan 20 Azure VM's meer dan 30 minuten bedraagt.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Stel voor de waarschuwing **de drempelwaarde** in op 20.

### <a name="single-machine-exceeds-rpo"></a>Enkele machine overschrijdt RPO

Stel een waarschuwing in als de RPO voor één Azure VM meer dan 30 minuten bedraagt.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Stel voor de waarschuwing **drempelwaarde** in op 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Testfailover voor meerdere machines meer dan 90 dagen

Stel een waarschuwing in als de laatste geslaagde testfailover meer dan 90 dagen was, voor meer dan 20 VM's. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Stel voor de waarschuwing **de drempelwaarde** in op 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Testfailover voor enkele machine meer dan 90 dagen

Stel een waarschuwing in als de laatste geslaagde testfailover voor een specifieke vm meer dan 90 dagen geleden was.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Stel voor de waarschuwing **drempelwaarde** in op 1.

### <a name="site-recovery-job-fails"></a>Site herstel taak mislukt

Stel een waarschuwing in als een sitehersteltaak (in dit geval de taak Opnieuw beveiligen) mislukt voor een scenario voor siteherstel, gedurende de laatste dag. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Stel voor de waarschuwing **de drempelwaarde** in op 1 en **Periode** op 1440 minuten om fouten op de laatste dag te controleren.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](site-recovery-monitor-and-troubleshoot.md) ingebouwde siteherstelbewaking.
