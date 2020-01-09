---
title: Door Azure gereserveerde IP-adressen (klassiek) beheren | Microsoft Docs
description: Meer informatie over gereserveerde IP-adressen (klassiek) en hoe u deze beheert met behulp van Azure PowerShell en Azure CLI.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: c1de86c1d12109853bb5d6d1aac4143caab9199f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638218"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Gereserveerd IP adressen (klassieke implementatie)

 IP-adressen in Azure worden onderverdeeld in twee categorieën: dynamisch en gereserveerd. Open bare IP-adressen die door Azure worden beheerd, zijn standaard dynamisch. Dit betekent dat het IP-adres dat wordt gebruikt voor een bepaalde Cloud service (VIP) of voor het rechtstreeks openen van een virtuele machine of een rolinstantie (ILPIP), van tijd tot tijd kan veranderen wanneer resources worden afgesloten of gestopt (toewijzing ongedaan gemaakt).

Als u wilt voor komen dat IP-adressen worden gewijzigd, kunt u een IP-adres reserveren. Gereserveerde Ip's kunnen alleen worden gebruikt als een VIP, zodat het IP-adres voor de Cloud service hetzelfde blijft, zelfs wanneer resources worden afgesloten of gestopt (toewijzing ongedaan gemaakt). Daarnaast kunt u bestaande dynamische IP-adressen die worden gebruikt als een VIP omzetten in een gereserveerd IP-adres.

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../azure-resource-manager/resource-manager-deployment-model.md). Dit artikel gaat over het gebruik van het klassieke implementatiemodel. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Meer informatie over het reserveren van een statisch openbaar IP-adres met behulp van het [Resource Manager-implementatie model](virtual-network-ip-addresses-overview-arm.md).

Lees het artikel [IP-adressen](virtual-network-ip-addresses-overview-classic.md) voor meer informatie over IP-adressen in Azure.

## <a name="when-do-i-need-a-reserved-ip"></a>Wanneer heb ik een gereserveerd IP-adres nodig?
* **U wilt ervoor zorgen dat het IP-adres in uw abonnement wordt gereserveerd**. Als u in een situatie een IP-adres wilt reserveren dat niet uit uw abonnement is vrijgegeven, moet u een gereserveerde open bare IP gebruiken.  
* **U wilt dat uw IP-adres bij uw Cloud service blijft, zelfs op de status van gestopt of ongedaan gemaakt (vm's)** . Als u wilt dat uw service wordt geopend met behulp van een IP-adres dat niet verandert, zelfs wanneer de virtuele machines in de Cloud service worden afgesloten of gestopt (toewijzing ongedaan gemaakt).
* **U wilt ervoor zorgen dat uitgaand verkeer van Azure gebruikmaakt van een voorspelbaar IP-adres**. Mogelijk hebt u uw on-premises firewall geconfigureerd om alleen verkeer van specifieke IP-adressen toe te staan. Door een IP-adres te reserveren, weet u het bron-IP-adressen en hoeft u de firewall regels niet bij te werken vanwege een wijziging in de IP.

## <a name="faqs"></a>Veelgestelde vragen
- Kan ik een gereserveerd IP-adres voor alle Azure-Services gebruiken?
    Nee. Gereserveerde Ip's kunnen alleen worden gebruikt voor Vm's en Cloud service-exemplaar rollen die worden weer gegeven via een VIP.
- Hoeveel gereserveerde Ip's kan ik hebben?
    Zie het artikel Azure- [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) voor meer informatie.
- Worden er kosten in rekening gebracht voor gereserveerde Ip's?
    Situaties. Zie de pagina met [prijs informatie voor gereserveerd IP](https://go.microsoft.com/fwlink/?LinkID=398482) voor prijs informatie.
- Hoe kan ik reserveert u een IP-adres?
    U kunt Power shell, de [Azure Management rest API](https://msdn.microsoft.com/library/azure/dn722420.aspx)of de [Azure Portal](https://portal.azure.com) gebruiken om een IP-adres in een Azure-regio te reserveren. Er is een gereserveerd IP-adres gekoppeld aan uw abonnement.
- Kan ik een gereserveerd IP-adres gebruiken met VNets voor affiniteit op basis van een groep?
    Nee. Gereserveerde Ip's worden alleen ondersteund in regionale VNets. Gereserveerde Ip's worden niet ondersteund voor VNets die zijn gekoppeld aan affiniteits groepen. Zie het artikel [over regionale VNets en affiniteits groepen](virtual-networks-migrate-to-regional-vnet.md) voor meer informatie over het koppelen van een VNet met een regio of affiniteits groep.

## <a name="manage-reserved-vips"></a>Gereserveerde Vip's beheren

### <a name="using-azure-powershell-classic"></a>Azure PowerShell gebruiken (klassiek)

Voordat u gereserveerde Ip's kunt gebruiken, moet u deze toevoegen aan uw abonnement. Maak een gereserveerd IP-adres uit de groep open bare IP-adressen die beschikbaar zijn op de locatie van de *centrale VS* als volgt:

> [!NOTE]
> Voor het klassieke implementatie model moet u de Service Management-versie van Azure PowerShell installeren. Zie [Installing the Azure PowerShell Service Management module](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0) (De Azure PowerShell Service Management-module installeren) voor meer informatie. 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
U kunt echter niet opgeven welk IP-adres wordt gereserveerd. Als u wilt weer geven welke IP-adressen zijn gereserveerd in uw abonnement, voert u de volgende Power shell-opdracht uit en ziet u de waarden voor *ReservedIPName* en *Address*:

```powershell
Get-AzureReservedIP
```

Verwachte uitvoer:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Wanneer u een gereserveerd IP-adres met Power Shell maakt, kunt u geen resource groep opgeven om het gereserveerde IP in te maken. Azure plaatst deze in een resource groep met de naam *standaard-netwerken* automatisch. Als u het gereserveerde IP-adres maakt met behulp van de [Azure Portal](https://portal.azure.com), kunt u een resource groep opgeven die u kiest. Als u echter het gereserveerde IP-adres in een andere resource groep dan *standaard netwerken* maakt, moet u, wanneer u verwijst naar het gereserveerde ip-`Remove-AzureReservedIP``Get-AzureReservedIP` adres, *groeps naam gereserveerde IP-naam*, verwijzen naar de naam van de resource groep.  Als u bijvoorbeeld een gereserveerd IP-adres met de naam *myReservedIP* in een resource groep met de naam *myResourceGroup*maakt, moet u verwijzen naar de naam van het gereserveerde IP-adres als *groep myResourceGroup myReservedIP*.   


Zodra een IP-adres is gereserveerd, blijft dit gekoppeld aan uw abonnement totdat u het verwijdert. Verwijder een gereserveerd IP-adres als volgt:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Azure CLI gebruiken (klassiek)
Maak een gereserveerd IP-adres uit de groep open bare IP-adressen die beschikbaar zijn op de centrale locatie van de *VS* , zoals het gebruik van klassieke Azure cli:

> [!NOTE]
> Voor een klassieke implementatie moet u Azure Classic CLI gebruiken. Zie [de klassieke Azure-cli installeren](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest) voor meer informatie over het installeren van de klassieke versie van Azure.
  
 Opdracht:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Voorbeeld:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

U kunt als volgt zien welke IP-adressen zijn gereserveerd in uw abonnement met behulp van Azure CLI: 

Opdracht:
```azurecli
azure network reserved-ip list
```
Zodra een IP-adres is gereserveerd, blijft dit gekoppeld aan uw abonnement totdat u het verwijdert. Verwijder een gereserveerd IP-adres als volgt:

Opdracht:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Voorbeeld:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Het IP-adres van een bestaande Cloud service reserveren
U kunt het IP-adres van een bestaande Cloud service reserveren door de para meter `-ServiceName` toe te voegen. Reserveer het IP-adres van een Cloud service- *TestService* in de locatie van de *centrale VS* als volgt:

- Azure PowerShell (klassiek) gebruiken:

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Azure CLI gebruiken (klassiek):
  
    Opdracht:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Voorbeeld:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Een gereserveerd IP-adres koppelen aan een nieuwe Cloud service
Met het volgende script wordt een nieuw gereserveerd IP-adres gemaakt en vervolgens gekoppeld aan een nieuwe Cloud service met de naam *TestService*.

### <a name="using-azure-powershell-classic"></a>Azure PowerShell gebruiken (klassiek)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Wanneer u een gereserveerd IP-adres maakt voor gebruik met een Cloud service, raadpleegt u nog steeds de virtuele machine met behulp van *VIP:&lt;poort nummer >* voor inkomende communicatie. Het reserveren van een IP betekent niet dat u rechtstreeks verbinding kunt maken met de virtuele machine. Het gereserveerde IP-adres wordt toegewezen aan de Cloud service waarop de virtuele machine is geïmplementeerd. Als u rechtstreeks verbinding wilt maken met een virtuele machine met een IP-adres, moet u een openbaar IP-adres op exemplaar niveau configureren. Een openbaar IP-adres op exemplaar niveau is een type openbaar IP-adres (een ILPIP) dat rechtstreeks aan uw virtuele machine wordt toegewezen. Deze kan niet worden gereserveerd. Lees het artikel [openbaar IP op exemplaarniveau (ILPIP)](virtual-networks-instance-level-public-ip.md) voor meer informatie.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Een gereserveerd IP-adres verwijderen uit een actieve implementatie

Verwijder een gereserveerd IP-adres dat is toegevoegd aan een nieuwe Cloud service als volgt: 
### <a name="using-azure-powershell-classic"></a>Azure PowerShell gebruiken (klassiek)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Azure CLI gebruiken (klassiek)
Opdracht:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Voorbeeld:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Als u een gereserveerd IP-adres uit een actieve implementatie verwijdert, wordt de reserve ring niet uit uw abonnement verwijderd. U hoeft alleen het IP-adres vrij te maken dat door een andere resource in uw abonnement wordt gebruikt.
> 

Als u een gereserveerd IP-adres volledig uit een abonnement wilt verwijderen, voert u de volgende opdracht uit:

Opdracht:

```azurecli
azure network reserved-ip delete <name>
```
Voorbeeld:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Een gereserveerd IP-adres aan een actieve implementatie koppelen

### <a name="using-azure-powershell-classic"></a>Azure PowerShell gebruiken (klassiek)

Met de volgende opdrachten maakt u een Cloud service met de naam *TestService2* met een nieuwe virtuele machine met de naam *TestVM2*. Het bestaande gereserveerde IP-adres met de naam *MyReservedIP* wordt vervolgens gekoppeld aan de Cloud service.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Azure CLI gebruiken (klassiek)
U kunt als volgt een nieuw gereserveerd IP-adres koppelen aan uw actieve Cloud service-implementatie met behulp van Azure CLI:

Opdracht:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Voorbeeld:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Een gereserveerd IP-adres koppelen aan een Cloud service met behulp van een service configuratie bestand
U kunt ook een gereserveerd IP-adres aan een Cloud Service koppelen met behulp van een service configuratie (CSCFG)-bestand. Het volgende XML-voor beeld laat zien hoe u een Cloud service kunt configureren voor het gebruik van een gereserveerde VIP met de naam *MyReservedIP*:
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>Volgende stappen
* Begrijp hoe [IP-adres Sering](virtual-network-ip-addresses-overview-classic.md) werkt in het klassieke implementatie model.
* Meer informatie over [gereserveerde privé-IP-adressen](virtual-networks-reserved-private-ip.md).
* Meer informatie over [open bare IP-adressen op exemplaar niveau (ILPIP)](virtual-networks-instance-level-public-ip.md).

