---
title: Virtuele SQL-machines in azure bulksgewijs registreren met de resource provider van de SQL-VM | Microsoft Docs
description: Registreer SQL Server Vm's bulksgewijs met de resource provider van de SQL-VM om de beheer baarheid te verbeteren.
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
ms.openlocfilehash: 80a1ac3ebe5f49d6a63f47e08e0b16114d75e91f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199212"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Virtuele SQL-machines in azure bulksgewijs registreren met de resource provider van de SQL-VM

In dit artikel wordt beschreven hoe u uw virtuele SQL Server Virtual Machine (VM) in azure bulksgewijs kunt registreren met de resource provider van de SQL-VM met behulp van de `Register-SqlVMs` Power shell-cmdlet.

De cmdlet `Register-SqlVMs` kan worden gebruikt voor het registreren van alle virtuele machines in een bepaalde lijst met abonnementen, resource groepen of een lijst met specifieke virtuele machines. De cmdlet registreert de virtuele machines in de modus _licht gewicht_ beheer en genereert vervolgens zowel een [rapport als een logboek bestand](#output-description). 

Het registratie proces heeft geen risico, heeft geen uitval tijd en wordt niet opnieuw opgestart SQL Server of de virtuele machine. 

Zie [SQL VM resource provider](virtual-machines-windows-sql-register-with-resource-provider.md)voor meer informatie over de resource provider. 

## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server-VM wilt registreren bij de resource provider, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/) dat is [geregistreerd bij de resource provider](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) en geen geregistreerde SQL Server virtuele machines bevat. 
- De client referenties die worden gebruikt om de virtuele machines te registreren, bestaan in een van de volgende RBAC-rollen: Inzender, **Inzender**of **eigenaar**van de **virtuele machine**. 
- De nieuwste versie van [AZ Power shell](/powershell/azure/new-azureps-module-az). 
- De meest recente versie van [AZ. SqlVirtualMachine] (https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0.

## <a name="getting-started"></a>Aan de slag

Voordat u doorgaat, moet u eerst een lokale kopie van het script maken, deze importeren als een Power shell-module en verbinding maken met Azure. 

### <a name="create-script"></a>Script maken

Als u het script wilt maken, kopieert u het [volledige script](#full-script) uit het einde van dit artikel en slaat u het lokaal op als `RegisterSqlVMs.psm1`. 

### <a name="import-script"></a>Script importeren

Zodra het script is gemaakt, kunt u het importeren als een module in de Power shell-terminal. 

Open een Power shell-terminal voor beheer en navigeer naar de locatie waar u het `RegisterSqlVMs.psm1` bestand hebt opgeslagen. Voer vervolgens de volgende Power shell-cmdlet uit om het script te importeren als een module: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Verbinding maken met Azure

Gebruik de volgende Power shell-cmdlet om verbinding te maken met Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Alle virtuele machines in de lijst met abonnementen 

Gebruik de volgende cmdlet om alle SQL Server virtuele machines in een lijst met abonnementen te registreren:

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

## <a name="all-vms-in-a-single-subscription"></a>Alle Vm's in één abonnement

Gebruik de volgende cmdlet om alle SQL Server virtuele machines in één abonnement te registreren: 

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

## <a name="all-vms-in-multiple-resource-groups"></a>Alle Vm's in meerdere resource groepen

Gebruik de volgende cmdlet om alle SQL Server virtuele machines in meerdere resource groepen binnen één abonnement te registreren:

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

## <a name="all-vms-in-a-resource-group"></a>Alle virtuele machines in een resource groep

Gebruik de volgende cmdlet om alle SQL Server virtuele machines in één resource groep te registreren: 

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

## <a name="specific-vms-in-single-resource-group"></a>Specifieke Vm's in één resource groep

Gebruik de volgende cmdlet om specifieke SQL Server virtuele machines in één resource groep te registreren:

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

Gebruik de volgende cmdlet om een specifieke SQL Server virtuele machine te registreren: 

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


## <a name="output-description"></a>Uitvoer beschrijving

Er wordt zowel een rapport-als een logboek bestand gegenereerd telkens wanneer de cmdlet `Register-SqlVMs` wordt gebruikt. 

### <a name="report"></a>Rapport

Het rapport wordt gegenereerd als een `.txt` bestand met de naam `RegisterSqlVMScriptReport<Timestamp>.txt` waarbij de tijds tempel het tijdstip is waarop de cmdlet is gestart. Het rapport bevat de volgende details:

| **Uitvoer waarde** | **Beschrijving** |
| :--------------  | :-------------- | 
| Het aantal abonnementen dat is geregistreerd voor omdat u geen toegang of referenties hebt, zijn onjuist | Dit geeft het aantal en de lijst met abonnementen die problemen met de verstrekte verificatie hebben. De gedetailleerde fout vindt u in het logboek door te zoeken naar de abonnements-ID. | 
| Het aantal abonnementen dat niet kan worden geprobeerd, omdat deze niet zijn geregistreerd bij de RP | Deze sectie bevat het aantal en de lijst met abonnementen die niet zijn geregistreerd bij de resource provider van de SQL-VM. |
| Totaal aantal gevonden Vm's | Het aantal virtuele machines dat is gevonden in het bereik van de para meters die zijn door gegeven aan de cmdlet. | 
| Vm's al geregistreerd | Het aantal virtuele machines dat is overgeslagen omdat het al is geregistreerd bij de resource provider. |
| Aantal Vm's dat is geregistreerd | Het aantal virtuele machines dat is geregistreerd na het uitvoeren van de cmdlet. Geeft een lijst van de geregistreerde virtuele machines in de indeling `SubscriptionID, Resource Group, Virtual Machine`. | 
| Aantal Vm's dat niet kan worden geregistreerd vanwege een fout | Het aantal virtuele machines dat niet kan worden geregistreerd wegens een fout. De details van de fout vindt u in het logboek bestand. | 
| Het aantal Vm's dat is overgeslagen als de virtuele machine of de windvlaag-agent op de VM is niet actief | Count en een lijst met virtuele machines die niet kunnen worden geregistreerd als de virtuele machine of de gast agent op de virtuele machine zijn niet uitgevoerd. Deze kunnen opnieuw worden geprobeerd wanneer de virtuele machine of gast agent is gestart. Meer informatie vindt u in het logboek bestand. |
| Aantal overgeslagen Vm's omdat deze niet worden uitgevoerd SQL Server op Windows | Het aantal virtuele machines dat is overgeslagen omdat deze niet worden uitgevoerd SQL Server of geen virtuele Windows-machine zijn. De virtuele machines worden weer gegeven in de indeling `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Logboek 

Fouten worden vastgelegd in het logboek bestand met de naam `VMsNotRegisteredDueToError<Timestamp>.log` waarbij tijds tempel het tijdstip is waarop het script is gestart. Als de fout zich op het abonnements niveau bevindt, bevat het logboek de door komma's gescheiden SubscriptionID en het fout bericht. Als de fout is opgetreden bij de registratie van de virtuele machine, bevat het logboek de abonnements-ID, de naam van de resource groep, de naam van de virtuele machine, de fout code en het bericht, gescheiden door komma's. 

## <a name="remarks"></a>Opmerkingen

Wanneer u SQL Server Vm's registreert bij de resource provider met behulp van het meegeleverde script, moet u rekening houden met het volgende:

- Registratie bij de resource provider vereist een gast agent die wordt uitgevoerd op de SQL Server VM. Installatie kopieën van Windows Server 2008 hebben geen gast agent, zodat deze virtuele machines mislukken en hand matig moeten worden geregistreerd met de [beheer modus](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)voor niet-agents.
- Er is een nieuwe logica ingebouwd om transparante fouten te verholpen. Als de virtuele machine is geregistreerd, is het een snelle bewerking. Als de registratie echter mislukt, wordt elke virtuele machine opnieuw geprobeerd.  Als zodanig is het belang rijk dat u veel tijd hebt om het registratie proces te volt ooien, maar de vereiste voor de werkelijke hoeveelheid tijd is afhankelijk van het type en het aantal fouten. 

## <a name="full-script"></a>Volledige script

Zie [bulk registratie van SQL-vm's met AZ Power shell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)voor het volledige script op github. 

Kopieer het volledige script en sla het op als `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]



## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
