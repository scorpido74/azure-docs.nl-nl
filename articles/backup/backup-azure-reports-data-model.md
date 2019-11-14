---
title: Gegevens model voor Azure Backup
description: In dit artikel vindt u informatie over Power BI gegevens model voor Azure Backup-rapporten.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: dacurwin
ms.openlocfilehash: f3db0e6a78eada52f2b5936f5194138d97ebcca6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074896"
---
# <a name="data-model-for-azure-backup-reports"></a>Gegevensmodel voor Azure Backup-rapporten

In dit artikel wordt het Power BI gegevens model beschreven dat wordt gebruikt voor het maken van Azure Backup-rapporten. Met dit gegevens model kunt u bestaande rapporten filteren op basis van relevante velden en belang rijker rapporten maken met behulp van tabellen en velden in het model.

## <a name="creating-new-reports-in-power-bi"></a>Nieuwe rapporten maken in Power BI

Power BI biedt aanpassings functies die u kunt gebruiken om [rapporten te maken met behulp van het gegevens model](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Azure Backup gegevens model gebruiken

U kunt de volgende velden gebruiken als onderdeel van het gegevens model voor het maken van rapporten en het aanpassen van bestaande rapporten.

### <a name="alert"></a>Waarschuwing

Deze tabel bevat basis velden en aggregaties over diverse velden met waarschuwingen.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Geheel getal |Aantal waarschuwingen dat is gemaakt in de geselecteerde tijds periode |
| %ActiveAlertsCreatedInPeriod |Percentage |Percentage actieve waarschuwingen in de geselecteerde tijds periode |
| %CriticalAlertsCreatedInPeriod |Percentage |Percentage kritieke waarschuwingen in de geselecteerde tijds periode |
| AlertOccurrenceDate |Date |Datum waarop de waarschuwing is gemaakt |
| AlertSeverity |Tekst |Ernst van de waarschuwing. Bijvoorbeeld kritiek |
| AlertStatus |Tekst |De status van de waarschuwing. Bijvoorbeeld: Active |
| AlertType |Tekst |Het type van de gegenereerde waarschuwing. Bijvoorbeeld back-up |
| AlertUniqueId |Tekst |Unieke ID van de gegenereerde waarschuwing |
| AsOnDateTime |Datum/tijd |Laatste vernieuwings tijd voor de geselecteerde rij |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Decimaal getal |Gemiddelde tijd (in minuten) voor het oplossen van de waarschuwing voor de geselecteerde tijds periode |
| EntityState |Tekst |Huidige status van het waarschuwings object. Bijvoorbeeld actief, verwijderd |

### <a name="backup-item"></a>Back-upitem

Deze tabel bevat basis velden en aggregaties over diverse velden met betrekking tot back-upitems.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| #BackupItems |Geheel getal |Aantal back-upitems |
| #UnprotectedBackupItems |Geheel getal |Aantal back-upitems gestopt voor beveiliging of geconfigureerd voor back-ups, maar back-ups zijn niet gestart|
| AsOnDateTime |Datum/tijd |Laatste vernieuwings tijd voor de geselecteerde rij |
| BackupItemFriendlyName |Tekst |Beschrijvende naam van het back-upitem |
| BackupItemId |Tekst |ID van back-upitem |
| BackupItemName |Tekst |Naam van back-upitem |
| BackupItemType |Tekst |Type back-upitem. Bijvoorbeeld: VM, FileFolder |
| EntityState |Tekst |Huidige status van het object met het back-upitem. Bijvoorbeeld actief, verwijderd |
| LastBackupDateTime |Datum/tijd |Tijdstip van laatste back-up voor geselecteerd back-upitem |
| LastBackupState |Tekst |Status van de laatste back-up voor het geselecteerde back-upitem. Bijvoorbeeld geslaagd, mislukt |
| LastSuccessfulBackupDateTime |Datum/tijd |Tijdstip van de laatste geslaagde back-up voor het geselecteerde back-upitem |
| ProtectionState |Tekst |Huidige beveiligings status van het back-upitem. Bijvoorbeeld: beveiligd, ProtectionStopped |

### <a name="calendar"></a>Agenda

Deze tabel bevat details over kalender velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| Date |Date |Geselecteerde datum voor het filteren van gegevens |
| DateKey |Tekst |Unieke sleutel voor elk datum item |
| DayDiff |Decimaal getal |Verschil in dag voor het filteren van gegevens. 0 duidt bijvoorbeeld op de gegevens van de huidige dag,-1 geeft de gegevens van de vorige dag aan, 0 en-1 geven gegevens op voor de huidige en vorige dag  |
| Maand |Tekst |Maand van het jaar dat is geselecteerd voor het filteren van gegevens, de maand begint op de eerste dag en eindigt op 31 dagen |
| MonthDate | Date |De datum in de maand waarin de maand eindigt, geselecteerd voor het filteren van gegevens |
| MonthDiff |Decimaal getal |Het verschil in de maand voor het filteren van gegevens. Bijvoorbeeld 0 geeft de gegevens van de huidige maand aan,-1 geeft de gegevens van de vorige maand aan, 0 en-1 geven gegevens aan voor de huidige en de vorige maand |
| Wekelijks |Tekst |De week die is geselecteerd voor het filteren van gegevens, begint op zondag en eindigt op zaterdag |
| WeekDate |Date |De datum in de week waarop de week eindigt, geselecteerd voor het filteren van gegevens |
| WeekDiff |Decimaal getal |Verschil in de week voor het filteren van gegevens. Bijvoorbeeld: 0 geeft de gegevens van de huidige week aan,-1 geeft de gegevens van de vorige week aan, 0 en-1 geven gegevens voor de huidige en vorige week aan |
| Year |Tekst |Kalender jaar geselecteerd voor het filteren van gegevens |
| YearDate |Date |De datum in het jaar waarin het jaar eindigt, geselecteerd voor het filteren van gegevens |

### <a name="job"></a>Taak

Deze tabel bevat basis velden en aggregaties ten opzichte van verschillende projectgerelateerde velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| #JobsCreatedInPeriod |Geheel getal |Aantal taken dat in de geselecteerde tijds periode is gemaakt |
| %FailuresForJobsCreatedInPeriod |Percentage |Percentage algemene taak fouten in de geselecteerde tijds periode |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Decimaal getal |80th percentiel waarde van gegevens die zijn overgebracht in MB voor **back-** uptaken die zijn gemaakt in de geselecteerde tijds periode |
| AsOnDateTime |Datum/tijd |Laatste vernieuwings tijd voor de geselecteerde rij |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Decimaal getal |Gemiddelde tijd in minuten voor **voltooide back-** uptaken die zijn gemaakt in de geselecteerde tijds periode |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Decimaal getal |Gemiddelde tijd in minuten voor **voltooide herstel** taken die zijn gemaakt in de geselecteerde tijds periode |
| BackupStorageDestination |Tekst |Doel van back-upopslag. Bijvoorbeeld Cloud, schijf  |
| EntityState |Tekst |Huidige status van het taak object. Bijvoorbeeld actief, verwijderd |
| JobFailureCode |Tekst |De teken reeks voor de fout code omdat de taak fout is opgetreden |
| JobOperation |Tekst |Bewerking waarvoor de taak wordt uitgevoerd. Bijvoorbeeld back-ups maken, herstellen, back-up configureren |
| JobStartDate |Date |Datum waarop de taak is gestart |
| JobStartTime |Time |Tijdstip waarop de taak is gestart |
| JobStatus |Tekst |De status van de voltooide taak. Bijvoorbeeld voltooid, mislukt |
| JobUniqueId |Tekst |Unieke ID voor het identificeren van de taak |

### <a name="policy"></a>Beleid

Deze tabel bevat basis velden en aggregaties over diverse aan het beleid gerelateerde velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| #Policies |Geheel getal |Het aantal back-upbeleiden dat in het systeem bestaat |
| #PoliciesInUse |Geheel getal |Het aantal beleids regels dat momenteel wordt gebruikt voor het configureren van back-ups |
| AsOnDateTime |Datum/tijd |Laatste vernieuwings tijd voor de geselecteerde rij |
| BackupDaysOfTheWeek |Tekst |Dagen van de week waarop back-ups zijn gepland |
| BackupFrequency |Tekst |De frequentie waarmee back-ups worden uitgevoerd. Bijvoorbeeld dagelijks, wekelijks |
| BackupTimes |Tekst |De datum en tijd waarop back-ups zijn gepland |
| DailyRetentionDuration |Geheel getal |Totale retentie duur in dagen voor geconfigureerde back-ups |
| DailyRetentionTimes |Tekst |De datum en tijd waarop een dagelijkse Bewaar periode is geconfigureerd |
| EntityState |Tekst |Huidige status van het beleids object. Bijvoorbeeld actief, verwijderd |
| MonthlyRetentionDaysOfTheMonth |Tekst |De datums van de maand die u hebt geselecteerd voor een maandelijkse retentie |
| MonthlyRetentionDaysOfTheWeek |Tekst |Dagen van de week geselecteerd voor een maandelijkse Bewaar periode |
| MonthlyRetentionDuration |Decimaal getal |Totale Bewaar periode in maanden voor geconfigureerde back-ups |
| MonthlyRetentionFormat |Tekst |Type configuratie voor maandelijkse retentie. Bijvoorbeeld dagelijks voor dag, wekelijks op basis van een week |
| MonthlyRetentionTimes |Tekst |De datum en tijd waarop de maandelijkse retentie is geconfigureerd |
| MonthlyRetentionWeeksOfTheMonth |Tekst |Weken van de maand waarin de maandelijkse retentie is geconfigureerd. Bijvoorbeeld eerste, laatste enz. |
| policyName |Tekst |De naam van het beleid dat is gedefinieerd |
| PolicyUniqueId |Tekst |Unieke ID voor het identificeren van het beleid |
| RetentionType |Tekst |Type Bewaar beleid. Bijvoorbeeld dagelijks, wekelijks, maandelijks, jaarlijks |
| WeeklyRetentionDaysOfTheWeek |Tekst |Dagen van de week geselecteerd voor een wekelijkse Bewaar periode |
| WeeklyRetentionDuration |Decimaal getal |Totale wekelijkse Bewaar periode in weken voor geconfigureerde back-ups |
| WeeklyRetentionTimes |Tekst |De datum en tijd waarop een wekelijkse Bewaar periode is geconfigureerd |
| YearlyRetentionDaysOfTheMonth |Tekst |De datums van de maand die zijn geselecteerd voor een jaarlijkse Bewaar periode |
| YearlyRetentionDaysOfTheWeek |Tekst |Dagen van de week geselecteerd voor een jaarlijkse Bewaar periode |
| YearlyRetentionDuration |Decimaal getal |Totale Bewaar duur in jaren voor geconfigureerde back-ups |
| YearlyRetentionFormat |Tekst |Type configuratie voor jaarlijkse retentie. Bijvoorbeeld dagelijks voor dag, wekelijks op basis van een week |
| YearlyRetentionMonthsOfTheYear |Tekst |Maanden van het jaar dat is geselecteerd voor een jaarlijkse Bewaar periode |
| YearlyRetentionTimes |Tekst |De datum en tijd waarop de jaarlijkse Bewaar periode is geconfigureerd |
| YearlyRetentionWeeksOfTheMonth |Tekst |Weken van de maand waarin jaarlijks retentie is geconfigureerd. Bijvoorbeeld eerste, laatste enz. |

### <a name="protected-server"></a>Beveiligde server

Deze tabel bevat basis velden en aggregaties over diverse beveiligde server velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| #ProtectedServers |Geheel getal |Aantal beveiligde servers |
| AsOnDateTime |Datum/tijd |Laatste vernieuwings tijd voor de geselecteerde rij |
| AzureBackupAgentOSType |Tekst |Type besturings systeem van Azure Backup Agent |
| AzureBackupAgentOSVersion |Tekst |Versie van het besturings systeem van Azure Backup Agent |
| AzureBackupAgentUpdateDate |Tekst |Datum waarop de agent back-upagent is bijgewerkt |
| AzureBackupAgentVersion |Tekst |Versie nummer van back-upversie van agent |
| BackupManagementType |Tekst |Provider type voor het uitvoeren van back-ups. Bijvoorbeeld, IaaSVM, FileFolder |
| EntityState |Tekst |Huidige status van het beveiligde server object. Bijvoorbeeld actief, verwijderd |
| ProtectedServerFriendlyName |Tekst |Beschrijvende naam van beveiligde server |
| ProtectedServerName |Tekst |Naam van beveiligde server |
| ProtectedServerType |Tekst |Type beveiligde server waarvan een back-up is gemaakt. Bijvoorbeeld IaaSVMContainer |
| ProtectedServerName |Tekst |De naam van de beveiligde server waartoe het back-upitem behoort |
| RegisteredContainerId |Tekst |ID van de container die is geregistreerd voor back-up |

### <a name="storage"></a>Storage

Deze tabel bevat basis velden en aggregaties voor verschillende velden die betrekking hebben op opslag.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| #ProtectedInstances |Decimaal getal |Aantal beveiligde instanties dat wordt gebruikt voor het berekenen van de front-end opslag in de facturering, berekend op basis van de laatste waarde in de geselecteerde tijd |
| AsOnDateTime |Datum/tijd |Laatste vernieuwings tijd voor de geselecteerde rij |
| CloudStorageInMB |Decimaal getal |Cloud back-upopslag die wordt gebruikt door back-ups, berekend op basis van de nieuwste waarde in de geselecteerde tijd |
| EntityState |Tekst |Huidige status van het object. Bijvoorbeeld actief, verwijderd |
| LastUpdatedDate |Date |De datum waarop de geselecteerde rij voor het laatst is bijgewerkt |

### <a name="time"></a>Time

Deze tabel bevat informatie over tijdgebonden velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| Uur |Time |Uur van de dag. Bijvoorbeeld 1:00:00 PM |
| HourNumber |Decimaal getal |Het uur nummer voor de dag. Bijvoorbeeld 13,00 |
| Minuut |Decimaal getal |Minuut van het uur |
| PeriodOfTheDay |Tekst |De tijd periode van de dag. Bijvoorbeeld 12-3 AM |
| Time |Time |Tijdstip van de dag. Bijvoorbeeld 12:00:01 AM |
| TimeKey |Tekst |Sleutel waarde om tijd weer te geven |

### <a name="vault"></a>Kluis

Deze tabel bevat basis velden en aggregaties voor verschillende velden die betrekking hebben op de kluis.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| #Vaults |Geheel getal |Aantal kluizen |
| AsOnDateTime |Datum/tijd |Laatste vernieuwings tijd voor de geselecteerde rij |
| AzureDataCenter |Tekst |Data Center waar de kluis zich bevindt |
| EntityState |Tekst |Huidige status van het kluis object. Bijvoorbeeld actief, verwijderd |
| StorageReplicationType |Tekst |Type opslag replicatie voor de kluis. Bijvoorbeeld georedundant |
| SubscriptionId |Tekst |Abonnements-ID van de klant die is geselecteerd voor het genereren van rapporten |
| vaultName |Tekst |Naam van de kluis |
| VaultTags |Tekst |Labels die zijn gekoppeld aan de kluis |

## <a name="next-steps"></a>Volgende stappen

Als u het gegevens model voor het maken van Azure Backup rapporten hebt bekeken, raadpleegt u de volgende artikelen voor meer informatie over het maken en weer geven van rapporten in Power BI.

* [Rapporten maken in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Rapporten filteren in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
