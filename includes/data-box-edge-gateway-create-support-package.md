---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176494"
---
Als u apparaatproblemen ondervindt, u een ondersteuningspakket maken op basis van de systeemlogboeken. Microsoft Support gebruikt dit pakket om de problemen op te lossen. Volg de volgende stappen om een ondersteuningspakket te maken:

1. [Maak verbinding met de PowerShell-interface van uw apparaat.](#connect-to-the-powershell-interface)
2. Gebruik `Get-HcsNodeSupportPackage` de opdracht om een ondersteuningspakket te maken. Het gebruik van de cmdlet is als volgt:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    De cmdlet verzamelt logboeken van uw apparaat en kopieert deze logboeken naar een opgegeven netwerk of lokaal aandeel.

    De gebruikte parameters zijn als volgt:

    - `-Path`- Geef het netwerk of het lokale pad op waarnaar u het ondersteuningspakket wilt kopiëren. (verplicht)
    - `-Credential`- Geef de referenties op om toegang te krijgen tot het beveiligde pad.
    - `-Zip`- Geef op om een zip-bestand te genereren.
    - `-Include`- Geef op de onderdelen op te nemen die in het ondersteuningspakket moeten worden opgenomen. Indien niet `Default` opgegeven, wordt aangenomen.
    - `-IncludeArchived`- Geef op om gearchiveerde logboeken op te nemen in het ondersteuningspakket.
    - `-IncludePeriodicStats`- Geef op om periodieke stat-logboeken op te nemen in het ondersteuningspakket.

    
