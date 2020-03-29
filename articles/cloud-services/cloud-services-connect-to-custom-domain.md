---
title: Een cloudservice verbinden met een aangepaste domeincontroller | Microsoft Documenten
description: Meer informatie over het koppelen van uw web-/werknemersrollen aan een aangepast AD-domein met PowerShell en AD-domeinextensie
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: d40e392984d2675c748bda00c61cdaeb1c0932da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387017"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Azure Cloud Services-rollen koppelen aan een aangepaste AD-domeincontroller die wordt gehost in Azure
We zullen eerst een Virtual Network (VNet) instellen in Azure. Vervolgens voegen we een Active Directory Domain Controller (gehost op een Azure Virtual Machine) toe aan het VNet. Vervolgens voegen we bestaande cloudservicerollen toe aan het vooraf gemaakte VNet en verbinden deze vervolgens met de domeincontroller.

Voordat we beginnen, een paar dingen om in gedachten te houden:

1. Deze zelfstudie maakt gebruik van PowerShell, dus zorg ervoor dat Azure PowerShell is geïnstalleerd en klaar voor gebruik. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor hulp bij het instellen van Azure PowerShell.
2. De exemplaren AD-domeincontroller en web-/werkrol moeten zich in het VNet bevinden.

Volg deze stapsgewijze handleiding en als je problemen tegenkomt, laat ons dan een reactie achter aan het einde van het artikel. Iemand zal terug naar u (ja, we lezen opmerkingen).

Het netwerk waarnaar wordt verwezen door de cloudservice moet een **klassiek virtueel netwerk**zijn.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
U een virtueel netwerk maken in Azure met behulp van de Azure-portal of PowerShell. Voor deze zelfstudie wordt PowerShell gebruikt. Zie [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md)als u een virtueel netwerk wilt maken met de Azure-portal. Het artikel gaat over het maken van een virtueel netwerk (Resource Manager), maar u moet een virtueel netwerk (Classic) maken voor cloudservices. Selecteer hiervoor in de portal **Een resource maken,** typ *virtueel netwerk* in het vak **Zoeken** en druk op **Enter**. Selecteer **virtueel netwerk**in de zoekresultaten onder **Alles**. Selecteer **onder Een implementatiemodel selecteren**en selecteer **Klassiek**en selecteer **Vervolgens Maken**. U dan de stappen in het artikel volgen.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Zodra u het virtuele netwerk hebt ingesteld, moet u een AD-domeincontroller maken. Voor deze zelfstudie stellen we een AD-domeincontroller in op een Azure Virtual Machine.

Maak hiervoor een virtuele machine via PowerShell met de volgende opdrachten:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Uw virtuele machine promoten bij een domeincontroller
Als u de virtuele machine wilt configureren als ad-domeincontroller, moet u zich aanmelden bij de virtuele machine en deze configureren.

Als u zich wilt aanmelden bij de VM, u het RDP-bestand via PowerShell downloaden, de volgende opdrachten gebruiken:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Zodra u bent aangemeld bij de VM, stelt u uw virtuele machine in als AD-domeincontroller door de stapsgewijze handleiding voor [het instellen van uw AD-domeincontroller voor klanten te volgen.](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Uw cloudservice toevoegen aan het virtuele netwerk
Vervolgens moet u uw implementatie van uw cloudservice toevoegen aan het nieuwe VNet. Om dit te doen, wijzigt u uw cloudservice cscfg door de relevante secties toe te voegen aan uw cscfg met behulp van Visual Studio of de editor van uw keuze.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Vervolgens bouwt u uw cloudservicesproject en implementeer t-subseer naar Azure. Zie [Een cloudservice maken en implementeren voor](cloud-services-how-to-create-deploy-portal.md) hulp bij het implementeren van uw cloudservicespakket naar Azure.

## <a name="connect-your-webworker-roles-to-the-domain"></a>Uw web-/werknemersrollen verbinden met het domein
Zodra uw cloudserviceproject is geïmplementeerd op Azure, koppelt u uw rolinstanties met het aangepaste AD-domein met behulp van de AD-domeinextensie. Als u de AD-domeinextensie wilt toevoegen aan uw bestaande implementatie van cloudservices en lid wilt worden van het aangepaste domein, voert u de volgende opdrachten uit in PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Dat is alles.

Uw cloudservices moeten worden samengevoegd met uw aangepaste domeincontroller. Als u meer wilt weten over de verschillende opties die beschikbaar zijn voor het configureren van AD-domeinextensie, gebruikt u de Help powershell. Een paar voorbeelden volgen:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



