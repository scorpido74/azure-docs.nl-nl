---
title: Gegevensexfiltratie beperken tot Azure Storage - Azure PowerShell
description: In dit artikel leert u hoe u de exfiltratie van virtuele netwerkgegevens beperken en beperken tot Azure Storage-bronnen met eindpuntbeleid voor virtuele netwerkservices met Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78253024"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Gegevensexfiltratie naar Azure Storage-accounts beheren met eindpuntbeleid voor virtuele netwerkservices met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met eindpuntbeleidsregels voor virtuele netwerkservices u toegangsbeheer toepassen op Azure Storage-accounts vanuit een virtueel netwerk via serviceeindpunten. Dit is een sleutel tot het beveiligen van uw workloads, het beheren van welke opslagaccounts zijn toegestaan en waar gegevensexfiltratie is toegestaan.
In dit artikel leert u het volgende:

* Maak een virtueel netwerk.
* Voeg een subnet toe en schakel serviceeindpunt in voor Azure Storage.
* Maak twee Azure Storage-accounts en geef netwerktoegang toe aan het subnet dat hierboven is gemaakt.
* Maak een eindpuntbeleid voor service om alleen toegang te geven tot een van de opslagaccounts.
* Implementeer een virtuele machine (VM) op het subnet.
* Bevestig de toegang tot het toegestane opslagaccount vanaf het subnet.
* Bevestig dat de toegang tot het niet-toegestane opslagaccount vanaf het subnet wordt geweigerd.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Voordat u een virtueel netwerk maakt, moet u een brongroep maken voor het virtuele netwerk en alle andere bronnen die in dit artikel zijn gemaakt. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup geopperd:* 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVirtualNetwork* met het adresvoorvoegsel *10.0.0.0/16 .*

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen

Maak een subnet in het virtuele netwerk. In dit voorbeeld wordt een subnet met de naam *Privé* gemaakt met een serviceeindpunt voor *Microsoft.Storage:* 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Netwerktoegang voor het subnet beperken

Maak beveiligingsregels voor netwerkbeveiligingsgroepen met [New-AzNetworkSecurityRuleConfig.](/powershell/module/az.network/new-aznetworksecurityruleconfig) Met de volgende regel u uitgaande toegang tot de openbare IP-adressen die zijn toegewezen aan de Azure Storage-service: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

De volgende regel weigert toegang tot alle openbare IP-adressen. De vorige regel overschrijft deze regel vanwege de hogere prioriteit, die toegang geeft tot de openbare IP-adressen van Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Met de volgende regel extern bureaublad-protocol (RDP) verkeer binnenkomen naar het subnet vanaf elke locatie. Verbindingen met extern bureaublad zijn toegestaan op het subnet, zodat u de toegang van het netwerk tot een bron in een latere stap bevestigen.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNsgPrivate geopperd.*

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Koppel de netwerkbeveiligingsgroep aan het *subnet Privé* aan [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) en schrijf vervolgens de subnetconfiguratie naar het virtuele netwerk. In het volgende voorbeeld wordt de *myNsgPrivate-netwerkbeveiligingsgroep* aan het *subnet Privé* koppelt:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Netwerktoegang tot Azure Storage-accounts beperken

De stappen die nodig zijn om netwerktoegang te beperken tot resources die zijn gemaakt met Azure-services waarvoor service-eindpunten zijn ingeschakeld, verschillen per service. Zie de documentatie voor afzonderlijke services voor specifieke stappen voor elke service. De rest van dit artikel bevat stappen om netwerktoegang voor een Azure Storage-account te beperken, als voorbeeld.

### <a name="create-two-storage-accounts"></a>Twee opslagaccounts maken

Maak een Azure-opslagaccount met [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Nadat het opslagaccount is gemaakt, haalt u de sleutel voor het opslagaccount op in een variabele met [Get-AzStorageAccountKey:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

De sleutel wordt gebruikt om een bestandsshare in een latere stap te maken. Voer `$storageAcctKey` de waarde in en noteer deze, omdat u deze ook handmatig in een latere stap moet invoeren wanneer u de bestandsshare in een station in een vm in kaart brengt.

Herhaal nu de bovenstaande stappen om een tweede opslagaccount te maken.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Haal ook de opslagaccountsleutel op van dit account voor het later gebruiken om een bestandsshare te maken.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Een bestandsshare maken in elk opslagaccount

Maak een context voor uw opslagaccount en sleutel met [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). De context bevat de naam en accountsleutel van het opslagaccount:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Maak een bestandsshare met [New-AzStorageShare:](/powershell/module/az.storage/new-azstorageshare)

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Alle netwerktoegang tot een opslagaccount weigeren

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk. Als u de toegang tot geselecteerde netwerken wilt beperken, wijzigt u de standaardactie in *Weigeren* met [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Nadat de netwerktoegang is geweigerd, is het opslagaccount niet via elk netwerk toegankelijk.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Alleen netwerktoegang vanaf het VNet-subnet inschakelen

Haal het gemaakte virtuele netwerk op met [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) en haal het privé subnetobject op in een variabele met [Get-AzVirtualNetworkSubnetConfig:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Geef netwerktoegang tot het opslagaccount vanaf het *subnet Privé* met [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Beleid toepassen om toegang te verlenen tot een geldig opslagaccount

Als u ervoor wilt zorgen dat de gebruikers in het virtuele netwerk alleen toegang hebben tot de Azure Storage-accounts die veilig en toegestaan zijn, u een eindpuntbeleid voor service maken met de lijst met toegestane opslagaccounts in de definitie. Dit beleid wordt vervolgens toegepast op het virtuele netwerksubnet dat via serviceeindpunten is verbonden met opslag.

### <a name="create-a-service-endpoint-policy"></a>Een eindpuntbeleid voor service maken

In deze sectie wordt de beleidsdefinitie gemaakt met de lijst met toegestane bronnen voor toegang via serviceeindpunt

De bron-id voor het eerste (toegestane) opslagaccount ophalen 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

De beleidsdefinitie maken om de bovenstaande resource toe te staan

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Het eindpuntbeleid van de service maken met de hierboven gemaakte beleidsdefinitie

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Het eindpuntbeleid van de service koppelen aan het subnet van het virtuele netwerk

After creating the service endpoint policy, you'll associate it with the target subnet with the service endpoint configuration for Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Toegangsbeperking voor Azure Storage-accounts valideren

### <a name="deploy-the-virtual-machine"></a>De virtuele machine implementeren

Als u netwerktoegang tot een opslagaccount wilt testen, implementeert u een VM in het subnet.

Maak een virtuele machine in het *subnet Privé* met [Nieuw-AzVM](/powershell/module/az.compute/new-azvm). Wanneer de volgende opdracht wordt uitgevoerd, wordt u gevraagd referenties op te geven. De waarden die u invoert, worden geconfigureerd als de gebruikersnaam en het wachtwoord voor de virtuele machine. Met de optie `-AsJob` wordt de virtuele machine op de achtergrond gemaakt, zodat u kunt doorgaan met de volgende stap.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Uitvoer die vergelijkbaar is met de volgende voorbeelduitvoer wordt geretourneerd:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Toegang tot het *toegestane* opslagaccount bevestigen

Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van een virtuele machine op te halen. In het volgende voorbeeld wordt het openbare IP-adres van de *myVmPrivate-vm* geretourneerd:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Vervang `<publicIpAddress>` in de volgende opdracht door het openbare IP-adres dat met de vorige opdracht is geretourneerd en voer vervolgens de volgende opdracht in:

```powershell
mstsc /v:<publicIpAddress>
```

Er wordt een Remote Desktop Protocol-bestand (.rdp) gemaakt en gedownload naar uw computer. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM. Selecteer **OK**. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.

Breng op de *myVmPrivate* VM het Azure-bestandsaandeel van toegestaan opslagaccount in kaart om Z te stimuleren met PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell retourneert uitvoer die er ongeveer zo uitziet als in dit voorbeeld:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

De Azure-bestandsshare is toegewezen aan station Z.

Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Bevestig dat de toegang tot *een niet-toegestaan* opslagaccount wordt geweigerd

Probeer op dezelfde *myVmPrivate-vm* de Azure-bestandsshare in kaart te brengen om X te rijden. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Toegang tot het aandeel wordt geweigerd `New-PSDrive : Access is denied` en u ontvangt een foutmelding. Toegang wordt geweigerd omdat het *niet-toegestane account* voor opslagaccount niet in de lijst met toegestane bronnen in het eindpuntbeleid van de service staat. 

Sluit de externe bureaubladsessie naar de VM *myVmPublic*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de brongroep en alle bronnen die deze bevat te verwijderen:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een eindpuntbeleid voor services toegepast op een eindpunt van de Azure-virtuele netwerkservice op Azure Storage. U hebt Azure Storage-accounts en beperkte netwerktoegang tot alleen bepaalde opslagaccounts (en dus anderen geweigerd) gemaakt vanuit een virtueel netwerksubnet. Zie Overzicht van [eindpunten voor servicevoor](virtual-network-service-endpoint-policies-overview.md)meer informatie over eindpuntbeleid voor services .
