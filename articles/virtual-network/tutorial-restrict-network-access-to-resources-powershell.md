---
title: Netwerktoegang beperken tot PaaS-bronnen - Azure PowerShell
description: In dit artikel leert u hoe u de netwerktoegang tot Azure-bronnen, zoals Azure Storage en Azure SQL Database, beperkt en beperkt met eindpunten voor virtuele netwerkservices met Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1d0cf65bb39dbda2b7451c50629ff8949c5507cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185541"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Netwerktoegang tot PaaS-bronnen beperken met eindpunten voor virtuele netwerkservice met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met service-eindpunten voor virtuele netwerken kunt u de netwerktoegang tot sommige Azure-servicebronnen beperken tot een subnet van een virtueel netwerk. U kunt ook internettoegang tot de resources verwijderen. Service-eindpunten zorgen voor een rechtstreekse verbinding van uw virtuele netwerk met ondersteunde Azure-services, zodat u de privéadresruimte van uw virtuele netwerk kunt gebruiken voor toegang tot de Azure-services. Verkeer dat bestemd is voor Azure-resources via de service-eindpunten blijft altijd op het Microsoft Azure-backbone-netwerk. In dit artikel leert u het volgende:

* Een virtueel netwerk maken met één subnet
* Een subnet toevoegen en een service-eindpunt inschakelen
* Een Azure-resource maken en alleen toegang ertoe toestaan vanaf een subnet
* Een virtuele machine (VM) implementeren op elk subnet
* Toegang tot een resource vanaf een subnet bevestigen
* Bevestigen dat toegang wordt geweigerd aan een resource vanaf een subnet en internet

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Voordat u een virtueel netwerk maakt, moet u een brongroep maken voor het virtuele netwerk en alle andere bronnen die in dit artikel zijn gemaakt. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup geopperd:* 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVirtualNetwork* met het adresvoorvoegsel *10.0.0.0/16 .*

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Maak een subnetconfiguratie met [Nieuw-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). In het volgende voorbeeld wordt een subnetconfiguratie gemaakt voor een subnet met de naam *Openbaar:*

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Maak het subnet in het virtuele netwerk door de subnetconfiguratie naar het virtuele netwerk te schrijven met [Set-AzVirtualNetwork:](/powershell/module/az.network/Set-azVirtualNetwork)

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen

U serviceeindpunten alleen inschakelen voor services die eindpunten van services ondersteunen. Serviceendpoint-services weergeven die beschikbaar zijn op een [Azure-locatie met Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice). In het volgende voorbeeld wordt een lijst geretourneerd met services die beschikbaar zijn in de *eastusregio.* De lijst met geretourneerde services wordt na verloop van tijd groter naarmate meer Azure-services serviceeindpunt worden ingeschakeld.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Maak een extra subnet in het virtuele netwerk. In dit voorbeeld wordt een subnet met de naam *Privé* gemaakt met een serviceeindpunt voor *Microsoft.Storage:* 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Netwerktoegang voor een subnet beperken

Maak beveiligingsregels voor netwerkbeveiligingsgroepen met [New-AzNetworkSecurityRuleConfig.](/powershell/module/az.network/new-aznetworksecurityruleconfig) Met de volgende regel u uitgaande toegang tot de openbare IP-adressen die zijn toegewezen aan de Azure Storage-service: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
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
  -Priority 110 `
  -Protocol * `
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
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Netwerktoegang tot een resource beperken

De stappen die nodig zijn om netwerktoegang te beperken tot resources die zijn gemaakt met Azure-services waarvoor service-eindpunten zijn ingeschakeld, verschillen per service. Zie de documentatie voor afzonderlijke services voor specifieke stappen voor elke service. De rest van dit artikel bevat stappen om netwerktoegang voor een Azure Storage-account te beperken, als voorbeeld.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Maak een Azure-opslagaccount met [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Vervang `<replace-with-your-unique-storage-account-name>` door een naam die uniek is voor alle Azure-locaties, tussen 3-24 tekens in lengte, met alleen cijfers en kleine letters.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Nadat het opslagaccount is gemaakt, haalt u de sleutel voor het opslagaccount op in een variabele met [Get-AzStorageAccountKey:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

De sleutel wordt gebruikt om een bestandsshare in een latere stap te maken. Voer `$storageAcctKey` de waarde in en noteer deze, omdat u deze ook handmatig in een latere stap moet invoeren wanneer u de bestandsshare in een station in een vm in kaart brengt.

### <a name="create-a-file-share-in-the-storage-account"></a>Een bestandsshare maken in het opslagaccount

Maak een context voor uw opslagaccount en sleutel met [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). De context bevat de naam en accountsleutel van het opslagaccount:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Maak een bestandsshare met [New-AzStorageShare:](/powershell/module/az.storage/new-azstorageshare)

$share = Nieuw-AzStorageShare mijn-bestand-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Alle netwerktoegang tot een opslagaccount weigeren

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk. Als u de toegang tot geselecteerde netwerken wilt beperken, wijzigt u de standaardactie in *Weigeren* met [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Nadat de netwerktoegang is geweigerd, is het opslagaccount niet via elk netwerk toegankelijk.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Netwerktoegang vanuit een subnet inschakelen

Haal het gemaakte virtuele netwerk op met [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) en haal het privé subnetobject op in een variabele met [Get-AzVirtualNetworkSubnetConfig:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Geef netwerktoegang tot het opslagaccount vanaf het *subnet Privé* met [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Virtuele machines maken

Implementeer een VM in elk subnet om de netwerktoegang tot een opslagaccount te testen.

### <a name="create-the-first-virtual-machine"></a>De eerste virtuele machine maken

Maak een virtuele machine in het *openbare* subnet met [Nieuw-AzVM](/powershell/module/az.compute/new-azvm). Wanneer de volgende opdracht wordt uitgevoerd, wordt u gevraagd referenties op te geven. De waarden die u invoert, worden geconfigureerd als de gebruikersnaam en het wachtwoord voor de virtuele machine. Met de optie `-AsJob` wordt de virtuele machine op de achtergrond gemaakt, zodat u kunt doorgaan met de volgende stap.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Uitvoer die vergelijkbaar is met de volgende voorbeelduitvoer wordt geretourneerd:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>De tweede virtuele machine maken

Maak een virtuele machine in het *subnet Privé:*

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Het duurt een paar minuten voordat Azure de VM maakt. Ga niet door naar de volgende stap totdat Azure klaar is met het maken van de VM en de uitvoer terugkeert naar PowerShell.

## <a name="confirm-access-to-storage-account"></a>Toegang tot opslagaccount bevestigen

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

Wijs op de VM *myVmPrivate* de Azure-bestandsshare toe aan station Z met behulp van PowerShell. Voordat u de opdrachten uitvoert `<storage-account-key>` `<storage-account-name>` die volgen, worden vervangen en met waarden van u die zijn opgegeven of opgehaald in [Een opslagaccount maken.](#create-a-storage-account)

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell retourneert uitvoer die er ongeveer zo uitziet als in dit voorbeeld:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

De Azure-bestandsshare is toegewezen aan station Z.

Controleer of de VM geen uitgaande connectiviteit heeft met andere openbare IP-adressen:

```powershell
ping bing.com
```

U krijgt geen antwoorden, omdat de netwerkbeveiligingsgroep die is gekoppeld aan het *Privé*-subnet geen uitgaande toegang toestaat aan andere openbare IP-adressen dan de adressen die zijn toegewezen aan de Azure Storage-service.

Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bevestigen dat toegang tot opslagaccount wordt geweigerd

Haal het openbare IP-adres van de *myVmPublic* VM:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Vervang `<publicIpAddress>` in de volgende opdracht door het openbare IP-adres dat met de vorige opdracht is geretourneerd en voer vervolgens de volgende opdracht in: 

```powershell
mstsc /v:<publicIpAddress>
```

Probeer op de *myVmPublic-vm* de Azure-bestandsshare in kaart te brengen om Z te rijden. Voordat u de opdrachten uitvoert `<storage-account-key>` `<storage-account-name>` die volgen, worden vervangen en met waarden van u die zijn opgegeven of opgehaald in [Een opslagaccount maken.](#create-a-storage-account)

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Toegang tot het aandeel wordt geweigerd `New-PSDrive : Access is denied` en u ontvangt een foutmelding. De toegang wordt geweigerd omdat de VM *myVmPublic* is geïmplementeerd in het *Openbare* subnet. Het *Openbare* subnet heeft geen service-eindpunt ingeschakeld voor Azure Storage en het opslagaccount staat alleen netwerktoegang toe van het *Privé*-subnet, en niet van het *Openbare*subnet.

Sluit de externe bureaubladsessie naar de VM *myVmPublic*.

Probeer vanaf uw computer de bestandsshares in het opslagaccount te bekijken met de volgende opdracht:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Toegang wordt geweigerd en u ontvangt een *Get-AzStorageFile: De externe server heeft een foutmelding geretourneerd: (403) Verboden. HTTP-statuscode: 403 - HTTP-foutbericht: deze aanvraag is niet geautoriseerd om deze bewerkingsfout uit te voeren,* omdat uw computer zich niet in het *subnet Privé* van het virtuele *myvirtualnetwerk* bevindt.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de brongroep en alle bronnen die deze bevat te verwijderen:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een serviceeindpunt ingeschakeld voor een virtueel netwerksubnet. U hebt geleerd dat service-eindpunten kunnen worden ingeschakeld voor met meerdere Azure-services geïmplementeerde resources. U hebt een Azure Storage-account gemaakt en netwerktoegang tot het opslagaccount beperkt tot alleen resources binnen een subnet van een virtueel netwerk. Zie voor meer informatie over service-eindpunten [Overzicht voor service-eindpunten](virtual-network-service-endpoints-overview.md) en [Subnetten beheren](virtual-network-manage-subnet.md).

Als u meerdere virtuele netwerken in uw account hebt, kunt u twee virtuele netwerken met elkaar verbinden zodat de resources in beide virtuele netwerken met elkaar kunnen communiceren. Zie [Virtuele netwerken verbinden](tutorial-connect-virtual-networks-powershell.md)voor meer informatie.
