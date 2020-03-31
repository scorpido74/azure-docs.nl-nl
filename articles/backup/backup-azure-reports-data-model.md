---
title: Gegevensmodel voor azure backup-diagnostische gebeurtenissen
description: Dit gegevensmodel verwijst naar de resourcespecifieke modus voor het verzenden van diagnostische gebeurtenissen naar Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583381"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Gegevensmodel voor Azure Backup Diagnostics-gebeurtenissen

## <a name="coreazurebackup"></a>CoreAzureBackup

In deze tabel vindt u informatie over kernback-upentiteiten, zoals kluizen en back-upitems.

| **Veld**                         | **Gegevenstype** | **Beschrijving**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Tekst          | Resource-id voor gegevens die worden verzameld. Bijvoorbeeld vault resource ID van Recovery Services. |
| OperationName                     | Tekst          | Dit veld vertegenwoordigt de naam van de huidige bewerking - BackupItem, BackupItemAssociation of ProtectedContainer. |
| Categorie                          | Tekst          | Dit veld vertegenwoordigt de categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht. CoreAzureBackup bijvoorbeeld. |
| AgentVersie                      | Tekst          | Versienummer van Agent Backup of de Protection Agent (in het geval van SC DPM en MABS) |
| AzureBackupAgentVersion           | Tekst          | Versie van de Azure Backup Agent op de Backup Management Server |
| AzureDataCenter                   | Tekst          | Datacenter waar de kluis zich bevindt                       |
| BackupItemAppVersie              | Tekst          | Toepassingsversie van het back-upitem                       |
| BackupItemFriendlyName            | Tekst          | Vriendelijke naam van het back-upitem                             |
| BackupItemName                    | Tekst          | Naam van het back-upitem                                      |
| BackupItemProtectionState         | Tekst          | Beveiligingsstatus van het back-upitem                          |
| BackupitemFrontendsize            | Tekst          | Front-endgrootte van het back-upitem                            |
| BackupItemType                    | Tekst          | Type back-upitem. Bijvoorbeeld: VM, FileFolder             |
| BackupItemUniqueId                | Tekst          | Unieke id van het back-upitem                         |
| BackupManagementServerType        | Tekst          | Type back-upbeheerserver, zoals in MABS, SC DPM     |
| BackupManagementServerUniqueId    | Tekst          | Veld om de back-upbeheerserver op unieke wijze te identificeren      |
| BackupManagementType              | Tekst          | Providertype voor server die back-upwerk doet. For example, IaaSVM, FileFolder |
| BackupManagementServerName        | Tekst          | Naam van de back-upbeheerserver                         |
| BackupManagementServerOSVersie   | Tekst          | BE-versie van de Backup Management Server                   |
| BackupManagementServerVersie     | Tekst          | Versie van de back-upbeheerserver                      |
| Laatste RecoveryPointLocatie       | Tekst          | Locatie van het laatste herstelpunt voor het back-upitem    |
| Laatste RecoveryPointTime           | DateTime      | Datum van het laatste herstelpunt voor het back-upitem   |
| OudsteRecoveryPointLocatie       | Tekst          | Locatie van het oudste herstelpunt voor het back-upitem    |
| OudsteRecoveryPointTime           | DateTime      | Datum van het laatste herstelpunt voor het back-upitem   |
| PolicyUniqueId                    | Tekst          | Unieke id om het beleid te identificeren                             |
| ProtectedContainerFriendlyName    | Tekst          | Vriendelijke naam van de beveiligde server                        |
| ProtectedContainerLocatie        | Tekst          | Of de beveiligde container zich on-premises of in Azure bevindt |
| ProtectedContainerName            | Tekst          | Naam van de beschermde container                            |
| ProtectedContainerOSType          | Tekst          | OS-type van de beveiligde container                          |
| ProtectedContainerOSVersie       | Tekst          | OS-versie van de beveiligde container                        |
| ProtectedContainerProtectionState | Tekst          | Beschermingstoestand van de beschermde container                  |
| ProtectedContainerType            | Tekst          | Of de beveiligde container een server of een container is  |
| ProtectedContainerUniqueId        | Tekst          | Unieke ID die wordt gebruikt om de beschermde container te identificeren voor alles behalve VM's een back-up met behulp van DPM, MABS |
| ProtectedContainerWorkloadType    | Tekst          | Er wordt een back-up van het type beveiligde container back-ups. For example, IaaSVMContainer |
| ProtectionGroupName               | Tekst          | Naam van de beveiligingsgroep waarin het back-upitem is beveiligd, voor SC DPM en MABS, indien van toepassing |
| ResourceGroupName                 | Tekst          | Resourcegroep van de resource (bijvoorbeeld de kluis Van Herstelservices) voor gegevens die worden verzameld |
| Schemaversie                     | Tekst          | Dit veld geeft de huidige versie van het schema aan, het is **V2** |
| Secundairback-upProtectionState    | Tekst          | Of secundaire beveiliging is ingeschakeld voor het back-upitem  |
| Status                             | Tekst          | Status van het object back-upitem. Actief, verwijderd |
| StorageReplicationType            | Tekst          | Type opslagreplicatie voor de kluis. GeoRedundant bijvoorbeeld |
| SubscriptionId                    | Tekst          | Abonnements-id van de bron (bijvoorbeeld de kluis Van Herstelservices) waarvoor gegevens worden verzameld |
| VaultName                         | Tekst          | Naam van de kluis                                            |
| VaultTags VaultTags                         | Tekst          | Tags die zijn gekoppeld aan de kluisbron                    |
| VaultUniqueId VaultUniqueId                     | Tekst          | Unieke identificatie van de kluis                             |
| SourceSystem                      | Tekst          | Bronsysteem van de huidige gegevens - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

In deze tabel vindt u informatie over waarschuwingsvelden.

| **Veld**                      | **Gegevenstype** | **Beschrijving**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Unieke id voor de bron over welke gegevens worden verzameld. Een vault resource-id van Recovery Services |
| OperationName                  | Tekst          | Naam van de huidige bewerking. Waarschuwing en waarschuwing            |
| Categorie                       | Tekst          | Categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht - AddonAzureBackupAlerts |
| Waarschuwingscode                      | Tekst          | Code om een waarschuwingstype op unieke wijze te identificeren                     |
| Status van waarschuwingsconsolidatie       | Tekst          | Identificeren of de waarschuwing een geconsolideerde waarschuwing is of niet         |
| AlertOccurrenceDateTime        | DateTime      | Datum en tijd waarop de waarschuwing is gemaakt                     |
| AlertRaisedOn AlertRaisedOn AlertRaisedOn AlertRaised                  | Tekst          | Type entiteit waarop de waarschuwing wordt gegenereerd                        |
| WaarschuwingErnst                  | Tekst          | Ernst van de waarschuwing. Kritieke                 |
| Waarschuwingsstatus                    | Tekst          | Status van de waarschuwing. Active bijvoorbeeld                     |
| AlertTimetoResolveInMinutes    | Aantal        | Tijd die nodig is om een waarschuwing op te lossen. Leeg voor actieve waarschuwingen.     |
| Waarschuwingstype                      | Tekst          | Type waarschuwing. Back-up bijvoorbeeld                           |
| AlertUniqueId                  | Tekst          | Unieke id van de gegenereerde waarschuwing                    |
| BackupItemUniqueId             | Tekst          | Unieke id van het back-upitem dat aan de waarschuwing is gekoppeld |
| BackupManagementServerUniqueId | Tekst          | Veld om de back-upbeheerserver op unieke wijze te identificeren, het back-upitem is beveiligd via, indien van toepassing |
| BackupManagementType           | Tekst          | Providertype voor server die back-upwerk doet, bijvoorbeeld IaaSVM, FileFolder |
| Aantal waarschuwingengeconsolideerd      | Aantal        | Aantal geconsolideerde waarschuwingen als het een geconsolideerde waarschuwing is  |
| ProtectedContainerUniqueId     | Tekst          | Unieke id van de beveiligde server die aan de waarschuwing is gekoppeld |
| Aanbevolen actie              | Tekst          | Actie aanbevolen om de waarschuwing op te lossen                      |
| Schemaversie                  | Tekst          | Huidige versie van het schema, bijvoorbeeld **V2**            |
| Status                          | Tekst          | Huidige status van het waarschuwingsobject, bijvoorbeeld Actief, Verwijderd |
| StorageUniqueId                | Tekst          | Unieke id die wordt gebruikt om de opslagentiteit te identificeren                |
| VaultUniqueId VaultUniqueId                  | Tekst          | Unieke id die wordt gebruikt om de kluis met betrekking tot de waarschuwing te identificeren    |
| SourceSystem                   | Tekst          | Bronsysteem van de huidige gegevens - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Deze tabel biedt basisvelden voor beveiligde instanties.

| **Veld**                      | **Gegevenstype** | **Beschrijving**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Unieke id voor de bron over welke gegevens worden verzameld. Een vault resource-id van Recovery Services |
| OperationName                  | Tekst          | Naam van de bewerking, bijvoorbeeld ProtectedInstance         |
| Categorie                       | Tekst          | Categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht - AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Tekst          | Unieke id van het back-upitem                                 |
| BackupManagementServerUniqueId | Tekst          | Veld om de back-upbeheerserver op unieke wijze te identificeren, het back-upitem is beveiligd via, indien van toepassing |
| BackupManagementType           | Tekst          | Providertype voor server die back-upwerk doet, bijvoorbeeld IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Tekst          | Unieke ID om de beveiligde container te identificeren waarop de taak wordt uitgevoerd |
| ProtectedInstanceCount         | Tekst          | Aantal beveiligde exemplaren voor het bijbehorende back-upitem of beveiligde container op die datumtijd |
| Schemaversie                  | Tekst          | Huidige versie van het schema, bijvoorbeeld **V2**            |
| Status                          | Tekst          | Status van het object back-upitem, bijvoorbeeld Actief, Verwijderd |
| VaultUniqueId VaultUniqueId                  | Tekst          | Unieke id van de beveiligde kluis die is gekoppeld aan de beveiligde instantie |
| SourceSystem                   | Tekst          | Bronsysteem van de huidige gegevens - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

In deze tabel vindt u meer informatie over taakgerelateerde velden.

| **Veld**                      | **Gegevenstype** | **Beschrijving**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services |
| OperationName                  | Tekst          | Dit veld vertegenwoordigt de naam van de huidige bewerking - Taak    |
| Categorie                       | Tekst          | Dit veld vertegenwoordigt categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht - AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Tekst          | Veld om aan te geven of de taak Ad Hoc of Gepland is           |
| BackupItemUniqueId             | Tekst          | Unieke id die wordt gebruikt om het back-upitem te identificeren dat betrekking heeft op de opslagentiteit |
| BackupManagementServerUniqueId | Tekst          | Unieke id die wordt gebruikt om de back-upbeheerserver met betrekking tot de opslagentiteit te identificeren |
| BackupManagementType           | Tekst          | Providertype voor het uitvoeren van back-ups, bijvoorbeeld IaaSVM, FileFolder waartoe deze waarschuwing behoort |
| DataTransferredInMB            | Aantal        | Gegevens die in MB worden overgedragen voor deze taak                          |
| JobDurationInSecs              | Aantal        | Totale taakduur in seconden                                |
| JobFailureCode                 | Tekst          | Tekenreeks Foutcode waardoor er een fout is gemaakt    |
| JobOperation (JobOperation)                   | Tekst          | Bewerking waarvoor taak wordt uitgevoerd, bijvoorbeeld Back-up, herstel, Back-up configureren |
| JobOperationSubType            | Tekst          | Subtype van de taakbewerking. Bijvoorbeeld 'Log', in het geval van Log Backup Job |
| JobStartDateTime               | DateTime      | Datum en tijd waarop de taak is gestart                       |
| JobStatus                      | Tekst          | Status van de voltooide taak, bijvoorbeeld Voltooid, Mislukt   |
| JobUniqueId                    | Tekst          | Unieke ID om de taak te identificeren                                |
| ProtectedContainerUniqueId     | Tekst          | Unieke id van de beveiligde server die aan de waarschuwing is gekoppeld |
| RecoveryJobDestination         | Tekst          | Bestemming van een hersteltaak, waarbij de gegevens worden hersteld   |
| RecoveryJobRPDateTime          | DateTime      | De datum, het tijdstip waarop het herstelpunt is gemaakt dat wordt hersteld |
| RecoveryJobLocatie            | Tekst          | De locatie waar het herstelpunt dat wordt teruggevonden is opgeslagen |
| HerstelLocatietype           | Tekst          | Type herstellocatie                                |
| Schemaversie                  | Tekst          | Huidige versie van het schema, bijvoorbeeld **V2**            |
| Status                          | Tekst          | Huidige status van het waarschuwingsobject, bijvoorbeeld Actief, Verwijderd |
| VaultUniqueId VaultUniqueId                  | Tekst          | Unieke id van de beveiligde kluis die aan de waarschuwing is gekoppeld |
| SourceSystem                   | Tekst          | Bronsysteem van de huidige gegevens - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

In deze tabel vindt u meer informatie over beleidsgerelateerde velden.

| **Veld**                       | **Gegevenstype**  | **Beschrijving**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Tekst           | Unieke id voor de bron over welke gegevens worden verzameld. Een vault resource-id van Recovery Services |
| OperationName                   | Tekst           | Naam van de bewerking, bijvoorbeeld Beleid of Beleidsassociatie |
| Categorie                        | Tekst           | Categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht - AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Tekst           | Dagen van de week waarop back-ups zijn gepland            |
| Back-upfrequentie                 | Tekst           | Frequentie waarmee back-ups worden uitgevoerd. Bijvoorbeeld, dagelijks, wekelijks |
| BackupManagementType            | Tekst           | Providertype voor server die back-upwerk doet. For example, IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Tekst           | Veld om de back-upbeheerserver op unieke wijze te identificeren, het back-upitem is beveiligd via, indien van toepassing |
| Back-uptijden                     | Tekst           | Datum en tijd waarop back-ups zijn gepland                     |
| DagelijkseRetentieDuur          | Geheel getal   | Totale bewaarduur in dagen voor geconfigureerde back-ups      |
| Dagelijkseretentietijden             | Tekst           | Datum en tijd waarop de dagelijkse retentie is geconfigureerd            |
| DiffbackupdaysofTheWeek         | Tekst           | Dagen van de week voor differentiële back-ups voor SQL in Azure VM Backup |
| DiffBackupFormat (DiffBackupFormat)                | Tekst           | Indeling voor differentiële back-ups voor SQL in Azure VM-back-up   |
| Diffbackupretentionduration     | Decimaal getal | Bewaarduur voor differentiële back-ups voor SQL in Azure VM Backup |
| DiffBackupTime (DiffBackupTime)                  | Time           | Tijd voor differentiële back-ups voor SQL in Azure VM Backup     |
| LogBackupFrequentie              | Decimaal getal | Frequentie voor Logboekback-ups voor SQL                            |
| LogBackupRetentionDuration LogBackupRetentionDuration LogBackupRetentionDuration LogBackup      | Decimaal getal | Bewaarduur voor Logboekbackups voor SQL in Azure VM Backup |
| MaandelijkseRetentieDagenVanTheMonth  | Tekst           | Weken van de maand waarin de maandelijkse retentie is geconfigureerd.  Bijvoorbeeld, First, Last, enz. |
| Maandelijkse RetentieDagenVanTheWeek   | Tekst           | Dagen van de week geselecteerd voor maandelijkse retentie              |
| MaandelijkseRetentieDuur        | Tekst           | Totale bewaarduur in maanden voor geconfigureerde back-ups    |
| MaandelijkseRetentieFormat          | Tekst           | Type configuratie voor maandelijkse retentie. Bijvoorbeeld, dagelijks voor dag gebaseerd, wekelijks voor week gebaseerd |
| Maandelijkse RetentieTijden           | Tekst           | Datum en tijd waarop de maandelijkse retentie is geconfigureerd           |
| MaandelijkseRetentieWekenOfTheMonth | Tekst           | Weken van de maand waarin de maandelijkse retentie is geconfigureerd.   Bijvoorbeeld, First, Last, enz. |
| PolicyName                      | Tekst           | Naam van het gedefinieerde beleid                                   |
| PolicyUniqueId                  | Tekst           | Unieke id om het beleid te identificeren                             |
| PolicyTimeZone                  | Tekst           | Tijdzone waarin de beleidstijdvelden zijn opgegeven in de logboeken |
| RetentieDuur               | Tekst           | Bewaarduur voor geconfigureerde back-ups                    |
| RetentionType                   | Tekst           | Type retentie                                            |
| Schemaversie                   | Tekst           | Dit veld geeft de huidige versie van het schema aan, het is **V2** |
| Status                           | Tekst           | Huidige status van het beleidsobject. Actief, verwijderd |
| SynchronisatieFrequencyPerDay  | Geheel getal   | Aantal keren op een dag dat een bestandsback-up wordt gesynchroniseerd voor SC DPM en MABS |
| VaultUniqueId VaultUniqueId                   | Tekst           | Unieke ID van de kluis waartoe dit beleid behoort          |
| Wekelijkse retentiedagenvande week    | Tekst           | Dagen van de week geselecteerd voor wekelijkse retentie               |
| Wekelijkse retentieduur         | Decimaal getal | Totale wekelijkse bewaarduur in weken voor geconfigureerde back-ups |
| Wekelijkse retentietijden            | Tekst           | Datum en tijd waarop de wekelijkse retentie is geconfigureerd            |
| JaarlijkseRetentieDagenVanTheMonth   | Tekst           | Datums van de maand geselecteerd voor jaarlijkse retentie             |
| JaarlijkseRetentieDagenVanTheWeek    | Tekst           | Dagen van de week geselecteerd voor jaarlijkse retentie               |
| JaarlijkseRetentieDuur         | Decimaal getal | Totale bewaarduur in jaren voor geconfigureerde back-ups     |
| JaarlijkseRetentieFormat           | Tekst           | Type configuratie voor jaarlijkse retentie, bijvoorbeeld dagelijks voor daggebaseerd, wekelijks voor weekgebaseerd |
| JaarlijkseRetentieMaandenOfTheYear  | Tekst           | Maanden van het jaar geselecteerd voor jaarlijkse retentie             |
| JaarlijkseRetentieTimes            | Tekst           | Datum en tijd waarop de jaarlijkse retentie is geconfigureerd            |
| JaarlijkseRetentieWekenOfTheMonth  | Tekst           | Weken van de maand geselecteerd voor jaarlijkse retentie             |
| SourceSystem                    | Tekst           | Bronsysteem van de huidige gegevens - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

In deze tabel vindt u informatie over opslaggerelateerde velden.

| **Veld**                      | **Gegevenstype** | **Beschrijving**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Resource-id voor gegevens die worden verzameld. Vault Resource-id van Recovery Services |
| OperationName                  | Tekst          | Dit veld vertegenwoordigt de naam van de huidige bewerking - Opslag- of opslagassociatie |
| Categorie                       | Tekst          | Dit veld vertegenwoordigt categorie diagnostische gegevens die naar Azure Monitor-logboeken worden gepusht - AddonAzureBackupStorage |
| BackupItemUniqueId             | Tekst          | Unieke id die wordt gebruikt om het back-upitem voor VM's te identificeren dat is back-ups met Behulp van DPM, MABS |
| BackupManagementServerUniqueId | Tekst          | Veld om de back-upbeheerserver op unieke wijze te identificeren, het back-upitem is beveiligd via, indien van toepassing |
| BackupManagementType           | Tekst          | Providertype voor server die back-upwerk doet. For example, IaaSVM, FileFolder |
| PreferredworkloadonVolume      | Tekst          | Werkbelasting waarvoor dit volume de voorkeur heeft voor opslag      |
| ProtectedContainerUniqueId     | Tekst          | Unieke id van de beveiligde server die aan de waarschuwing is gekoppeld |
| Schemaversie                  | Tekst          | Versie van het schema. **V2**                   |
| Status                          | Tekst          | Status van het object back-upitem. Actief, verwijderd |
| StorageAllocatedInMBs          | Aantal        | Grootte van de opslag toegewezen door het overeenkomstige back-upitem in de overeenkomstige opslag van het type Disk |
| OpslagVerbruikerInMBs           | Aantal        | Omvang van de opslag die wordt verbruikt door het bijbehorende back-upitem in de desbetreffende opslag |
| StorageName                    | Tekst          | Naam van de opslagentiteit. Bijvoorbeeld E:\                      |
| StorageTotalSizeInGBs          | Tekst          | Totale omvang van de opslag, in GB, verbruikt door opslagentiteit     |
| StorageType                    | Tekst          | Type opslag, bijvoorbeeld Cloud, Volume, Schijf             |
| StorageUniqueId                | Tekst          | Unieke id die wordt gebruikt om de opslagentiteit te identificeren                |
| VaultUniqueId VaultUniqueId                  | Tekst          | Unieke id die wordt gebruikt om de kluis met betrekking tot de opslagentiteit te identificeren |
| VolumeFriendlyName             | Tekst          | Vriendelijke naam van het opslagvolume                          |
| SourceSystem                   | Tekst          | Bronsysteem van de huidige gegevens - Azure                    |

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het verzenden van diagnostische gegevens naar Log Analytics](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [Meer informatie over het schrijven van query's in resourcespecifieke tabellen](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)