---
title: Gegevens model voor diagnostische gebeurtenissen van Azure Backup
description: Dit gegevens model is een verwijzing naar de resource-specifieke modus voor het verzenden van diagnostische gebeurtenissen naar Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 86416d0d74296069878aa7f33b549102a52ea488
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281075"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Gegevens model voor diagnostische gebeurtenissen van Azure Backup

## <a name="coreazurebackup"></a>CoreAzureBackup

Deze tabel bevat informatie over de belangrijkste back-upentiteiten, zoals kluizen en back-upitems.

| **Veld**                         | **Gegevens type** | **Beschrijving**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Tekst          | De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID. |
| OperationName                     | Tekst          | Dit veld vertegenwoordigt de naam van de huidige bewerking BackupItem, BackupItemAssociation of ProtectedContainer. |
| Categorie                          | Tekst          | Dit veld vertegenwoordigt de categorie diagnostische gegevens die naar Azure Monitor logboeken worden gepusht. Bijvoorbeeld CoreAzureBackup. |
| AgentVersion                      | Tekst          | Het versie nummer van de back-up van de agent of de beveiligings agent (in het geval van SC DPM en MABS) |
| AzureBackupAgentVersion           | Tekst          | Versie van de Azure Backup-Agent op de server voor back-upbeheer |
| AzureDataCenter                   | Tekst          | Het Data Center waar de kluis zich bevindt                       |
| BackupItemAppVersion              | Tekst          | Toepassings versie van het back-upitem                       |
| BackupItemFriendlyName            | Tekst          | Beschrijvende naam van het back-upitem                             |
| BackupItemName                    | Tekst          | De naam van het back-upitem                                      |
| BackupItemProtectionState         | Tekst          | De beveiligings status van het back-upitem                          |
| BackupItemFrontEndSize            | Tekst          | Front-end grootte van het back-upitem                            |
| BackupItemType                    | Tekst          | Type back-upitem. Bijvoorbeeld: VM, FileFolder             |
| BackupItemUniqueId                | Tekst          | De unieke id van het back-upitem                         |
| BackupManagementServerType        | Tekst          | Type van de server voor back-upbeheer, zoals in MABS, SC DPM     |
| BackupManagementServerUniqueId    | Tekst          | Veld om de back-upbeheerserver op unieke wijze te identificeren      |
| BackupManagementType              | Tekst          | Provider type voor het uitvoeren van de back-uptaak van de server. Bijvoorbeeld, IaaSVM, FileFolder |
| BackupManagementServerName        | Tekst          | Naam van de server voor back-upbeheer                         |
| BackupManagementServerOSVersion   | Tekst          | Versie van het besturings systeem van de server voor back-upbeheer                   |
| BackupManagementServerVersion     | Tekst          | Versie van de server voor back-upbeheer                      |
| LatestRecoveryPointLocation       | Tekst          | Locatie van het meest recente herstel punt voor het back-upitem    |
| LatestRecoveryPointTime           | DateTime      | Datum en tijd van het laatste herstel punt voor het back-upitem   |
| OldestRecoveryPointLocation       | Tekst          | Locatie van het oudste herstel punt voor het back-upitem    |
| OldestRecoveryPointTime           | DateTime      | Datum en tijd van het laatste herstel punt voor het back-upitem   |
| PolicyUniqueId                    | Tekst          | Unieke ID voor het identificeren van het beleid                             |
| ProtectedContainerFriendlyName    | Tekst          | Beschrijvende naam van de beveiligde server                        |
| ProtectedContainerLocation        | Tekst          | Of de beveiligde container zich lokaal of in azure bevindt |
| ProtectedContainerName            | Tekst          | De naam van de beveiligde container                            |
| ProtectedContainerOSType          | Tekst          | Type besturings systeem van de beveiligde container                          |
| ProtectedContainerOSVersion       | Tekst          | Versie van het besturings systeem van de beveiligde container                        |
| ProtectedContainerProtectionState | Tekst          | Beveiligings status van de beveiligde container                  |
| ProtectedContainerType            | Tekst          | Of de beveiligde container een server of een container is  |
| ProtectedContainerUniqueId        | Tekst          | Unieke ID die wordt gebruikt om de beveiligde container te identificeren voor alles behalve Vm's waarvoor een back-up is gemaakt met behulp van DPM, MABS |
| ProtectedContainerWorkloadType    | Tekst          | Type van de beveiligde container waarvan een back-up is gemaakt. Bijvoorbeeld IaaSVMContainer |
| ProtectionGroupName               | Tekst          | Naam van de beveiligings groep waarop het back-upitem is beveiligd, voor SC DPM en MABS, indien van toepassing |
| ResourceGroupName                 | Tekst          | De resource groep van de resource (bijvoorbeeld Recovery Services kluis) voor de gegevens die worden verzameld |
| SchemaVersion                     | Tekst          | In dit veld wordt de huidige versie van het schema aangegeven, het is **v2** |
| SecondaryBackupProtectionState    | Tekst          | Of secundaire beveiliging is ingeschakeld voor het back-upitem  |
| Status                             | Tekst          | Status van het object voor het back-upitem. Bijvoorbeeld actief, verwijderd |
| StorageReplicationType            | Tekst          | Type opslag replicatie voor de kluis. Bijvoorbeeld georedundant |
| SubscriptionId                    | Tekst          | De abonnements-id van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| vaultName                         | Tekst          | Naam van de kluis                                            |
| VaultTags                         | Tekst          | Labels die zijn gekoppeld aan de kluis resource                    |
| VaultUniqueId                     | Tekst          | De unieke id van de kluis                             |
| SourceSystem                      | Tekst          | Bron systeem van de huidige gegevens-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Deze tabel bevat details over velden die betrekking hebben op waarschuwingen.

| **Veld**                      | **Gegevens type** | **Beschrijving**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | De unieke id voor de resource waarover gegevens worden verzameld. Bijvoorbeeld een Recovery Services kluis Resource-ID |
| OperationName                  | Tekst          | De naam van de huidige bewerking. Bijvoorbeeld waarschuwing            |
| Categorie                       | Tekst          | Categorie diagnostische gegevens die naar Azure Monitor-logboeken zijn gepusht-AddonAzureBackupAlerts |
| AlertCode                      | Tekst          | Code voor een unieke identificatie van een waarschuwings type                     |
| AlertConsolidationStatus       | Tekst          | Vaststellen of de waarschuwing een geconsolideerde waarschuwing is         |
| AlertOccurrenceDateTime        | DateTime      | De datum en het tijdstip waarop de waarschuwing is gemaakt                     |
| AlertRaisedOn                  | Tekst          | Het type entiteit waarop de waarschuwing is opgetreden                        |
| AlertSeverity                  | Tekst          | Ernst van de waarschuwing. Bijvoorbeeld kritiek                 |
| AlertStatus                    | Tekst          | De status van de waarschuwing. Bijvoorbeeld: Active                     |
| AlertTimeToResolveInMinutes    | Aantal        | De tijd die nodig is om een waarschuwing op te lossen. Leeg voor actieve waarschuwingen.     |
| AlertType                      | Tekst          | Type waarschuwing. Bijvoorbeeld back-up                           |
| AlertUniqueId                  | Tekst          | De unieke id van de gegenereerde waarschuwing                    |
| BackupItemUniqueId             | Tekst          | De unieke id van het back-upitem dat aan de waarschuwing is gekoppeld |
| BackupManagementServerUniqueId | Tekst          | Veld voor het identificeren van de back-upbeheerserver waarop het back-upitem is beveiligd, indien van toepassing |
| BackupManagementType           | Tekst          | Provider type voor het uitvoeren van een back-uptaak van de server, bijvoorbeeld IaaSVM, FileFolder |
| CountOfAlertsConsolidated      | Aantal        | Aantal geconsolidatiete waarschuwingen als het een geconsolideerde waarschuwing is  |
| ProtectedContainerUniqueId     | Tekst          | De unieke id van de beveiligde server die is gekoppeld aan de waarschuwing |
| RecommendedAction              | Tekst          | Aanbevolen actie om de waarschuwing op te lossen                      |
| SchemaVersion                  | Tekst          | Huidige versie van het schema, bijvoorbeeld **v2**            |
| Status                          | Tekst          | Huidige status van het waarschuwings object, bijvoorbeeld actief, verwijderd |
| StorageUniqueId                | Tekst          | Unieke ID die wordt gebruikt voor het identificeren van de opslag entiteit                |
| VaultUniqueId                  | Tekst          | Unieke ID die wordt gebruikt om de kluis te identificeren die is gerelateerd aan de waarschuwing    |
| SourceSystem                   | Tekst          | Bron systeem van de huidige gegevens-Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Deze tabel bevat basis velden die betrekking hebben op een beveiligd exemplaar.

| **Veld**                      | **Gegevens type** | **Beschrijving**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | De unieke id voor de resource waarover gegevens worden verzameld. Bijvoorbeeld een Recovery Services kluis Resource-ID |
| OperationName                  | Tekst          | De naam van de bewerking, bijvoorbeeld ProtectedInstance         |
| Categorie                       | Tekst          | Categorie diagnostische gegevens die naar Azure Monitor-logboeken zijn gepusht-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Tekst          | De unieke ID van het back-upitem                                 |
| BackupManagementServerUniqueId | Tekst          | Veld voor het identificeren van de back-upbeheerserver waarop het back-upitem is beveiligd, indien van toepassing |
| BackupManagementType           | Tekst          | Provider type voor het uitvoeren van een back-uptaak van de server, bijvoorbeeld IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Tekst          | Unieke ID voor het identificeren van de beveiligde container waarop de taak wordt uitgevoerd |
| ProtectedInstanceCount         | Tekst          | Aantal beveiligde instanties voor het bijbehorende back-upitem of de beveiligde container op die datum-tijd |
| SchemaVersion                  | Tekst          | Huidige versie van het schema, bijvoorbeeld **v2**            |
| Status                          | Tekst          | Status van het object voor het back-upitem, bijvoorbeeld actief, verwijderd |
| VaultUniqueId                  | Tekst          | De unieke id van de beveiligde kluis die is gekoppeld aan het beveiligde exemplaar |
| SourceSystem                   | Tekst          | Bron systeem van de huidige gegevens-Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Deze tabel bevat details over projectgerelateerde velden.

| **Veld**                      | **Gegevens type** | **Beschrijving**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID |
| OperationName                  | Tekst          | Dit veld bevat de naam van de huidige bewerking-taak    |
| Categorie                       | Tekst          | Dit veld vertegenwoordigt een categorie met diagnostische gegevens die naar Azure Monitor logboeken zijn gepusht-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Tekst          | Veld om op te geven of de taak ad-hoc of gepland is           |
| BackupItemUniqueId             | Tekst          | Unieke ID die wordt gebruikt om het back-upitem te identificeren dat is gerelateerd aan de opslag entiteit |
| BackupManagementServerUniqueId | Tekst          | Unieke ID die wordt gebruikt om de back-upbeheerserver te identificeren die betrekking heeft op de opslag entiteit |
| BackupManagementType           | Tekst          | Provider type voor het uitvoeren van back-ups, bijvoorbeeld IaaSVM, FileFolder waartoe deze waarschuwing behoort |
| DataTransferredInMB            | Aantal        | Gegevens die worden overgebracht in MB voor deze taak                          |
| JobDurationInSecs              | Aantal        | Totale taak duur in seconden                                |
| JobFailureCode                 | Tekst          | De teken reeks voor de fout code omdat de taak fout is opgetreden    |
| JobOperation                   | Tekst          | Bewerking waarvoor de taak wordt uitgevoerd bijvoorbeeld back-ups maken, herstellen, back-up configureren |
| JobOperationSubType            | Tekst          | Subtype van de taak bewerking. Bijvoorbeeld ' Log ', in het geval van een logboek back-uptaak |
| JobStartDateTime               | DateTime      | Datum en tijd waarop de taak is gestart                       |
| JobStatus                      | Tekst          | De status van de voltooide taak, bijvoorbeeld voltooid, is mislukt   |
| JobUniqueId                    | Tekst          | Unieke ID voor het identificeren van de taak                                |
| ProtectedContainerUniqueId     | Tekst          | De unieke id van de beveiligde server die is gekoppeld aan de waarschuwing |
| RecoveryJobDestination         | Tekst          | Doel van een herstel taak, waarbij de gegevens worden hersteld   |
| RecoveryJobRPDateTime          | DateTime      | De datum, het tijdstip waarop het herstel punt dat wordt hersteld, is gemaakt |
| RecoveryJobLocation            | Tekst          | De locatie waar het herstel punt dat wordt hersteld is opgeslagen |
| RecoveryLocationType           | Tekst          | Type van de herstel locatie                                |
| SchemaVersion                  | Tekst          | Huidige versie van het schema, bijvoorbeeld **v2**            |
| Status                          | Tekst          | Huidige status van het waarschuwings object, bijvoorbeeld actief, verwijderd |
| VaultUniqueId                  | Tekst          | De unieke id van de beveiligde kluis die is gekoppeld aan de waarschuwing |
| SourceSystem                   | Tekst          | Bron systeem van de huidige gegevens-Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Deze tabel bevat details over velden die betrekking hebben op het beleid.

| **Veld**                       | **Gegevens type**  | **Beschrijving**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Tekst           | De unieke id voor de resource waarover gegevens worden verzameld. Bijvoorbeeld een Recovery Services kluis Resource-ID |
| OperationName                   | Tekst           | De naam van de bewerking, bijvoorbeeld beleid of PolicyAssociation |
| Categorie                        | Tekst           | Categorie diagnostische gegevens die naar Azure Monitor-logboeken zijn gepusht-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Tekst           | Dagen van de week waarop back-ups zijn gepland            |
| BackupFrequency                 | Tekst           | De frequentie waarmee back-ups worden uitgevoerd. Bijvoorbeeld dagelijks, wekelijks |
| BackupManagementType            | Tekst           | Provider type voor het uitvoeren van de back-uptaak van de server. Bijvoorbeeld, IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Tekst           | Veld voor het identificeren van de back-upbeheerserver waarop het back-upitem is beveiligd, indien van toepassing |
| BackupTimes                     | Tekst           | De datum en tijd waarop back-ups zijn gepland                     |
| DailyRetentionDuration          | Geheel getal   | Totale retentie duur in dagen voor geconfigureerde back-ups      |
| DailyRetentionTimes             | Tekst           | De datum en tijd waarop een dagelijkse Bewaar periode is geconfigureerd            |
| DiffBackupDaysOfTheWeek         | Tekst           | Dagen van de week voor differentiële back-ups voor SQL in azure VM-back-up |
| DiffBackupFormat                | Tekst           | Indeling voor differentiële back-ups voor SQL in azure VM-back-up   |
| DiffBackupRetentionDuration     | Decimaal getal | Bewaar periode voor differentiële back-ups voor SQL in azure VM-back-up |
| DiffBackupTime                  | Time           | Tijd voor differentiële back-ups voor SQL in azure VM-back-up     |
| LogBackupFrequency              | Decimaal getal | Frequentie voor logboek back-ups voor SQL                            |
| LogBackupRetentionDuration      | Decimaal getal | Bewaar periode voor back-ups van Logboeken voor SQL in azure VM-back-up |
| MonthlyRetentionDaysOfTheMonth  | Tekst           | Weken van de maand waarin de maandelijkse retentie is geconfigureerd.  Bijvoorbeeld eerste, laatste, etc. |
| MonthlyRetentionDaysOfTheWeek   | Tekst           | Dagen van de week geselecteerd voor een maandelijkse Bewaar periode              |
| MonthlyRetentionDuration        | Tekst           | Totale Bewaar periode in maanden voor geconfigureerde back-ups    |
| MonthlyRetentionFormat          | Tekst           | Type configuratie voor maandelijkse retentie. Bijvoorbeeld dagelijks voor dag, wekelijks op basis van een week |
| MonthlyRetentionTimes           | Tekst           | De datum en tijd waarop de maandelijkse retentie is geconfigureerd           |
| MonthlyRetentionWeeksOfTheMonth | Tekst           | Weken van de maand waarin de maandelijkse retentie is geconfigureerd.   Bijvoorbeeld eerste, laatste, etc. |
| PolicyName                      | Tekst           | De naam van het beleid dat is gedefinieerd                                   |
| PolicyUniqueId                  | Tekst           | Unieke ID voor het identificeren van het beleid                             |
| PolicyTimeZone                  | Tekst           | De tijd zone waarin de velden van het regel beleid worden opgegeven in de logboeken |
| RetentionDuration               | Tekst           | Bewaar periode voor geconfigureerde back-ups                    |
| RetentionType                   | Tekst           | Type Bewaar periode                                            |
| SchemaVersion                   | Tekst           | Dit veld geeft de huidige versie van het schema aan. Dit is **v2** |
| Status                           | Tekst           | Huidige status van het beleids object. Bijvoorbeeld actief, verwijderd |
| SynchronisationFrequencyPerDay  | Geheel getal   | Aantal keren per dag dat een back-up van een bestand wordt gesynchroniseerd voor SC DPM en MABS |
| VaultUniqueId                   | Tekst           | De unieke ID van de kluis waartoe dit beleid behoort          |
| WeeklyRetentionDaysOfTheWeek    | Tekst           | Dagen van de week geselecteerd voor een wekelijkse Bewaar periode               |
| WeeklyRetentionDuration         | Decimaal getal | Totale wekelijkse Bewaar periode in weken voor geconfigureerde back-ups |
| WeeklyRetentionTimes            | Tekst           | De datum en tijd waarop een wekelijkse Bewaar periode is geconfigureerd            |
| YearlyRetentionDaysOfTheMonth   | Tekst           | De datums van de maand die zijn geselecteerd voor een jaarlijkse Bewaar periode             |
| YearlyRetentionDaysOfTheWeek    | Tekst           | Dagen van de week geselecteerd voor een jaarlijkse Bewaar periode               |
| YearlyRetentionDuration         | Decimaal getal | Totale Bewaar duur in jaren voor geconfigureerde back-ups     |
| YearlyRetentionFormat           | Tekst           | Type configuratie voor jaarlijks bewaren, bijvoorbeeld dagelijks voor dag, wekelijks voor op basis van een week |
| YearlyRetentionMonthsOfTheYear  | Tekst           | Maanden van het jaar dat is geselecteerd voor een jaarlijkse Bewaar periode             |
| YearlyRetentionTimes            | Tekst           | De datum en tijd waarop de jaarlijkse Bewaar periode is geconfigureerd            |
| YearlyRetentionWeeksOfTheMonth  | Tekst           | Weken van de maand geselecteerd voor een jaarlijkse Bewaar periode             |
| SourceSystem                    | Tekst           | Bron systeem van de huidige gegevens-Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Deze tabel bevat details over velden die betrekking hebben op opslag.

| **Veld**                      | **Gegevens type** | **Beschrijving**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID |
| OperationName                  | Tekst          | Dit veld bevat de naam van de huidige bewerking-opslag of StorageAssociation |
| Categorie                       | Tekst          | Dit veld vertegenwoordigt een categorie met diagnostische gegevens die naar Azure Monitor logboeken zijn gepusht-AddonAzureBackupStorage |
| BackupItemUniqueId             | Tekst          | Unieke ID die wordt gebruikt voor het identificeren van het back-upitem voor Vm's waarvan een back-up is gemaakt met DPM, MABS |
| BackupManagementServerUniqueId | Tekst          | Veld voor het identificeren van de back-upbeheerserver waarop het back-upitem is beveiligd, indien van toepassing |
| BackupManagementType           | Tekst          | Provider type voor het uitvoeren van de back-uptaak van de server. Bijvoorbeeld, IaaSVM, FileFolder |
| PreferredWorkloadOnVolume      | Tekst          | De werk belasting waarvoor dit volume de voorkeurs opslag is      |
| ProtectedContainerUniqueId     | Tekst          | De unieke id van de beveiligde server die is gekoppeld aan de waarschuwing |
| SchemaVersion                  | Tekst          | De versie van het schema. Bijvoorbeeld **v2**                   |
| Status                          | Tekst          | Status van het object voor het back-upitem. Bijvoorbeeld actief, verwijderd |
| StorageAllocatedInMBs          | Aantal        | Grootte van de opslag die wordt toegewezen door het bijbehorende back-upitem in de bijbehorende opslag van het type schijf |
| StorageConsumedInMBs           | Aantal        | Grootte van de opslag die wordt gebruikt door het bijbehorende back-upitem in de bijbehorende opslag |
| StorageName                    | Tekst          | De naam van de opslag entiteit. Bijvoorbeeld E:\                      |
| StorageTotalSizeInGBs          | Tekst          | Totale grootte van de opslag, in GB, die wordt verbruikt door de opslag entiteit     |
| StorageType                    | Tekst          | Type opslag, bijvoorbeeld Cloud, volume, schijf             |
| StorageUniqueId                | Tekst          | Unieke ID die wordt gebruikt voor het identificeren van de opslag entiteit                |
| VaultUniqueId                  | Tekst          | Unieke ID die wordt gebruikt om de kluis te identificeren die is gerelateerd aan de opslag entiteit |
| VolumeFriendlyName             | Tekst          | Beschrijvende naam van het opslag volume                          |
| SourceSystem                   | Tekst          | Bron systeem van de huidige gegevens-Azure                    |

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het verzenden van diagnostische gegevens naar Log Analytics](https://aka.ms/AA6il6r)