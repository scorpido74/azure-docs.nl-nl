---
title: Een virtuele machine (klassiek) met meerdere Nic's maken-Azure PowerShell | Microsoft Docs
description: Meer informatie over het maken van een virtuele machine (klassiek) met meerdere Nic's met behulp van Power shell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: af22bc43a06be74c7a4b6c869725a19fc87a0f3e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058730"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Een virtuele machine (klassiek) met meerdere Nic's maken met behulp van Power shell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

U kunt virtuele machines (Vm's) in azure maken en meerdere netwerk interfaces (Nic's) aan elke virtuele machine koppelen. Meerdere Nic's zorgen voor schei ding van verkeers typen op verschillende Nic's. Eén NIC kan bijvoorbeeld communiceren met het Internet, terwijl een andere netwerk adapter alleen communiceert met interne bronnen die niet zijn verbonden met internet. De mogelijkheid om netwerk verkeer over meerdere Nic's te scheiden, is vereist voor veel virtuele netwerk apparaten, zoals het leveren van toepassingen en WAN-optimalisatie oplossingen.

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources:  [Resource Manager en het klassieke model](../resource-manager-deployment-model.md). Dit artikel gaat over het gebruik van het klassieke implementatiemodel. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Meer informatie over het uitvoeren van deze stappen met het [Resource Manager-implementatie model](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

In de volgende stappen wordt een resource groep met de naam *IaaSStory* gebruikt voor de webservers en een resource groep met de naam *IaaSStory-back-end* voor de DB-servers.

## <a name="prerequisites"></a>Vereisten

Voordat u de DB-servers kunt maken, moet u de resource groep *IaaSStory* maken met alle benodigde resources voor dit scenario. Voer de volgende stappen uit om deze resources te maken. Maak een virtueel netwerk door de stappen in het artikel [een virtueel netwerk maken](virtual-networks-create-vnet-classic-netcfg-ps.md) te volgen.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>De back-end-Vm's maken
De back-end-Vm's zijn afhankelijk van het maken van de volgende resources:

* **Back-end-subnet**. De database servers maken deel uit van een afzonderlijk subnet om verkeer te scheiden. Het onderstaande script verwacht dat dit subnet bestaat in een vnet met de naam *WTestVnet*.
* **Opslag account voor gegevens schijven**. Voor betere prestaties gebruiken de gegevens schijven op de database servers een SSD-technologie (Solid State Drive), waarvoor een Premium-opslag account is vereist. Zorg ervoor dat de Azure-locatie die u implementeert voor de ondersteuning van Premium Storage.
* **Beschikbaarheidsset**. Alle database servers worden toegevoegd aan één beschikbaarheidsset om ervoor te zorgen dat ten minste één van de Vm's tijdens het onderhoud actief is.

### <a name="step-1---start-your-script"></a>Stap 1: Start uw script
U kunt het volledige Power shell-script dat [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)wordt gebruikt, downloaden. Volg de onderstaande stappen om het script te wijzigen in uw omgeving.

1. Wijzig de waarden van de onderstaande variabelen op basis van uw bestaande resource groep die hierboven is geïmplementeerd in [vereisten](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Wijzig de waarden van de onderstaande variabelen op basis van de waarden die u wilt gebruiken voor de back-end-implementatie.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Stap 2: Maak benodigde resources voor uw Vm's
U moet een nieuwe Cloud service en een opslag account maken voor de gegevens schijven voor alle virtuele machines. U moet ook een installatie kopie en een lokaal Administrator-account voor de virtuele machines opgeven. Als u deze resources wilt maken, voert u de volgende stappen uit:

1. Maak een nieuwe Cloud service.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Maak een nieuw Premium-opslag account.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Stel het opslag account in dat hierboven is gemaakt als het huidige opslag account voor uw abonnement.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Selecteer een installatie kopie voor de virtuele machine.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Stel de referenties van het lokale beheerders account in.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Stap 3: Vm's maken
U moet een lus gebruiken om zoveel Vm's te maken als u wilt en de benodigde Nic's en virtuele machines in de lus te maken. Voer de volgende stappen uit om de Nic's en Vm's te maken.

1. Start een `for` lus om de opdrachten te herhalen om een virtuele machine en twee nic's zo vaak als nodig te maken, op basis van de `$numberOfVMs` waarde van de variabele.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Maak een `VMConfig` -object dat de installatie kopie, grootte en beschikbaarheidsset voor de virtuele machine specificeert.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Richt de VM in als een Windows-VM.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Stel de standaard-NIC in en wijs deze toe aan een statisch IP-adres.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Voeg een tweede NIC toe voor elke VM.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Maak op gegevens schijven voor elke VM.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Maak elke VM en beëindig de lus.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Stap 4: het script uitvoeren
Nu u het script hebt gedownload en gewijzigd op basis van uw behoeften, runt u het script om de back-end-database Vm's te maken met meerdere Nic's.

1. Sla het script op en voer dit uit vanuit de **Power shell** -opdracht prompt of **Power shell ISE**. U ziet de eerste uitvoer, zoals hieronder wordt weer gegeven.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Vul de gegevens in die nodig zijn bij de prompt voor referenties en klik op **OK**. De volgende uitvoer wordt geretourneerd.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Stap 5: route ring configureren in het besturings systeem van de virtuele machine

Azure DHCP wijst een standaard gateway toe aan de eerste (primaire) netwerk interface die is gekoppeld aan de virtuele machine. Azure wijst geen standaardgateway toe aan extra (secundaire) netwerkinterfaces die zijn gekoppeld aan een virtuele machine. Daarom kunt u standaard niet communiceren met resources buiten het subnet waarin een secundaire netwerkinterface zich bevindt. Secundaire netwerkinterfaces kunnen echter wel communiceren met resources buiten hun subnet. Als u route ring wilt configureren voor secundaire netwerk interfaces, raadpleegt u de volgende artikelen:

- [Een Windows-VM configureren voor meerdere Nic's](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Een Linux-VM configureren voor meerdere Nic's](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
