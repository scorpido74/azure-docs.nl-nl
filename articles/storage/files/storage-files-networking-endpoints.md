---
title: Netwerkeindpunten configureren voor Azure Files | Microsoft Docs
description: Een overzicht van de netwerkopties voor Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4695164e7bcbc63b852f2f4364cdccbc8ea7d8c4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849311"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure Files-netwerkeindpunten configureren
Azure Files biedt twee hoofdtypen eindpunten voor toegang tot Azure-bestandsshares: 
- Openbare eindpunten, die een openbaar IP-adres hebben en overal ter wereld toegankelijk zijn.
- Privé-eindpunten, die zich binnen een virtueel netwerk bevinden en een privé-IP-adres hebben in de adresruimte van het virtuele netwerk.

Openbare en privé-eindpunten bestaan in het Azure-opslagaccount. Een opslagaccount is een beheerconstructie die een gedeelde opslaggroep vertegenwoordigt waarin u meerdere bestandsshares kunt implementeren, evenals andere opslagresources, zoals blob-containers of wachtrijen.

Dit artikel richt zich op het configureren van de eindpunten van een opslagaccount voor het rechtstreeks openen van de Azure-bestandsshare. De meeste informatie in dit document is ook van toepassing op de manier waarop Azure File Sync samenwerkt met openbare en privé-eindpunten voor het opslagaccount. Als u echter meer wilt weten over aandachtspunten voor netwerken bij een Azure File Sync-implementatie, raadpleegt u [Azure File Sync proxy and firewall settings](storage-sync-files-firewall-and-proxy.md) (Azure File Sync-proxy- en firewallinstellingen).

Het is raadzaam om [Azure Files networking considerations](storage-files-networking-overview.md) (Aandachtspunten voor Azure Files-netwerken) te lezen voordat u dit artikel verder leest.

## <a name="prerequisites"></a>Vereisten
- In dit artikel wordt ervan uitgegaan dat u al een Azure-abonnement hebt gemaakt. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- In dit artikel wordt ervan uitgegaan dat u al een Azure-bestandsshare hebt gemaakt in een opslagaccount waarmee u on-premises verbinding wilt maken. Zie [Een Azure-bestandsshare maken](storage-how-to-create-file-share.md) als u wilt lezen hoe u een Azure-bestandsshare maakt.
- Als u van plan bent om Azure PowerShell te gebruiken, [installeert u de nieuwste versie](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
Als u een privé-eindpunt maakt voor uw opslagaccount, worden de volgende Azure-resources geïmplementeerd:

- **Een privé-eindpunt**: Een Azure-resource die het privé-eindpunt van het opslagaccount voorstelt. U kunt dit zien als een resource die een verbinding opzet tussen een opslagaccount en een netwerkinterface.
- **Een netwerkinterface (NIC)** : De netwerkinterface die een privé-IP-adres onderhoudt binnen het opgeven virtuele netwerk/subnet. Dit is exact dezelfde resource die wordt geïmplementeerd als u een virtuele machine implementeert, alleen wordt de resource nu niet toegewezen aan een VM, maar is deze het eigendom van het privé-eindpunt.
- **Een privé-DNS-zone**: Als u niet eerder een privé-eindpunt hebt geïmplementeerd voor dit virtuele netwerk, wordt er een nieuwe privé-DNS-zone geïmplementeerd voor uw virtuele netwerk. Er wordt ook een DNS A-record gemaakt voor het opslagaccount in deze DNS-zone. Als u al een privé-eindpunt hebt geïmplementeerd in dit virtuele netwerk, wordt er een nieuwe A-record voor het opslagaccount toegevoegd aan de bestaande DNS-zone. Het implementeren van een DNS-zone is optioneel, maar wordt wel sterk aanbevolen. Het is vereist als u Azure-bestandsshares koppelt met behulp van een AD-serviceprincipal of de FileREST-API.

> [!Note]  
> In dit artikel wordt het DNS-achtervoegsel voor opslagaccounts gebruikt voor de openbare regio's van Azure, te weten `core.windows.net`. Deze opmerking geldt ook voor onafhankelijke Azure-clouds zoals de Azure-cloud voor de Amerikaanse overheid en de Azure China-cloud. In dat geval vervangt u het achtervoegsel door dat van de onafhankelijke cloud. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Ga naar het opslagaccount waarvoor u een privé-eindpunt wilt maken. Selecteer **Privé-eindpuntverbindingen** in de inhoudsopgave van het opslagaccount en selecteer vervolgens **+ Privé-eindpunt** om een nieuw privé-eindpunt te maken. 

![Een schermopname van de optie Privé-eindpuntverbindingen in de inhoudsopgave van het opslagaccount](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Er wordt een wizard gestart waarin u meerdere pagina's moet invullen.

Selecteer op de blade **Algemeen** de gewenste resourcegroep, geef een naam op en selecteer de regio voor het privé-eindpunt. U kunt hier kiezen wat u wilt. De waarden hoeven niet overeen te komen met die van het opslagaccount. Het is wel zo dat u het privé-eindpunt moet maken in dezelfde regio als het virtuele netwerk waarin u het privé-eindpunt wilt maken.

![Een schermopname van de blade Algemeen in de sectie Een privé-eindpunt maken](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

Schakel op de blade **Resource** het keuzerondje **Verbinding maken met een Azure-resource in mijn directory** in. Selecteer onder **Resourcetype** **Microsoft.Storage/storageAccounts** als het type resource. Geef in het veld **Resource** het opslagaccount op met de Azure-bestandsshare waarmee u verbinding wilt maken. Selecteer voor Subresource van doel de waarde **bestand**, aangezien dit voor Azure Files is.

Op de blade **Configuratie** kunt u het specifieke virtuele netwerk en het subnet selecteren waaraan u het privé-eindpunt wilt toevoegen. Selecteer het virtuele netwerk dat u hierboven hebt gemaakt. U moet een ander subnet selecteren dan het subnet waaraan u hierboven het service-eindpunt hebt toegevoegd. De blade Configuratie bevat ook de gegevens voor het maken/bijwerken van de privé-DNS-zone. We adviseren om de standaardzone `privatelink.file.core.windows.net` te gebruiken.

![Een schermopname van de sectie Configuratie](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Klik op **Beoordelen en maken** om het privé-eindpunt te maken. 

Als er een virtuele machine aanwezig is in het virtuele netwerk, of als u het doorsturen in DNS hebt geconfigureerd zoals [hier](storage-files-networking-dns.md) wordt beschreven, kunt u testen of uw privé-eindpunt goed is ingesteld door de volgende opdrachten uit te voeren vanuit PowerShell, vanaf de opdrachtregel of vanuit de terminal (werkt voor Windows, Linux en macOS). U moet `<storage-account-name>` door de juiste naam van het opslagaccount:

```
nslookup <storage-account-name>.file.core.windows.net
```

Als alles in orde is, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres is van het privé-eindpunt in uw virtuele netwerk (de weergegeven uitvoer is voor Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u een privé-eindpunt wilt maken voor uw opslagaccount, moet u eerst een verwijzing naar uw opslagaccount opvragen, evenals naar het subnet in het virtuele netwerk waaraan u het privé-eindpunt wilt toevoegen. Vervang `<storage-account-resource-group-name>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>` en `<vnet-subnet-name>` hieronder:

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

Als u een privé-eindpunt wilt maken, moet u een Private Link-serviceverbinding maken met het opslagaccount. Deze verbinding geeft u op als invoer bij het maken van het privé-eindpunt. 

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

Door het maken van een privé-DNS-zone in Azure kan de oorspronkelijke naam van het opslagaccount, zoals `storageaccount.file.core.windows.net`, worden omgezet in het privé-IP-adres in het virtuele netwerk. Hoewel optioneel vanuit het perspectief van het maken van een privé-eindpunt, is dit expliciet vereist voor het koppelen van de Azure-bestandsshare met behulp van een AD-principal van een gebruiker of de REST-API.  

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

U beschikt nu over een verwijzing naar de privé-DNS-zone en kunt daarom een A-record gaan maken voor uw opslagaccount.

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

Als er een virtuele machine aanwezig is in het virtuele netwerk, of als u het doorsturen in DNS hebt geconfigureerd zoals [hier](storage-files-networking-dns.md) wordt beschreven, kunt u testen of uw privé-eindpunt goed is ingesteld door de volgende opdrachten uit te voeren:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Als alles in orde is, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres is van het privé-eindpunt in uw virtuele netwerk:

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
Als u een privé-eindpunt wilt maken voor uw opslagaccount, moet u eerst een verwijzing naar uw opslagaccount opvragen, evenals naar het subnet in het virtuele netwerk waaraan u het privé-eindpunt wilt toevoegen. Vervang `<storage-account-resource-group-name>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>` en `<vnet-subnet-name>` hieronder:

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

Als u een privé-eindpunt wilt maken, moet u er eerst voor zorgen dat het netwerkbeleid voor het privé-eindpunt van het subnet is uitgeschakeld. Vervolgens kunt u een privé-eindpunt maken met de opdracht `az network private-endpoint create`.

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

Door het maken van een privé-DNS-zone in Azure kan de oorspronkelijke naam van het opslagaccount, zoals `storageaccount.file.core.windows.net`, worden omgezet in het privé-IP-adres in het virtuele netwerk. Hoewel optioneel vanuit het perspectief van het maken van een privé-eindpunt, is dit expliciet vereist voor het koppelen van de Azure-bestandsshare met behulp van een AD-principal van een gebruiker of de REST-API.  

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

U beschikt nu over een verwijzing naar de privé-DNS-zone en kunt daarom een A-record gaan maken voor uw opslagaccount.

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

Als er een virtuele machine aanwezig is in het virtuele netwerk, of als u het doorsturen in DNS hebt geconfigureerd zoals [hier](storage-files-networking-dns.md) wordt beschreven, kunt u testen of uw privé-eindpunt goed is ingesteld door de volgende opdrachten uit te voeren:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Als alles in orde is, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres is van het privé-eindpunt in uw virtuele netwerk. U moet nog steeds storageaccount.file.core.windows.net gebruiken om verbinding te maken met uw bestandsshare en niet het privatelink-pad.

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
U kunt de toegang tot het openbare eindpunt beperken via de firewallinstellingen voor het opslagaccount. Over het algemeen beperken de meeste beleidsregels van firewalls voor een opslagaccount de netwerktoegang tot een of meer virtuele netwerken. Er zijn twee benaderingen voor het beperken van de toegang van een opslagaccount tot een virtueel netwerk:

- [Een of meer privé-eindpunten maken voor het opslagaccount](#create-a-private-endpoint) en alle toegang tot het openbare eindpunt beperken. Hierdoor heeft alleen verkeer dat afkomstig is uit de gewenste virtuele netwerken toegang tot de Azure-bestandsshares binnen het opslagaccount.
- Het openbare eindpunt beperken tot een of meer virtuele netwerken. Hiervoor wordt een voorziening van het virtuele netwerk gebruikt met de naam *service-eindpunten*. Wanneer u het verkeer naar een opslagaccount beperkt via een service-eindpunt, krijgt u nog steeds toegang tot het opslagaccount via het openbare IP-adres.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Alle toegang tot het openbare eindpunt beperken
Wanneer alle toegang tot het openbare eindpunt wordt beperkt, is het opslagaccount nog toegankelijk via de privé-eindpunten van het account. Anderszins geldige aanvragen naar het openbare eindpunt van het opslagaccount worden geweigerd. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Ga naar het opslagaccount waarvoor u alle toegang tot het openbare eindpunt wilt beperken. Selecteer in de inhoudsopgave voor het opslagaccount **Firewalls en virtuele netwerken**.

Selecteer bovenaan de pagina het keuzerondje **Geselecteerde netwerken**. Hierdoor komen er een aantal instellingen beschikbaar voor het beperken van de toegang tot het openbare eindpunt. Selecteer **Vertrouwde Microsoft-services toegang geven tot dit serviceaccount** om vertrouwde Microsoft-services, zoals Azure File Sync, toegang te geven tot het opslagaccount.

![Schermopname van de blade Firewalls en virtuele netwerken met de gewenste beperkingen](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Met de volgende PowerShell-opdracht wordt al het verkeer geweigerd naar het openbare eindpunt van het opslagaccount. In deze opdracht is de parameter `-Bypass` ingesteld op `AzureServices`. Hierdoor kunnen vertrouwde Microsoft-services, zoals Azure File Sync, via het openbare eindpunt toegang krijgen tot het opslagaccount.

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
Met de volgende CLI-opdracht wordt al het verkeer geweigerd naar het openbare eindpunt van het opslagaccount. In deze opdracht is de parameter `-bypass` ingesteld op `AzureServices`. Hierdoor kunnen vertrouwde Microsoft-services, zoals Azure File Sync, via het openbare eindpunt toegang krijgen tot het opslagaccount.

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
Wanneer u de toegang tot het opslagaccount beperkt tot bepaalde virtuele netwerken, staat u aanvragen naar het openbare eindpunt toe vanuit de opgegeven virtuele netwerken. Hiervoor wordt een voorziening van het virtuele netwerk gebruikt met de naam *service-eindpunten*. Deze voorziening kan worden gebruikt met of zonder privé-eindpunten.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Ga naar het opslagaccount waarvoor u het openbare eindpunt wilt beperken tot bepaalde virtuele netwerken. Selecteer in de inhoudsopgave voor het opslagaccount **Firewalls en virtuele netwerken**. 

Selecteer bovenaan de pagina het keuzerondje **Geselecteerde netwerken**. Hierdoor komen er een aantal instellingen beschikbaar voor het beperken van de toegang tot het openbare eindpunt. Klik op **+ Bestaand virtueel netwerk toevoegen** om het specifieke virtuele netwerk te selecteren dat toegang mag hebben tot het opslagaccount via het openbare eindpunt. Hiervoor moet u een virtueel netwerk en een subnet selecteren voor dat virtuele netwerk. 

Selecteer **Vertrouwde Microsoft-services toegang geven tot dit serviceaccount** om vertrouwde Microsoft-services, zoals Azure File Sync, toegang te geven tot het opslagaccount.

![Schermopname van de blade Firewalls en virtuele netwerken waarop een bepaald virtueel netwerk toegang heeft gekregen tot het opslagaccount via het openbare eindpunt](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om de toegang tot het openbare eindpunt van het opslagaccount met behulp van service-eindpunten te beperken tot specifieke virtuele netwerken, moeten we eerst gegevens verzamelen van het opslagaccount en het virtuele netwerk. Geef waarden op voor `<storage-account-resource-group>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>` en `<subnet-name>` om deze gegevens te verzamelen.

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

Om verkeer vanuit het virtuele netwerk via de netwerkinfrastructuur van Azure door te laten naar het openbare eindpunt van het opslagaccount, moet het service-eindpunt `Microsoft.Storage` beschikbaar zijn in het subnet van het virtuele netwerk. Met de volgende PowerShell-opdrachten wordt het service-eindpunt `Microsoft.Storage` toegevoegd aan het subnet als het daar nog niet aanwezig is.

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

De laatste stap om verkeer naar het opslagaccount te beperken, is het opstellen van een netwerkregel en die toe te voegen aan de set met netwerkregels voor het opslagaccount.

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
Om de toegang tot het openbare eindpunt van het opslagaccount met behulp van service-eindpunten te beperken tot specifieke virtuele netwerken, moeten we eerst gegevens verzamelen van het opslagaccount en het virtuele netwerk. Geef waarden op voor `<storage-account-resource-group>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>` en `<subnet-name>` om deze gegevens te verzamelen.

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

Om verkeer vanuit het virtuele netwerk via de netwerkinfrastructuur van Azure door te laten naar het openbare eindpunt van het opslagaccount, moet het service-eindpunt `Microsoft.Storage` beschikbaar zijn in het subnet van het virtuele netwerk. Met de volgende CLI-opdrachten wordt het service-eindpunt `Microsoft.Storage` toegevoegd aan het subnet als het daar nog niet aanwezig is.

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

De laatste stap om verkeer naar het opslagaccount te beperken, is het opstellen van een netwerkregel en die toe te voegen aan de set met netwerkregels voor het opslagaccount.

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
- [Azure Files networking considerations](storage-files-networking-overview.md) (Aandachtspunten voor Azure Files-netwerken)
- [Configuring DNS forwarding for Azure Files](storage-files-networking-dns.md) (DNS doorsturen configureren voor Azure Files)
- [Configuring S2S VPN for Azure Files](storage-files-configure-s2s-vpn.md) (S2S-VPN configureren voor Azure Files)
