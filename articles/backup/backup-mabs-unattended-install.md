---
title: Achtergrond installatie van Azure Backup Server v2
description: Gebruik een Power shell-script om Azure Backup Server v2 op de achtergrond te installeren. Dit type installatie wordt ook wel een installatie zonder toezicht genoemd.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 3777aecea5e25b33a7010ad90887829406e491ae
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210164"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Een installatie zonder toezicht van Azure Backup Server uitvoeren

Meer informatie over het uitvoeren van een installatie zonder toezicht van Azure Backup Server.

Deze stappen zijn niet van toepassing als u Azure Backup Server v1 installeert.

## <a name="install-backup-server"></a>Back-upserver installeren

1. Maak een tekst bestand op de server die als host fungeert voor Azure Backup Server v2 of hoger. (U kunt het bestand in Klad blok of in een andere tekst editor maken.) Sla het bestand op als MABSSetup. ini.

2. Plak de volgende code in het bestand MABSSetup. ini. Vervang de tekst tussen de haakjes (\< \>) door waarden uit uw omgeving. De volgende tekst is een voor beeld:

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

3. Sla het bestand op. Voer vervolgens de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid op de installatie server:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

U kunt deze vlaggen gebruiken voor de installatie:</br>
**/f**:. ini-bestandspad</br>
**/l**: Logboekpad</br>
**/i**: Installatiepad</br>
**/x**: Uninstall-pad</br>

## <a name="next-steps"></a>Volgende stappen
Nadat u de back-upserver installeert, leert u hoe u uw server voorbereidt of een werk belasting gaat beveiligen.

- [Werk belastingen voor de back-upserver voorbereiden](backup-azure-microsoft-azure-backup.md)
- [Back-upserver gebruiken om een back-up te maken van een VMware-Server](backup-azure-backup-server-vmware.md)
- [Back-upserver gebruiken om back-ups te maken van SQL Server](backup-azure-sql-mabs.md)
- [Modern Backup Storage toevoegen aan de back-upserver](backup-mabs-add-storage.md)
