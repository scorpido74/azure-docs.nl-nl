---
title: Key Vault integreren met een klassieke Azure SQL Server VM
description: Meer informatie over het automatiseren van de configuratie van SQL Server-versleuteling voor gebruik met Azure Key Vault. In dit onderwerp wordt uitgelegd hoe u Azure Key Vault Integration gebruiken met virtuele SQL Server-machines in het klassieke implementatiemodel.
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
ms.openlocfilehash: f878c6f7a59328e2f68ffbaee066bba4a5b6c898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978134"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Azure Key Vault Integration for SQL Server configureren op Azure Virtual Machines (Klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassiek](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Overzicht
Er zijn meerdere SQL Server-versleutelingsfuncties, zoals [transparante gegevensversleuteling (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [versleuteling op kolomniveau (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)en [back-upversleuteling](https://msdn.microsoft.com/library/dn449489.aspx). Deze vormen van versleuteling vereisen dat u de cryptografische sleutels beheert en opslaat die u voor versleuteling gebruikt. De AKV-service (Azure Key Vault) is ontworpen om de beveiliging en het beheer van deze sleutels op een veilige en zeer beschikbare locatie te verbeteren. Met [de SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server deze sleutels gebruiken vanuit Azure Key Vault.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

Als u SQL Server met on-premises machines uitvoert, zijn er [stappen die u volgen om toegang te krijgen tot Azure Key Vault vanaf uw on-premises SQL Server-machine.](https://msdn.microsoft.com/library/dn198405.aspx) Maar voor SQL Server in Azure VM's u tijd besparen met de azure *key vault-integratiefunctie.* Met een paar Azure PowerShell-cmdlets om deze functie in te schakelen, u de configuratie automatiseren die nodig is voor een SQL VM om toegang te krijgen tot uw sleutelkluis.

Wanneer deze functie is ingeschakeld, installeert deze automatisch de SQL Server Connector, configureert u de EKM-provider om toegang te krijgen tot Azure Key Vault en maakt u de referenties waarmee u toegang hebt tot uw kluis. Als u de stappen in de eerder genoemde on-premises documentatie bekijkt, u zien dat deze functie stap 2 en 3 automatiseert. Het enige wat je nog handmatig hoeft te doen, is het maken van de sleutelkluis en toetsen. Van daaruit wordt de volledige installatie van uw SQL VM geautomatiseerd. Zodra deze functie deze installatie heeft voltooid, u T-SQL-instructies uitvoeren om te beginnen met het versleutelen van uw databases of back-ups zoals u dat normaal zou doen.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV-integratie configureren
Gebruik PowerShell om Azure Key Vault-integratie te configureren. De volgende secties geven een overzicht van de vereiste parameters en vervolgens een voorbeeld powershell-script.

### <a name="install-the-sql-server-iaas-extension"></a>De SQL Server IaaS-extensie installeren
Installeer eerst [de SQL Server IaaS-extensie.](../classic/sql-server-agent-extension.md)

### <a name="understand-the-input-parameters"></a>De invoerparameters begrijpen
In de volgende tabel worden de parameters weergegeven die nodig zijn om het PowerShell-script in de volgende sectie uit te voeren.

| Parameter | Beschrijving | Voorbeeld |
| --- | --- | --- |
| **$akvURL** |**De URL van de sleutelkluis** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Naam serviceprincipal** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Service Principal geheim** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Referentienaam**: de Azure Sleutelkluis-integratie maakt een referentie in SQL Server, zodat de virtuele machine toegang heeft tot de sleutelkluis. Kies een naam voor deze referentie. |"mycred1" |
| **$vmName** |**Naam van virtuele machine:** de naam van een eerder gemaakte SQL VM. |"myvmname" |
| **$serviceName** |**Servicenaam:** de naam van de CloudService die is gekoppeld aan de SQL VM. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>AKV-integratie inschakelen met PowerShell
De cmdlet **Nieuw-AzureVMSqlServerKeyVaultConfig maakt** een configuratieobject voor de azure key vault-integratiefunctie. De **Set-AzureVMSqlServerExtension** configureert deze integratie met de parameter **KeyVaultCredentialSettings.** In de volgende stappen wordt uitgelegd hoe u deze opdrachten gebruikt.

1. Configureer in Azure PowerShell eerst de invoerparameters met uw specifieke waarden zoals beschreven in de vorige secties van dit onderwerp. Het volgende script is een voorbeeld.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Gebruik vervolgens het volgende script om AKV-integratie te configureren en in te schakelen.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

De SQL IaaS Agent Extension zal de SQL VM bijwerken met deze nieuwe configuratie.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

