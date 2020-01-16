---
title: Statisch intern particulier IP-Azure VM-klassiek
description: Informatie over statische interne Ip's (spannings dips) en hoe u deze beheert
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a13a0a54e9ded48cc5848843f4c329b2dea90f65
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975217"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Een statisch intern privé IP-adres instellen met behulp van Power shell (klassiek)
In de meeste gevallen hoeft u geen statisch intern IP-adres voor uw virtuele machine op te geven. Vm's in een virtueel netwerk ontvangen automatisch een intern IP-adres uit een bereik dat u opgeeft. In bepaalde gevallen is het opgeven van een statisch IP-adres voor een bepaalde virtuele machine zinvol. Als uw virtuele machine bijvoorbeeld DNS gaat uitvoeren of als een domein controller is. Een statisch intern IP-adres blijft met de VM, zelfs via de status voor stoppen/ongedaan maken. 

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../azure-resource-manager/management/deployment-models.md). Dit artikel gaat over het gebruik van het klassieke implementatiemodel. Micro soft raadt aan de meeste nieuwe implementaties het [Resource Manager-implementatie model](virtual-networks-static-private-ip-arm-ps.md)te gebruiken.
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>De Azure PowerShell service management-module installeren

Voordat u de volgende opdrachten uitvoert, moet u ervoor zorgen dat de [Azure PowerShell service management-module](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) op de computer is geïnstalleerd. Zie [Azure module in PowerShell Gallery](https://www.powershellgallery.com/packages/Azure/5.3.0)voor de versie geschiedenis van Azure PowerShell service management-module.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Controleren of een specifiek IP-adres beschikbaar is
Als u wilt controleren of het IP-adres *10.0.0.7* beschikbaar is in een vnet met de naam *TestVnet*, voert u de volgende Power shell-opdracht uit en controleert u de waarde voor *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Als u de bovenstaande opdracht in een veilige omgeving wilt testen, volgt u de richt lijnen in [een virtueel netwerk (klassiek) maken](virtual-networks-create-vnet-classic-pportal.md) om een vnet met de naam *TestVnet* te maken en ervoor te zorgen dat de adres ruimte *10.0.0.0/8* wordt gebruikt.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Een statisch intern IP-adres opgeven bij het maken van een VM
Het Power shell-script hieronder maakt een nieuwe Cloud service met de naam *TestService*, haalt een installatie kopie op uit Azure en maakt vervolgens een virtuele machine met de naam *TestVM* in de nieuwe Cloud service met behulp van de opgehaalde installatie kopie, stelt de virtuele machine in een subnet met de naam *subnet-1*en stelt *10.0.0.7* in als een statisch intern IP-adres voor de virtuele machine:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Statische interne IP-gegevens ophalen voor een virtuele machine
Als u de statische interne IP-gegevens voor de virtuele machine wilt weer geven die zijn gemaakt met het bovenstaande script, voert u de volgende Power shell-opdracht uit en bekijkt u de waarden voor *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Een statisch intern IP-adres verwijderen van een virtuele machine
Voer de volgende Power shell-opdracht uit om het statische interne IP-adres dat is toegevoegd aan de virtuele machine, te verwijderen uit het bovenstaande script:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Een statisch intern IP-adres toevoegen aan een bestaande virtuele machine
Voer de volgende opdracht uit om een statisch intern IP-adres toe te voegen aan de virtuele machine die is gemaakt met behulp van het bovenstaande script:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Volgende stappen
[Gereserveerd IP](virtual-networks-reserved-public-ip.md)

[Openbaar IP-adres op exemplaar niveau (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Gereserveerd IP REST-Api's](https://msdn.microsoft.com/library/azure/dn722420.aspx)

