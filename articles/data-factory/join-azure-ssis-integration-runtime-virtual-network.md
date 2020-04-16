---
title: Een Azure-SSIS Integration Runtime samenvoegen met een virtueel netwerk
description: Meer informatie over het deelnemen aan een runtime van Azure-SSIS-integratie naar een virtueel Azure-netwerk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: cf13dbe17738ca1ae658c73bb0092a219b4823d1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415909"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Een Azure-SSIS Integration Runtime samenvoegen met een virtueel netwerk

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wanneer u SQL Server Integration Services (SSIS) gebruikt in Azure Data Factory, moet u in de volgende scenario's deelnemen aan uw Azure-SSIS-integratieruntime (IR) met een Virtueel Azure-netwerk:

- U wilt verbinding maken met on-premises gegevensarchieven van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR zonder een zelf gehoste IR als proxy te configureren of te beheren. 

- U wilt SSIS-catalogusdatabase (SSISDB) hosten in een Azure SQL-database met IP-firewallregels/eindpunten voor virtuele netwerkservices of een beheerde instantie met privéeindpunt. 

- U wilt verbinding maken met Azure-resources die zijn geconfigureerd met eindpunten voor virtuele netwerkservices van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR.

- U wilt verbinding maken met gegevensopslag/resources die zijn geconfigureerd met IP-firewallregels van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR.

Met Data Factory u uw Azure-SSIS IR aansluiten bij een virtueel netwerk dat is gemaakt via het klassieke implementatiemodel of het Azure Resource Manager-implementatiemodel.

> [!IMPORTANT]
> Het klassieke virtuele netwerk wordt afgeschaft, dus gebruik in plaats daarvan het virtuele Azure Resource Manager-netwerk.  Als u het klassieke virtuele netwerk al gebruikt, schakelt u zo snel mogelijk over naar het virtuele Azure Resource Manager-netwerk.

Het [configureren van een SSIS-integratieruntime (Azure-SQL Server Integration Services) (IR) om deel te nemen aan een zelfstudie met een virtueel netwerk,](tutorial-deploy-ssis-virtual-network.md) toont de minimale stappen via Azure-portal. In dit artikel wordt uitgebreid met de zelfstudie en worden alle optionele taken beschreven:

- Als u gebruik maakt van virtueel netwerk (klassiek).
- Als u uw eigen openbare IP-adressen meeneemt voor de Azure-SSIS IR.
- Als u uw eigen DNS-server (Domain Name System) gebruikt.
- Als u een netwerkbeveiligingsgroep (NSG) op het subnet gebruikt.
- Als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt.
- Als u aangepaste Azure-SSIS IR gebruikt.
- Als u Azure Powershell-inrichting gebruikt.

## <a name="access-to-on-premises-data-stores"></a>Toegang tot on-premises gegevensarchieven

Als uw SSIS-pakketten toegang krijgen tot on-premises gegevensarchieven, u uw Azure-SSIS IR aansluiten bij een virtueel netwerk dat is verbonden met het on-premises netwerk. U ook een zelf gehoste IR configureren en beheren als proxy voor uw Azure-SSIS IR. Zie [Een zelf gehoste IR configureren als proxy voor een Azure-SSIS IR voor](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)meer informatie. 

Wanneer u uw Azure-SSIS IR aansluit bij een virtueel netwerk, onthoudt u de volgende belangrijke punten: 

- Als er geen virtueel netwerk is verbonden met uw on-premises netwerk, maakt u eerst een [virtueel Azure Resource Manager-netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) voor uw Azure-SSIS IR om lid te worden. Configureer vervolgens een site-to-site [VPN-gatewayverbinding](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) of [ExpressRoute-verbinding](../expressroute/expressroute-howto-linkvnet-classic.md) van dat virtuele netwerk naar uw on-premises netwerk. 

- Als een virtueel Azure Resource Manager-netwerk al is verbonden met uw on-premises netwerk op dezelfde locatie als uw Azure-SSIS IR, u de IR aansluiten bij dat virtuele netwerk. 

- Als een klassiek virtueel netwerk al is verbonden met uw on-premises netwerk op een andere locatie dan uw Azure-SSIS IR, u een [azure resource manager virtueel netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken waar uw Azure-SSIS IR aan kan deelnemen. Configureer vervolgens een virtuele netwerkverbinding met [classic-to-Azure Resource Manager.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 
 
- Als een virtueel Azure Resource Manager-netwerk al is verbonden met uw on-premises netwerk op een andere locatie dan uw Azure-SSIS IR, u eerst een [virtueel Azure Resource Manager-netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken waar uw Azure-SSIS IR lid van kan worden. Configureer vervolgens een virtuele netwerkverbinding van Azure Resource Manager-naar-Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>De SSIS-catalogus hosten in SQL-database

Als u uw SSIS-catalogus host in een Azure SQL-database met eindpunten voor virtuele netwerkservices, controleert u of u uw Azure-SSIS IR aansluit bij hetzelfde virtuele netwerk en subnet.

Als u uw SSIS-catalogus host in een beheerde instantie met privéeindpunt, moet u ervoor zorgen dat u deelneemt aan uw Azure-SSIS IR met hetzelfde virtuele netwerk, maar in een ander subnet dan het beheerde exemplaar. Als u uw Azure-SSIS IR wilt aansluiten op een ander virtueel netwerk dan de beheerde instantie, raden we u aan virtuele netwerkpeering (die beperkt is tot dezelfde regio) of een verbinding van virtueel netwerk naar virtueel netwerk. Zie Uw [toepassing verbinden met de beheerde instantie azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md)voor meer informatie.

## <a name="access-to-azure-services"></a>Toegang tot Azure-services

Als uw SSIS-pakketten toegang krijgen tot Azure-bronnen die eindpunten voor [virtuele netwerkservices](../virtual-network/virtual-network-service-endpoints-overview.md) ondersteunen en u toegang wilt krijgen tot deze bronnen vanuit Azure-SSIS IR, u uw Azure-SSIS IR aansluiten bij een subnet van een virtueel netwerk dat is geconfigureerd voor eindpunten voor virtuele netwerkservices en vervolgens een virtuele netwerkregel toevoegen aan de relevante Azure-bronnen om toegang te verlenen vanaf hetzelfde subnet.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Toegang tot gegevensbronnen die worden beschermd door ip-firewallregel

Als uw SSIS-pakketten toegang hebben tot gegevensopslag/bronnen die alleen specifieke statische openbare IP-adressen toestaan en u toegang wilt krijgen tot die bronnen vanuit Azure-SSIS IR, u uw eigen [openbare IP-adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) voor Azure-SSIS IR toevoegen terwijl u deze aansluit bij een virtueel netwerk en vervolgens een IP-firewallregel toevoegen aan de relevante bronnen om toegang te verlenen vanaf die IP-adressen.

In alle gevallen kan het virtuele netwerk alleen worden geïmplementeerd via het Azure Resource Manager-implementatiemodel.

De volgende secties geven meer details. 

## <a name="virtual-network-configuration"></a>Virtuele netwerkconfiguratie

Stel uw virtuele netwerk in om aan deze vereisten te voldoen: 

- Zorg ervoor `Microsoft.Batch` dat dit een geregistreerde provider is onder het abonnement van uw virtuele netwerksubnet dat de Azure-SSIS IR host. Als u een klassiek virtueel `MicrosoftAzureBatch` netwerk gebruikt, u ook deelnemen aan de rol Classic Virtual Machine Contributor voor dat virtuele netwerk. 

- Zorg ervoor dat u over de vereiste machtigingen beschikt. Zie [Machtigingen instellen](#perms)voor meer informatie .

- Selecteer het juiste subnet om de Azure-SSIS IR te hosten. Zie [Het subnet selecteren](#subnet)voor meer informatie . 

- Zie [De statische openbare IP-adressen selecteren](#publicIP) als u uw eigen openbare IP-adressen meeneemt voor de Azure-SSIS IR

- Zie [De DNS-server instellen](#dns_server)als u uw eigen DNS-server (Domain Name System) in het virtuele netwerk gebruikt. 

- Zie [Een NSG instellen](#nsg)als u een netwerkbeveiligingsgroep (NSG) op het subnet gebruikt. 

- Zie [Azure ExpressRoute of een UDR](#route)gebruiken als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt. 

- Zorg ervoor dat de brongroep van het virtuele netwerk (of de brongroep openbare IP-adressen als u uw eigen openbare IP-adressen meeneemt) bepaalde Azure-netwerkbronnen kan maken en verwijderen. Zie [De resourcegroep instellen](#resource-group)voor meer informatie . 

- Als u uw Azure-SSIS IR aanpast zoals beschreven in [de aangepaste installatie voor Azure-SSIS IR,](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)krijgen uw Azure-SSIS IR-knooppunten privé-IP-adressen van een vooraf gedefinieerd bereik van 172.16.0.0 tot 172.31.255.255. Zorg er dus voor dat de privé-IP-adresbereiken van uw virtuele of on-premises netwerken niet botsen met dit bereik.

In dit diagram worden de vereiste verbindingen voor uw Azure-SSIS IR weergegeven:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>Machtigingen instellen

De gebruiker die azure-SSIS IR maakt, moet de volgende machtigingen hebben:

- Als u uw SSIS IR aansluit bij een virtueel Azure Resource Manager-netwerk, hebt u twee opties:

  - Gebruik de ingebouwde rol netwerkbijdrager. Deze rol wordt geleverd met de _Microsoft.Network\* /_ toestemming, die een veel groter bereik dan nodig heeft.

  - Maak een aangepaste rol die alleen de benodigde _Microsoft.Network/virtualNetworks/\*/join/action-toestemming_ bevat. Als u ook uw eigen openbare IP-adressen voor Azure-SSIS IR wilt toevoegen terwijl u deze wilt samenvoegen tot een virtueel Azure Resource Manager-netwerk, u ook _Microsoft.Network/publicIPAddresses/*/join/action-machtigingen_ in de rol opnemen.

- Als u uw SSIS IR aansluit bij een klassiek virtueel netwerk, raden we u aan de ingebouwde functie voor virtuele apparaten voor virtuele machines te gebruiken. Anders moet u een aangepaste rol definiëren die de toestemming bevat om lid te worden van het virtuele netwerk.

### <a name="select-the-subnet"></a><a name="subnet"></a>Het subnet selecteren

Als u een subnet kiest: 

- Selecteer het GatewaySubnet niet om een Azure-SSIS IR te implementeren. Het is speciaal voor virtuele netwerkgateways. 

- Controleer of het subnet dat u selecteert voldoende beschikbare adresruimte heeft voor het Azure-SSIS IR om te gebruiken. Laat beschikbare IP-adressen voor ten minste twee keer het IR-knooppuntnummer. Azure reserveert een aantal IP-adressen binnen elk subnet. Deze adressen kunnen niet worden gebruikt. De eerste en laatste IP-adressen van de subnetten zijn gereserveerd voor protocolconformiteit en er worden nog drie adressen gebruikt voor Azure-services. Zie [Zijn er beperkingen voor het gebruik van IP-adressen binnen deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Gebruik geen subnet dat uitsluitend wordt bezet door andere Azure-services (bijvoorbeeld SQL Database-beheerde instantie, App Service, enzovoort). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>De statische openbare IP-adressen selecteren

Als u uw eigen statische openbare IP-adressen voor Azure-SSIS IR wilt meenemen terwijl u deze wilt samenvoegen tot een virtueel netwerk, controleert u of ze voldoen aan de volgende vereisten:

- Precies twee ongebruikte bronnen die nog niet zijn gekoppeld aan andere Azure-bronnen moeten worden verstrekt. De extra wordt gebruikt wanneer we uw Azure-SSIS IR periodiek upgraden. Houd er rekening mee dat één openbaar IP-adres niet kan worden gedeeld tussen uw actieve Azure-SSIS-IRs.

- Ze moeten beide statische degenen van standaard type. Raadpleeg [SKU's van openbaar IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) voor meer informatie.

- Ze moeten allebei een DNS-naam hebben. Als u geen DNS-naam hebt opgegeven bij het maken ervan, u dit doen op azure-portal.

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Zij en het virtuele netwerk moeten onder hetzelfde abonnement en in dezelfde regio.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>De DNS-server instellen 
Als u uw eigen DNS-server moet gebruiken in een virtueel netwerk dat is verbonden door uw Azure-SSIS IR om uw eigen hostnaam op te lossen, moet u ervoor zorgen dat deze ook globale Azure-hostnamen kan oplossen (bijvoorbeeld een Azure Storage-blob met de naam `<your storage account>.blob.core.windows.net`). 

Een aanbevolen aanpak is hieronder: 

-   Configureer de aangepaste DNS om aanvragen door te sturen naar Azure DNS. U onopgeloste DNS-records doorsturen naar het IP-adres van de Azure-recursieve resolvers (168.63.129.16) op uw eigen DNS-server. 

Zie [Naamomzetting die uw eigen DNS-server gebruikt](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)voor meer informatie. 

> [!NOTE]
> Gebruik een volledig gekwalificeerde domeinnaam (FQDN) voor de naam van `<your_private_server>.contoso.com` uw `<your_private_server>`privé-host, bijvoorbeeld gebruik in plaats van , omdat Azure-SSIS IR uw eigen DNS-achtervoegsel niet automatisch toevoegt.

### <a name="set-up-an-nsg"></a><a name="nsg"></a>Een NSG instellen
Als u een NSG moet implementeren voor het subnet dat wordt gebruikt door uw Azure-SSIS IR, u binnenkomend en uitgaand verkeer toestaan via de volgende poorten: 

-   **Binnenkomende vereiste van Azure-SSIS IR**

| Richting | Transportprotocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel | Opmerkingen |
|---|---|---|---|---|---|---|
| Inkomend | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (als u lid wordt van de IR bij een virtueel netwerk van Resource Manager) <br/><br/>10100, 20100, 30100 (als u lid wordt van de IR tot een klassiek virtueel netwerk)| De Service Data Factory gebruikt deze poorten om te communiceren met de knooppunten van uw Azure-SSIS IR in het virtuele netwerk. <br/><br/> Of u nu een NSG op subnetniveau maakt of niet, Data Factory configureert altijd een NSG op het niveau van de netwerkinterfacekaarten (NIC's) die zijn gekoppeld aan de virtuele machines die de Azure-SSIS IR hosten. Alleen binnenkomend verkeer van IP-adressen van Data Factory op de opgegeven poorten is toegestaan door dat NSG op NIC-niveau. Zelfs als u deze poorten opent voor internetverkeer op subnetniveau, wordt verkeer van IP-adressen die geen IP-adressen van de Data Factory zijn, geblokkeerd op NIC-niveau. |
| Inkomend | TCP | CorpNetSaw (CorpNetSaw) | * | VirtualNetwork | 3389 | (Optioneel) Deze regel is alleen vereist wanneer Microsoft-supporter de klant vraagt om open te staan voor geavanceerde probleemoplossing en kan direct na het oplossen van problemen worden gesloten. **CorpNetSaw-servicetag** maakt het alleen mogelijk om werkstations met beveiligde toegang op het bedrijfsnetwerk van Microsoft te gebruiken voor extern bureaublad. En deze servicetag kan niet worden geselecteerd uit portal en is alleen beschikbaar via Azure PowerShell of Azure CLI. <br/><br/> Op NIC-niveau NSG is poort 3389 standaard geopend en u poort 3389 op subnetniveau NSG bedienen, ondertussen heeft Azure-SSIS IR poort 3389 standaard uitgeschakeld bij de Windows Firewall-regel op elk IR-knooppunt voor bescherming. |
||||||||

-   **Uitgaande vereiste van Azure-SSIS IR**

| Richting | Transportprotocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel | Opmerkingen |
|---|---|---|---|---|---|---|
| Uitgaand | TCP | VirtualNetwork | * | AzureCloud | 443 | De knooppunten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort om toegang te krijgen tot Azure-services, zoals Azure Storage en Azure Event Hubs. |
| Uitgaand | TCP | VirtualNetwork | * | Internet | 80 | (Optioneel) De knooppunten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort om een certificaatintrekkingslijst van het internet te downloaden. Als u dit verkeer blokkeert, u prestatiedowngrade ervaren bij het starten van IR en de mogelijkheid verliezen om de certificaatintrekkingslijst te controleren op certificaatgebruik. Als u de bestemming voor bepaalde FQDN's verder wilt beperken, raadpleegt u **Azure ExpressRoute of udr-sectie gebruiken**|
| Uitgaand | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | (Optioneel) Deze regel is alleen vereist wanneer de knooppunten van uw Azure-SSIS IR in het virtuele netwerk toegang krijgen tot een SSISDB die wordt gehost door uw SQL Database-server. Als uw SQL Database-serververbindingsbeleid is ingesteld op **Proxy** in plaats van **Redirect,** is alleen poort 1433 nodig. <br/><br/> Deze uitgaande beveiligingsregel is niet van toepassing op een SSISDB die wordt gehost door uw beheerde instantie in het virtuele netwerk of azure databaseserver dat is geconfigureerd met een privéeindpunt. |
| Uitgaand | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Optioneel) Deze regel is alleen vereist wanneer de knooppunten van uw Azure-SSIS IR in het virtuele netwerk toegang krijgen tot een SSISDB die wordt gehost door uw beheerde instantie in het virtuele netwerk of Azure Database-server geconfigureerd met privéeindpunt. Als uw SQL Database-serververbindingsbeleid is ingesteld op **Proxy** in plaats van **Redirect,** is alleen poort 1433 nodig. |
| Uitgaand | TCP | VirtualNetwork | * | Storage | 445 | (Optioneel) Deze regel is alleen vereist wanneer u het SSIS-pakket wilt uitvoeren dat is opgeslagen in Azure-bestanden. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Azure ExpressRoute of UDR gebruiken
Als u uitgaand verkeer van Azure-SSIS IR wilt inspecteren, u het gestarte verkeer van Azure-SSIS IR naar on-premises firewallappliance via [Azure ExpressRoute-forcetunneling](https://azure.microsoft.com/services/expressroute/) (reclame voor een BGP-route, 0.0.0.0/0, naar het virtuele netwerk) of naar Network Virtual Appliance (NVA) als firewall of Azure [Firewall](https://docs.microsoft.com/azure/firewall/) via [UDr's routeren.](../virtual-network/virtual-networks-udr-overview.md) 

![NVA-scenario voor Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Je moet onder dingen doen om het hele scenario te laten werken
   -   Binnenkomend verkeer tussen Azure Batch-beheerservices en de Azure-SSIS IR kan niet worden omgeleid via firewall-appliance.
   -   Het firewalltoestel moet uitgaand verkeer toestaan dat vereist is door Azure-SSIS IR.

Binnenkomend verkeer tussen Azure Batch-beheerservices en de Azure-SSIS IR kan niet worden doorgestuurd naar firewall-toestel, anders wordt het verkeer verbroken als gevolg van asymmetrische routeringsproblemen. Routes moeten worden gedefinieerd voor inkomend verkeer, zodat het verkeer kan terugantwoorden op dezelfde manier als het binnenkwam. U specifieke UDR's definiëren om verkeer tussen Azure Batch-beheerservices en azure-SSIS IR te routeren met het volgende hoptype als **internet.**

Als uw Azure-SSIS IR zich `UK South` bijvoorbeeld bevindt en u uitgaand verkeer via Azure Firewall wilt inspecteren, `BatchNodeManagement.UKSouth` krijgt u eerst een IP-bereiklijst met servicetag van de downloadkoppeling van het [IP-bereik](https://www.microsoft.com/download/details.aspx?id=56519) van de servicetags of via de [Api voor detectie van servicetags.](https://aka.ms/discoveryapi) Pas vervolgens de volgende UDR's van gerelateerde IP-bereikroutes toe met het volgende hoptype als **internet,** samen met de 0.0.0.0/0-route met het volgende hoptype als **virtueel toestel**.

![UDR-instellingen voor Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Deze aanpak brengt extra onderhoudskosten met zich mee. Controleer regelmatig het IP-bereik en voeg nieuwe IP-bereiken toe aan uw UDR om te voorkomen dat de Azure-SSIS IR wordt gebroken. We raden aan om het IP-bereik maandelijks te controleren, want wanneer het nieuwe IP in de servicetag verschijnt, duurt het IP nog een maand. 

Als u de instelling van UDR-regels eenvoudiger wilt maken, u het volgende Powershell-script uitvoeren om UDR-regels toe te voegen voor Azure Batch-beheerservices:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Als firewalltoestel uitgaand verkeer toestaat, moet u uitgaande poorten toestaan die hetzelfde zijn als de vereisten in nsg-uitgaande regels.
-   Poort 443 met bestemming als Azure Cloud-services.

    Als u Azure Firewall gebruikt, u de netwerkregel opgeven met AzureCloud-servicetag. Voor firewall van de andere typen u eenvoudig bestemming toestaan als alle voor poort 443 of onder FQDN's toestaan op basis van het type azure-omgeving:

    | Azure-omgeving | Eindpunten                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Openbare Azure-peering      | <ul><li><b>Azure-gegevensfabriek (beheer)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (Beheer)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure-containerregister (aangepaste installatie)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Gebeurtenishub (logboekregistratie)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Microsoft Logging-service (intern gebruik)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure-gegevensfabriek (beheer)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (Beheer)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure-containerregister (aangepaste installatie)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Gebeurtenishub (logboekregistratie)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft Logging-service (intern gebruik)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure-gegevensfabriek (beheer)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (Beheer)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure-containerregister (aangepaste installatie)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Gebeurtenishub (logboekregistratie)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft Logging-service (intern gebruik)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Wat betreft de FQDN's van Azure Storage, Azure Container Registry en Event Hub, u er ook voor kiezen om de volgende serviceeindpunten voor uw virtuele netwerk in te schakelen, zodat netwerkverkeer naar deze eindpunten via azure-backbonenetwerk verloopt in plaats van naar uw firewalltoestel te worden doorgestuurd:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Poort 80 met bestemming als CRL-downloadsites.

    U moet onderstaande FQDN's toestaan die worden gebruikt als CRL-downloadsites (Certificate Revocation List) van certificaten voor Azure-SSIS IR-beheerdoel:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Als u certificaten met verschillende CRL gebruikt, wordt u voorgesteld om ze ook op te nemen. U dit lezen om meer te weten te komen over [de certificate revocation list](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Als u dit verkeer niet toestaan, u prestatiedowngrade ervaren bij het starten van Azure-SSIS IR en de mogelijkheid verliezen om de certificaatintrekkingslijst te controleren op certificaatgebruik, wat vanuit beveiligingsoogpunt niet wordt aanbevolen.

-   Poort 1433, 11000-11999 met bestemming als Azure SQL (alleen vereist wanneer de knooppunten van uw Azure-SSIS IR in het virtuele netwerk toegang krijgen tot een SSISDB die wordt gehost door uw SQL Database-server).

    Als u Azure Firewall gebruikt, u netwerkregel opgeven met Azure SQL Service Tag, anders u de bestemming toestaan als specifieke azure sql-url in firewall-toestel.

-   Poort 445 met bestemming als Azure Storage (alleen vereist wanneer u SSIS-pakket uitvoert dat is opgeslagen in Azure-bestanden).

    Als u Azure Firewall gebruikt, u netwerkregel opgeven met Storage Service Tag, anders u de bestemming toestaan als specifieke url voor azure-bestandsopslag in firewall-toestel.

> [!NOTE]
> Als u voor Azure SQL en Storage eindpunten van de Virtual Network-service configureert op uw subnet, wordt het verkeer tussen Azure-SSIS IR en Azure SQL in dezelfde regio \ Azure Storage in dezelfde regio of gekoppelde regio rechtstreeks doorgestuurd naar het Microsoft Azure-backbonenetwerk in plaats van naar uw firewalltoestel.

Als u geen mogelijkheid nodig hebt om uitgaand verkeer van Azure-SSIS IR te inspecteren, u eenvoudig een route toepassen om al het verkeer naar het volgende hoptype **Internet**te forceren:

-   In een Azure ExpressRoute-scenario u een route van 0.0.0.0/0 met het volgende hoptype toepassen als **internet** op het subnet dat de Azure-SSIS IR host. 
-   In een NVA-scenario u de bestaande route 0.0.0.0/0 wijzigen die is toegepast op het subnet waarop azure-SSIS IR wordt geïnstalleerd van het volgende hoptype als **Virtueel toestel** naar **internet.**

![Een route toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Geef route op met het volgende hoptype **Internet** betekent niet dat al het verkeer via internet gaat. Zolang het doeladres voor een van de services van Azure is, leidt Azure het verkeer rechtstreeks naar de service via het backbone-netwerk van Azure, in plaats van het verkeer naar internet te routeren.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>De resourcegroep instellen

De Azure-SSIS IR moet bepaalde netwerkbronnen maken onder dezelfde brongroep als het virtuele netwerk. Deze bronnen omvatten:
- Een Azure load balancer, met de naam * \<Guid>-azurebatch-cloudserviceloadbalancer*.
- Een Openbaar IP-adres in Azure, met de naam * \<Guid>-azurebatch-cloudservicepublicip*.
- Een netwerkwerkgroep met de naam * \<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> U nu uw eigen statische openbare IP-adressen voor Azure-SSIS IR meenemen. In dit scenario maken we alleen de azure load balancer en netwerkbeveiligingsgroep onder dezelfde resourcegroep als uw statische openbare IP-adressen in plaats van het virtuele netwerk.

Deze bronnen worden gemaakt wanneer uw Azure-SSIS IR wordt gestart. Ze worden verwijderd wanneer uw Azure-SSIS IR stopt. Als u uw eigen statische openbare IP-adressen voor Azure-SSIS IR meeneemt, worden uw eigen statische openbare IP-adressen niet verwijderd wanneer uw Azure-SSIS IR stopt. Als u wilt voorkomen dat uw Azure-SSIS IR wordt geblokkeerd, u deze netwerkbronnen niet opnieuw gebruiken in uw andere bronnen.

Zorg ervoor dat u geen resourcevergrendeling hebt op de brongroep/het abonnement waartoe het virtuele netwerk/uw statische openbare IP-adressen behoren. Als u een vergrendelbericht/verwijdervergrendeling voor lezen configureert, mislukt en stopt u uw Azure-SSIS IR of reageert deze niet meer.

Zorg ervoor dat u geen Azure-beleid hebt dat voorkomt dat de volgende resources worden gemaakt onder de brongroep/abonnement waartoe het virtuele netwerk/uw statische openbare IP-adressen behoren: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Zorg ervoor dat het resourcequotum van uw abonnement voldoende is voor de bovenstaande drie netwerkbronnen. Specifiek voor elke Azure-SSIS IR die in virtueel netwerk is gemaakt, moet u twee gratis quota reserveren voor elk van de bovenstaande drie netwerkbronnen. Het extra quotum wordt gebruikt wanneer we uw Azure-SSIS IR periodiek upgraden.

### <a name="faq"></a><a name="faq"></a>FAQ

- Hoe kan ik het openbare IP-adres beschermen dat op mijn Azure-SSIS IR wordt weergegeven voor inkomende verbinding? Is het mogelijk om het openbare IP-adres te verwijderen?
 
  Op dit moment wordt automatisch een openbaar IP-adres gemaakt wanneer uw Azure-SSIS IR lid wordt van een virtueel netwerk. We hebben wel een NSG op NIC-niveau om alleen Azure Batch-beheerservices toe te staan om inbound-connect te maken met uw Azure-SSIS IR. U ook een NSG op subnetniveau opgeven voor binnenkomende bescherming.

  Als u niet wilt dat een openbaar IP-adres wordt blootgesteld, u [overwegen een zelf gehoste IR te configureren als proxy voor uw Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) in plaats van uw Azure-SSIS IR toe te voegen aan een virtueel netwerk, als dit van toepassing is op uw scenario.
 
- Kan ik het openbare IP-adres van mijn Azure-SSIS IR toevoegen aan de lijst met toegestane firewalls voor mijn gegevensbronnen?

  U nu uw eigen statische openbare IP-adressen voor Azure-SSIS IR meenemen. In dit geval u uw IP-adressen toevoegen aan de lijst met toegestane gegevens van de firewall voor uw gegevensbronnen. U hieronder ook andere opties overwegen om gegevenstoegang vanuit uw Azure-SSIS IR te beveiligen, afhankelijk van uw scenario:

  - Als uw gegevensbron on-premises is, u, nadat u een virtueel netwerk hebt aangesloten op uw on-premises netwerk en uw Azure-SSIS IR hebt aangesloten bij het subnet van het virtuele netwerk, het privé-IP-adresbereik van dat subnet toevoegen aan de lijst met toestaan van de firewall voor uw gegevensbron.
  - Als uw gegevensbron een Azure-service is die eindpunten voor virtuele netwerkservices ondersteunt, u een eindpunt voor virtuele netwerkservice configureren op uw virtuele netwerksubnet en uw Azure-SSIS IR aansluiten bij dat subnet. U vervolgens een virtuele netwerkregel met dat subnet toevoegen aan de firewall voor uw gegevensbron.
  - Als uw gegevensbron een niet-Azure-cloudservice is, u een UDR gebruiken om uitgaand verkeer van uw Azure-SSIS IR naar een NVA/Azure Firewall te routeren via een statisch openbaar IP-adres. Vervolgens u het statische openbare IP-adres van uw NVA/Azure Firewall toevoegen aan de lijst met toestaan van de firewall voor uw gegevensbron.
  - Als geen van de bovenstaande opties aan uw behoeften voldoet, u [overwegen een zelf gehoste IR te configureren als proxy voor uw Azure-SSIS IR.](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) U vervolgens het statische openbare IP-adres van de machine dat uw zelfgehoste IR host, toevoegen aan de lijst met toestaan van de firewall voor uw gegevensbron.

- Waarom moet ik twee statische openbare adressen verstrekken als ik mijn eigen adressen wil meenemen voor Azure-SSIS IR?

  Azure-SSIS IR wordt regelmatig automatisch bijgewerkt. Nieuwe knooppunten worden gemaakt tijdens de upgrade en oude worden verwijderd. Om downtime te voorkomen, worden de oude knooppunten echter pas verwijderd als de nieuwe knooppunten klaar zijn. Zo kan uw eerste statische openbare IP-adres dat door de oude knooppunten wordt gebruikt, niet onmiddellijk worden vrijgegeven en hebben we uw tweede statische openbare IP-adres nodig om de nieuwe knooppunten te maken.

- Ik heb mijn eigen statische openbare IP-adressen voor Azure-SSIS IR meegebracht, maar waarom heeft het nog steeds geen toegang tot mijn gegevensbronnen?

  - Controleer of de twee statische openbare IP-adressen beide worden toegevoegd aan de lijst met toegestane gegevens van de firewall voor uw gegevensbronnen. Elke keer dat uw Azure-SSIS IR wordt geüpgraded, wordt het statische openbare IP-adres geschakeld tussen de twee die door u zijn meegenomen. Als u er slechts één toevoegt aan de lijst met toegestane gegevens, wordt de toegang tot gegevens voor uw Azure-SSIS IR na de upgrade verbroken.
  - Als uw gegevensbron een Azure-service is, controleert u of u deze hebt geconfigureerd met eindpunten voor virtuele netwerkservices. Als dat het geval is, wordt het verkeer van Azure-SSIS IR naar uw gegevensbron overgeschakeld naar de privé-IP-adressen die door Azure-services worden beheerd en wordt het toevoegen van uw eigen statische openbare IP-adressen aan de lijst met toestaan van de firewall voor uw gegevensbron niet van kracht.

## <a name="azure-portal-data-factory-ui"></a>Azure-portal (Gebruikersinterface voor gegevensfabriek)

In deze sectie ziet u hoe u een bestaand Azure-SSIS IR aansluiten bij een virtueel netwerk (klassiek of Azure Resource Manager) met behulp van de Azure-portal en de gebruikersinterface van de gegevensfabriek. 

Voordat u uw Azure-SSIS IR aansluit bij het virtuele netwerk, moet u het virtuele netwerk correct configureren. Volg de stappen in de sectie die van toepassing is op uw type virtueel netwerk (klassiek of Azure Resource Manager). Volg vervolgens de stappen in het derde gedeelte om uw Azure-SSIS IR aan het virtuele netwerk te voegen. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Een virtueel Azure Resource Manager-netwerk configureren

Gebruik de portal om een virtueel Azure Resource Manager-netwerk te configureren voordat u probeert er een Azure-SSIS IR aan toe te voegen.

1. Microsoft Edge of Google Chrome starten. Momenteel ondersteunen alleen deze webbrowsers de Gebruikersinterface van Data Factory. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

1. Selecteer **Meer services**. Filter voor en selecteer **Virtuele netwerken**. 

1. Filter voor en selecteer uw virtuele netwerk in de lijst. 

1. Selecteer **eigenschappen**op de pagina **Virtueel netwerk** . 

1. Selecteer de kopieerknop voor **RESOURCE-id** om de bron-id voor het virtuele netwerk naar het klembord te kopiëren. Sla de id op vanaf het klembord in OneNote of een bestand. 

1. Selecteer **Subnetten**in het linkermenu . Controleer of het aantal beschikbare adressen groter is dan de knooppunten in uw Azure-SSIS IR. 

1. Controleer of de Azure Batch-provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of registreer de Azure Batch-provider. Als u al een Azure Batch-account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u azure-SSIS IR maakt in de portal Gegevensfabriek, wordt de Azure Batch-provider automatisch voor u geregistreerd.) 

   1. Selecteer In de Azure-portal in het linkermenu de optie **Abonnementen**. 

   1. Selecteer uw abonnement. 

   1. Selecteer aan de linkerkant **Resourceproviders**en bevestig dat **Microsoft.Batch** een geregistreerde provider is. 

   ![Bevestiging van de status "Geregistreerd"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Als u **Microsoft.Batch** niet in de lijst ziet, maakt [u een leeg Azure Batch-account](../batch/batch-account-create-portal.md) in uw abonnement om deze te registreren. U het later verwijderen. 

### <a name="configure-a-classic-virtual-network"></a>Een klassiek virtueel netwerk configureren

Gebruik de portal om een klassiek virtueel netwerk te configureren voordat u probeert er een Azure-SSIS IR bij aan te voegen. 

1. Microsoft Edge of Google Chrome starten. Momenteel ondersteunen alleen deze webbrowsers de Gebruikersinterface van Data Factory. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

1. Selecteer **Meer services**. Filter voor en selecteer **Virtuele netwerken (klassiek)**. 

1. Filter voor en selecteer uw virtuele netwerk in de lijst. 

1. Selecteer Eigenschappen op de pagina **Virtueel netwerk (klassiek)** de optie **Eigenschappen**. 

   ![Klassieke virtuele netwerkbron-id](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecteer de kopieerknop voor **RESOURCE-id** om de bron-id voor het klassieke netwerk naar het klembord te kopiëren. Sla de id op vanaf het klembord in OneNote of een bestand. 

1. Selecteer **Subnetten**in het linkermenu . Controleer of het aantal beschikbare adressen groter is dan de knooppunten in uw Azure-SSIS IR. 

   ![Aantal beschikbare adressen in het virtuele netwerk](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Word lid **van MicrosoftAzureBatch** met de rol **Klassieke virtuele machinebijdrager** voor het virtuele netwerk. 

   1. Selecteer in het linkermenu **Het toegangsbesturingselement (IAM)** en selecteer het tabblad **Toewijzingen van rollen.** 

       ![Knoppen 'Toegangsbeheer' en 'Toevoegen'](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selecteer **Roltoewijzing toevoegen**.

   1. Selecteer op de pagina **Roltoewijzing toevoegen** voor **Rol**de optie **Klassieke virtuele machinebijdrager**. Plak in het vak **Selecteren** **ddbf3205-c6bd-46ae-8127-60eb93363864**en selecteer **vervolgens Microsoft Azure Batch** in de lijst met zoekresultaten. 

       ![Zoekresultaten op de pagina Roltoewijzing toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Selecteer **Opslaan** om de instellingen op te slaan en sluit de pagina. 

       ![Toegangsinstellingen opslaan](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Controleer of u **Microsoft Azure Batch** ziet in de lijst met bijdragers. 

       ![Azure Batch-toegang bevestigen](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Controleer of de Azure Batch-provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of registreer de Azure Batch-provider. Als u al een Azure Batch-account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u azure-SSIS IR maakt in de portal Gegevensfabriek, wordt de Azure Batch-provider automatisch voor u geregistreerd.) 

   1. Selecteer In de Azure-portal in het linkermenu de optie **Abonnementen**. 

   1. Selecteer uw abonnement. 

   1. Selecteer aan de linkerkant **Resourceproviders**en bevestig dat **Microsoft.Batch** een geregistreerde provider is. 

   ![Bevestiging van de status "Geregistreerd"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Als u **Microsoft.Batch** niet in de lijst ziet, maakt [u een leeg Azure Batch-account](../batch/batch-account-create-portal.md) in uw abonnement om deze te registreren. U het later verwijderen. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Word lid van azure-SSIS IR naar een virtueel netwerk

Nadat u uw azure resource manager virtueel netwerk of klassiek virtueel netwerk hebt geconfigureerd, u de Azure-SSIS IR aansluiten bij het virtuele netwerk:

1. Microsoft Edge of Google Chrome starten. Momenteel ondersteunen alleen deze webbrowsers de Gebruikersinterface van Data Factory. 

1. Selecteer **gegevensfabrieken**in de [Azure-portal](https://portal.azure.com)in het linkermenu . Als u **gegevensfabrieken** niet in het menu ziet, selecteert u **Meer services**en selecteert u vervolgens in de sectie INTELLIGENTIE **+ ANALYTICS** **gegevensfabrieken**. 

   ![Lijst van gegevensfabrieken](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecteer uw gegevensfabriek met de Azure-SSIS IR in de lijst. U ziet de startpagina van uw gegevensfabriek. Selecteer de tegel **Auteur & Monitor.** U ziet de gebruikersinterface van de gegevensfabriek op een apart tabblad. 

   ![Startpagina van de gegevensfactory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Schakel in de gebruikersinterface van de gegevensfabriek over naar het tabblad **Bewerken,** selecteer **Verbindingen**en schakel over naar het tabblad **Runtimes voor integratie.** 

   ![Tabblad 'Inburgeringslooptijden'](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Als uw Azure-SSIS IR wordt uitgevoerd, selecteert u in de lijst **Integratieruntimes** in de kolom **Acties** de knop **Stoppen** voor uw Azure-SSIS IR. U uw Azure-SSIS IR pas bewerken als u deze stopt. 

   ![Stop de IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Selecteer in de lijst **Programma's voor integratie** in de kolom **Acties** de knop **Bewerken** voor uw Azure-SSIS IR. 

   ![De runtime voor integratie bewerken](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Ga in het installatiepaneel voor de instelling van de integratieruntime door de secties **Algemene instellingen** en **SQL-instellingen** door de knop **Volgende te** selecteren. 

1. Ga als een **opdeandere sectie Geavanceerde instellingen:** 

   1. Schakel **het Selectievakje Een VNet selecteren voor uw Runtime Azure-SSIS-integratie om lid te worden, ADF in staat te stellen bepaalde netwerkbronnen te maken en optioneel het selectievakje Eigen statische openbare IP-adressen mee** te nemen. 

   1. Selecteer **bij Abonnement**het Azure-abonnement met uw virtuele netwerk.

   1. Voor **Locatie**is dezelfde locatie van uw intredetijd voor integratie geselecteerd.

   1. Selecteer **bij Type**het type van uw virtuele netwerk: klassiek of Azure Resource Manager. We raden u aan een virtueel Azure Resource Manager-netwerk te selecteren, omdat klassieke virtuele netwerken binnenkort worden afgeschaft.

   1. Selecteer voor **VNet-naam**de naam van uw virtuele netwerk. Het moet dezelfde zijn die wordt gebruikt voor uw Azure SQL Database-server met eindpunten voor virtuele netwerkservices of beheerde instantie met privéeindpunt om SSISDB te hosten. Of het moet dezelfde zijn die is aangesloten op uw on-premises netwerk. Anders kan het elk virtueel netwerk zijn om uw eigen statische openbare IP-adressen voor Azure-SSIS IR mee te nemen.

   1. Selecteer **bij Subnet Name**de naam van subnet voor uw virtuele netwerk. Het moet dezelfde zijn die wordt gebruikt voor uw Azure SQL Database-server met eindpunten voor virtuele netwerkservice om SSISDB te hosten. Of het moet een ander subnet zijn dan het subnet dat wordt gebruikt voor uw beheerde instantie met privéeindpunt om SSISDB te hosten. Anders kan het elk subnet zijn om uw eigen statische openbare IP-adressen voor Azure-SSIS IR mee te nemen.

   1. Schakel het selectievakje **Statische openbare IP-adressen voor uw Azure-SSIS-integratieruntime meenemen** in om te kiezen of u uw eigen statische openbare IP-adressen wilt meenemen voor Azure-SSIS IR, zodat u ze op de firewall voor uw gegevensbronnen toestaan.

      Voer de volgende stappen uit als u het selectievakje inschakelt.

      1. Selecteer **voor het eerste statische openbare IP-adres**het eerste statische openbare IP-adres dat voldoet aan de [vereisten](#publicIP) voor uw Azure-SSIS IR. Als u geen nieuwe koppeling hebt, klikt u op Nieuwe koppeling **maken** om statische openbare IP-adressen op azure-portal te maken en klik vervolgens hier op de vernieuwingsknop, zodat u ze selecteren.
      
      1. Selecteer **voor tweede statische openbare IP-adres**het tweede statische openbare IP-adres dat voldoet aan de [vereisten](#publicIP) voor uw Azure-SSIS IR. Als u geen nieuwe koppeling hebt, klikt u op Nieuwe koppeling **maken** om statische openbare IP-adressen op azure-portal te maken en klik vervolgens hier op de vernieuwingsknop, zodat u ze selecteren.

   1. Selecteer **VNet-validatie**. Als de validatie is geslaagd, selecteert u **Doorgaan**. 

   ![Geavanceerde instellingen met een virtueel netwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Bekijk **in** de sectie Overzicht alle instellingen voor uw Azure-SSIS IR. Selecteer vervolgens **Bijwerken**.

1. Start uw Azure-SSIS IR door de knop **Start** te selecteren in de kolom **Acties** voor uw Azure-SSIS IR. Het duurt ongeveer 20 tot 30 minuten om de Azure-SSIS IR te starten die lid wordt van een virtueel netwerk. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>De variabelen definiëren

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Een virtueel netwerk configureren

Voordat u uw Azure-SSIS IR aansluiten bij een virtueel netwerk, moet u het virtuele netwerk configureren. Als u automatisch virtuele netwerkmachtigingen en -instellingen wilt configureren voor uw Azure-SSIS IR om deel te nemen aan het virtuele netwerk, voegt u het volgende script toe:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Een Azure-SSIS IR maken en deze aansluiten bij een virtueel netwerk

U een Azure-SSIS IR maken en deze tegelijkertijd aansluiten bij een virtueel netwerk. Zie [Een Azure-SSIS IR maken](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)voor het volledige script en de volledige instructies.

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Een bestaand Azure-SSIS IR aansluiten bij een virtueel netwerk

In [het artikel Maken van een Azure-SSIS IR](create-azure-ssis-integration-runtime.md) ziet u hoe u een Azure-SSIS IR maakt en deze aansluit bij een virtueel netwerk in hetzelfde script. Als u al een Azure-SSIS IR hebt, voert u de volgende stappen uit om deze samen te voegen met het virtuele netwerk: 
1. Stop de Azure-SSIS IR. 
1. Configureer azure-SSIS IR om lid te worden van het virtuele netwerk. 
1. Start de Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>De Azure-SSIS IR stoppen

U moet de Azure-SSIS IR stoppen voordat u deze aansluiten bij een virtueel netwerk. Met deze opdracht worden alle knooppunten vrijgegeven en wordt facturering gestopt:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Virtuele netwerkinstellingen configureren voor de Azure-SSIS IR om lid te worden

Als u instellingen wilt configureren voor het virtuele netwerk waaraan het Azure-SSIS wordt verbonden, gebruikt u dit script: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>De Azure-SSIS IR configureren

Voer de `Set-AzDataFactoryV2IntegrationRuntime` opdracht uit om uw Azure-SSIS IR aan een virtueel netwerk te voegen: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>De Azure-SSIS IR starten

Voer de volgende opdracht uit om de Azure-SSIS IR te starten: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Deze opdracht duurt 20 tot 30 minuten om te voltooien.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure-SSIS IR: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dit artikel bevat algemene conceptuele informatie over IRs, waaronder Azure-SSIS IR. 
- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). Deze zelfstudie bevat stapsgewijze instructies voor het maken van uw Azure-SSIS IR. Het maakt gebruik van Azure SQL Database om de SSIS-catalogus te hosten. 
- [Maak een Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dit artikel wordt uitgebreid over de tutorial. Het biedt instructies over het gebruik van Azure SQL Database met eindpunten voor virtuele netwerkservices of beheerde instantie in een virtueel netwerk om de SSIS-catalogus te hosten. Het laat zien hoe u uw Azure-SSIS IR aansluiten bij een virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel ziet u hoe u informatie over uw Azure-SSIS IR krijgen. Het biedt statusbeschrijvingen voor de geretourneerde informatie. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel ziet u hoe u uw Azure-SSIS IR stoppen, starten of verwijderen. Het laat u ook zien hoe u uw Azure-SSIS IR schalen door knooppunten toe te voegen.