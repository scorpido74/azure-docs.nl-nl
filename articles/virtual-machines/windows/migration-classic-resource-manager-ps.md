---
title: Migreren naar Resource Manager met PowerShell
description: In dit artikel wordt de door het platform ondersteunde migratie van IaaS-bronnen zoals virtuele machines (VM's), virtuele netwerken en opslagaccounts van klassiek naar Azure Resource Manager doorlopen met Azure PowerShell-opdrachten
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 12a77c4c21a26f1ec52bb3ffdc312df56d3c4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249969"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>IaaS-resources migreren van klassiek naar Azure Resource Manager met PowerShell

> [!IMPORTANT]
> Vandaag de dag gebruikt ongeveer 90% van de IaaS VM's [Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Vanaf 28 februari 2020 zijn klassieke VM's afgeschaft en op 1 maart 2023 volledig met pensioen gegaan. [Meer informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe het u beïnvloedt.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

In deze stappen ziet u hoe u Azure PowerShell-opdrachten gebruiken om infrastructuur als serviceresources (IaaS) te migreren van het klassieke implementatiemodel naar het implementatiemodel azure resource manager.

Als u wilt, u ook resources migreren met behulp van de [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* Zie [Platformondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager voor](migration-classic-resource-manager-overview.md)achtergrondinformatie over ondersteunde migratiescenario's.
* Zie Technische deep dive over [platformondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)voor gedetailleerde richtlijnen en een migratie-walkthrough.
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md).

<br>
Hier volgt een stroomdiagram om te bepalen in welke volgorde stappen moeten worden uitgevoerd tijdens een migratieproces.

![Schermafbeelding van de migratiestappen](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Stap 1: Migratieplan
Hier volgen een paar aanbevolen procedures die u aanbeveelt bij het evalueren of u IaaS-bronnen wilt migreren van klassiek naar Resource Manager:

* Lees de [ondersteunde en niet-ondersteunde functies en configuraties](migration-classic-resource-manager-overview.md)door. Als u virtuele machines hebt die niet-ondersteunde configuraties of functies gebruiken, wacht u tot de configuratie of functieondersteuning wordt aangekondigd. Als het aan uw behoeften voldoet, verwijdert u deze functie of verwijdert u deze configuratie om migratie in te schakelen.
* Als u geautomatiseerde scripts hebt die uw infrastructuur en toepassingen vandaag implementeren, probeert u een vergelijkbare testinstelling te maken met behulp van deze scripts voor migratie. U ook voorbeeldomgevingen instellen met behulp van de Azure-portal.

> [!IMPORTANT]
> Toepassingsgateways worden momenteel niet ondersteund voor migratie van klassiek naar Resourcebeheer. Als u een virtueel netwerk wilt migreren met een toepassingsgateway, verwijdert u de gateway voordat u een bewerking Voorbereiden uitvoert om het netwerk te verplaatsen. Nadat u de migratie hebt voltooid, sluit u de gateway opnieuw aan in Azure Resource Manager.
>
> Azure ExpressRoute-gateways die verbinding maken met ExpressRoute-circuits in een ander abonnement, kunnen niet automatisch worden gemigreerd. Verwijder in dergelijke gevallen de ExpressRoute-gateway, migreert u het virtuele netwerk en maakt u de gateway opnieuw. Zie [ExpressRoute-circuits migreren en bijbehorende virtuele netwerken van de klassieker naar het implementatiemodel Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md)voor meer informatie.

## <a name="step-2-install-the-latest-version-of-powershell"></a>Stap 2: Installeer de nieuwste versie van PowerShell
Er zijn twee belangrijke opties om Azure PowerShell te installeren: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) of [WebPlatform Installer (WebPI).](https://aka.ms/webpi-azps) WebPI ontvangt maandelijkse updates. PowerShell Gallery ontvangt continu updates. Dit artikel is gebaseerd op Azure PowerShell-versie 2.1.0.

Zie [Azure PowerShell installeren en configureren voor](/powershell/azure/overview)installatie-instructies.

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Stap 3: Ervoor zorgen dat u een beheerder bent voor het abonnement
Als u deze migratie wilt uitvoeren, moet u worden toegevoegd als medebeheerder voor het abonnement in de [Azure-portal.](https://portal.azure.com)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Abonnement**in het menu **Hub** . Als u het niet ziet, selecteert u **Alle services**.
3. Zoek de juiste abonnementsvermelding en bekijk het veld **MIJN ROL.** Voor een medebeheerder moet de waarde _accountbeheerder_zijn.

Als u geen coadministrator toevoegen, neemt u contact op met een servicebeheerder of medebeheerder om het abonnement te laten toevoegen.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Stap 4: Uw abonnement instellen en u aanmelden voor migratie
Start eerst een PowerShell-prompt. Voor migratie stelt u uw omgeving in voor zowel klassiek als Resource Manager.

Meld u aan bij uw account voor het Resource Manager-model.

```powershell
    Connect-AzAccount
```

Ontvang de beschikbare abonnementen met de volgende opdracht:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Stel uw Azure-abonnement in voor de huidige sessie. In dit voorbeeld wordt de standaardabonnementsnaam ingesteld op **Mijn Azure-abonnement**. Vervang de naam van het voorbeeldabonnement door de eigen naam.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registratie is een eenmalige stap, maar u moet dit één keer doen voordat u een poging tot migratie doet. Zonder registratie ziet u het volgende foutbericht:
>
> *BadRequest : Abonnement is niet geregistreerd voor migratie.*

Registreer u bij de migratieresourceprovider met de volgende opdracht:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Wacht vijf minuten tot de registratie is voltooid. Controleer de status van de goedkeuring met behulp van de volgende opdracht:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Zorg ervoor dat `Registered` RegistrationState is voordat u verder gaat.

Voordat u overstapt op het klassieke implementatiemodel, moet u ervoor zorgen dat u voldoende vCPU's van Azure Resource Manager hebt in de Azure-regio van uw huidige implementatie of virtueel netwerk. U de volgende PowerShell-opdracht gebruiken om het huidige aantal vCPU's in Azure Resource Manager te controleren. Zie [Limieten en Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)voor meer informatie over vCPU-quota.

In dit voorbeeld wordt de beschikbaarheid in de **regio West-VS** gecontroleerd. Vervang de naam van het voorbeeldgebied door de naam van het voorbeeld.

```powershell
    Get-AzVMUsage -Location "West US"
```

Meld u nu aan bij uw account voor het klassieke implementatiemodel.

```powershell
    Add-AzureAccount
```

Ontvang de beschikbare abonnementen met de volgende opdracht:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Stel uw Azure-abonnement in voor de huidige sessie. In dit voorbeeld wordt het standaardabonnement ingesteld op **Mijn Azure-abonnement**. Vervang de naam van het voorbeeldabonnement door de eigen naam.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Stap 5: Opdrachten uitvoeren om uw IaaS-resources te migreren
* [VM's migreren in een cloudservice (niet in een virtueel netwerk)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [VM's migreren in een virtueel netwerk](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Een opslagaccount migreren](#step-52-migrate-a-storage-account)

> [!NOTE]
> Alle hier beschreven operaties zijn idempotent. Als u een ander probleem hebt dan een niet-ondersteunde functie of een configuratiefout, raden we u aan de bewerking voor te bereiden, af te breken of te plegen opnieuw te proberen. Het platform probeert de actie vervolgens opnieuw.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Stap 5.1: Optie 1 - Virtuele machines migreren in een cloudservice (niet in een virtueel netwerk)
Download de lijst met cloudservices met behulp van de volgende opdracht. Kies vervolgens de cloudservice die u wilt migreren. Als de VM's in de cloudservice zich in een virtueel netwerk bevinden of als ze web- of werknemersrollen hebben, retourneert de opdracht een foutbericht.

```powershell
    Get-AzureService | ft Servicename
```

Download de implementatienaam voor de cloudservice. In dit voorbeeld is de servicenaam **Mijn service**. Vervang de naam van de voorbeeldservice door uw eigen servicenaam.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Bereid de virtuele machines in de cloudservice voor op migratie. Je hebt twee opties om uit te kiezen.

* **Optie 1: Migreer de VM's naar een virtueel netwerk dat door een platform is gemaakt.**

    Controleer eerst of u de cloudservice migreren met behulp van de volgende opdrachten:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    In de volgende opdracht worden waarschuwingen en fouten weergegeven die migratie blokkeren. Als de validatie is geslaagd, u verder gaan met de stap Voorbereiden.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Optie 2: Migreren naar een bestaand virtueel netwerk in het implementatiemodel ResourceBeheer.**

    In dit voorbeeld wordt de naam van de brongroep ingesteld op **myResourceGroup,** de naam van het virtuele netwerk op **myVirtualNetwork**en de subnetnaam op **mySubNet**. Vervang de namen in het voorbeeld door de namen van uw eigen bronnen.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Controleer eerst of u het virtuele netwerk migreren met de volgende opdracht:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    In de volgende opdracht worden waarschuwingen en fouten weergegeven die migratie blokkeren. Als de validatie is geslaagd, u doorgaan met de volgende stap Voorbereiden:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Nadat de bewerking Voorbereiden is geslaagd met een van de voorgaande opties, zoekt u de migratiestatus van de VM's op. Zorg ervoor dat ze `Prepared` in de staat zijn.

In dit voorbeeld wordt de VM-naam ingesteld op **myVM**. Vervang de voorbeeldnaam door uw eigen VM-naam.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Controleer de configuratie voor de voorbereide resources met PowerShell of de Azure-portal. Als u nog niet klaar bent voor migratie en u terug wilt naar de oude status, gebruikt u de volgende opdracht:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Als de voorbereide configuratie er goed uitziet, u verder gaan en de resources vastleggen met behulp van de volgende opdracht:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Stap 5.1: Optie 2 - Virtuele machines migreren in een virtueel netwerk

Als u virtuele machines wilt migreren in een virtueel netwerk, migreert u het virtuele netwerk. De virtuele machines migreren automatisch met het virtuele netwerk. Kies het virtuele netwerk dat u wilt migreren.
> [!NOTE]
> [Migreer één virtuele machine](migrate-single-classic-to-resource-manager.md) die is gemaakt met behulp van het klassieke implementatiemodel door een nieuwe virtuele resourcemachine met beheerde schijven te maken met behulp van de VHD-bestanden (OS en gegevens) van de virtuele machine.
<br>

> [!NOTE]
> De naam van het virtuele netwerk kan afwijken van wat in de nieuwe portal wordt weergegeven. De nieuwe Azure-portal `[vnet-name]`geeft de naam weer als `Group [resource-group-name] [vnet-name]`, maar de werkelijke virtuele netwerknaam is van type . Voordat u de migratie start, zoekt u de `Get-AzureVnetSite | Select -Property Name` werkelijke virtuele netwerknaam op met behulp van de opdracht of bekijkt u deze in de oude Azure-portal. 

In dit voorbeeld wordt de naam van het virtuele netwerk ingesteld op **myVnet.** Vervang het voorbeeld virtuele netwerknaam door uw eigen netwerk.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Als het virtuele netwerk web- of werknemersrollen of VM's met niet-ondersteunde configuraties bevat, krijgt u een foutbericht voor validatie.

Controleer eerst of u het virtuele netwerk migreren met de volgende opdracht:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

In de volgende opdracht worden waarschuwingen en fouten weergegeven die migratie blokkeren. Als de validatie is geslaagd, u doorgaan met de volgende stap Voorbereiden:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Controleer de configuratie voor de voorbereide virtuele machines met Azure PowerShell of de Azure-portal. Als u nog niet klaar bent voor migratie en u terug wilt naar de oude status, gebruikt u de volgende opdracht:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Als de voorbereide configuratie er goed uitziet, u verder gaan en de resources vastleggen met behulp van de volgende opdracht:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Stap 5.2: Een opslagaccount migreren
Nadat u klaar bent met het migreren van de virtuele machines, voert u de volgende vereistecontroles uit voordat u de opslagaccounts migreert.

> [!NOTE]
> Als uw opslagaccount geen gekoppelde schijven of VM-gegevens heeft, u rechtstreeks naar de sectie 'Opslagaccounts valideren en migratie starten' overgaan.

* Voorwaarde controleert of u vm's hebt gemigreerd of als uw opslagaccount schijfbronnen heeft:
    * Migreer virtuele machines waarvan de schijven zijn opgeslagen in het opslagaccount.

        Met de volgende opdracht retourneert rolename- en disknaam-eigenschappen van alle VM-schijven in het opslagaccount. RoleName is de naam van de virtuele machine waaraan een schijf is gekoppeld. Als deze opdracht schijven retourneert, moet u ervoor zorgen dat virtuele machines waaraan deze schijven zijn gekoppeld, worden gemigreerd voordat u het opslagaccount migreert.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Verwijder niet-gekoppelde VM-schijven die zijn opgeslagen in het opslagaccount.

        Zoek niet-gekoppelde VM-schijven in het opslagaccount met behulp van de volgende opdracht:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Als de vorige opdracht schijven retourneert, verwijdert u deze schijven met de volgende opdracht:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * VM-afbeeldingen verwijderen die zijn opgeslagen in het opslagaccount.

        Met de volgende opdracht retourneert u alle VM-afbeeldingen met besturingssysteemschijven die zijn opgeslagen in het opslagaccount.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Met de volgende opdracht retourneert u alle VM-afbeeldingen met gegevensschijven die zijn opgeslagen in het opslagaccount.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Verwijder alle VM-afbeeldingen die door de vorige opdrachten zijn geretourneerd met deze opdracht:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Opslagaccounts valideren en migratie starten.

    Valideer elk opslagaccount voor migratie met behulp van de volgende opdracht. In dit voorbeeld is de naam van het opslagaccount **myStorageAccount**. Vervang de voorbeeldnaam door de naam van uw eigen opslagaccount.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    De volgende stap is het voorbereiden van het opslagaccount voor migratie.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Controleer de configuratie voor het voorbereide opslagaccount met Azure PowerShell of de Azure-portal. Als u nog niet klaar bent voor migratie en u terug wilt naar de oude status, gebruikt u de volgende opdracht:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Als de voorbereide configuratie er goed uitziet, u verder gaan en de resources vastleggen met behulp van de volgende opdracht:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van platformondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Communitytools voor het helpen bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest gestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
