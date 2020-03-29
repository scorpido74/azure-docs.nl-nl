---
title: SQL-virtuele machines in Azure bulkregister met de SQL VM-resourceprovider | Microsoft Documenten
description: Bulkregister SQL Server VM's bij de SQL VM-resourceprovider om de beheerbaarheid te verbeteren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353888"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Sql-virtuele machines in Azure bulkregistreren met de SQL VM-resourceprovider

In dit artikel wordt beschreven hoe u uw VIRTUELE SQL Server-virtuele machine `Register-SqlVMs` (VM) in Azure registreren bij de SQL VM-resourceprovider met behulp van de PowerShell-cmdlet.

De `Register-SqlVMs` cmdlet kan worden gebruikt om alle virtuele machines te registreren in een bepaalde lijst met abonnementen, resourcegroepen of een lijst met specifieke virtuele machines. De cmdlet registreert de virtuele machines in _de lichtgewicht_ beheermodus en genereert vervolgens zowel een rapport als [een logboekbestand.](#output-description) 

Het registratieproces brengt geen risico met zich mee, heeft geen downtime en start SQL Server of de virtuele machine niet opnieuw op. 

Zie [SQL VM-resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md)voor meer informatie over de resourceprovider. 

## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server VM wilt registreren bij de resourceprovider, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/) dat is [geregistreerd bij de resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) en niet-geregistreerde virtuele SQL Server-machines bevat. 
- De clientreferenties die worden gebruikt om de virtuele machines te registreren, bestaan in een van de volgende RBAC-rollen: **virtuele machinebijdrager,** **bijdrager**of **eigenaar**. 
- De nieuwste versie van [Az PowerShell](/powershell/azure/new-azureps-module-az). 
- De nieuwste versie van [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Aan de slag

Voordat u verdergaat, moet u eerst een lokale kopie van het script maken, importeren als een PowerShell-module en verbinding maken met Azure. 

### <a name="create-script"></a>Script maken

Als u het script wilt maken, kopieert u het volledige `RegisterSqlVMs.psm1` [script](#full-script) vanaf het einde van dit artikel en slaat u het lokaal op als . 

### <a name="import-script"></a>Script importeren

Zodra het script is gemaakt, u het importeren als een module in de Powershell-terminal. 

Open een administratieve PowerShell-terminal en `RegisterSqlVMs.psm1` navigeer naar de plaats waar u het bestand hebt opgeslagen. Voer vervolgens de volgende PowerShell-cmdlet uit om het script als module te importeren: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Verbinding maken met Azure

Gebruik de volgende PowerShell-cmdlet om verbinding te maken met Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Alle VM's in de lijst met abonnementen 

Gebruik de volgende cmdlet om alle virtuele SQL Server-machines te registreren in een lijst met abonnementen:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Voorbeelduitvoer: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Alle VM's in één abonnement

Gebruik de volgende cmdlet om alle virtuele SQL Server-machines in één abonnement te registreren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Voorbeelduitvoer:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Alle VM's in meerdere resourcegroepen

Gebruik de volgende cmdlet om alle virtuele SQL Server-machines in meerdere resourcegroepen binnen één abonnement te registreren:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Voorbeelduitvoer:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Alle VM's in een resourcegroep

Gebruik de volgende cmdlet om alle virtuele SQL Server-machines in één resourcegroep te registreren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Voorbeelduitvoer:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>Specifieke VM's in één resourcegroep

Gebruik de volgende cmdlet om specifieke virtuele SQL Server-machines te registreren in één resourcegroep:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Voorbeelduitvoer:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>Specifieke VM

Gebruik de volgende cmdlet om een specifieke virtuele SQL Server-machine te registreren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Voorbeelduitvoer:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Uitvoerbeschrijving

Zowel een rapport als een logbestand wordt gegenereerd telkens wanneer de `Register-SqlVMs` cmdlet wordt gebruikt. 

### <a name="report"></a>Rapport

Het rapport wordt `.txt` gegenereerd `RegisterSqlVMScriptReport<Timestamp>.txt` als een bestand met de naam waarbij de tijdstempel het tijdstip is waarop de cmdlet is gestart. Het rapport bevat de volgende details:

| **Uitvoerwaarde** | **Beschrijving** |
| :--------------  | :-------------- | 
| Aantal registratieabonnementen is mislukt omdat u geen toegang hebt of omdat referenties onjuist zijn | Dit geeft het aantal en de lijst met abonnementen die problemen hadden met de meegeleverde verificatie. De gedetailleerde fout is te vinden in het logboek door te zoeken naar de abonnements-ID. | 
| Aantal abonnementen dat niet kon worden geprobeerd omdat ze niet zijn geregistreerd bij de RP | Deze sectie bevat het aantal en de lijst met abonnementen die niet zijn geregistreerd bij de SQL VM-resourceprovider. |
| Totaal aantal VM's gevonden | De telling van virtuele machines die werden gevonden in het bereik van de parameters doorgegeven aan de cmdlet. | 
| VM's al geregistreerd | Het aantal virtuele machines dat werd overgeslagen omdat ze al waren geregistreerd bij de resourceprovider. |
| Aantal vm's dat met succes is geregistreerd | Het aantal virtuele machines dat met succes werd geregistreerd na het uitvoeren van de cmdlet. Hiermee worden de geregistreerde `SubscriptionID, Resource Group, Virtual Machine`virtuele machines in de indeling vermeld . | 
| Aantal VM's kan niet worden geregistreerd vanwege een fout | Aantal virtuele machines die niet kunnen registreren vanwege een fout. De details van de fout zijn te vinden in het logboekbestand. | 
| Aantal VM's dat is overgeslagen omdat de VM of de gust-agent op VM niet wordt uitgevoerd | Tellen en lijst van virtuele machines die niet kunnen worden geregistreerd als de virtuele machine of de gast agent op de virtuele machine werden niet uitgevoerd. Deze kunnen opnieuw worden geprobeerd zodra de virtuele machine of gastagent is gestart. Details zijn te vinden in het logbestand. |
| Aantal VM's dat is overgeslagen omdat ze sql server niet op Windows uitvoeren | Aantal virtuele machines die zijn overgeslagen omdat ze geen SQL Server uitvoeren of geen virtuele Windows-machine zijn. De virtuele machines worden vermeld `SubscriptionID, Resource Group, Virtual Machine`in het formaat . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Logboek 

Fouten worden geregistreerd in `VMsNotRegisteredDueToError<Timestamp>.log` het logboekbestand met de naam timestamp is het moment waarop het script is gestart. Als de fout zich op abonnementsniveau bevindt, bevat het logboek de door komma's gescheiden SubscriptionID en het foutbericht. Als de fout bij de registratie van de virtuele machine is, bevat het logboek de abonnements-id, de naam van de resourcegroep, de naam van de virtuele machine, de foutcode en het bericht gescheiden door komma's. 

## <a name="remarks"></a>Opmerkingen

Wanneer u SQL Server VM's registreert bij de resourceprovider met behulp van het meegeleverde script, houdt u rekening met het volgende:

- Voor registratie bij de resourceprovider is een gastagent vereist die op de SQL Server VM wordt uitgevoerd. Windows Server 2008-afbeeldingen hebben geen gastagent, dus deze virtuele machines mislukken en moeten handmatig worden geregistreerd met behulp van de [NoAgent-beheermodus.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)
- Er is opnieuw proberen logica ingebouwd om transparante fouten te overwinnen. Als de virtuele machine met succes wordt geregistreerd, dan is het een snelle werking. Echter, als de registratie mislukt, dan is elke virtuele machine zal opnieuw worden geprobeerd.  Als zodanig moet u veel tijd om het registratieproces te voltooien - hoewel de werkelijke tijdvereiste is afhankelijk van het type en het aantal fouten. 

## <a name="full-script"></a>Volledige script

Zie [Bulkregister SQL VM's met Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)voor het volledige script op GitHub. 

Kopieer het volledige script `RegisterSqLVMs.psm1`en sla het op als .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-vm](virtual-machines-windows-sql-server-iaas-faq.md)
* [Richtlijnen voor prijzen voor SQL Server op een Windows-vm](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notities voor SQL Server vrijgeven op een Windows-vm](virtual-machines-windows-sql-server-iaas-release-notes.md)
