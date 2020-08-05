---
title: "Zelfstudie: VM's maken waarop een SQL-, IIS-, .NET-stack wordt uitgevoerd in Azure"
description: In deze zelfstudie leert u hoe de Azure SQL, IIS, .NET-stack installeert op een virtuele Windows-machine in Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c53194bd345c18ac582acd538f1e8f8e1e34d54
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027849"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Zelfstudie: De SQL-, IIS-, .NET-stack installeren op een Windows-VM met Azure PowerShell

In deze zelfstudie installeren we een SQL-, IIS-, .NET-stack met Azure PowerShell. Deze stack bestaat uit twee virtuele machines met Windows Server 2016, waarvan een met IIS en .NET en een met SQL Server.

> [!div class="checklist"]
> * Een virtuele machine maken 
> * IIS en .NET Core SDK installeren op de virtuele machine
> * Een VM maken waarop SQL Server wordt uitgevoerd
> * De SQL Server-extensie installeren

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-an-iis-vm"></a>Een virtuele IIS-machine maken 

In dit voorbeeld gebruiken we de cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm) in PowerShell Cloud Shell om snel een Windows Server 2016-VM te maken en vervolgens IIS en .NET Framework te installeren. De virtuele IIS en SQL-machines delen een resourcegroep en een virtueel netwerk, dus voor deze namen maken we variabelen.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Installeer IIS en .NET Framework met behulp van de aangepaste scriptextensie met de cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension).

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Nog een subnet maken

Maak een tweede subnet voor de SQL-VM. Haal het VNet op met behulp van [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Maak een configuratie voor het subnet met behulp van [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Werk het VNet bij met de nieuwe subnetgegevens met behulp van [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL-VM

Gebruik een vooraf geconfigureerde Azure Marketplace-installatiekopie van een SQL-server om de SQL-VM te maken. We maken eerst de virtuele machine en installeren vervolgens de SQL Server-extensie op de VM. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Gebruik [Set-AzVMSqlServerExtension](/powershell/module/az.compute/set-azvmsqlserverextension) om de [SQL Server-extensie](../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md) toe te voegen aan de SQL-VM.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een SQL&#92;IIS&#92;.NET-stack geïnstalleerd met behulp van Azure PowerShell. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een virtuele machine maken 
> * IIS en .NET Core SDK installeren op de virtuele machine
> * Een VM maken waarop SQL Server wordt uitgevoerd
> * De SQL Server-extensie installeren

Ga door naar de volgende zelfstudie om te leren hoe u een IIS-webserver kunt beveiligen met behulp van TLS-/SSL-certificaten.

> [!div class="nextstepaction"]
> [Een IIS-webserver beveiligen met TSL-/SSL-certificaten](tutorial-secure-web-server.md)
