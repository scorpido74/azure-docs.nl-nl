---
title: Een klassieke SQL Server-VM maken (Power shell)
description: Bevat stappen en Power shell-scripts voor het maken van een Azure-VM met SQL Server galerie installatie kopieën van virtuele machines. In dit onderwerp wordt gebruikgemaakt van de klassieke implementatie modus.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 8757b634b76867a2d5ccce3dcfdc9d66ef25c25e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032736"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Een SQL Server virtuele machine inrichten met behulp van Azure PowerShell (klassiek)

In dit artikel worden de stappen beschreven voor het maken van een SQL Server virtuele machine in azure met behulp van de Power shell-cmdlets.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/resource-manager-deployment-model.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

Voor de Resource Manager-versie van dit onderwerp raadpleegt [u een SQL Server virtuele machine inrichten met behulp van Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Power Shell installeren en configureren:
1. Als u geen Azure-account hebt, gaat u naar [Azure, gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
2. [Down load en installeer de meest recente Azure PowerShell-opdrachten](/powershell/azure/overview).
3. Start Windows Power shell en verbind deze met uw Azure-abonnement met de opdracht **add-AzureAccount** .

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>De Azure-doel regio bepalen

Uw SQL Server virtuele machine wordt gehost in een Cloud service die een specifieke Azure-regio bevindt. De volgende stappen helpen u bij het bepalen van de regio, het opslag account en de Cloud service die voor de rest van de zelf studie wordt gebruikt.

1. Bepaal het Data Center dat u wilt gebruiken voor het hosten van uw SQL Server-VM. Met de volgende Power shell-opdracht wordt een lijst met beschik bare regio namen weer gegeven.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Wanneer u de gewenste locatie hebt opgegeven, stelt u een variabele met de naam **$dcLocation** in op die regio. Met de volgende opdracht stelt u bijvoorbeeld de regio in op ' Oost-VS ':

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Uw abonnement en opslag account instellen

1. Bepaal het Azure-abonnement dat u wilt gebruiken voor de nieuwe virtuele machine.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Wijs uw doel-Azure-abonnement toe aan de **$subscr** variabele. Stel deze vervolgens in als uw huidige Azure-abonnement.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Controleer vervolgens op bestaande opslag accounts. In het volgende script worden alle opslag accounts weer gegeven die voor komen in de gekozen regio:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Als u een nieuw opslag account nodig hebt, maakt u eerst een naam voor het opslag account met een kleine letters met de opdracht New-AzureStorageAccount, zoals in het volgende voor beeld: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Wijs de naam van het doel opslag account toe aan de **$staccount**. Gebruik vervolgens **set-abonnement** om het abonnement en het huidige opslag account in te stellen.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Een installatie kopie van een SQL Server virtuele machine selecteren

1. Bekijk de lijst met beschik bare installatie kopieën voor virtuele machines van SQL Server in de galerie. Deze installatie kopieën hebben allemaal een **ImageFamily** -eigenschap die begint met ' SQL '. Met de volgende query wordt de afbeeldings familie weer gegeven die beschikbaar is SQL Server vooraf geïnstalleerd.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Wanneer u de installatie kopie serie van de virtuele machine vindt, kunnen er meerdere gepubliceerde installatie kopieën in deze serie zijn. Gebruik het volgende script om de meest recente naam van de installatie kopie van de virtuele machine te vinden voor de geselecteerde installatie kopie familie (zoals **SQL Server 2016 RTM Enter prise op Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>De virtuele machine maken

Maak ten slotte de virtuele machine met Power shell:

1. Maak een Cloud service om de nieuwe VM te hosten. Het is ook mogelijk om in plaats daarvan een bestaande Cloud service te gebruiken. Maak een nieuwe variabele **$svcname** met de korte naam van de Cloud service.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Geef de naam van de virtuele machine en een grootte op. Zie [Virtual Machine sizes for Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Engelstalig) voor meer informatie over de grootte van virtuele machines.

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Geef het lokale Administrator-account en-wacht woord op.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Voer het volgende script uit om de virtuele machine te maken.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Zie de sectie **uw opdrachtset bouwen** in [gebruik Azure PowerShell voor het maken en vooraf configureren van op Windows gebaseerde virtual machines](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)voor meer uitleg en configuratie opties.

## <a name="example-powershell-script"></a>Voor beeld Power shell-script

Het volgende script geeft een voor beeld van een volledig script waarmee een **SQL Server 2016 RTM Enter prise op een virtuele machine met Windows Server 2012 R2** wordt gemaakt. Als u dit script gebruikt, moet u de eerste variabelen aanpassen op basis van de vorige stappen in dit onderwerp.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Verbinding maken met extern bureau blad

1. Maak de RDP-bestanden in de map document van de huidige gebruiker om deze virtuele machines te starten om de installatie te volt ooien:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. Start het RDP-bestand in de map documenten. Maak verbinding met de gebruikers naam en het wacht woord van de beheerder die u eerder hebt opgegeven (als uw gebruikers naam bijvoorbeeld VMAdmin is, geeft u "\VMAdmin" op als de gebruiker en geeft u het wacht woord op).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>De configuratie van de SQL Server machine voor externe toegang volt ooien

Nadat u zich hebt aangemeld bij de computer met extern bureau blad, configureert u SQL Server op basis van de instructies in de [stappen voor het configureren van SQL Server connectiviteit in een Azure-VM](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Volgende stappen

Meer instructies voor het inrichten van virtuele machines met Power shell vindt u in de [documentatie van virtual machines](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

In veel gevallen is de volgende stap het migreren van uw data bases naar deze nieuwe SQL Server VM. Zie [een Data Base migreren naar SQL Server op een virtuele machine van Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)voor meer informatie over database migratie.

Als u de Azure Portal ook wilt gebruiken om SQL-Virtual Machines te maken, raadpleegt u [een SQL Server virtuele machine inrichten in azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Houd er rekening mee dat de zelf studie die u via de portal doorgeeft, virtuele machines maakt met behulp van het aanbevolen Resource Manager-model in plaats van het klassieke model dat wordt gebruikt in dit Power shell-onderwerp.

Naast deze bronnen wordt u aangeraden [andere onderwerpen te bekijken die betrekking hebben op het uitvoeren van SQL Server in Azure virtual machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
