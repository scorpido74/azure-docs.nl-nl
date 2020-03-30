---
title: Privé-IP-adressen configureren voor VM's (Klassiek) - Azure PowerShell | Microsoft Documenten
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines (Classic) met PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00a4f282da3a943516ffce584247ac20935526b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058713"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Privé-IP-adressen configureren voor een virtuele machine (Classic) met PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel. U ook [een statisch privé-IP-adres beheren in het implementatiemodel resourcebeheer](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

De onderstaande powershell-opdrachten verwachten een eenvoudige omgeving die al is gemaakt. Als u de opdrachten wilt uitvoeren zoals deze in dit document worden weergegeven, bouwt u eerst de testomgeving die is beschreven in [Een VNet maken.](virtual-networks-create-vnet-classic-netcfg-ps.md)

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Controleren of er een specifiek IP-adres beschikbaar is
Voer de volgende PowerShell-opdracht uit om te controleren of het IP-adres *192.168.1.101* beschikbaar is in een VNet met de naam *TestVNet,* voert u de volgende PowerShell-opdracht uit en controleert u de waarde voor *IsAvailable:*

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Verwachte uitvoer:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Een statisch privé-IP-adres opgeven bij het maken van een vm
Het PowerShell-script hieronder maakt een nieuwe cloudservice met de naam *TestService,* haalt vervolgens een afbeelding uit Azure op, maakt een VM met de naam *DNS01* in de nieuwe cloudservice met behulp van de opgehaalde afbeelding, stelt de VM in op een subnet met de naam *FrontEnd*en stelt *192.168.1.7* in als een statisch privé-IP-adres voor de VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Verwachte uitvoer:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Statische privé-IP-adresgegevens ophalen voor een virtuele-
Als u de statische privé-IP-adresgegevens voor de vm wilt weergeven die met het bovenstaande script is gemaakt, voert u de volgende PowerShell-opdracht uit en houdt u de waarden voor *IpAddress*in acht:

    Get-AzureVM -Name DNS01 -ServiceName TestService

Verwachte uitvoer:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
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

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Een statisch privé-IP-adres verwijderen uit een virtuele machine
Voer de volgende PowerShell-opdracht uit om het statische privé-IP-adres te verwijderen dat aan de vm in het bovenstaande script is toegevoegd:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Verwachte uitvoer:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Een statisch privé-IP-adres toevoegen aan een bestaande virtuele-
Voer de volgende opdracht uit om een statisch privé-IP-adres toe te voegen aan de vm die is gemaakt met het bovenstaande script:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Verwachte uitvoer:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen instellen in het besturingssysteem

Het wordt aanbevolen dat u het privé-IP-ip dat is toegewezen aan de virtuele Azure-machine niet statisch toewijst in het besturingssysteem van een vm, tenzij dit nodig is. Als u het privé-IP-adres in het besturingssysteem handmatig instelt, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de Azure VM, of u de verbinding met de virtuele machine verliezen. U mag nooit handmatig het openbare IP-adres toewijzen dat is toegewezen aan een virtuele Azure-machine in het besturingssysteem van de virtuele machine.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [gereserveerde openbare IP-adressen.](virtual-networks-reserved-public-ip.md)
* Meer informatie over [IP-adressen op instantieniveau.](virtual-networks-instance-level-public-ip.md)
* Raadpleeg de [gereserveerde IP REST API's](https://msdn.microsoft.com/library/azure/dn722420.aspx).

