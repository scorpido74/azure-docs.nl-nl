---
title: Azure Backup for SQL Server wordt uitgevoerd in Azure VM
description: In dit artikel leest u hoe u Azure Backup registreert in SQL Server in een virtuele Azure-machine.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247381"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup for SQL Server wordt uitgevoerd in Azure VM

Azure Backup biedt onder andere ondersteuning voor het maken van back-ups zoals SQL Server die wordt uitgevoerd in Azure VM's. Aangezien de SQL-toepassing binnen een Azure VM wordt uitgevoerd, heeft de back-upservice toestemming nodig om toegang te krijgen tot de toepassing en de benodigde gegevens op te halen.
Hiervoor installeert Azure Backup de **AzureBackupWindowsWorkload-extensie** op de VM, waarin de SQL Server wordt uitgevoerd, tijdens het registratieproces dat door de gebruiker wordt geactiveerd.

## <a name="prerequisites"></a>Vereisten

Raadpleeg voor de lijst met ondersteunde scenario's de [ondersteuningsmatrix](../../backup/sql-support-matrix.md#scenario-support) die wordt ondersteund door Azure Backup.

## <a name="network-connectivity"></a>Netwerkconnectiviteit

Azure Backup ondersteunt NSG-tags, het implementeren van een proxyserver of vermelde IP-bereiken; voor meer informatie over elk van de methoden, verwijzen dit [artikel](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Extensieschema

Het uitbreidingsschema en de eigenschapswaarden zijn de configuratiewaarden (runtime-instellingen) die service doorgeeft aan de CRP-API. Deze config waarden worden gebruikt tijdens de registratie en upgrade. **AzureBackupWindowsWorkload-extensie** maakt ook gebruik van dit schema. Het schema is vooraf ingesteld; een nieuwe parameter kan worden toegevoegd in het objectStr-veld

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

In de volgende JSON wordt het schema voor de extensie WorkloadBackup weergegeven.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Eigenschapswaarden

Name | Waarde/voorbeeld | Gegevenstype
 --- | --- | ---
landinstellingen | nl-nl  |  tekenreeks
Taskid | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | tekenreeks
objectStr <br/> (openbareInstellingen)  | "eyJjb250YWluZXJQcm9wZZWWVzIjp7IkNvbnRhaW5lcklEIJoiMzVjMjQXYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkWiSWRNZ210Q29udG FpbmVySWQiOjM0NTY3ODG5LCJSZXNvNvJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZMFlLWE5ODItOTkwOWMyMGNjVhwiWiU3Vic2NyaXB0aWuSWQiOiJkNGEzO Ny1iYjYYLTQ2MWMtODmYS1jNTM5ODI3ZTgzTgzNTQIJVbmlxdWVDb250YWluZXJOYW1lJoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWZJNzNhJg3OTcyIn0sInN0YW1wTGldCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==" | tekenreeks
opdrachtStartTimeUTCTicks | "636967192566036845"  | tekenreeks
vmType  | "microsoft.compute/virtualmachines"  | tekenreeks
objectStr <br/> (protectedInstellingen) | "eyJjb250YWluZXJQcm9wZZWWVzIjp7IkNvbnRhaW5lcklEIJoiMzVjMjQXYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkWiSWRNZ210Q29udG FpbmVySWQiOjM0NTY3ODG5LCJSZXNvNvJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZMFlLWE5ODItOTkwOWMyMGNjVhwiWiU3Vic2NyaXB0aWuSWQiOiJkNGEzO Ny1iYjYYLTQ2MWMtODmYS1jNTM5ODI3ZTgzTgzNTQIJVbmlxdWVDb250YWluZXJOYW1lJoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWZJNzNhJg3OTcyIn0sInN0YW1wTGldCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==" | tekenreeks
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | tekenreeks
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | tekenreeks

## <a name="template-deployment"></a>Sjabloonimplementatie

We raden aan om azurebackupWindowsWorkload-extensie toe te voegen aan een virtuele machine door SQL Server-back-up op de virtuele machine in te schakelen. Dit kan worden bereikt via de [resourcemanagersjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) die is ontworpen voor het automatiseren van back-ups op een SQL Server VM.

## <a name="powershell-deployment"></a>PowerShell-implementatie

U moet de Azure VM die de SQL-toepassing bevat 'registreren' met een vault voor herstelservices. Tijdens de registratie wordt de AzureBackupWindowsWorkload-extensie op de VM geïnstalleerd. Gebruik [register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet om de VM te registreren.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

De opdracht retourneert een **back-upcontainer** van deze bron en de status wordt **geregistreerd.**

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) over richtlijnen voor het oplossen van azure SQL Server VM-back-ups
- [Veelvoorkomende vragen](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) over het maken van back-ups van SQL Server-databases die worden uitgevoerd op virtuele Azure-machines (VM's) en die de Azure Backup-service gebruiken.
