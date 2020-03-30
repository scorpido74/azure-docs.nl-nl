---
title: Azure Monitor registreert gegevensmodel
description: Lees in dit artikel meer informatie over de gegevensmodellen van Azure Monitor Log Analytics voor Azure Backup-gegevens.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586373"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Logboekanalysegegevensmodel voor Azure Backup-gegevens

Gebruik het gegevensmodel Log Analytics om aangepaste waarschuwingen te maken van Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Dit gegevensmodel is een verwijzing naar de Azure Diagnostics Mode voor het verzenden van diagnostische gebeurtenissen naar Log Analytics (LA). Als u het gegevensmodel voor de nieuwe resourcespecifieke modus wilt leren, u verwijzen naar het volgende artikel: [Gegevensmodel voor diagnostische gebeurtenissen voor Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

## <a name="using-azure-backup-data-model"></a>Azure Backup-gegevensmodel gebruiken

U de volgende velden als onderdeel van het gegevensmodel gebruiken om visuals, aangepaste query's en dashboard te maken volgens uw vereisten.

### <a name="alert"></a>Waarschuwing

In deze tabel vindt u informatie over waarschuwingsvelden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| AlertUniqueId_s |Tekst |Unieke id van de gegenereerde waarschuwing |
| AlertType_s |Tekst |Type waarschuwing, bijvoorbeeld Back-up |
| AlertStatus_s |Tekst |Status van de waarschuwing, bijvoorbeeld Actief |
| AlertOccurrenceDateTime_s |Datum/tijd |Datum en tijd waarop een waarschuwing is gemaakt |
| AlertSeverity_s |Tekst |Ernst van de waarschuwing, bijvoorbeeld Kritiek |
|AlertTimeToResolveInMinutes_s    | Aantal        |Tijd die nodig is om een waarschuwing op te lossen. Leeg voor actieve waarschuwingen.         |
|AlertConsolidationStatus_s   |Tekst         |Identificeren of de waarschuwing een geconsolideerde waarschuwing is of niet         |
|CountOfAlertsConsolidated_s     |Aantal         |Aantal geconsolideerde waarschuwingen als het een geconsolideerde waarschuwing is          |
|AlertRaisedOn_s     |Tekst         |Type entiteit waarop de waarschuwing wordt gegenereerd         |
|AlertCode_s     |Tekst         |Code om een waarschuwingstype op unieke wijze te identificeren         |
|RecommendedAction_s   |Tekst         |Actie aanbevolen om de waarschuwing op te lossen         |
| EventName_s |Tekst |Naam van het evenement. Altijd AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unieke id van het back-upitem dat aan de waarschuwing is gekoppeld |
| SchemaVersion_s |Tekst |Huidige versie van het schema, bijvoorbeeld **V2** |
| State_s |Tekst |Huidige status van het waarschuwingsobject, bijvoorbeeld Actief, Verwijderd |
| BackupManagementType_s |Tekst |Providertype voor het uitvoeren van back-ups, bijvoorbeeld IaaSVM, FileFolder waartoe deze waarschuwing behoort |
| OperationName |Tekst |Naam van de huidige bewerking, bijvoorbeeld Alert |
| Categorie |Tekst |Categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht. Altijd AzureBackupReport |
| Resource |Tekst |Dit is de bron waarvoor gegevens worden verzameld, het toont Recovery Services kluis naam |
| ProtectedContainerUniqueId_s |Tekst |Unieke id van de beveiligde server die is gekoppeld aan de waarschuwing (Was ProtectedServerUniqueId_s in V1)|
| VaultUniqueId_s |Tekst |Unieke id van de beveiligde kluis die aan de waarschuwing is gekoppeld |
| SourceSystem |Tekst |Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst |Unieke id voor de bron over welke gegevens worden verzameld. Een vault resource-id van Recovery Services |
| SubscriptionId |Tekst |Abonnements-id van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |Resourcegroep van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resourceprovider waarvoor gegevens worden verzameld. For example, Microsoft.RecoveryServices |
| ResourceType |Tekst |Resourcetype waarvoor gegevens worden verzameld. Bijvoorbeeld, Vaults |

### <a name="backupitem"></a>Back-upitem

In deze tabel vindt u informatie over back-upitemgerelateerde velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| EventName_s |Tekst |Naam van het evenement. Altijd AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unieke id van het back-upitem |
| BackupItemId_s |Tekst |Id van back-upitem (Dit veld is alleen voor v1-schema) |
| BackupItemName_s |Tekst |Naam van back-upitem |
| BackupItemFriendlyName_s |Tekst |Vriendelijke naam van back-upitem |
| BackupItemType_s |Tekst |Type back-upitem, bijvoorbeeld VM, FileFolder |
| BackupItemProtectionState_s |Tekst |Beveiligingsstatus van het back-upitem |
| BackupItemAppVersion_s |Tekst |Toepassingsversie van het back-upitem |
| ProtectionState_s |Tekst |Huidige beveiligingsstatus van het back-upitem, bijvoorbeeld Protected, ProtectionStopped |
| ProtectionGroupName_s |Tekst | Naam van de beveiligingsgroep waarin het back-upitem is beveiligd, voor SC DPM en MABS, indien van toepassing|
| SecondaryBackupProtectionState_s |Tekst |Of secundaire beveiliging is ingeschakeld voor het back-upitem|
| SchemaVersion_s |Tekst |Versie van het schema, bijvoorbeeld **V2** |
| State_s |Tekst |Status van het object back-upitem, bijvoorbeeld Actief, Verwijderd |
| BackupManagementType_s |Tekst |Providertype voor het uitvoeren van back-ups, bijvoorbeeld IaaSVM, FileFolder waartoe dit back-upitem behoort |
| OperationName |Tekst |Naam van de bewerking, bijvoorbeeld BackupItem |
| Categorie |Tekst |Categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht. Altijd AzureBackupReport |
| Resource |Tekst |Bron waarvoor gegevens worden verzameld, bijvoorbeeld de naam van de vault van Recovery Services |
| SourceSystem |Tekst |Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst |Resource-id voor gegevens die worden verzameld, bijvoorbeeld vault resource-id van Recovery Services |
| SubscriptionId |Tekst |Abonnements-id van de resource (bijvoorbeeld. Recovery Services kluis) voor gegevens die worden verzameld |
| ResourceGroup |Tekst |Resourcegroep van de resource (bijvoorbeeld. Recovery Services kluis) voor gegevens die worden verzameld |
| ResourceProvider |Tekst |Resourceprovider voor gegevens die worden verzameld, bijvoorbeeld Microsoft.RecoveryServices |
| ResourceType |Tekst |Type van de bron voor gegevens die worden verzameld, bijvoorbeeld Kluizen |

### <a name="backupitemassociation"></a>BackupItemAssociation

In deze tabel vindt u informatie over koppelingen naar back-upitems met verschillende entiteiten.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| EventName_s |Tekst |Dit veld vertegenwoordigt de naam van deze gebeurtenis, het is altijd AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unieke id van het back-upitem |
| SchemaVersion_s |Tekst |Dit veld geeft de huidige versie van het schema aan, het is **V2** |
| State_s |Tekst |Huidige status van het koppelingsobject voor back-upitems, bijvoorbeeld Actief, Verwijderd |
| BackupManagementType_s |Tekst |Providertype voor server die back-upwerk doet, bijvoorbeeld IaaSVM, FileFolder |
| BackupItemSourceSize_s |Tekst | Front-endgrootte van het back-upitem |
| BackupManagementServerUniqueId_s |Tekst | Veld om de back-upbeheerserver op unieke wijze te identificeren, het back-upitem is beveiligd via, indien van toepassing |
| Categorie |Tekst |Dit veld vertegenwoordigt categorie diagnostische gegevens die naar Log Analytics zijn gepusht, het is AzureBackupReport |
| OperationName |Tekst |Dit veld vertegenwoordigt de naam van de huidige bewerking - BackupItemAssociation |
| Resource |Tekst |Dit is de bron waarvoor gegevens worden verzameld, het toont Recovery Services kluis naam |
| ProtectedContainerUniqueId_s |Tekst |Unieke id van de beveiligde server die is gekoppeld aan het back-upitem (Was ProtectedServerUniqueId_s in V1) |
| VaultUniqueId_s |Tekst |Unieke id van de kluis met het back-upitem |
| SourceSystem |Tekst |Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst |Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services |
| SubscriptionId |Tekst |Abonnements-id van de resource (bijvoorbeeld. Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |Resourcegroep van de resource (bijvoorbeeld. Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resourceprovider voor gegevens die worden verzameld, bijvoorbeeld Microsoft.RecoveryServices |
| ResourceType |Tekst |Type van de bron voor gegevens wordt verzameld, bijvoorbeeld Vaults |

### <a name="backupmanagementserver"></a>BackupManagementServer

In deze tabel vindt u informatie over koppelingen naar back-upitems met verschillende entiteiten.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
|BackupManagementServerName_s     |Tekst         |Naam van de back-upbeheerserver        |
|AzureBackupAgentVersion_s     |Tekst         |Versie van de Azure Backup Agent op de Backup Management Server          |
|BackupManagementServerVersion_s     |Tekst         |Versie van de back-upbeheerserver|
|BackupManagementServerOSVersion_s    |Tekst            |BE-versie van de Backup Management Server|
|BackupManagementServerType_s     |Tekst         |Type back-upbeheerserver als MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Tekst         |Veld om de back-upbeheerserver op unieke wijze te identificeren       |
| SourceSystem |Tekst |Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst |Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services |
| SubscriptionId |Tekst |Abonnements-id van de resource (bijvoorbeeld. Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |Resourcegroep van de resource (bijvoorbeeld. Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resourceprovider voor gegevens die worden verzameld, bijvoorbeeld Microsoft.RecoveryServices |
| ResourceType |Tekst |Type van de bron voor gegevens wordt verzameld, bijvoorbeeld Vaults |

### <a name="job"></a>Taak

In deze tabel vindt u meer informatie over taakgerelateerde velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| EventName_s |Tekst |Naam van het evenement. Altijd AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unieke id van het back-upitem |
| SchemaVersion_s |Tekst |Versie van het schema, bijvoorbeeld **V2** |
| State_s |Tekst |Huidige status van het taakobject, bijvoorbeeld Actief, Verwijderd |
| BackupManagementType_s |Tekst |Providertype voor server die back-upwerk doet, bijvoorbeeld IaaSVM, FileFolder |
| OperationName |Tekst |Dit veld vertegenwoordigt de naam van de huidige bewerking - Taak |
| Categorie |Tekst |Dit veld vertegenwoordigt categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht, het is AzureBackupReport |
| Resource |Tekst |Dit is de bron waarvoor gegevens worden verzameld, het toont Recovery Services kluis naam |
| ProtectedServerUniqueId_s |Tekst |Unieke id van de beveiligde server die aan de taak is gekoppeld |
| ProtectedContainerUniqueId_s |Tekst | Unieke ID om de beveiligde container te identificeren waarop de taak wordt uitgevoerd |
| VaultUniqueId_s |Tekst |Unieke id van de beveiligde kluis |
| JobOperation_s |Tekst |Bewerking waarvoor taak wordt uitgevoerd, bijvoorbeeld Back-up, herstel, Back-up configureren |
| JobStatus_s |Tekst |Status van de voltooide taak, bijvoorbeeld Voltooid, Mislukt |
| JobFailureCode_s |Tekst |Tekenreeks Foutcode waardoor er een fout is gemaakt |
| JobStartDateTime_s |Datum/tijd |Datum en tijd waarop de taak is gestart |
| BackupStorageDestination_s |Tekst |Bestemming van back-upopslag, bijvoorbeeld Cloud, Disk  |
| AdHocOrScheduledJob_s |Tekst | Veld om aan te geven of de taak Ad Hoc of Gepland is |
| JobDurationInSecs_s | Aantal |Totale taakduur in seconden |
| DataTransferredInMB_s | Aantal |Gegevens die in MB worden overgedragen voor deze taak|
| JobUniqueId_g |Tekst |Unieke ID om de taak te identificeren |
| RecoveryJobDestination_s |Tekst | Bestemming van een hersteltaak, waarbij de gegevens worden hersteld |
| RecoveryJobRPDateTime_s |DateTime | De datum, tijd waarop het herstelpunt is gemaakt dat wordt hersteld |
| RecoveryJobRPLocation_s |Tekst | De locatie waar het herstelpunt dat wordt teruggevonden is opgeslagen|
| SourceSystem |Tekst |Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst |Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services|
| SubscriptionId |Tekst |Abonnements-id van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |Resourcegroep van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resourceprovider waarvoor gegevens worden verzameld. For example, Microsoft.RecoveryServices |
| ResourceType |Tekst |Resourcetype waarvoor gegevens worden verzameld. Bijvoorbeeld, Vaults |

### <a name="policy"></a>Beleid

In deze tabel vindt u meer informatie over beleidsgerelateerde velden.

| Veld | Gegevenstype | Toepasselijke versies | Beschrijving |
| --- | --- | --- | --- |
| EventName_s |Tekst ||Dit veld vertegenwoordigt de naam van deze gebeurtenis, het is altijd AzureBackupCentralReport |
| SchemaVersion_s |Tekst ||Dit veld geeft de huidige versie van het schema aan, het is **V2** |
| State_s |Tekst ||Huidige status van het beleidsobject, bijvoorbeeld Actief, Verwijderd |
| BackupManagementType_s |Tekst ||Providertype voor server die back-upwerk doet, bijvoorbeeld IaaSVM, FileFolder |
| OperationName |Tekst ||Dit veld vertegenwoordigt de naam van de huidige bewerking - Beleid |
| Categorie |Tekst ||Dit veld vertegenwoordigt categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht, het is AzureBackupReport |
| Resource |Tekst ||Dit is de bron waarvoor gegevens worden verzameld, het toont Recovery Services kluis naam |
| PolicyUniqueId_g |Tekst ||Unieke id om het beleid te identificeren |
| PolicyName_s |Tekst ||Naam van het gedefinieerde beleid |
| BackupFrequency_s |Tekst ||Frequentie waarmee back-ups worden uitgevoerd, bijvoorbeeld dagelijks, wekelijks |
| BackupTimes_s |Tekst ||Datum en tijd waarop back-ups zijn gepland |
| BackupDaysOfTheWeek_s |Tekst ||Dagen van de week waarop back-ups zijn gepland |
| RetentionDuration_s |Geheel getal ||Bewaarduur voor geconfigureerde back-ups |
| DailyRetentionDuration_s |Geheel getal ||Totale bewaarduur in dagen voor geconfigureerde back-ups |
| DailyRetentionTimes_s |Tekst ||Datum en tijd waarop de dagelijkse retentie is geconfigureerd |
| WeeklyRetentionDuration_s |Decimaal getal ||Totale wekelijkse bewaarduur in weken voor geconfigureerde back-ups |
| WeeklyRetentionTimes_s |Tekst ||Datum en tijd waarop de wekelijkse retentie is geconfigureerd |
| WeeklyRetentionDaysOfTheWeek_s |Tekst ||Dagen van de week geselecteerd voor wekelijkse retentie |
| MonthlyRetentionDuration_s |Decimaal getal ||Totale bewaarduur in maanden voor geconfigureerde back-ups |
| MonthlyRetentionTimes_s |Tekst ||Datum en tijd waarop de maandelijkse retentie is geconfigureerd |
| MonthlyRetentionFormat_s |Tekst ||Type configuratie voor maandelijkse retentie, bijvoorbeeld dagelijks voor daggebaseerd, wekelijks voor weekgebaseerd |
| MonthlyRetentionDaysOfTheWeek_s |Tekst ||Dagen van de week geselecteerd voor maandelijkse retentie |
| MonthlyRetentionWeeksOfTheMonth_s |Tekst ||Weken van de maand waarin maandelijkse retentie is geconfigureerd, bijvoorbeeld First, Last etc. |
| YearlyRetentionDuration_s |Decimaal getal ||Totale bewaarduur in jaren voor geconfigureerde back-ups |
| YearlyRetentionTimes_s |Tekst ||Datum en tijd waarop de jaarlijkse retentie is geconfigureerd |
| YearlyRetentionMonthsOfTheYear_s |Tekst ||Maanden van het jaar geselecteerd voor jaarlijkse retentie |
| YearlyRetentionFormat_s |Tekst ||Type configuratie voor jaarlijkse retentie, bijvoorbeeld dagelijks voor daggebaseerd, wekelijks voor weekgebaseerd | |
| YearlyRetentionDaysOfTheMonth_s |Tekst ||Datums van de maand geselecteerd voor jaarlijkse retentie |
| SynchronisationFrequencyPerDay_s |Geheel getal |v2|Aantal keren op een dag dat een bestandsback-up wordt gesynchroniseerd voor SC DPM en MABS |
| DiffBackupFormat_s |Tekst |v2|Indeling voor differentiële back-ups voor SQL in Azure VM-back-up |
| DiffBackupTime_s |Time |v2|Tijd voor differentiële back-ups voor SQL in Azure VM Backup|
| DiffBackupRetentionDuration_s |Decimaal getal |v2|Bewaarduur voor differentiële back-ups voor SQL in Azure VM Backup|
| LogBackupFrequency_s |Decimaal getal |v2|Frequentie voor Logboekback-ups voor SQL|
| LogBackupRetentionDuration_s |Decimaal getal |v2|Bewaarduur voor Logboekbackups voor SQL in Azure VM Backup|
| DiffBackupDaysofTheWeek_s |Tekst |v2|Dagen van de week voor differentiële back-ups voor SQL in Azure VM Backup|
| SourceSystem |Tekst ||Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst ||Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services |
| SubscriptionId |Tekst ||Abonnements-id van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst ||Resourcegroep van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst ||Resourceprovider waarvoor gegevens worden verzameld. For example, Microsoft.RecoveryServices |
| ResourceType |Tekst ||Resourcetype waarvoor gegevens worden verzameld. Bijvoorbeeld, Vaults |

### <a name="policyassociation"></a>PolicyAssociation

In deze tabel vindt u informatie over beleidsassociaties met verschillende entiteiten.

| Veld | Gegevenstype | Toepasselijke versies | Beschrijving |
| --- | --- | --- | --- |
| EventName_s |Tekst ||Dit veld vertegenwoordigt de naam van deze gebeurtenis, het is altijd AzureBackupCentralReport |
| SchemaVersion_s |Tekst ||Dit veld geeft de huidige versie van het schema aan, het is **V2** |
| State_s |Tekst ||Huidige status van het beleidsobject, bijvoorbeeld Actief, Verwijderd |
| BackupManagementType_s |Tekst ||Providertype voor server die back-upwerk doet, bijvoorbeeld IaaSVM, FileFolder |
| OperationName |Tekst ||Dit veld vertegenwoordigt de naam van de huidige bewerking - PolicyAssociation |
| Categorie |Tekst ||Dit veld vertegenwoordigt categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht, het is AzureBackupReport |
| Resource |Tekst ||Dit is de bron waarvoor gegevens worden verzameld, het toont Recovery Services kluis naam |
| PolicyUniqueId_g |Tekst ||Unieke id om het beleid te identificeren |
| VaultUniqueId_s |Tekst ||Unieke ID van de kluis waartoe dit beleid behoort |
| BackupManagementServerUniqueId_s |Tekst |v2 |Veld om de back-upbeheerserver op unieke wijze te identificeren, het back-upitem is beveiligd via, indien van toepassing        |
| SourceSystem |Tekst ||Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst ||Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services |
| SubscriptionId |Tekst ||Abonnements-id van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst ||Resourcegroep van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst ||Resourceprovider waarvoor gegevens worden verzameld. For example, Microsoft.RecoveryServices |
| ResourceType |Tekst ||Resourcetype waarvoor gegevens worden verzameld. Bijvoorbeeld, Vaults |

### <a name="protected-container"></a>Beveiligde container

Deze tabel bevat basisvelden over beveiligde containers. (Was ProtectedServer in v1)

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Tekst | Veld om een beveiligde container op unieke wijze te identificeren |
| ProtectedContainerOSType_s |Tekst |OS-type van de beveiligde container |
| ProtectedContainerOSVersion_s |Tekst |OS-versie van de beveiligde container |
| AgentVersion_s |Tekst |Versienummer van Agent Backup of de Protection Agent (in het geval van SC DPM en MABS) |
| BackupManagementType_s |Tekst |Providertype voor het uitvoeren van back-ups. For example, IaaSVM, FileFolder |
| EntityState_s |Tekst |Huidige status van het beveiligde serverobject. Actief, verwijderd |
| ProtectedContainerFriendlyName_s |Tekst |Vriendelijke naam van beveiligde server |
| ProtectedContainerName_s |Tekst |Naam van de beschermde container |
| ProtectedContainerWorkloadType_s |Tekst |Er wordt een back-up van het type beveiligde container back-ups. For example, IaaSVMContainer |
| ProtectedContainerLocation_s |Tekst |Of de beveiligde container zich on-premises of in Azure bevindt |
| ProtectedContainerType_s |Tekst |Of de beveiligde container een server of een container is |
| ProtectedContainerProtectionState_s.  |Tekst |Beschermingstoestand van de beschermde container |

### <a name="storage"></a>Storage

In deze tabel vindt u informatie over opslaggerelateerde velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| CloudStorageInBytes_s |Decimaal getal |Cloudback-upopslag die wordt gebruikt door back-ups, berekend op basis van de laatste waarde (Dit veld is alleen voor v1-schema)|
| ProtectedInstances_s |Decimaal getal |Aantal beveiligde exemplaren dat wordt gebruikt voor het berekenen van frontendopslag in facturering, berekend op basis van de meest recente waarde |
| EventName_s |Tekst |Dit veld vertegenwoordigt de naam van deze gebeurtenis, het is altijd AzureBackupCentralReport |
| SchemaVersion_s |Tekst |Dit veld geeft de huidige versie van het schema aan, het is **V2** |
| State_s |Tekst |Huidige status van het opslagobject, bijvoorbeeld Actief, Verwijderd |
| BackupManagementType_s |Tekst |Providertype voor server die back-upwerk doet, bijvoorbeeld IaaSVM, FileFolder |
| OperationName |Tekst |Dit veld vertegenwoordigt de naam van de huidige bewerking - Opslag |
| Categorie |Tekst |Dit veld vertegenwoordigt categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht, het is AzureBackupReport |
| Resource |Tekst |Dit is de bron waarvoor gegevens worden verzameld, het toont Recovery Services kluis naam |
| ProtectedServerUniqueId_s |Tekst |Unieke id van de beveiligde server waarvoor opslag wordt berekend |
| VaultUniqueId_s |Tekst |Unieke ID van de kluis voor opslag wordt berekend |
| SourceSystem |Tekst |Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst |Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services |
| SubscriptionId |Tekst |Abonnements-id van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |Resourcegroep van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resourceprovider waarvoor gegevens worden verzameld. For example, Microsoft.RecoveryServices |
| ResourceType |Tekst |Resourcetype waarvoor gegevens worden verzameld. Bijvoorbeeld, Vaults |
| StorageUniqueId_s |Tekst |Unieke id die wordt gebruikt om de opslagentiteit te identificeren |
| StorageType_s |Tekst |Type opslag, bijvoorbeeld Cloud, Volume, Schijf |
| StorageName_s |Tekst |Naam van de opslagentiteit, bijvoorbeeld E:\ |
| StorageTotalSizeInGBs_s |Tekst |Totale omvang van de opslag, in GB, verbruikt door opslagentiteit|

### <a name="storageassociation"></a>StorageAssociation

Deze tabel biedt basisopslaggerelateerde velden die opslag verbinden met andere entiteiten.

| Veld | Gegevenstype | Beschrijving |
| --- | --- |  --- |
| StorageUniqueId_s |Tekst |Unieke id die wordt gebruikt om de opslagentiteit te identificeren |
| SchemaVersion_s |Tekst |Dit veld geeft de huidige versie van het schema aan, het is **V2** |
| BackupItemUniqueId_s |Tekst |Unieke id die wordt gebruikt om het back-upitem te identificeren dat betrekking heeft op de opslagentiteit |
| BackupManagementServerUniqueId_s |Tekst |Unieke id die wordt gebruikt om de back-upbeheerserver met betrekking tot de opslagentiteit te identificeren|
| VaultUniqueId_s |Tekst |Unieke id die wordt gebruikt om de kluis met betrekking tot de opslagentiteit te identificeren|
| StorageConsumedInMBs_s |Aantal|Omvang van de opslag die wordt verbruikt door het bijbehorende back-upitem in de desbetreffende opslag |
| StorageAllocatedInMBs_s |Aantal |Grootte van de opslag toegewezen door het overeenkomstige back-upitem in de overeenkomstige opslag van het type Disk|

### <a name="vault"></a>Kluis

In deze tabel vindt u informatie over kluisgerelateerde velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| EventName_s |Tekst |Dit veld vertegenwoordigt de naam van deze gebeurtenis, het is altijd AzureBackupCentralReport |
| SchemaVersion_s |Tekst |Dit veld geeft de huidige versie van het schema aan, het is **V2** |
| State_s |Tekst |Huidige status van het kluisobject, bijvoorbeeld Actief, Verwijderd |
| OperationName |Tekst |Dit veld vertegenwoordigt de naam van de huidige bewerking - Vault |
| Categorie |Tekst |Dit veld vertegenwoordigt categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht, het is AzureBackupReport |
| Resource |Tekst |Dit is de bron waarvoor gegevens worden verzameld, het toont Recovery Services kluis naam |
| VaultUniqueId_s |Tekst |Unieke ID van de kluis |
| VaultName_s |Tekst |Naam van de kluis |
| AzureDataCenter_s |Tekst |Datacenter waar de kluis zich bevindt |
| StorageReplicationType_s |Tekst |Type opslagreplicatie voor de kluis, bijvoorbeeld GeoRedundant |
| SourceSystem |Tekst |Bronsysteem van de huidige gegevens - Azure |
| ResourceId |Tekst |Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services |
| SubscriptionId |Tekst |Abonnements-id van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |Resourcegroep van de resource (bijvoorbeeld. Vault Recovery Services) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resourceprovider waarvoor gegevens worden verzameld. For example, Microsoft.RecoveryServices |
| ResourceType |Tekst |Resourcetype waarvoor gegevens worden verzameld. Bijvoorbeeld, Vaults |

### <a name="backup-management-server"></a>Back-upbeheerserver

Deze tabel bevat basisvelden over back-upbeheerservers.

|Veld  |Gegevenstype  | Beschrijving  |
|---------|---------|----------|
|BackupManagementServerName_s     |Tekst         |Naam van de back-upbeheerserver        |
|AzureBackupAgentVersion_s     |Tekst         |Versie van de Azure Backup Agent op de Backup Management Server          |
|BackupManagementServerVersion_s     |Tekst         |Versie van de back-upbeheerserver|
|BackupManagementServerOSVersion_s     |Tekst            |BE-versie van de Backup Management Server|
|BackupManagementServerType_s     |Tekst         |Type back-upbeheerserver als MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Tekst         |Veld om de back-upbeheerserver op unieke wijze te identificeren       |

### <a name="preferredworkloadonvolume"></a>PreferredworkloadonVolume

In deze tabel wordt de werkbelasting(en) opgegeven waarmee een volume is gekoppeld.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| StorageUniqueId_s |Tekst |Unieke id die wordt gebruikt om de opslagentiteit te identificeren |
| BackupItemType_s |Tekst |De workloads waarvoor dit volume de voorkeur heeft|

### <a name="protectedinstance"></a>ProtectedInstance

Deze tabel biedt basisvelden voor beveiligde instanties.

| Veld | Gegevenstype |Toepasselijke versies | Beschrijving |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Tekst |v2|Unieke id die wordt gebruikt om het back-upitem voor VM's te identificeren dat is back-ups met Behulp van DPM, MABS|
| ProtectedContainerUniqueId_s |Tekst |v2|Unieke ID die wordt gebruikt om de beschermde container te identificeren voor alles behalve VM's een back-up met behulp van DPM, MABS|
| ProtectedInstanceCount_s |Tekst |v2|Aantal beveiligde exemplaren voor het bijbehorende back-upitem of beveiligde container op die datumtijd|

### <a name="recoverypoint"></a>Herstelpunt

Deze tabel biedt basisvelden voor herstelpunten.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| BackupItemUniqueId_s |Tekst |Unieke id die wordt gebruikt om het back-upitem voor VM's te identificeren dat is back-ups met Behulp van DPM, MABS|
| OldestRecoveryPointTime_s |Tekst |Datumtijd van het oudste herstelpunt voor het back-upitem|
| OldestRecoveryPointLocation_s |Tekst |Locatie van het oudste herstelpunt voor het back-upitem|
| LatestRecoveryPointTime_s |Tekst |Datum van het laatste herstelpunt voor het back-upitem|
| LatestRecoveryPointLocation_s |Tekst |Locatie van het laatste herstelpunt voor het back-upitem|

## <a name="sample-kusto-queries"></a>Voorbeeld van Kusto-query's

Hieronder vindt u enkele voorbeelden waarmee u query's schrijven op Azure Backup-gegevens die zich in de tabel Azure Diagnostics bevinden:

- Alle succesvolle back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Alle mislukte back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Alle succesvolle Azure VM-back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alle succesvolle SQL-logboekback-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alle succesvolle Azure Backup-agenttaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````
    
## <a name="next-steps"></a>Volgende stappen

Zodra u het gegevensmodel hebt bekeken, u beginnen met [het maken van aangepaste query's](../azure-monitor/learn/tutorial-logs-dashboards.md) in Azure Monitor-logboeken om uw eigen dashboard te bouwen.
