---
title: Open bare IP-adressen (klassiek) van Azure-exemplaar | Microsoft Docs
description: Meer informatie over ILPIP-adressen (instance level open bare IP) en hoe u deze beheert met behulp van Power shell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: 74d10c8fbe2f82d6148f5e13cb57c46dd645f76f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979546"
---
# <a name="instance-level-public-ip-classic-overview"></a>Overzicht van open bare IP-adressen (klassiek) op exemplaar niveau
Een openbaar IP-adres (ILPIP) op exemplaar niveau is een openbaar IP-adressen dat u rechtstreeks kunt toewijzen aan een virtuele machine of Cloud Services rolinstantie, in plaats van naar de Cloud service waar uw VM of rolinstantie zich bevindt. Een ILPIP neemt niet de plaats van het virtuele IP-adres (VIP) dat is toegewezen aan uw Cloud service. In plaats daarvan is het een extra IP-adres dat u kunt gebruiken om rechtstreeks verbinding te maken met uw VM of rolinstantie.

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dit artikel gaat over het gebruik van het klassieke implementatiemodel. Micro soft raadt aan Vm's te maken met Resource Manager. Zorg ervoor dat u begrijpt hoe [IP-adressen](virtual-network-ip-addresses-overview-classic.md) werken in Azure.

![Verschil tussen ILPIP en VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Zoals weer gegeven in afbeelding 1, wordt de Cloud service geopend met behulp van een VIP, terwijl de afzonderlijke Vm's normaal gesp roken worden gebruikt via VIP:&lt;poort nummer&gt;. Door een ILPIP toe te wijzen aan een specifieke virtuele machine, kan de virtuele machine rechtstreeks worden geopend met behulp van dat IP-adres.

Wanneer u een Cloud service in azure maakt, worden de bijbehorende DNS-records automatisch gemaakt om toegang tot de service toe te staan via een Fully Qualified Domain Name (FQDN), in plaats van de daad werkelijke VIP te gebruiken. Hetzelfde proces treedt op voor een ILPIP, waardoor toegang tot de virtuele machine of het rolinstantie wordt toegestaan door de FQDN in plaats van de ILPIP. Als u bijvoorbeeld een Cloud service met de naam *contosoadservice*maakt en u een webrol met de naam *contosoweb* met twee exemplaren configureert, en in. cscfg `domainNameLabel` is ingesteld op *WebPublicIP*, registreert Azure de volgende records voor de instanties:


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> U kunt slechts één ILPIP toewijzen voor elke VM of rolinstantie. U kunt Maxi maal vijf ILPIPs per abonnement gebruiken. ILPIPs worden niet ondersteund voor Vm's met meerdere NIC'S.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Waarom zou ik een ILPIP aanvragen?
Als u verbinding wilt kunnen maken met uw virtuele machine of rolinstantie met een IP-adres dat rechtstreeks aan de VM is toegewezen, in plaats van de Cloud service-VIP te gebruiken:&lt;poort nummer&gt;, vraagt u een ILPIP aan voor uw virtuele machine of uw rolinstantie.

* **Actieve FTP** : door een ILPIP toe te wijzen aan een virtuele machine, kan het verkeer ontvangen op elke poort. Er zijn geen eind punten vereist voor de virtuele machine om verkeer te ontvangen.  Zie [overzicht van FTP-protocollen](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) voor meer informatie over het FTP-protocol.
* **Uitgaand IP** -verkeer dat afkomstig is van de virtuele machine, wordt toegewezen aan de ILPIP als bron en de ILPIP unieke identificatie van de virtuele machine naar externe entiteiten.

> [!NOTE]
> In het verleden werd een ILPIP-adres aangeduid als een openbaar IP-adres (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Een ILPIP voor een virtuele machine beheren
De volgende taken bieden u de mogelijkheid om ILPIPs te maken, toe te wijzen en te verwijderen uit Vm's:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Een ILPIP aanvragen tijdens het maken van een virtuele machine met behulp van Power shell
Met het volgende Power shell-script maakt u een Cloud service met de naam *FTPService*, haalt u een installatie kopie op uit Azure, maakt u een virtuele machine met de naam *FTPInstance* met behulp van de opgehaalde installatie kopie, stelt u de virtuele machine in op een ILPIP en voegt u de virtuele machine toe aan de nieuwe

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
Als u een ander opslag account wilt opgeven als locatie van de nieuwe VM-schijf, kunt u de para meter **MediaLocation** gebruiken:

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>ILPIP-gegevens ophalen voor een virtuele machine
Als u de ILPIP-informatie voor de virtuele machine die met het vorige script is gemaakt, wilt weer geven, voert u de volgende Power shell-opdracht uit en bekijkt u de waarden voor *PublicIPAddress* en *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Verwachte uitvoer:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Een ILPIP verwijderen uit een virtuele machine
Als u de ILPIP die is toegevoegd aan de virtuele machine in het vorige script wilt verwijderen, voert u de volgende Power shell-opdracht uit:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Een ILPIP toevoegen aan een bestaande virtuele machine
Voer de volgende opdracht uit om een ILPIP toe te voegen aan de virtuele machine die is gemaakt met behulp van het script:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Een ILPIP beheren voor een Cloud Services rolinstantie

Voer de volgende stappen uit om een ILPIP toe te voegen aan een Cloud Services Role:

1. Down load het. cscfg-bestand voor de Cloud service door de stappen in het artikel [Cloud Services configureren te](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) volt ooien.
2. Werk het cscfg-bestand bij door het `InstanceAddress`-element toe te voegen. In het volgende voor beeld wordt een ILPIP met de naam *MyPublicIP* toegevoegd aan een rolinstantie met de naam *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Upload het. cscfg-bestand voor de Cloud service door de stappen in het artikel [Cloud Services configureren te](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) volt ooien.

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>ILPIP-gegevens ophalen voor een Cloud service
Als u de ILPIP-informatie per rolinstantie wilt weer geven, voert u de volgende Power shell-opdracht uit en bekijkt u de waarden voor *PublicIPAddress*, *PublicIPName*, *PublicIPDomainNameLabel* en *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

U kunt ook `nslookup` gebruiken om een query uit te zoeken naar een record van het subdomein:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Volgende stappen
* Begrijp hoe [IP-adres Sering](virtual-network-ip-addresses-overview-classic.md) werkt in het klassieke implementatie model.
* Meer informatie over [gereserveerde ip's](virtual-networks-reserved-public-ip.md).
