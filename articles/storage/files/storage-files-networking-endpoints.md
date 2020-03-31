---
title: Eindpunten azure files-netwerk configureren | Microsoft Documenten
description: Een overzicht van netwerkopties voor Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cc487e8def180735606aa010651dde40ef93908e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082505"
---
# <a name="configuring-azure-files-network-endpoints"></a>Eindpunten azure files-netwerk configureren
Azure Files biedt twee hoofdtypen eindpunten voor toegang tot Azure-bestandsshares: 
- Openbare eindpunten, die een openbaar IP-adres hebben en overal ter wereld toegankelijk zijn.
- Privéeindpunten, die binnen een virtueel netwerk bestaan en een privé-IP-adres hebben vanuit de adresruimte van dat virtuele netwerk.

Openbare en privé eindpunten bestaan op het Azure-opslagaccount. Een opslagaccount is een beheerconstructie die een gedeelde opslaggroep vertegenwoordigt waarin u meerdere bestandsshares implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen.

In dit artikel wordt gemikt op het configureren van de eindpunten van een opslagaccount voor het rechtstreeks openen van de Azure-bestandsshare. De meeste details in dit document zijn ook van toepassing op de manier waarop Azure File Sync werkt met openbare en private eindpunten voor het opslagaccount, maar voor meer informatie over netwerkoverwegingen voor een Azure File Sync-implementatie, zie [het configureren van Azure File Sync-proxy en firewall-instellingen.](storage-sync-files-firewall-and-proxy.md)

We raden u aan [azure files-netwerkoverwegingen](storage-files-networking-overview.md) te lezen voordat u deze handleiding leest.

## <a name="prerequisites"></a>Vereisten
- In dit artikel wordt ervan uitgegaan dat u al een Azure-abonnement hebt gemaakt. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- In dit artikel wordt ervan uitgegaan dat u al een Azure-bestandsshare hebt gemaakt in een opslagaccount waarmee u vanuit on-premises verbinding wilt maken. Zie [Een Azure-bestandsshare maken](storage-how-to-create-file-share.md)voor meer informatie over het maken van een Azure-bestandsshare.
- Als u Azure PowerShell wilt gebruiken, [installeert u de nieuwste versie.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Als u de Azure CLI wilt gebruiken, [installeert u de nieuwste versie.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
Als u een privéeindpunt voor uw opslagaccount maakt, worden de volgende Azure-resources geïmplementeerd:

- **Een privéeindpunt:** een Azure-bron die het privéeindpunt van het opslagaccount weergeeft. U dit zien als een bron die een opslagaccount en een netwerkinterface verbindt.
- **Een netwerkinterface (NIC):** de netwerkinterface die een privé-IP-adres binnen het opgegeven virtuele netwerk/subnet onderhoudt. Dit is exact dezelfde resource die wordt geïmplementeerd wanneer u een virtuele machine implementeert, maar in plaats van te worden toegewezen aan een VM, is deze eigendom van het privéeindpunt.
- **Een privé-DNS-zone:** Als u nog nooit eerder een privéeindpunt voor dit virtuele netwerk hebt geïmplementeerd, wordt een nieuwe privé-DNS-zone geïmplementeerd voor uw virtuele netwerk. Er wordt ook een DNS A-record gemaakt voor het opslagaccount in deze DNS-zone. Als u al een privéeindpunt in dit virtuele netwerk hebt geïmplementeerd, wordt een nieuwe A-record voor het opslagaccount toegevoegd aan de bestaande DNS-zone. Het implementeren van een DNS-zone is optioneel, maar sterk aanbevolen, en vereist als u uw Azure-bestanddeelt met een AD-serviceprincipal of de FileREST-API gebruikt.

> [!Note]  
> In dit artikel wordt het DNS-achtervoegsel `core.windows.net`van het opslagaccount voor de azure public-regio's gebruikt. Dit commentaar is ook van toepassing op Azure Sovereign-clouds zoals de Azure Us Government-cloud en de Azure China-cloud - vervang gewoon de juiste achtervoegsels voor uw omgeving. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigeer naar het opslagaccount waarvoor u een privéeindpunt wilt maken. Selecteer in de inhoudsopgave voor het opslagaccount **Privéeindpuntverbindingen**en vervolgens **+ Privéeindpunt** om een nieuw privéeindpunt te maken. 

![Een schermafbeelding van het item privéeindpuntverbindingen in de inhoudsopgave van het opslagaccount](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

De resulterende wizard heeft meerdere pagina's om te voltooien.

Selecteer in het blad **Basisbeginselen** de gewenste resourcegroep, naam en regio voor uw privéeindpunt. Deze kunnen zijn wat u wilt, ze hoeven toch niet overeen te komen met het opslagaccount, hoewel u het privéeindpunt moet maken in dezelfde regio als het virtuele netwerk waarin u het privéeindpunt wilt maken.

![Een schermafbeelding van de sectie Basisbeginselen van de sectie Privéeindpunt maken](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

Selecteer in het **resourceblad** de keuzerondje voor **Verbinding maken met een Azure-bron in mijn map**. Selecteer **Microsoft.Storage/storageAccounts** voor het brontype onder **Resourcetype.** Het veld **Resource** is het opslagaccount met het Azure-bestandsaandeel waarmee u verbinding wilt maken. Doelsubbron is **bestand,** omdat dit voor Azure Files is.

Met **het configuratieblad** u het specifieke virtuele netwerk en subnet selecteren waaraan u uw privéeindpunt wilt toevoegen. Selecteer het virtuele netwerk dat u hierboven hebt gemaakt. U moet een duidelijk subnet selecteren uit het subnet waaraan u het eindpunt van de service hebt toegevoegd. Het configuratieblad bevat ook de informatie voor het maken/bijwerken van de privé-DNS-zone. We raden u `privatelink.file.core.windows.net` aan de standaardzone te gebruiken.

![Een schermafbeelding van de sectie Configuratie](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Klik **op Controleren + maken** om het privéeindpunt te maken. 

Als u een virtuele machine in uw virtuele netwerk hebt, of als u DNS forwarding hebt geconfigureerd zoals [hier](storage-files-networking-dns.md)beschreven, u testen of uw privéeindpunt correct is ingesteld door de volgende opdrachten van PowerShell, de opdrachtregel of de terminal (werkt voor Windows, Linux of macOS) uit te voeren. U moet `<storage-account-name>` de juiste naam van het opslagaccount vervangen:

```
nslookup <storage-account-name>.file.core.windows.net
```

Als alles succesvol heeft gewerkt, ziet u `192.168.0.5` de volgende uitvoer, waar is het privé-IP-adres van het privéeindpunt in uw virtuele netwerk (uitvoer weergegeven voor Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Als u een privéeindpunt voor uw opslagaccount wilt maken, moet u eerst een verwijzing naar uw opslagaccount en het virtuele netwerksubnet krijgen waaraan u het privéeindpunt wilt toevoegen. Vervangen `<storage-account-resource-group-name>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<vnet-subnet-name>` , en lager:

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

Als u een privéeindpunt wilt maken, moet u een privékoppelingsserviceverbinding maken met het opslagaccount. De private link service verbinding is een input voor het maken van het privé eindpunt. 

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

Als u een Azure private DNS-zone maakt, `storageaccount.file.core.windows.net` wordt de oorspronkelijke naam van het opslagaccount mogelijk gemaakt, zoals het oplossen van het privé-IP-adres in het virtuele netwerk. Hoewel optioneel vanuit het perspectief van het maken van een privéeindpunt, is het expliciet vereist voor het monteren van de Azure-bestandsshare met behulp van een AD-gebruikersprincipal of toegang via de REST API.  

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

Nu u een verwijzing hebt naar de privé-DNS-zone, moet u een A-record maken voor uw opslagaccount.

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

Als u een virtuele machine in uw virtuele netwerk hebt of als u DNS forwarding hebt geconfigureerd zoals [hier](storage-files-networking-dns.md)beschreven, u testen of uw privéeindpunt correct is ingesteld met de volgende opdrachten:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.File) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Als alles succesvol heeft gewerkt, ziet u `192.168.0.5` de volgende uitvoer, waar is het privé-IP-adres van het privéeindpunt in uw virtuele netwerk:

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
Als u een privéeindpunt voor uw opslagaccount wilt maken, moet u eerst een verwijzing naar uw opslagaccount en het virtuele netwerksubnet krijgen waaraan u het privéeindpunt wilt toevoegen. Vervangen `<storage-account-resource-group-name>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<vnet-subnet-name>` , en lager:

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

Als u een privéeindpunt wilt maken, moet u er eerst voor zorgen dat het privéeindpuntnetwerkbeleid van het subnet is ingesteld op uitgeschakeld. Vervolgens u een privéeindpunt `az network private-endpoint create` maken met de opdracht

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

Als u een Azure private DNS-zone maakt, `storageaccount.file.core.windows.net` wordt de oorspronkelijke naam van het opslagaccount mogelijk gemaakt, zoals het oplossen van het privé-IP-adres in het virtuele netwerk. Hoewel optioneel vanuit het perspectief van het maken van een privéeindpunt, is het expliciet vereist voor het monteren van de Azure-bestandsshare met behulp van een AD-gebruikersprincipal of toegang via de REST API.  

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

Nu u een verwijzing hebt naar de privé-DNS-zone, moet u een A-record maken voor uw opslagaccount.

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

Als u een virtuele machine in uw virtuele netwerk hebt of als u DNS forwarding hebt geconfigureerd zoals [hier](storage-files-networking-dns.md)beschreven, u testen of uw privéeindpunt correct is ingesteld met de volgende opdrachten:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.File" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Als alles succesvol heeft gewerkt, ziet u `192.168.0.5` de volgende uitvoer, waar is het privé-IP-adres van het privéeindpunt in uw virtuele netwerk:

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>Toegang tot het openbare eindpunt beperken
U de toegang tot het openbare eindpunt beperken met de firewall-instellingen voor opslagaccount. In het algemeen beperkt het meeste firewallbeleid voor een opslagaccount de toegang tot netwerken tot een of meer virtuele netwerken. Er zijn twee benaderingen om de toegang tot een opslagaccount te beperken tot een virtueel netwerk:

- [Maak een of meer privéeindpunten voor het opslagaccount](#create-a-private-endpoint) en beperk alle toegang tot het openbare eindpunt. Dit zorgt ervoor dat alleen verkeer dat afkomstig is van de gewenste virtuele netwerken toegang heeft tot de Azure-bestandsshares binnen het opslagaccount.
- Beperk het openbare eindpunt tot een of meer virtuele netwerken. Dit werkt met behulp van een mogelijkheid van het virtuele netwerk genaamd *service eindpunten*. Wanneer u het verkeer via een serviceeindpunt beperkt tot een opslagaccount, hebt u nog steeds toegang tot het opslagaccount via het openbare IP-adres.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Alle toegang tot het openbare eindpunt beperken
Wanneer alle toegang tot het openbare eindpunt is beperkt, is het opslagaccount nog steeds toegankelijk via de privéeindpunten. Anders worden geldige aanvragen voor het openbare eindpunt van het opslagaccount afgewezen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigeer naar het opslagaccount waarvoor u alle toegang tot het openbare eindpunt wilt beperken. Selecteer **Firewalls en virtuele netwerken**in de inhoudsopgave voor het opslagaccount.

Selecteer boven aan de pagina de knop **Keuzerondje netwerken.** Hiermee worden een aantal instellingen ongedaan gemaakt voor het beheren van de beperking van het openbare eindpunt. Schakel **Vertrouwde Microsoft-services toestaan om toegang te krijgen tot dit serviceaccount,** zodat vertrouwde microsoft-services van de eerste partij, zoals Azure File Sync, toegang krijgen tot het opslagaccount.

![Schermafbeelding van het firewalls- en virtuele netwerkblad met de juiste beperkingen op zijn plaats](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Met de volgende PowerShell-opdracht wordt al het verkeer naar het openbare eindpunt van het opslagaccount niet weergegeven. Houd er rekening `-Bypass` mee dat `AzureServices`met deze opdracht de parameter is ingesteld op . Hierdoor kunnen vertrouwde first-partyservices zoals Azure File Sync toegang krijgen tot het opslagaccount via het openbare eindpunt.

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
Met de volgende opdracht CLI wordt al het verkeer naar het openbare eindpunt van het opslagaccount niet weergegeven. Houd er rekening `-bypass` mee dat `AzureServices`met deze opdracht de parameter is ingesteld op . Hierdoor kunnen vertrouwde first-partyservices zoals Azure File Sync toegang krijgen tot het opslagaccount via het openbare eindpunt.

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

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Toegang tot het openbare eindpunt beperken tot specifieke virtuele netwerken
Wanneer u het opslagaccount beperkt tot specifieke virtuele netwerken, staat u aanvragen toe aan het openbare eindpunt vanuit de opgegeven virtuele netwerken. Dit werkt met behulp van een mogelijkheid van het virtuele netwerk genaamd *service eindpunten*. Dit kan worden gebruikt met of zonder privé eindpunten.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigeer naar het opslagaccount waarvoor u het openbare eindpunt wilt beperken tot specifieke virtuele netwerken. Selecteer **Firewalls en virtuele netwerken**in de inhoudsopgave voor het opslagaccount. 

Selecteer boven aan de pagina de knop **Keuzerondje netwerken.** Hiermee worden een aantal instellingen ongedaan gemaakt voor het beheren van de beperking van het openbare eindpunt. Klik **op +Bestaand virtueel netwerk toevoegen** om het specifieke virtuele netwerk te selecteren dat toegang moet krijgen tot het opslagaccount via het openbare eindpunt. Hiervoor moet een virtueel netwerk en een subnet voor dat virtuele netwerk worden geselecteerd. 

Schakel **Vertrouwde Microsoft-services toestaan om toegang te krijgen tot dit serviceaccount,** zodat vertrouwde microsoft-services van de eerste partij, zoals Azure File Sync, toegang krijgen tot het opslagaccount.

![Schermafbeelding van het firewalls- en virtuele netwerkblad met een specifiek virtueel netwerk dat toegang heeft tot het opslagaccount via het openbare eindpunt](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Om de toegang tot het openbare eindpunt van het opslagaccount te beperken tot specifieke virtuele netwerken met behulp van serviceeindpunten, moeten we eerst informatie verzamelen over het opslagaccount en het virtuele netwerk. Vul `<storage-account-resource-group>`deze `<storage-account-name>` `<vnet-resource-group-name>`gegevens `<vnet-name>`in, , en om `<subnet-name>` deze informatie te verzamelen.

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

Om verkeer uit het virtuele netwerk toe te staan door de Azure-netwerkstructuur om naar het openbare eindpunt `Microsoft.Storage` van het opslagaccount te gaan, moet het subnet van het virtuele netwerk het serviceeindpunt hebben blootgesteld. Met de volgende PowerShell-opdrachten `Microsoft.Storage` wordt het eindpunt van de service aan het subnet toegevoegd als het er nog niet is.

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

De laatste stap in het beperken van verkeer naar het opslagaccount is het maken van een netwerkregel en het toevoegen aan de netwerkregelset van het opslagaccount.

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
Om de toegang tot het openbare eindpunt van het opslagaccount te beperken tot specifieke virtuele netwerken met behulp van serviceeindpunten, moeten we eerst informatie verzamelen over het opslagaccount en het virtuele netwerk. Vul `<storage-account-resource-group>`deze `<storage-account-name>` `<vnet-resource-group-name>`gegevens `<vnet-name>`in, , en om `<subnet-name>` deze informatie te verzamelen.

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

Om verkeer uit het virtuele netwerk toe te staan door de Azure-netwerkstructuur om naar het openbare eindpunt `Microsoft.Storage` van het opslagaccount te gaan, moet het subnet van het virtuele netwerk het serviceeindpunt hebben blootgesteld. Met de volgende CLI-opdrachten `Microsoft.Storage` wordt het eindpunt van de service aan het subnet toegevoegd als het er nog niet is.

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

De laatste stap in het beperken van verkeer naar het opslagaccount is het maken van een netwerkregel en het toevoegen aan de netwerkregelset van het opslagaccount.

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
- [Overwegingen voor Azure Files-netwerken](storage-files-networking-overview.md)
- [DNS-forwarding voor Azure-bestanden configureren](storage-files-networking-dns.md)
- [S2S VPN configureren voor Azure Files](storage-files-configure-s2s-vpn.md)