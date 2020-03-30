---
title: DNS-forwarding configureren voor Azure Files | Microsoft Documenten
description: Een overzicht van netwerkopties voor Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 35dfbcb274721049f2160719222ca89038c93356
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082498"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>DNS-forwarding voor Azure-bestanden configureren
Met Azure Files u privéeindpunten maken voor de opslagaccounts die uw bestandsshares bevatten. Hoewel handig voor veel verschillende toepassingen, zijn privéeindpunten vooral handig voor het maken van verbinding met uw Azure-bestandsshares vanuit uw on-premises netwerk via een VPN- of ExpressRoute-verbinding via private-peering. 

Om verbindingen met uw opslagaccount over uw netwerktunnel te laten gaan, moet de volledig gekwalificeerde domeinnaam (FQDN) van uw opslagaccount worden opgelost naar het privé-IP-adres van uw privé-eindpunt. Om dit te bereiken, moet u het`core.windows.net` opslageindpuntachtervoegsel (voor openbare cloudregio's) doorsturen naar de Azure private DNS-service die toegankelijk is vanuit uw virtuele netwerk. In deze handleiding wordt uitgelegd hoe u DNS-forwarding instellen en configureren om het privé-eindpunt-IP-adres van uw opslagaccount op de juiste manier op te lossen.

We raden u ten zeerste aan [planning voor een Azure Files-implementatie](storage-files-planning.md) en Azure [Files-netwerkoverwegingen](storage-files-networking-overview.md) te lezen voordat u de in dit artikel beschreven stappen voltooit.

## <a name="overview"></a>Overzicht
Azure Files biedt twee hoofdtypen eindpunten voor toegang tot Azure-bestandsshares: 
- Openbare eindpunten, die een openbaar IP-adres hebben en overal ter wereld toegankelijk zijn.
- Privéeindpunten, die binnen een virtueel netwerk bestaan en een privé-IP-adres hebben vanuit de adresruimte van dat virtuele netwerk.

Openbare en privé eindpunten bestaan op het Azure-opslagaccount. Een opslagaccount is een beheerconstructie die een gedeelde opslaggroep vertegenwoordigt waarin u meerdere bestandsshares implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen.

Elk opslagaccount heeft een volledig gekwalificeerde domeinnaam (FQDN). Voor de public cloudregio's volgt deze `storageaccount.file.core.windows.net` `storageaccount` FQDN het patroon waar de naam van het opslagaccount is. Wanneer u verzoeken doet tegen deze naam, zoals het monteren van het aandeel op uw werkstation met SMB, voert uw besturingssysteem een DNS-lookup uit om de volledig gekwalificeerde domeinnaam op te lossen naar een IP-adres waarnaar het kan worden gebruikt om de SMB-aanvragen te verzenden.

Lost standaard `storageaccount.file.core.windows.net` op naar het IP-adres van het openbare eindpunt. Het openbare eindpunt voor een opslagaccount wordt gehost op een Azure-opslagcluster dat de openbare eindpunten van veel andere opslagaccounts host. Wanneer u een privéeindpunt maakt, wordt een privé-DNS-zone gekoppeld aan het virtuele `storageaccount.file.core.windows.net` netwerk waaraan het is toegevoegd, met een CNAME-recordtoewijzing aan een A-recordvermelding voor het privé-IP-adres van het privéeindpunt van uw opslagaccount. Hierdoor u `storageaccount.file.core.windows.net` FQDN binnen het virtuele netwerk gebruiken en het naar het IP-adres van het privé-eindpunt laten oplossen.

Aangezien ons uiteindelijke doel is om toegang te krijgen tot de Azure-bestandsshares die binnen het opslagaccount worden gehost via een netwerktunnel zoals een VPN- of ExpressRoute-verbinding, moet u uw on-premises DNS-servers configureren om aanvragen door te sturen naar de Azure Bestandenservice voor de Azure private DNS-service. Om dit te bereiken, moet u `*.core.windows.net` *voorwaardelijke doorstuurtijd* van (of het juiste opslageindpuntachtervoegsel voor de nationale clouds van de Amerikaanse overheid, Duitsland of China) instellen naar een DNS-server die wordt gehost binnen uw virtuele Azure-netwerk. Deze DNS-server stuurt het verzoek vervolgens opnieuw door naar de privé-DNS-service van Azure, waarmee de volledig gekwalificeerde domeinnaam van het opslagaccount wordt opgelost naar het juiste privé-IP-adres.

Als u DNS-forwarding voor Azure Files configureert, moet een virtuele machine worden uitgevoerd om een DNS-server te hosten om de aanvragen door te sturen, maar dit is een eenmalige stap voor alle Azure-bestandsshares die binnen uw virtuele netwerk worden gehost. Bovendien is dit geen exclusieve vereiste voor Azure Files - elke Azure-service die privéeindpunten ondersteunt die u wilt openen vanaf on-premises, kan gebruik maken van de DNS-forwarding die u in deze handleiding configureert: Azure Blob-opslag, SQL Azure, Cosmos DB, Enz. 

Deze handleiding toont de stappen voor het configureren van DNS-forwarding voor het Azure-opslageindpunt, dus naast Azure-bestanden worden ook DNS-naamomzettingsverzoeken voor alle andere Azure-opslagservices (Azure Blob-opslag, Azure Table-opslag, Azure Queue-opslag, enz.) worden doorgestuurd naar de privé-DNS-service van Azure. Desgewenst kunnen ook extra eindpunten voor andere Azure-services worden toegevoegd. DNS-forwarding naar uw on-premises DNS-servers wordt ook geconfigureerd, zodat cloudbronnen binnen uw virtuele netwerk (zoals een DFS-N-server) on-premises machinenamen kunnen oplossen. 

## <a name="prerequisites"></a>Vereisten
Voordat u DNS-forwarding naar Azure Files instellen, moet u de volgende stappen hebben voltooid:

- Een opslagaccount met een Azure-bestandsaandeel dat u wilt monteren. Zie [Een Azure-bestandsshare maken](storage-how-to-create-file-share.md)voor meer informatie over het maken van een opslagaccount en een Azure-bestandsshare.
- Een privéeindpunt voor het opslagaccount. Zie [Een privéeindpunt maken](storage-files-networking-endpoints.md#create-a-private-endpoint)voor meer informatie over het maken van een privéeindpunt voor Azure-bestanden.
- De [nieuwste versie](https://docs.microsoft.com/powershell/azure/install-az-ps) van de Azure PowerShell-module.

> [!Important]  
> In deze handleiding wordt ervan uitgegaan dat u de DNS-server binnen Windows Server gebruikt in uw on-premises omgeving. Alle stappen die in deze handleiding worden beschreven, zijn mogelijk met elke DNS-server, niet alleen de Windows DNS-server.

## <a name="manually-configuring-dns-forwarding"></a>Dns-forwarding handmatig configureren
Als u al DNS-servers in uw Azure virtuele netwerk hebt, of als u gewoon liever uw eigen virtuele machines implementeert als DNS-servers volgens welke methodologie uw organisatie ook gebruikt, u DNS handmatig configureren met de ingebouwde DNS-server PowerShell-cmdlets.

Maak op uw on-premises DNS-servers `Add-DnsServerConditionalForwarderZone`een voorwaardelijke expediteer met behulp van . Deze voorwaardelijke expediteur moet worden geïmplementeerd op al uw on-premises DNS-servers om effectief te zijn bij het correct doorsturen van verkeer naar Azure. Vergeet niet `<azure-dns-server-ip>` om te vervangen door de juiste IP-adressen voor uw omgeving.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Op de DNS-servers binnen uw virtuele Azure-netwerk moet u ook een forwarder inschakelen, zodat aanvragen voor de DNS-zone voor opslagaccount `168.63.129.16`worden gericht op de Azure private DNS-service, die wordt voorgeschreven door het gereserveerde IP-adres. (Vergeet niet `$storageAccountEndpoint` te worden ingevuld als u de opdrachten uitvoert in een andere PowerShell-sessie.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>De hybride module Azure Files gebruiken om DNS-forwarding te configureren
Om het configureren van DNS forwarding zo eenvoudig mogelijk te maken, hebben we automatisering geleverd in de Azure Files Hybrid-module. De cmdlets die worden verstrekt voor het manipuleren van DNS in deze module helpen u DNS-servers in uw virtuele Azure-netwerk te implementeren en uw on-premises DNS-servers bij te werken om naar hen door te sturen. 

Als u de Azure Files Hybrid-module nog nooit hebt gebruikt, moet u deze eerst op uw werkstation installeren. Download de [nieuwste versie](https://github.com/Azure-Samples/azure-files-samples/releases) van de Azure Files Hybrid PowerShell-module:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

Het implementeren van de DNS-forwarding-oplossing heeft twee stappen, het maken van een DNS-doorstuurregelset, die bepaalt naar welke Azure-services u aanvragen wilt doorsturen en naar de daadwerkelijke implementatie van de DNS-expediteurs. 

In het volgende voorbeeld worden aanvragen doorgestuurd naar het opslagaccount, inclusief aanvragen naar Azure-bestanden, Azure Blob-opslag, Azure Table-opslag en Azure Queue-opslag. Indien gewenst u doorsturen voor extra Azure-service toevoegen aan de regel via de `-AzureEndpoints` parameter van de `New-AzDnsForwardingRuleSet` cmdlet. Vergeet niet `<virtual-network-resource-group>` `<virtual-network-name>`om `<subnet-name>` te vervangen, , en met de juiste waarden voor uw omgeving.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Mogelijk vindt u het bovendien nuttig/noodzakelijk om een aantal aanvullende parameters te leveren:

| Parameternaam | Type | Beschrijving |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Standaard worden de DNS-servers geïmplementeerd in dezelfde brongroep als het virtuele netwerk. Als dit niet gewenst is, u met deze parameter een alternatieve resourcegroep kiezen waardeze moet worden geïmplementeerd. |
| `DnsForwarderRootName` | `string` | Standaard hebben de DNS-servers die in Azure `DnsFwder-*`zijn geïmplementeerd de namen , waarbij het sterretje wordt ingevuld door een iterator. Deze parameter verandert de wortel van die `DnsFwder`naam (d.w.z. ). |
| `VmTemporaryPassword` | `SecureString` | Standaard wordt een willekeurig wachtwoord gekozen voor het tijdelijke standaardaccount dat een VM heeft voordat het domein is samengevoegd. Nadat het domein is samengevoegd, wordt het standaardaccount uitgeschakeld. |
| `DomainToJoin` | `string` | Het domein dat moet worden samengevoegd met de DNS VM(s) om lid te worden. Standaard wordt dit domein gekozen op basis van het domein van de computer waar u de cmdlets uitvoert. |
| `DnsForwarderRedundancyCount` | `int` | Het aantal DNS-VM's dat moet worden geïmplementeerd voor uw virtuele netwerk. `New-AzDnsForwarder` Implementeert standaard twee DNS-servers in uw virtuele Azure-netwerk in een beschikbaarheidsset om redundantie te garanderen. Dit nummer kan naar wens worden gewijzigd. |
| `OnPremDnsHostNames` | `HashSet<string>` | Een handmatig opgegeven lijst met on-premises DNS-hostnamen om doorstuurgers op te maken. Deze parameter is handig wanneer u forwarders niet op alle on-premises DNS-servers wilt toepassen, bijvoorbeeld wanneer u een reeks clients met handmatig opgegeven DNS-namen hebt. |
| `Credential` | `PSCredential` | Een referentie die u moet gebruiken bij het bijwerken van de DNS-servers. Dit is handig wanneer het gebruikersaccount waarmee u bent ingelogd geen machtigingen heeft om DNS-instellingen te wijzigen. |
| `SkipParentDomain` | `SwitchParameter` | Standaard worden DNS-expediteurs toegepast op het domein op het hoogste niveau dat in uw omgeving bestaat. Als u `northamerica.corp.contoso.com` bijvoorbeeld een onderliggend `corp.contoso.com`domein is van , wordt de `corp.contoso.com`expediteer gemaakt voor de DNS-servers die zijn gekoppeld aan . Deze parameter zorgt ervoor dat `northamerica.corp.contoso.com`expediteurs worden gemaakt in . |

## <a name="confirm-dns-forwarders"></a>DNS-expediteurs bevestigen
Voordat u test om te zien of de DNS-expediteurs zijn toegepast, `Clear-DnsClientCache`raden we u aan de DNS-cache op uw lokale werkstation te wissen met behulp van . Gebruik `Resolve-DnsName` of. `nslookup`

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Als de naamomzetting succesvol is, moet u het opgeloste IP-adres zien dat overeenkomt met het IP-adres van uw opslagaccount.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Als u al een VPN- of ExpressRoute-verbinding `Test-NetConnection` hebt ingesteld, u ook zien dat een TCP-verbinding met uw opslagaccount kan worden gemaakt.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Zie ook
- [Implementatie van Azure Files plannen](storage-files-planning.md)
- [Overwegingen voor Azure Files-netwerken](storage-files-networking-overview.md)
- [Eindpunten azure files-netwerk configureren](storage-files-networking-endpoints.md)