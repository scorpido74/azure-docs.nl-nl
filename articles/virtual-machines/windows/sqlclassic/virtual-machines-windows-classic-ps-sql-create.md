---
title: Een klassieke SQL Server VM (PowerShell) maken
description: Biedt stappen en PowerShell-scripts voor het maken van een Azure VM met sql server-galerieafbeeldingen voor virtuele machines. In dit onderwerp wordt de klassieke implementatiemodus gebruikt.
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
ms.openlocfilehash: 5bfdcfab37091a5f581ce147c0a6af5ccd8147a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914786"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Een virtuele SQL Server-machine inrichten met Azure PowerShell (klassiek)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

In dit artikel vindt u stappen voor het maken van een virtuele SQL Server-machine in Azure met behulp van de PowerShell-cmdlets.

> [!NOTE] 
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

Zie [Een virtuele SQL Server-machine inrichten met Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md)voor de resourcebeheerversie van dit onderwerp.

### <a name="install-and-configure-powershell"></a>PowerShell installeren en configureren:
1. Als u geen Azure-account hebt, gaat u naar [Azure, gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
2. [Download en installeer de nieuwste Azure PowerShell-opdrachten.](/powershell/azure/overview)
3. Start Windows PowerShell en verbind het met uw Azure-abonnement met de opdracht **Add-AzureAccount.**

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Uw azure-doelregio bepalen

Uw SQL Server Virtual Machine wordt gehost in een cloudservice die zich bevindt in een specifieke Azure-regio. Met de volgende stappen u uw regio, opslagaccount en cloudservice bepalen die voor de rest van de zelfstudie worden gebruikt.

1. Bepaal het datacenter dat u wilt gebruiken om uw SQL Server VM te hosten. Met de volgende PowerShell-opdracht wordt een lijst met beschikbare regionamen weergegeven.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Zodra u uw gewenste locatie hebt geïdentificeerd, stelt u een variabele in met de naam **$dcLocation** naar die regio. Met de volgende opdracht stelt u bijvoorbeeld de regio in op 'Oost-VS':

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Uw abonnements- en opslagaccount instellen

1. Bepaal het Azure-abonnement dat u voor de nieuwe virtuele machine gebruikt.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Wijs uw doelAzure-abonnement toe aan de **variabele $subscr.** Stel dit vervolgens in als uw huidige Azure-abonnement.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Controleer vervolgens op bestaande opslagaccounts. In het volgende script worden alle opslagaccounts weergegeven die in de door u gekozen regio bestaan:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Als u een nieuw opslagaccount nodig hebt, maakt u eerst een accountnaam voor kleine kleine letters met de opdracht Nieuw-AzureStorageAccount zoals in het volgende voorbeeld:`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Wijs de naam van het doelopslagaccount toe aan de **$staccount**. Gebruik vervolgens **Set-AzureSubscription** om het abonnement en het huidige opslagaccount in te stellen.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Een sql server-afbeelding voor virtuele machines selecteren

1. Ontdek de lijst met beschikbare SQL Server virtuele machines afbeeldingen uit de galerij. Deze afbeeldingen hebben allemaal een **eigenschap ImageFamily** die begint met "SQL". In de volgende query wordt de afbeeldingsfamilie weergegeven waarop SQL Server vooraf is geïnstalleerd.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Wanneer u de virtuele machine afbeelding familie, kunnen er meerdere gepubliceerde afbeeldingen in deze familie. Gebruik het volgende script om de laatst gepubliceerde afbeeldingsnaam voor virtuele machines te vinden voor uw geselecteerde afbeeldingsfamilie (zoals **SQL Server 2016 RTM Enterprise op Windows Server 2012 R2):**

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>De virtuele machine maken

Maak ten slotte de virtuele machine met PowerShell:

1. Maak een cloudservice om de nieuwe VM te hosten. Houd er rekening mee dat het ook mogelijk is om in plaats daarvan een bestaande cloudservice te gebruiken. Maak een nieuwe variabele **$svcname** met de korte naam van de cloudservice.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Geef de naam van de virtuele machine en een grootte op. Zie [Virtuele machineformaten voor Azure voor](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie over virtuele machineformaten.

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Geef het lokale beheerdersaccount en wachtwoord op.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Voer het volgende script uit om de virtuele machine te maken.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Zie de sectie **Uw opdrachtset bouwen** in [Azure PowerShell gebruiken om virtuele machines in Windows te maken en vooraf te configureren](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)voor aanvullende uitleg- en configuratieopties.

## <a name="example-powershell-script"></a>Voorbeeld PowerShell-script

Het volgende script bevat een voorbeeld van een compleet script waarmee een **SQL Server 2016 RTM Enterprise op de virtuele machine van Windows Server 2012 R2** wordt gemaakt. Als u dit script gebruikt, moet u de eerste variabelen aanpassen op basis van de vorige stappen in dit onderwerp.

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

## <a name="connect-with-remote-desktop"></a>Verbinding maken met extern bureaublad

1. Maak de RDP-bestanden in de documentmap van de huidige gebruiker om deze virtuele machines te starten om de installatie te voltooien:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. Start het RDP-bestand in de map documenten. Maak verbinding met de eerder verstrekte gebruikersnaam en wachtwoord van de beheerder (bijvoorbeeld als uw gebruikersnaam VMAdmin was, geef "\VMAdmin" op als gebruiker en geef het wachtwoord op).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>De configuratie van de SQL Server Machine voor externe toegang voltooien

Nadat u zich hebt aanmeldt bij de machine met extern bureaublad, configureert u SQL Server op basis van de instructies in [Stappen voor het configureren van SQL Server-connectiviteit in een Azure VM.](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)

## <a name="next-steps"></a>Volgende stappen

U vindt aanvullende instructies voor het inrichten van virtuele machines met PowerShell in de [documentatie van virtuele machines.](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

In veel gevallen is de volgende stap het migreren van uw databases naar deze nieuwe SQL Server VM. Zie Een database [migreren naar SQL Server op een Azure VM](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)voor richtlijnen voor databasemigratie.

Zie [Een SQL Server Virtual Machine inrichten op Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)als u ook de Azure-portal wilt gebruiken om SQL Virtual Machines te maken. Houd er rekening mee dat de zelfstudie die u door de portal leidt, VM's maakt met behulp van het aanbevolen Resource Manager-model, in plaats van het klassieke model dat wordt gebruikt in dit PowerShell-onderwerp.

Naast deze bronnen raden we u aan [andere onderwerpen met betrekking tot het uitvoeren van SQL Server in Azure Virtual Machines te bekijken.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
