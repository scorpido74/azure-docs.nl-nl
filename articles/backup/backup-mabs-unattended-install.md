---
title: Stille installatie van Azure Backup Server V2
description: Gebruik een PowerShell-script om Azure Backup Server V2 in stilte te installeren. Dit soort installatie wordt ook wel een onbewaakte installatie genoemd.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172245"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Een onbeheerde installatie van Azure Backup Server uitvoeren

Meer informatie over het uitvoeren van een onbeheerde installatie van Azure Backup Server.

Deze stappen zijn niet van toepassing als u Azure Backup Server V1 installeert.

## <a name="install-backup-server"></a>Back-upserver installeren

1. Maak een tekstbestand op de server die Azure Backup Server V2 of hoger host. (U het bestand maken in Kladblok of in een andere teksteditor.) Sla het bestand op als MABSSetup.ini.

2. Plak de volgende code in het mABSSetup.ini-bestand. Vervang de tekst in\< \>de haakjes ( ) door waarden uit uw omgeving. De volgende tekst is een voorbeeld:

   ```text
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Sla het bestand op. Voer vervolgens bij een opdrachtprompt met verhoogde bevoegdheid op de installatieserver de opdracht in:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

U deze vlaggen gebruiken voor de installatie:</br>
**/f**: .ini-bestandspad</br>
**/l**: Logboekpad</br>
**/i**: Installatiepad</br>
**/x**: Pad verwijderen</br>

## <a name="next-steps"></a>Volgende stappen

Nadat u back-upserver hebt geïnstalleerd, leest u hoe u uw server voorbereidt of begint u met het beveiligen van een werkbelasting.

- [Back-upserverworkloads voorbereiden](backup-azure-microsoft-azure-backup.md)
- [Back-upserver gebruiken om een back-up van een VMware-server te maken](backup-azure-backup-server-vmware.md)
- [Back-upserver gebruiken om een back-up te maken van SQL Server](backup-azure-sql-mabs.md)
- [Moderne back-upopslag toevoegen aan back-upserver](backup-mabs-add-storage.md)
