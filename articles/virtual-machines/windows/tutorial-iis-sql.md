---
title: Zelfstudie - VM's maken met een SQL-, IIS-, .NET-stack in Azure
description: In deze zelfstudie leert u hoe de Azure SQL, IIS, .NET-stack installeert op een virtuele Windows-machine in Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 762d67a291ec33e13ca9e7311b0cf11da140dd6c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80154267"
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

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U Cloud Shell ook starten op [https://shell.azure.com/powershell](https://shell.azure.com/powershell)een apart browsertabblad door naar. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-an-iis-vm"></a>Een IIS-vm maken 

In dit voorbeeld gebruiken we de cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) in PowerShell Cloud Shell om snel een Windows Server 2016-VM te maken en vervolgens IIS en .NET Framework te installeren. De virtuele IIS en SQL-machines delen een resourcegroep en een virtueel netwerk, dus voor deze namen maken we variabelen.


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

Installeer IIS en .NET Framework met behulp van de aangepaste scriptextensie met de cmdlet [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).

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

Maak een configuratie voor het subnet met behulp van [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Werk het VNet bij met de nieuwe subnetgegevens met behulp van [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork)
   
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

Gebruik [Set-AzVMSqlServerExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension) om de [SQL Server-extensie](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) toe te voegen aan de SQL-VM.

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

Ga naar de volgende zelfstudie voor meer informatie over het beveiligen van IIS-webserver met TLS/SSL-certificaten.

> [!div class="nextstepaction"]
> [Beveilig iIS-webserver met TLS/SSL-certificaten](tutorial-secure-web-server.md)

