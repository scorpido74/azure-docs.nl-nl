---
title: Logboeken voor Azure-updatebeheer
description: In dit artikel wordt beschreven hoe u de logboeken voor Updatebeheer in uw logboekanalysewerkruimte opvragen.
services: automation
ms.subservice: update-management
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: f31168d47f31d8e740c95cb3d9e449f473cc78dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216847"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Query-updaterecords voor Updatebeheer in Azure-monitorlogboeken

Naast de details die worden verstrekt in de updatebeheeroplossing, u zoeken naar de logboeken die zijn opgeslagen in uw Log Analytics-werkruimte. Selecteer op de oplossingspagina in het linkerdeelvenster **Logboeken**. De pagina **Logboekzoeken** wordt geopend.

U ook leren hoe u de query's aanpassen of gebruiken van verschillende clients en meer door te bezoeken: [Log Analytics search API-documentatie](https://dev.loganalytics.io/).

## <a name="update-records"></a>Records bijwerken

Records die worden verzameld door Update Management voor Windows- en Linux-VM's en de gegevenstypen die worden weergegeven in de zoekresultaten van logboeken. In de volgende secties worden deze records beschreven.

### <a name="required-updates"></a>Vereiste updates

Er `RequiredUpdate` wordt een record gemaakt met een type dat wordt gemaakt voor updates die door een machine zijn vereist. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving | 
|----------|-------------|
| Computer | Volledig gekwalificeerde domeinnaam van de rapportagemachine. |
| KBID | Knowledge base artikel-ID voor de Windows-update. |
| ManagementGroupName | Naam van de beheergroep Operations Manager of de werkruimte Log Analytics. | 
| Product | De producten waarvoor de update van toepassing is. | 
| PublishDate | De datum waarop de update is gedownload en geïnstalleerd vanuit Windows Update. |
| server | | 
| SourceHealthServiceId | Unieke id die de Windows-agent-id van Log Analytics vertegenwoordigt. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unieke id die de instantie van Azure Active Directory van uw organisatie vertegenwoordigt. | 
| TimeGenerated | Datum en tijd waarop de record is gemaakt. | 
| Type | *Update* | 
| Classificatie bijwerken | Geeft aan welk type updates kan worden toegepast. Voor Windows:<br> *Essentiële updates*<br> *Beveiligingsupdates*<br> *Updatepakketten*<br> *Functiepakketten*<br> *Servicepacks*<br> *Definitie-updates*<br> *Hulpprogramma's*<br> *Updates*. Voor Linux:<br> *Essentiële en beveiligingsupdates*<br> *Overige* |
| Ernst bijwerken | Ernst beoordeling voor de kwetsbaarheid. Waarden zijn:<br> *Kritiek*<br> *Belangrijk*<br> *Matige*<br> *Lage* |
| Titel bijwerken | De titel van de update.|

### <a name="update"></a>Update

Er `Update` wordt een record gemaakt met een type updates die beschikbaar zijn en de installatiestatus ervan voor een machine. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving | 
|----------|-------------|
| GoedkeuringSbron | Alleen van toepassing op Windows-besturingssysteem. Waarde is *Microsoft Update*. |
| Goedgekeurd | *Waar* of *onwaar* |
| Classificatie | *Updates* |
| Computer | Volledig gekwalificeerde domeinnaam van de rapportagemachine. |
| Computeromgeving | *Azure* of *Niet-Azure*. |
| MSRCBulletinID | Id-nummer van beveiligingsbulletin | 
| MSRCSeverity | Ernst beoordeling voor de kwetsbaarheid. Waarden zijn:<br> *Kritiek*<br> *Belangrijk*<br> *Matige*<br> *Lage* |  
| KBID | Knowledge base artikel-ID voor de Windows-update. |
| ManagementGroupName | Naam van de beheergroep Operations Manager of de werkruimte Log Analytics. |
| UpdateID | Unieke id van de software-update. |
| RevisionNumber | Het revisienummer van een specifieke revisie van een update. |
| Optioneel | *Waar* of *onwaar* | 
| RebootBehavior | Het rebootgedrag na het installeren/verwijderen van een update. |
| _ResourceId | Unieke id voor de resource waarmee de record is gekoppeld. |
| Type | *Update* |
| VMUUID | Unieke id voor de virtuele machine. |
| MG | Unieke id voor de beheergroep of Log Analytics-werkruimte. | 
| TenantId | Unieke id die de instantie van Azure Active Directory van uw organisatie vertegenwoordigt. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Datum en tijd waarop de record is gemaakt. | 
| SourceComputerId | Unieke id die de broncomputer vertegenwoordigt. | 
| Titel | De titel van de update. |
| PublishedDate (UTC) | De datum waarop de update is gedownload en geïnstalleerd vanuit Windows Update.  |
| UpdateState | De huidige status van de update. | 
| Product | De producten waarvoor de update van toepassing is. |
| SubscriptionId | De unieke id voor het Azure-abonnement. | 
| ResourceGroup | Naam van de resourcegroep waarvan de resource lid is. | 
| ResourceProvider | Hiermee geeft u de resourceprovider op. | 
| Resource | De naam van de resource. | 
| ResourceType | Naam van het resourcetype. | 

### <a name="update-agent"></a>Agent bijwerken

Er `UpdateAgent` wordt een record gemaakt met een type tekst die details geeft over de updateagent op de machine. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving | 
|----------|-------------|
| AgeofoldestMissingRequiredUpdate | | 
| Automatisch bijwerkeningeschakeld | | 
| Computer | Volledig gekwalificeerde domeinnaam van de rapportagemachine. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Naam van de beheergroep Operations Manager of de werkruimte Log Analytics. |
| OSVersion | De versie van het besturingssysteem. |
| server | |
| SourceHealthServiceId | Unieke id die de Windows-agent-id van Log Analytics vertegenwoordigt. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unieke id die de instantie van Azure Active Directory van uw organisatie vertegenwoordigt. |
| TimeGenerated | Datum en tijd waarop de record is gemaakt. |
| Type | *Update* | 
| WindowsUpdateAgentVersion | Versie van de Windows Update-agent. |
| WSUSServer | Hiermee worden fouten weergegeven als de Windows Update-agent een probleem heeft om te helpen bij het oplossen van problemen. |

### <a name="update-deployment-status"></a>Implementatiestatus bijwerken 

Er `UpdateRunProgress` wordt een record gemaakt met een type bestand dat de implementatiestatus van een geplande implementatie per machine ondersteunt. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving | 
|----------|-------------|
| Computer | Volledig gekwalificeerde domeinnaam van de rapportagemachine. |
| Computeromgeving | *Azure* of *Niet-Azure*. | 
| CorrelationId | Unieke id van de runbook-taak die wordt uitgevoerd voor de update. |
| EndTime | Het moment waarop het synchronisatieproces is beëindigd. | 
| Foutresultaat | Windows Update-foutcode gegenereerd als een update niet wordt geïnstalleerd. | 
| Installatiestatus | De mogelijke installatiestatus van een update op de clientcomputer, *In uitvoering*, *Geslaagd*, *Gedeeltelijk mislukt*. |
| KBID | Knowledge base artikel-ID voor de Windows-update. | 
| ManagementGroupName | Naam van de beheergroep Operations Manager of de werkruimte Log Analytics. |
| OSType | Hiermee geeft u het type besturingssysteem, *Windows* of *Linux op.* | 
| Product | De producten waarvoor de update van toepassing is. |
| Resource | De naam van de resource. | 
| ResourceId | Unieke id voor de resource waarmee de record is gekoppeld. |
| ResourceProvider | Hiermee geeft u de resourceprovider op. | 
| ResourceType | Naam van het resourcetype. | 
| SourceComputerId | Unieke id die de broncomputer vertegenwoordigt. | 
| SourceSystem | *OperationsManager* |
| StartTime | Tijd wanneer de update is gepland om te worden geïnstalleerd. |
| SubscriptionId | De unieke id voor het Azure-abonnement. | 
| SucceededOnRetry | Hiermee wordt weergegeven wanneer de update-uitvoering is mislukt bij de eerste poging en de huidige bewerking een poging tot nieuwe poging is. |
| TimeGenerated | Datum en tijd waarop de record is gemaakt. |
| Titel | De titel van de update. |
| Type | *UpdateRunProgress* |
| UpdateId (UpdateId) | Unieke id van de software-update. |
| VMUUID | Unieke id voor de virtuele machine. |
| _ResourceId | Unieke id voor de resource waarmee de record is gekoppeld. |

### <a name="update-summary"></a>Overzicht bijwerken 

Er `UpdateSummary` wordt een record gemaakt met een type bestand dat updatesamenvatting per machine biedt. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving | 
|----------|-------------|
| Computer | Volledig gekwalificeerde domeinnaam van de rapportagemachine. |
| Computeromgeving | *Azure* of *Niet-Azure*. | 
| CriticalUpdatesMissing | Aantal kritieke updates dat van toepassing is. | 
| ManagementGroupName | Naam van de beheergroep Operations Manager of de werkruimte Log Analytics. |
| NETRuntimeVersion | Versie van .NET Framework geïnstalleerd op de Windows-computer. |
| OldestMissingSecurityUpdateBucket | Waarden zijn:<br> *Recent*<br> *30 dagen geleden*<br> *60 dagen geleden*<br> *Ouder* | 
| OldestMissingSecurityUpdateInDays | Totaal aantal dagen voor de oudste update die is gedetecteerd als van toepassing die niet is geïnstalleerd. |
| OsVersion | De versie van het besturingssysteem. |
| OtherUpdatesMissing | Aantal gedetecteerde updates ontbreekt. |
| Resource |  De naam van de resource. | 
| ResourceGroup | Naam van de resourcegroep waarvan de resource lid is. |
| ResourceId | Unieke id voor de resource waarmee de record is gekoppeld. |
| ResourceProvider | Hiermee geeft u de resourceprovider op. |
| ResourceType | Naam van het resourcetype. |
| Opnieuw opstartenAfhankelijk | *Waar* of *Onwaar*. |
| SecurityUpdatesMissing | Aantal ontbrekende beveiligingsupdates van toepassing.| 
| SourceComputerId | Unieke id voor de virtuele machine. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | De unieke id voor het Azure-abonnement. |
| TimeGenerated | Datum en tijd waarop de record is gemaakt. |
| TotalUpdatesMissing | Totaal aantal ontbrekende updates van toepassing. | 
| Type | *UpdateSummary* |
| VMUUID | Unieke id voor de virtuele machine. |
| WindowsUpdateAgentVersion | Versie van de Windows Update-agent. |
| WindowsUpdateSetting | Hiermee wordt de status van de Windows Update-agent weergegeven. Mogelijke waarden zijn:<br> *Geplande installatie*<br> *Melding vóór installatie*<br> Fout teruggekeerd van ongezonde WUA-agent. | 
| WSUSServer | Hiermee worden fouten weergegeven als de Windows Update-agent een probleem heeft om te helpen bij het oplossen van problemen. |
| _ResourceId | Unieke id voor de resource waarmee de record is gekoppeld. |

## <a name="sample-queries"></a>Voorbeeldquery's

In de volgende secties worden voorbeeldlogboekquery's opgenomen voor updaterecords die worden verzameld voor Updatebeheer.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Controleren of niet-Azure-machines aan boord zijn

Voer een van de volgende logboekzoekopdrachten uit om te bevestigen dat direct verbonden machines communiceren met Azure Monitor-logboeken.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Op een Windows-computer u de volgende gegevens controleren om de verbinding met Azure Monitor-logboeken te verifiëren:

1. Open **Microsoft Monitoring Agent**in het Configuratiescherm . Op het tabblad **Azure Log Analytics** geeft de agent het volgende bericht weer: de Microsoft Monitoring Agent is verbonden met **Logboekanalyse**.
2. Open het Windows-gebeurtenislogboek. Ga naar **Logboeken voor toepassingen en services\Operations Manager** en zoek naar gebeurtenis-id 3000 en gebeurtenis-id 5002 via de **bronserviceconnector**. Deze gebeurtenissen geven aan of de computer is geregistreerd bij de Log Analytics-werkruimte en of deze de configuratie ontvangt.

Als de agent niet kan communiceren met Azure Monitor-logboeken en de agent is geconfigureerd om met internet te communiceren via een firewall of proxyserver, controleert u of de firewall of proxyserver correct is geconfigureerd. Zie [Netwerkconfiguratie voor Windows-agent](../azure-monitor/platform/agent-windows.md) of [Netwerkconfiguratie voor Linux-agent voor](../log-analytics/log-analytics-agent-linux.md)meer informatie over het controleren van de firewall of proxyserver.

> [!NOTE]
> Als uw Linux-systemen zijn geconfigureerd om te communiceren met een proxy of Log Analytics Gateway en u deze oplossing onboarding, werkt u de *proxy.conf-machtigingen* bij om de omiusergroep toestemming te geven voor het bestand met behulp van de volgende opdrachten:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nieuw toegevoegde Linux-agents tonen een status van **bijgewerkt** nadat een beoordeling is uitgevoerd. Dit proces kan maximaal zes uur duren.

Zie [Operations Manager-integratie valideren met Azure Monitor-logboeken](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)als u wilt controleren of een beheergroep Operations Manager communiceert met Azure Monitor-logboeken.

### <a name="single-azure-vm-assessment-queries-windows"></a>Query's voor één Azure VM-beoordeling (Windows)

Vervang de VMUUID-waarde door de VM-GUID van de virtuele machine die u opvraagt. U de VMUUID vinden die moet worden gebruikt door de volgende query uit te voeren in Azure Monitor-logboeken:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Overzicht van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Query's voor één Azure VM -beoordeling (Linux)

Voor sommige Linux-distro's is er een [endianness-mismatch](https://en.wikipedia.org/wiki/Endianness) met de VMUUID-waarde die afkomstig is van Azure Resource Manager en wat is opgeslagen in Azure Monitor-logboeken. De volgende query controleert op een overeenkomst op een van beide endianness. Vervang de VMUUID-waarden door het grote endiaanse en -endian-formaat van de GUID om de resultaten correct te retourneren. U de VMUUID vinden die moet worden gebruikt door de volgende query uit te voeren in Azure Monitor-logboeken:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Overzicht van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Multi-VM-beoordelingsquery's

#### <a name="computers-summary"></a>Overzicht van computers

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

#### <a name="missing-updates-summary"></a>Overzicht van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lijst met computers

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Volgende stappen

* Gebruik logboekzoekopdrachten in [Azure Monitor-logboeken](../log-analytics/log-analytics-log-searches.md) om gedetailleerde updategegevens weer te geven.
* [Maak waarschuwingen](automation-tutorial-update-management.md#configure-alerts) voor de implementatiestatus van updates.
