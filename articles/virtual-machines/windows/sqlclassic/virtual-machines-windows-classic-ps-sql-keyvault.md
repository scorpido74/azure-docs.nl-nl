---
title: Key Vault integreren met een klassieke Azure SQL Server-VM
description: Meer informatie over het automatiseren van de configuratie van SQL Server versleuteling voor gebruik met Azure Key Vault. In dit onderwerp wordt uitgelegd hoe u Azure Key Vault integratie kunt gebruiken met SQL Server virtuele machines maken in het klassieke implementatie model.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: ddf23126154f5bc62c49f62ac4adf517d6987091
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033460"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Azure Key Vault integratie configureren voor SQL Server op Azure Virtual Machines (klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassiek](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Overzicht
Er zijn meerdere SQL Server versleutelings functies, zoals [transparante gegevens versleuteling (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [versleuteling op kolom niveau (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)en [back-upcodering](https://msdn.microsoft.com/library/dn449489.aspx). Voor deze coderings vormen moet u de cryptografische sleutels die u voor versleuteling gebruikt, beheren en opslaan. De Azure Key Vault-service (Azure) is ontworpen voor het verbeteren van de beveiliging en het beheer van deze sleutels op een veilige en Maxi maal beschik bare locatie. Met de [SQL Server-connector](https://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server deze sleutels vanuit Azure Key Vault gebruiken.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/resource-manager-deployment-model.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

Als u SQL Server met on-premises machines uitvoert, zijn er [stappen die u kunt volgen om toegang te krijgen tot Azure Key Vault vanaf uw on-premises SQL Server computer](https://msdn.microsoft.com/library/dn198405.aspx). Maar voor SQL Server in azure-Vm's kunt u tijd besparen door gebruik te maken van de functie voor *Azure Key Vault integratie* . Met enkele Azure PowerShell-cmdlets om deze functie in te scha kelen, kunt u de configuratie automatiseren die nodig is voor een SQL-VM om toegang te krijgen tot uw sleutel kluis.

Als deze functie is ingeschakeld, wordt de SQL Server-connector automatisch geïnstalleerd, wordt de EKM-provider geconfigureerd voor toegang tot Azure Key Vault en wordt de referentie gemaakt waarmee u toegang krijgt tot uw kluis. Als u de stappen in de eerder genoemde on-premises documentatie hebt bekeken, kunt u zien dat deze functie stap 2 en 3 automatiseert. Het enige wat u nog steeds hand matig moet doen, is door de sleutel kluis en sleutels te maken. Van daaruit wordt de volledige installatie van uw SQL-VM geautomatiseerd. Zodra deze functie is voltooid, kunt u T-SQL-instructies uitvoeren om te beginnen met het versleutelen van uw data bases of back-ups zoals u dat gewend bent.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Azure-integratie configureren
Gebruik Power shell om Azure Key Vault-integratie te configureren. In de volgende secties vindt u een overzicht van de vereiste para meters en vervolgens een Power shell-voorbeeld script.

### <a name="install-the-sql-server-iaas-extension"></a>De SQL Server IaaS-extensie installeren
Installeer eerst [de SQL Server IaaS-extensie](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Meer informatie over de invoer parameters
De volgende tabel bevat de para meters die vereist zijn om het Power shell-script uit te voeren in de volgende sectie.

| Parameter | Beschrijving | Voorbeeld |
| --- | --- | --- |
| **$akvURL** |**De sleutel kluis-URL** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Principal-naam van service** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Geheim van Service-Principal** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Referentienaam**: de Azure Sleutelkluis-integratie maakt een referentie in SQL Server, zodat de virtuele machine toegang heeft tot de sleutelkluis. Kies een naam voor deze referentie. |"mycred1" |
| **$vmName** |**Naam van de virtuele machine**: de naam van een eerder gemaakte SQL-VM. |"myvmname" |
| **$serviceName** |**Service naam**: de naam van de Cloud service die aan de SQL-VM is gekoppeld. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Azure-integratie met Power shell inschakelen
Met de cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** maakt u een configuratie object voor de functie integratie van Azure Key Vault. Met de **set-AzureVMSqlServerExtension** configureert u deze integratie met de para meter **KeyVaultCredentialSettings** . De volgende stappen laten zien hoe u deze opdrachten kunt gebruiken.

1. In Azure PowerShell configureert u eerst de invoer parameters met uw specifieke waarden zoals beschreven in de vorige secties van dit onderwerp. Het volgende script is een voor beeld.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Gebruik vervolgens het volgende script om Azure-integratie te configureren en in te scha kelen.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

De SQL IaaS agent-extensie werkt de SQL-VM bij met deze nieuwe configuratie.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

