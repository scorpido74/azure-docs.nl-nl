---
title: Azure Files netwerk eindpunten configureren | Microsoft Docs
description: Een overzicht van de netwerk opties voor Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cc487e8def180735606aa010651dde40ef93908e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082505"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure Files netwerk eindpunten configureren
Azure Files biedt twee hoofd typen eind punten voor toegang tot Azure-bestands shares: 
- Open bare eind punten, die een openbaar IP-adres hebben en toegankelijk zijn vanaf elke locatie in de wereld.
- Privé-eind punten, die zich binnen een virtueel netwerk bevinden en een persoonlijk IP-adres hebben in de adres ruimte van het virtuele netwerk.

Open bare en persoonlijke eind punten bestaan in het Azure-opslag account. Een opslag account is een beheer constructie die een gedeelde opslag groep vertegenwoordigt, waarbij u meerdere bestands shares en andere opslag resources, zoals BLOB-containers of wacht rijen, kunt implementeren.

Dit artikel richt zich op het configureren van de eind punten van een opslag account voor het rechtstreeks openen van de Azure-bestands share. De meeste details die in dit document worden gegeven, zijn ook van toepassing op de manier waarop Azure File Sync samenwerkt met open bare en privé-eind punten voor het opslag account, maar Zie [Azure file sync proxy-en Firewall instellingen configureren](storage-sync-files-firewall-and-proxy.md)voor meer informatie over netwerk overwegingen voor een Azure file sync-implementatie.

U wordt aangeraden [Azure files netwerk overwegingen](storage-files-networking-overview.md) te lezen voordat u deze hand leiding leest.

## <a name="prerequisites"></a>Vereisten
- In dit artikel wordt ervan uitgegaan dat u al een Azure-abonnement hebt gemaakt. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- In dit artikel wordt ervan uitgegaan dat u al een Azure-bestands share hebt gemaakt in een opslag account waarmee u vanuit on-premises verbinding wilt maken. Zie [een Azure-bestands share maken](storage-how-to-create-file-share.md)voor meer informatie over het maken van een Azure-bestands share.
- Als u Azure PowerShell wilt gebruiken, [installeert u de nieuwste versie](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Als u de Azure CLI wilt gebruiken, [installeert u de nieuwste versie](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
Als u een persoonlijk eind punt voor uw opslag account maakt, worden de volgende Azure-resources geïmplementeerd:

- **Een persoonlijk eind punt**: een Azure-resource die het persoonlijke eind punt van het opslag account vertegenwoordigt. U kunt dit beschouwen als een resource die een opslag account en een netwerk interface verbindt.
- **Een netwerk interface (NIC)**: de netwerk interface die een privé-IP-adres in het opgegeven virtuele netwerk/subnet beheert. Dit is precies dezelfde resource die tijdens het implementeren van een virtuele machine wordt geïmplementeerd, maar niet wordt toegewezen aan een VM, is het eigendom van het persoonlijke eind punt.
- **Een persoonlijke DNS-zone**: als u nog nooit een persoonlijk eind punt voor dit virtuele netwerk hebt geïmplementeerd, wordt er een nieuwe privé-DNS-zone voor uw virtuele netwerk geïmplementeerd. Er wordt ook een DNS A-record gemaakt voor het opslag account in deze DNS-zone. Als u al een persoonlijk eind punt in dit virtuele netwerk hebt geïmplementeerd, wordt er een nieuwe A-record voor het opslag account toegevoegd aan de bestaande DNS-zone. Het implementeren van een DNS-zone is optioneel, maar wordt wel nadrukkelijk aanbevolen en vereist als u uw Azure-bestands shares koppelt aan een AD-Service-Principal of de FileREST-API gebruikt.

> [!Note]  
> In dit artikel wordt het DNS-achtervoegsel van het opslag account gebruikt voor `core.windows.net`de open bare Azure-regio's. Dit commentaar is ook van toepassing op Azure soevereine Clouds, zoals de Azure-Cloud voor de Amerikaanse overheid en de cloud van Azure China. Vervang gewoon de juiste achtervoegsels voor uw omgeving. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigeer naar het opslag account waarvoor u een persoonlijk eind punt wilt maken. Selecteer in de inhouds opgave voor het opslag account **particuliere eindpunt verbindingen**en vervolgens **+ privé-eind punt** om een nieuw persoonlijk eind punt te maken. 

![Een scherm opname van het item privé-eindpunt verbindingen in de inhouds opgave van het opslag account](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

De resulterende wizard heeft meerdere pagina's om te volt ooien.

Selecteer op de Blade **basis beginselen** de gewenste resource groep, naam en regio voor uw persoonlijke eind punt. Deze kunnen wille keurig zijn, maar moeten niet overeenkomen met het opslag account, hoewel u het persoonlijke eind punt moet maken in dezelfde regio als het virtuele netwerk waarin u het persoonlijke eind punt wilt maken.

![Een scherm afbeelding van de sectie basis beginselen van de sectie persoonlijk eind punt maken](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

Selecteer op de Blade **resource** het keuze rondje om **verbinding te maken met een Azure-resource in mijn Directory**. Onder **resource type**selecteert u **micro soft. Storage/Storage accounts** voor het bron type. Het veld **resource** is het opslag account met de Azure-bestands share waarmee u verbinding wilt maken. Doel-subresource is een **bestand**, omdat dit voor Azure files is.

Op de Blade **configuratie** kunt u het specifieke virtuele netwerk en subnet selecteren waaraan u uw persoonlijke eind punt wilt toevoegen. Selecteer het virtuele netwerk dat u hierboven hebt gemaakt. U moet een uniek subnet selecteren in het subnet waaraan u het service-eind punt hebt toegevoegd. De Blade configuratie bevat ook de informatie voor het maken of bijwerken van de privé-DNS-zone. U kunt het beste de `privatelink.file.core.windows.net` standaard zone gebruiken.

![Een scherm opname van de configuratie sectie](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Klik op **beoordeling + maken** om het persoonlijke eind punt te maken. 

Als u een virtuele machine in uw virtuele netwerk hebt, of als u DNS-door sturing hebt geconfigureerd zoals [hier](storage-files-networking-dns.md)wordt beschreven, kunt u testen of uw persoonlijke eind punt correct is ingesteld door de volgende opdrachten uit te voeren vanuit Power shell, de opdracht regel of de Terminal (werkt voor Windows, Linux of macOS). U moet de `<storage-account-name>` juiste naam voor het opslag account vervangen:

```
nslookup <storage-account-name>.file.core.windows.net
```

Als alles goed werkte, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres van het privé-eind punt in uw virtuele netwerk is (uitvoer weer gegeven voor Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u een persoonlijk eind punt voor uw opslag account wilt maken, moet u eerst een verwijzing naar uw opslag account en het subnet van het virtuele netwerk ophalen waaraan u het persoonlijke eind punt wilt toevoegen. Replace `<storage-account-resource-group-name>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>`en `<vnet-subnet-name>` lager:

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group-name>"
$storageAccountName = "<storage-account-name>"
$virtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$virtualNetworkName = "<vnet-name>"
$subnetName = "<vnet-subnet-name>"

# Get storage account reference, and throw error if it doesn't exist
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageAccount) {
    $errorMessage = "Storage account $storageAccountName not found "
    $errorMessage += "in resource group $storageAccountResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get virtual network reference, and throw error if it doesn't exist
$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $virtualNetworkName `
        -ErrorAction SilentlyContinue

if ($null -eq $virtualNetwork) {
    $errorMessage = "Virtual network $virtualNetworkName not found "
    $errorMessage += "in resource group $virtualNetworkResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get reference to virtual network subnet, and throw error if it doesn't exist
$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $virtualNetworkName." `
            -ErrorAction Stop
}
```

Als u een persoonlijk eind punt wilt maken, moet u een verbinding maken met het opslag account. De verbinding met de service voor persoonlijke koppelingen is een invoer voor het maken van het persoonlijke eind punt. 

```PowerShell
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork | Out-Null

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageAccountName-Connection" `
        -PrivateLinkServiceId $storageAccount.Id `
        -GroupId "file"

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name "$storageAccountName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Het maken van een privé-DNS-zone van Azure maakt de oorspronkelijke naam van het `storageaccount.file.core.windows.net` opslag account mogelijk, zoals het oplossen van het privé-IP-adres in het virtuele netwerk. Hoewel optioneel vanuit het perspectief van het maken van een persoonlijk eind punt, is het expliciet vereist voor het koppelen van de Azure-bestands share met behulp van een AD-gebruikers principal of om toegang te krijgen via de REST API.  

```PowerShell
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$storageAccountSuffix = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
$dnsZoneName = "privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```

Nu u een verwijzing naar de privé-DNS-zone hebt, moet u een A-record maken voor uw opslag account.

```PowerShell
$privateEndpointIP = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object @{ 
        Name = "NetworkInterfaces"; 
        Expression = { Get-AzNetworkInterface -ResourceId $_.Id } 
    } | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateIpAddress

$privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $privateEndpointIP

New-AzPrivateDnsRecordSet `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $storageAccountName `
        -RecordType A `
        -ZoneName $dnsZoneName `
        -Ttl 600 `
        -PrivateDnsRecords $privateDnsRecordConfig `
        -ErrorAction Stop | `
    Out-Null
```

Als u een virtuele machine in uw virtuele netwerk hebt, of als u DNS-door sturing hebt geconfigureerd zoals [hier](storage-files-networking-dns.md)wordt beschreven, kunt u testen of uw persoonlijke eind punt correct is ingesteld met de volgende opdrachten:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.File) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Als alles goed werkte, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres van het privé-eind punt in uw virtuele netwerk is:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u een persoonlijk eind punt voor uw opslag account wilt maken, moet u eerst een verwijzing naar uw opslag account en het subnet van het virtuele netwerk ophalen waaraan u het persoonlijke eind punt wilt toevoegen. Replace `<storage-account-resource-group-name>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>`en `<vnet-subnet-name>` lager:

```bash
storageAccountResourceGroupName="<storage-account-resource-group-name>"
storageAccountName="<storage-account-name>"
virtualNetworkResourceGroupName="<vnet-resource-group-name>"
virtualNetworkName="<vnet-name>"
subnetName="<vnet-subnet-name>"

# Get storage account ID 
storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

# Get virtual network ID
virtualNetwork=$(az network vnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --name $virtualNetworkName \
        --query "id" | \
    tr -d '"')

# Get subnet ID
subnet=$(az network vnet subnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --vnet-name $virtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Als u een persoonlijk eind punt wilt maken, moet u eerst controleren of het beleid voor het particuliere eindpunt netwerk van het subnet is ingesteld op uitgeschakeld. Vervolgens kunt u met de `az network private-endpoint create` opdracht een persoonlijk eind punt maken

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $virtualNetworkResourceGroupName \
        --name "$storageAccountName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageAccount \
        --group-ids "file" \
        --connection-name "$storageAccountName-Connection" \
        --query "id" | \
    tr -d '"')
```

Het maken van een privé-DNS-zone van Azure maakt de oorspronkelijke naam van het `storageaccount.file.core.windows.net` opslag account mogelijk, zoals het oplossen van het privé-IP-adres in het virtuele netwerk. Hoewel optioneel vanuit het perspectief van het maken van een persoonlijk eind punt, is het expliciet vereist voor het koppelen van de Azure-bestands share met behulp van een AD-gebruikers principal of om toegang te krijgen via de REST API.  

```bash
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
storageAccountSuffix=$(az cloud show \
        --query "suffixes.storageEndpoint" | \
    tr -d '"')

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
dnsZoneName="privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        1 > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $resourceGroupName \
            --zone-name $zoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
fi
```

Nu u een verwijzing naar de privé-DNS-zone hebt, moet u een A-record maken voor uw opslag account.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateEndpointIP=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[0].privateIpAddress" | \
    tr -d '"')

az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name $storageAccountName \
        --output none

az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name $storageAccountName \
        --ipv4-address $privateEndpointIP \
        --output none
```

Als u een virtuele machine in uw virtuele netwerk hebt, of als u DNS-door sturing hebt geconfigureerd zoals [hier](storage-files-networking-dns.md)wordt beschreven, kunt u testen of uw persoonlijke eind punt correct is ingesteld met de volgende opdrachten:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.File" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Als alles goed werkte, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres van het privé-eind punt in uw virtuele netwerk is:

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>De toegang tot het open bare eind punt beperken
U kunt de toegang tot het open bare eind punt beperken met de firewall instellingen van het opslag account. Over het algemeen beperkt de meeste firewall-beleids regels voor een opslag account netwerk toegang tot een of meer virtuele netwerken. Er zijn twee manieren om de toegang tot een opslag account te beperken tot een virtueel netwerk:

- [Maak een of meer persoonlijke eind punten voor het opslag account](#create-a-private-endpoint) en beperk de toegang tot het open bare eind punt. Dit zorgt ervoor dat alleen verkeer dat afkomstig is van binnen de gewenste virtuele netwerken toegang heeft tot de Azure-bestands shares in het opslag account.
- Beperk het open bare eind punt tot een of meer virtuele netwerken. Dit werkt met behulp van een mogelijkheid van het virtuele netwerk met de naam *service-eind punten*. Wanneer u het verkeer beperkt tot een opslag account via een service-eind punt, hebt u nog steeds toegang tot het opslag account via het open bare IP-adres.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Toegang tot het open bare eind punt beperken
Als alle toegang tot het open bare eind punt is beperkt, is het opslag account nog steeds toegankelijk via het persoonlijke eind punt. Anders worden geldige aanvragen voor het open bare eind punt van het opslag account geweigerd. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigeer naar het opslag account waarvoor u alle toegang tot het open bare eind punt wilt beperken. Selecteer **firewalls en virtuele netwerken**in de inhouds opgave van het opslag account.

Selecteer boven aan de pagina het keuze rondje **geselecteerde netwerken** . Hiermee wordt het verbergen van een aantal instellingen voor het beheren van de beperking van het open bare eind punt ongedaan gemaakt. Schakel **vertrouwde micro soft-Services toegang geven tot dit service account** in om vertrouwde micro soft-Services, zoals Azure file sync, toe te staan om toegang te krijgen tot het opslag account.

![Scherm afbeelding van de Blade firewalls en virtuele netwerken met de juiste beperkende beperkingen](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Met de volgende Power shell-opdracht wordt al het verkeer naar het open bare eind punt van het opslag account geweigerd. Houd er rekening mee dat met `-Bypass` deze opdracht de `AzureServices`para meter is ingesteld op. Hiermee kunnen vertrouwde services van de eerste partij, zoals Azure File Sync, toegang krijgen tot het opslag account via het open bare eind punt.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Met de volgende CLI-opdracht wordt al het verkeer naar het open bare eind punt van het opslag account geweigerd. Houd er rekening mee dat met `-bypass` deze opdracht de `AzureServices`para meter is ingesteld op. Hiermee kunnen vertrouwde services van de eerste partij, zoals Azure File Sync, toegang krijgen tot het opslag account via het open bare eind punt.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Beperk de toegang tot het open bare eind punt tot specifieke virtuele netwerken
Wanneer u het opslag account beperkt tot specifieke virtuele netwerken, kunt u vanuit de opgegeven virtuele netwerken aanvragen naar het open bare eind punt toestaan. Dit werkt met behulp van een mogelijkheid van het virtuele netwerk met de naam *service-eind punten*. Dit kan worden gebruikt met of zonder persoonlijke eind punten.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigeer naar het opslag account waarvoor u het open bare eind punt wilt beperken tot specifieke virtuele netwerken. Selecteer **firewalls en virtuele netwerken**in de inhouds opgave van het opslag account. 

Selecteer boven aan de pagina het keuze rondje **geselecteerde netwerken** . Hiermee wordt het verbergen van een aantal instellingen voor het beheren van de beperking van het open bare eind punt ongedaan gemaakt. Klik op **+ bestaand virtueel netwerk toevoegen** om het specifieke virtuele netwerk te selecteren dat moet worden toegestaan om toegang te krijgen tot het opslag account via het open bare eind punt. Hiervoor moet u een virtueel netwerk en een subnet voor dat virtuele netwerk selecteren. 

Schakel **vertrouwde micro soft-Services toegang geven tot dit service account** in om vertrouwde micro soft-Services, zoals Azure file sync, toe te staan om toegang te krijgen tot het opslag account.

![Scherm afbeelding van de Blade firewalls en virtuele netwerken met een specifiek virtueel netwerk dat toegang heeft tot het opslag account via het open bare eind punt](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u de toegang tot het open bare eind punt van het opslag account wilt beperken tot specifieke virtuele netwerken die gebruikmaken van service-eind punten, moet u eerst informatie verzamelen over het opslag account en het virtuele netwerk. Vul `<storage-account-resource-group>` `<storage-account-name>`, `<vnet-resource-group-name>` `<subnet-name>` ,, en in om deze informatie te verzamelen. `<vnet-name>`

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group>"
$storageAccountName = "<storage-account-name>"
$restrictToVirtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$restrictToVirtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"

$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction Stop

$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $restrictToVirtualNetworkResourceGroupName `
        -Name $restrictToVirtualNetworkName `
        -ErrorAction Stop

$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $restrictToVirtualNetworkName." `
            -ErrorAction Stop
}
```

Als u wilt dat verkeer van het virtuele netwerk wordt toegestaan door de Azure-netwerk infrastructuur om naar het open bare eind punt van het opslag account te gaan, moet het `Microsoft.Storage` service-eind punt in het subnet van het virtuele netwerk worden weer gegeven. Met de volgende Power shell-opdrachten wordt `Microsoft.Storage` het eind punt van de service toegevoegd aan het subnet als dit nog niet is gebeurd.

```PowerShell
$serviceEndpoints = $subnet | `
    Select-Object -ExpandProperty ServiceEndpoints | `
    Select-Object -ExpandProperty Service

if ($serviceEndpoints -notcontains "Microsoft.Storage") {
    if ($null -eq $serviceEndpoints) {
        $serviceEndpoints = @("Microsoft.Storage")
    } elseif ($serviceEndpoints -is [string]) {
        $serviceEndpoints = @($serviceEndpoints, "Microsoft.Storage")
    } else {
        $serviceEndpoints += "Microsoft.Storage"
    }

    $virtualNetwork = $virtualNetwork | Set-AzVirtualNetworkSubnetConfig `
            -Name $subnetName `
            -AddressPrefix $subnet.AddressPrefix `
            -ServiceEndpoint $serviceEndpoints `
            -WarningAction SilentlyContinue `
            -ErrorAction Stop | `
        Set-AzVirtualNetwork `
            -ErrorAction Stop
}
```

De laatste stap bij het beperken van het verkeer naar het opslag account is het maken van een netwerk regel en het toevoegen van de netwerk regel van het opslag account.

```PowerShell
$networkRule = $storageAccount | Add-AzStorageAccountNetworkRule `
    -VirtualNetworkResourceId $subnet.Id `
    -ErrorAction Stop

$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -VirtualNetworkRule $networkRule `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u de toegang tot het open bare eind punt van het opslag account wilt beperken tot specifieke virtuele netwerken die gebruikmaken van service-eind punten, moet u eerst informatie verzamelen over het opslag account en het virtuele netwerk. Vul `<storage-account-resource-group>` `<storage-account-name>`, `<vnet-resource-group-name>` `<subnet-name>` ,, en in om deze informatie te verzamelen. `<vnet-name>`

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Als u wilt dat verkeer van het virtuele netwerk wordt toegestaan door de Azure-netwerk infrastructuur om naar het open bare eind punt van het opslag account te gaan, moet het `Microsoft.Storage` service-eind punt in het subnet van het virtuele netwerk worden weer gegeven. Met de volgende CLI-opdrachten wordt het `Microsoft.Storage` eind punt van de service toegevoegd aan het subnet als dit nog niet is gebeurd.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

De laatste stap bij het beperken van het verkeer naar het opslag account is het maken van een netwerk regel en het toevoegen van de netwerk regel van het opslag account.

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```

---

## <a name="see-also"></a>Zie ook
- [Azure Files-netwerk overwegingen](storage-files-networking-overview.md)
- [DNS doorsturen configureren voor Azure Files](storage-files-networking-dns.md)
- [S2S VPN configureren voor Azure Files](storage-files-configure-s2s-vpn.md)