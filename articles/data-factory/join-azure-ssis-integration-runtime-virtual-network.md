---
title: Een Azure-SSIS-integratie-runtime toevoegen aan een virtueel netwerk
description: Meer informatie over hoe u een Azure-SSIS-integratie-runtime kunt koppelen aan een virtueel Azure-netwerk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: a4b0debc712504e8cb3c6d61372bd3a82c7932bb
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497035"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Een Azure-SSIS-integratie-runtime toevoegen aan een virtueel netwerk

Wanneer u SQL Server Integration Services (SSIS) in Azure Data Factory gebruikt, moet u uw Azure-SSIS Integration runtime (IR) koppelen aan een virtueel Azure-netwerk in de volgende scenario's:

- U wilt verbinding maken met on-premises gegevens archieven uit SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR zonder een zelf-hostende IR te hoeven configureren of beheren als proxy. 

- U wilt de SSIS-catalogus database (SSISDB) hosten in een Azure SQL Database met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt. 

- U wilt verbinding maken met Azure-resources die zijn geconfigureerd met virtuele netwerk service-eind punten van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR.

- U wilt verbinding maken met gegevens archieven/-resources die zijn geconfigureerd met IP-firewall regels van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR.

Met Data Factory kunt u uw Azure-SSIS IR toevoegen aan een virtueel netwerk dat is gemaakt via het klassieke implementatie model of het implementatie model van Azure Resource Manager. 

> [!IMPORTANT]
> Het klassieke virtuele netwerk wordt afgeschaft. gebruik in plaats daarvan het Azure Resource Manager virtuele netwerk.  Als u het klassieke virtuele netwerk al gebruikt, schakelt u zo snel mogelijk over naar het Azure Resource Manager virtuele netwerk.

## <a name="access-to-on-premises-data-stores"></a>Toegang tot on-premises gegevens archieven

Als uw SSIS-pakketten toegang hebben tot on-premises gegevens opslag, kunt u uw Azure-SSIS IR koppelen aan een virtueel netwerk dat is verbonden met het on-premises netwerk. U kunt ook een zelf-hostende IR configureren en beheren als proxy voor uw Azure-SSIS IR. Zie [een zelf-hostende IR configureren als proxy voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)voor meer informatie. 

Onthoud de volgende belang rijke punten wanneer u uw Azure-SSIS IR lid maakt van een virtueel netwerk: 

- Als er geen virtueel netwerk is verbonden met uw on-premises netwerk, moet u eerst een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken om uw Azure-SSIS IR te koppelen. Configureer vervolgens een site-naar-site [-VPN-gateway verbinding](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) of een [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) -verbinding van het virtuele netwerk naar uw on-premises netwerk. 

- Als een Azure Resource Manager virtueel netwerk al is verbonden met uw on-premises netwerk op dezelfde locatie als uw Azure-SSIS IR, kunt u de IR toevoegen aan het virtuele netwerk. 

- Als er al een klassiek virtueel netwerk is verbonden met uw on-premises netwerk op een andere locatie dan uw Azure-SSIS IR, kunt u een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken om aan uw Azure-SSIS IR deel te nemen. Configureer vervolgens een [klassieke-naar-Azure Resource Manager virtuele netwerk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) verbinding. 
 
- Als er al een Azure Resource Manager virtueel netwerk is verbonden met uw on-premises netwerk op een andere locatie dan uw Azure-SSIS IR, kunt u eerst een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md##create-a-virtual-network) maken om uw Azure-SSIS IR te koppelen. Configureer vervolgens een Azure Resource Manager-to-Azure Resource Manager virtuele netwerk verbinding. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>De SSIS-catalogus in SQL Database hosten

Als u uw SSIS-catalogus host in een Azure SQL Database met virtuele netwerk service-eind punten, moet u uw Azure-SSIS IR toevoegen aan hetzelfde virtuele netwerk en subnet.

Als u uw SSIS-catalogus host in een beheerd exemplaar met een persoonlijk eind punt, moet u ervoor zorgen dat u uw Azure-SSIS IR toevoegt aan hetzelfde virtuele netwerk, maar in een ander subnet dan het beheerde exemplaar. Als u uw Azure-SSIS IR wilt toevoegen aan een ander virtueel netwerk dan het beheerde exemplaar, kunt u het beste de peering van het virtuele netwerk (dit is beperkt tot dezelfde regio) of een verbinding tussen een virtueel netwerk en een virtueel netwerk. Zie [verbinding maken tussen uw toepassing en Azure SQL database Managed instance](../sql-database/sql-database-managed-instance-connect-app.md)(Engelstalig) voor meer informatie.

## <a name="access-to-azure-services"></a>Toegang tot Azure-services

Als uw SSIS pakketten toegang tot Azure-resources die ondersteuning bieden voor [virtuele netwerk service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) en u de toegang tot deze resources wilt beveiligen vanaf Azure-SSIS IR, kunt u uw Azure-SSIS IR toevoegen aan een subnet van een virtueel netwerk dat is geconfigureerd voor eind punten van virtuele netwerken en vervolgens een regel voor het virtuele netwerk toevoegen aan de relevante Azure-resources om toegang vanuit hetzelfde subnet toe te staan.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Toegang tot gegevens bronnen die worden beveiligd door de IP-firewall regel

Als uw SSIS pakketten toegang biedt tot gegevens archieven/resources die alleen specifieke statische open bare IP-adressen toestaan en u de toegang tot deze resources wilt beveiligen vanaf Azure-SSIS IR, kunt u uw eigen [open bare IP-adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) voor Azure-SSIS IR meenemen tijdens het verbinden met een virtueel netwerk en vervolgens een IP-firewall regel toevoegen aan de relevante bronnen om toegang via deze IP-adressen toe te staan.

In alle gevallen kan het virtuele netwerk alleen worden geïmplementeerd via het Azure Resource Manager-implementatie model.

In de volgende secties vindt u meer informatie. 

## <a name="virtual-network-configuration"></a>Configuratie van virtueel netwerk

Stel uw virtuele netwerk in om aan deze vereisten te voldoen: 

- Zorg ervoor dat `Microsoft.Batch` een geregistreerde provider is onder het abonnement van het subnet van het virtuele netwerk dat als host fungeert voor de Azure-SSIS IR. Als u een klassiek virtueel netwerk gebruikt, moet u ook `MicrosoftAzureBatch` toevoegen aan de rol Inzender voor klassieke virtuele machines voor dat virtuele netwerk. 

- Zorg ervoor dat u over de vereiste machtigingen beschikt. Zie [machtigingen instellen](#perms)voor meer informatie.

- Selecteer het juiste subnet om de Azure-SSIS IR te hosten. Zie [het subnet selecteren](#subnet)voor meer informatie. 

- Als u uw eigen open bare IP-adressen voor de Azure-SSIS IR, raadpleegt u [de statische open bare IP-adressen selecteren](#publicIP)

- Als u uw eigen Domain Name System (DNS)-server in het virtuele netwerk gebruikt, raadpleegt u [de DNS-server instellen](#dns_server). 

- Als u een netwerk beveiligings groep (NSG) in het subnet gebruikt, raadpleegt u [een NSG instellen](#nsg). 

- Als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt, raadpleegt u [Azure ExpressRoute of een UDR gebruiken](#route). 

- Zorg ervoor dat de resource groep van het virtuele netwerk (of de resource groep Public IP-adressen als u uw eigen open bare IP-adressen meebrengt) bepaalde Azure-netwerk resources kan maken en verwijderen. Zie [de resource groep instellen](#resource-group)voor meer informatie. 

- Als u uw Azure-SSIS IR aanpast zoals beschreven in de [aangepaste installatie voor Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup), krijgen uw Azure-SSIS IR-knoop punten privé-IP-adressen van een vooraf gedefinieerd bereik van 172.16.0.0 tot 172.31.255.255. Zorg er dus voor dat de privé-IP-adresbereiken van uw virtuele of on-premises netwerken niet conflicteren met dit bereik.

Dit diagram toont de vereiste verbindingen voor uw Azure-SSIS IR:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Machtigingen instellen

De gebruiker die de Azure-SSIS IR maakt, moet de volgende machtigingen hebben:

- Als u uw SSIS-IR koppelt aan een Azure Resource Manager virtueel netwerk, hebt u twee opties:

  - Gebruik de ingebouwde rol netwerk bijdrager. Deze rol wordt geleverd met de machtiging _micro soft. Network/\*_ , die een veel groter bereik heeft dan nodig is.

  - Maak een aangepaste rol die alleen de benodigde _micro soft. Network/virtualNetworks/\*-_ machtiging voor/join/Action bevat. Als u ook uw eigen open bare IP-adressen voor Azure-SSIS IR wilt gebruiken terwijl u deze toevoegt aan een Azure Resource Manager virtueel netwerk, moet u ook de machtiging _micro soft. Network/publicIPAddresses/*/join/Action_ opnemen in de rol.

- Als u uw SSIS-IR aan een klassiek virtueel netwerk koppelt, wordt u aangeraden de ingebouwde rol van de klassieke virtuele machine te gebruiken. Anders moet u een aangepaste rol definiëren die de machtiging bevat om lid te worden van het virtuele netwerk.

### <a name="subnet"></a>Het subnet selecteren

Bij het kiezen van een subnet: 

- Selecteer de GatewaySubnet voor het implementeren van een Azure-SSIS IR niet. Het is toegewezen voor virtuele netwerk gateways. 

- Zorg ervoor dat het door u geselecteerde subnet voldoende beschik bare adres ruimte heeft om de Azure-SSIS IR te gebruiken. Houd de beschik bare IP-adressen voor ten minste twee keer het IR-knooppunt nummer. Azure reserveert een aantal IP-adressen binnen elk subnet. Deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit en er worden nog drie adressen voor Azure-Services gebruikt. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie. 

- Gebruik geen subnet dat uitsluitend wordt ingen Omen door andere Azure-Services (bijvoorbeeld SQL Database beheerde instantie, App Service, enzovoort). 

### <a name="publicIP"></a>De statische open bare IP-adressen selecteren

Als u uw eigen statische open bare IP-adressen voor Azure-SSIS IR wilt meenemen tijdens het toevoegen aan een virtueel netwerk, moet u ervoor zorgen dat ze voldoen aan de volgende vereisten:

- Er moeten precies twee ongebruikte taken worden gegeven die nog niet aan andere Azure-resources zijn gekoppeld. De extra versie wordt gebruikt wanneer uw Azure-SSIS IR regel matig wordt bijgewerkt.

- Ze moeten beide statisch zijn van het standaard type. Raadpleeg de [sku's van het open bare IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) voor meer informatie.

- Ze moeten beide een DNS-naam hebben. Als u geen DNS-naam hebt ingevoerd bij het maken van deze, kunt u dit doen op Azure Portal.

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Ze en het virtuele netwerk moeten zich onder hetzelfde abonnement en in dezelfde regio bevinden.

### <a name="dns_server"></a>De DNS-server instellen 

Als u uw eigen DNS-server moet gebruiken in een virtueel netwerk dat is gekoppeld aan uw Azure-SSIS IR, moet u ervoor zorgen dat de globale namen van Azure-hosts worden omgezet (bijvoorbeeld een Azure Storage-blob met de naam `<your storage account>.blob.core.windows.net`). 

De volgende stappen worden aanbevolen: 

- Configureer de aangepaste DNS-server voor het door sturen van aanvragen naar Azure DNS. U kunt niet-omgezette DNS-records door sturen naar het IP-adres van de recursieve resolvers van Azure (168.63.129.16) op uw eigen DNS-server. 

- Stel de aangepaste DNS in als de primaire DNS-server voor het virtuele netwerk. Azure DNS instellen als de secundaire DNS-server. Registreer het IP-adres van de Azure recursieve resolver (168.63.129.16) als een secundaire DNS-server voor het geval uw eigen DNS-server niet beschikbaar is. 

Zie [naam omzetting die gebruikmaakt van uw eigen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)voor meer informatie. 

### <a name="nsg"></a>Een NSG instellen

Als u een NSG wilt implementeren voor het subnet dat door uw Azure-SSIS IR wordt gebruikt, kunt u inkomend en uitgaand verkeer via de volgende poorten toestaan: 

| Richting | Transport Protocol | Bron | Poortbereik van bron | Bestemming | Poortbereik van doel | Opmerkingen |
|---|---|---|---|---|---|---|
| Inkomend | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (als u de IR koppelt aan een virtueel netwerk van Resource Manager) <br/><br/>10100, 20100, 30100 (als u de IR koppelt aan een klassiek virtueel netwerk)| De Data Factory-service gebruikt deze poorten om te communiceren met de knoop punten van uw Azure-SSIS IR in het virtuele netwerk. <br/><br/> Of u nu een NSG op subnetniveau maakt, Data Factory altijd een NSG configureert op het niveau van de netwerk interface kaarten (Nic's) die zijn gekoppeld aan de virtuele machines die de Azure-SSIS IR hosten. Alleen binnenkomend verkeer van Data Factory IP-adressen op de opgegeven poorten is toegestaan door deze NSG op NIC-niveau. Zelfs als u deze poorten opent op het Internet verkeer op subnetniveau, wordt verkeer van IP-adressen die niet Data Factory IP-adressen worden geblokkeerd op het niveau van de NIC. |
| Uitgaand | TCP | VirtualNetwork | * | AzureCloud | 443 | De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort voor toegang tot Azure-Services, zoals Azure Storage en Azure Event Hubs. |
| Uitgaand | TCP | VirtualNetwork | * | Internet | 80 | De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort om een certificaatintrekkingslijst van Internet te downloaden. |
| Uitgaand | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poorten om toegang te krijgen tot een SSISDB die wordt gehost door uw SQL Database-Server. Als uw SQL Database Server-verbindings beleid is ingesteld op **proxy** in plaats van de **omleiding**, is alleen poort 1433 vereist. Deze uitgaande beveiligings regel is niet van toepassing op een SSISDB die wordt gehost door uw beheerde instantie in het virtuele netwerk. |
||||||||

### <a name="route"></a>Azure ExpressRoute of een UDR gebruiken

Wanneer u een [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -circuit verbindt met uw virtuele netwerk infrastructuur om uw on-premises netwerk naar Azure uit te breiden, gebruikt een gemeen schappelijke configuratie geforceerde tunneling (adverteren van een BGP-route, 0.0.0.0/0, naar het virtuele netwerk). Deze tunneling dwingt uitgaand Internet verkeer van de virtuele netwerk stroom naar een on-premises netwerk apparaat voor inspectie en logboek registratie. 
 
Of u kunt [udr's](../virtual-network/virtual-networks-udr-overview.md) definiëren voor het afdwingen van uitgaand Internet verkeer van het subnet dat als host fungeert voor de Azure-SSIS IR naar een ander subnet dat fungeert als firewall of Azure Firewall voor inspectie en logboek registratie. 

In beide gevallen zal de verkeers route de vereiste binnenkomende verbindingen van afhankelijke Azure Data Factory Services (met name Azure Batch Management Services) afbreken naar de Azure-SSIS IR in het virtuele netwerk. Om dit te voor komen, definieert u een of meer Udr's in het subnet dat de Azure-SSIS IR bevat. 

U kunt de route 0.0.0.0/0 Toep assen met het type van de volgende hop als **Internet** op het subnet dat als host fungeert voor de Azure-SSIS IR in een Azure ExpressRoute-scenario. Of u kunt de bestaande route van 0.0.0.0/0 wijzigen van het type volgende hop als **virtueel apparaat** op **Internet** in een NVA-scenario.

![Een route toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Als u zich zorgen maakt over de mogelijkheid om uitgaand Internet verkeer van dat subnet te controleren, kunt u specifieke Udr's definiëren voor het routeren van verkeer alleen tussen Azure Batch beheer Services en de Azure-SSIS IR met het type volgende hop als **Internet**.

Als uw Azure-SSIS IR zich bijvoorbeeld op `UK South`bevindt, ontvangt u een lijst met IP-adres bereik van de servicetag `BatchNodeManagement.UKSouth` van de service [Tags IP-adres bereik downloaden koppeling](https://www.microsoft.com/en-us/download/details.aspx?id=56519) of via de [service tag discovery-API](https://aka.ms/discoveryapi). Pas vervolgens de volgende Udr's van gerelateerde IP-bereik routes toe met het type volgende hop als **Internet**.

![UDR-instellingen voor Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Deze aanpak is een extra onderhouds kosten. Controleer regel matig het IP-adres bereik en voeg nieuwe IP-adresbereiken toe aan uw UDR om te voor komen dat de Azure-SSIS IR wordt verbroken. Het is raadzaam om het IP-bereik maandelijks te controleren. Wanneer het nieuwe IP-adres wordt weer gegeven in de servicetag van de service, zal het IP-adres van kracht worden. 

### <a name="resource-group"></a>De resource groep instellen

Het Azure-SSIS IR moet bepaalde netwerk bronnen maken onder dezelfde resource groep als het virtuele netwerk. Deze resources omvatten:
- Een Azure-load balancer, met de naam *\<Guid >-azurebatch-cloudserviceloadbalancer*.
- Een openbaar IP-adres van Azure, met de naam *\<Guid >-azurebatch-cloudservicepublicip*.
- Een netwerk beveiligings groep, met de naam *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> U kunt nu uw eigen statische open bare IP-adressen voor Azure-SSIS IR nemen. In dit scenario worden alleen de Azure load balancer-en netwerk beveiligings groep onder dezelfde resource groep gemaakt als uw statische open bare IP-adressen in plaats van het virtuele netwerk.

Deze resources worden gemaakt wanneer uw Azure-SSIS IR wordt gestart. Ze worden verwijderd wanneer uw Azure-SSIS IR wordt gestopt. Als u uw eigen statische open bare IP-adressen voor Azure-SSIS IR meebrengt, worden ze niet verwijderd wanneer de Azure-SSIS IR wordt gestopt. Als u wilt voor komen dat uw Azure-SSIS IR worden gestopt, moet u deze netwerk bronnen niet opnieuw gebruiken in uw andere resources. 

Zorg ervoor dat er geen resource vergrendeling is voor de resource groep/het abonnement waartoe het virtuele netwerk/uw statische open bare IP-adressen behoren. Als u een alleen-lezen/Verwijder vergrendeling configureert, mislukt het starten en stoppen van de Azure-SSIS IR, of reageert het niet meer.

Zorg ervoor dat u geen Azure-beleid hebt waarmee wordt voor komen dat de volgende resources worden gemaakt onder de resource groep/het abonnement waartoe het virtuele netwerk/uw statische open bare IP-adressen behoren: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>FAQ

- Hoe kan ik het open bare IP-adres dat op mijn Azure-SSIS IR wordt weer gegeven, beveiligen voor een binnenkomende verbinding? Is het mogelijk om het open bare IP-adres te verwijderen?
 
  Nu wordt er automatisch een openbaar IP-adres gemaakt wanneer uw Azure-SSIS IR lid wordt van een virtueel netwerk. We hebben een NSG op NIC-niveau waarmee alleen Azure Batch-beheer Services als inkomend-verbinding kunnen maken met uw Azure-SSIS IR. U kunt ook een NSG op subnetniveau opgeven voor inkomende beveiliging.

  Als u niet wilt dat een openbaar IP-adres wordt weer gegeven, kunt u [een zelf-hostende IR configureren als proxy voor uw Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) in plaats van uw Azure-SSIS IR aan een virtueel netwerk te koppelen, als dit van toepassing is op uw scenario.
 
- Kan ik het open bare IP-adres van mijn Azure-SSIS IR toevoegen aan de acceptatie lijst van de firewall voor mijn gegevens bronnen?

  U kunt nu uw eigen statische open bare IP-adressen voor Azure-SSIS IR nemen. In dit geval kunt u uw IP-adressen toevoegen aan de acceptatie lijst van de firewall voor uw gegevens bronnen. U kunt ook andere opties hieronder gebruiken om de toegang tot gegevens vanuit uw Azure-SSIS IR te beveiligen, afhankelijk van uw scenario:

  - Als uw gegevens bron zich op locatie bevindt, nadat u een virtueel netwerk hebt verbonden met uw on-premises netwerk en uw Azure-SSIS IR aan het subnet van het virtuele netwerk hebt toegevoegd, kunt u het privé IP-adres bereik van dat subnet toevoegen aan de acceptatie lijst van de firewall voor uw gegevens bron .
  - Als uw gegevens bron een Azure-service is die ondersteuning biedt voor service-eind punten van een virtueel netwerk, kunt u een service-eind punt voor een virtueel netwerk configureren in het subnet van het virtuele netwerk en uw Azure-SSIS IR toevoegen aan dat subnet. U kunt vervolgens een regel voor het virtuele netwerk met dat subnet toevoegen aan de firewall voor uw gegevens bron.
  - Als uw gegevens bron een niet-Azure-Cloud service is, kunt u een UDR gebruiken om uitgaand verkeer van uw Azure-SSIS IR door te sturen naar een NVA/Azure Firewall via een statisch openbaar IP-adres. U kunt vervolgens het statische open bare IP-adres van uw NVA/Azure Firewall toevoegen aan de acceptatie lijst van de firewall voor uw gegevens bron.
  - Als geen van de bovenstaande opties aan uw behoeften voldoet, kunt u overwegen om [een zelf-hostende IR te configureren als proxy voor uw Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Vervolgens kunt u het statische open bare IP-adres van de computer die als host fungeert voor uw zelf-hostende IR toevoegen aan de acceptatie lijst van de firewall voor uw gegevens bron.

- Waarom moet ik twee statische open bare adressen opgeven als ik mijn eigen persoonlijke adres wil opnemen voor Azure-SSIS IR?

  Azure-SSIS IR wordt regel matig automatisch bijgewerkt. Nieuwe knoop punten worden tijdens de upgrade gemaakt en oude worden verwijderd. Om uitval tijd te voor komen, worden de oude knoop punten echter pas verwijderd als de nieuwe knooppunten gereed zijn. Uw eerste statische open bare IP-adres dat wordt gebruikt door de oude knoop punten kan dus niet onmiddellijk worden vrijgegeven en we hebben uw tweede statische open bare IP-adres nodig om de nieuwe knoop punten te maken.

- Ik heb mijn eigen statische open bare IP-adressen voor Azure-SSIS IR en waarom heb ik nog steeds geen toegang tot mijn gegevens bronnen?

  - Controleer of de twee statische open bare IP-adressen beide zijn toegevoegd aan de acceptatie lijst van de firewall voor uw gegevens bronnen. Telkens wanneer uw Azure-SSIS IR wordt geüpgraded, wordt het statische open bare IP-adres tussen de twee door u overgezet. Als u slechts een van beide toevoegt aan de acceptatie lijst, wordt de toegang tot gegevens voor uw Azure-SSIS IR verbroken na de upgrade.
  - Als uw gegevens bron een Azure-service is, controleert u of u deze hebt geconfigureerd met Service-eind punten voor virtuele netwerken. Als dat het geval is, zal het verkeer van Azure-SSIS IR naar uw gegevens bron overschakelen op het gebruik van de privé-IP-adressen die worden beheerd door Azure-Services, en het toevoegen van uw eigen statische open bare IP-adressen aan de acceptatie lijst van de firewall voor uw gegevens bron wordt niet van kracht.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory gebruikers interface)

In deze sectie wordt beschreven hoe u een bestaande Azure-SSIS IR kunt toevoegen aan een virtueel netwerk (klassiek of Azure Resource Manager) met behulp van de Azure Portal en Data Factory gebruikers interface. 

Voordat u uw Azure-SSIS IR aan het virtuele netwerk toevoegt, moet u het virtuele netwerk correct configureren. Volg de stappen in de sectie die van toepassing is op uw type virtueel netwerk (klassiek of Azure Resource Manager). Volg vervolgens de stappen in het derde gedeelte om uw Azure-SSIS IR toe te voegen aan het virtuele netwerk. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Een Azure Resource Manager virtueel netwerk configureren

Gebruik de portal om een Azure Resource Manager virtueel netwerk te configureren voordat u probeert een Azure-SSIS IR te koppelen.

1. Start micro soft Edge of Google Chrome. Momenteel ondersteunen alleen deze webbrowsers de Data Factory-gebruikers interface. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 

1. Selecteer **meer services**. Filter voor en selecteer **virtuele netwerken**. 

1. Filter en selecteer uw virtuele netwerk in de lijst. 

1. Selecteer op de pagina **virtueel netwerk** **Eigenschappen**. 

1. Selecteer de knop kopiëren voor de **resource-id** om de resource-id voor het virtuele netwerk naar het klem bord te kopiëren. Sla de ID op in het klem bord in OneNote of in een bestand. 

1. Selecteer **subnetten**in het menu links. Zorg ervoor dat het aantal beschik bare adressen groter is dan de knoop punten in uw Azure-SSIS IR. 

1. Controleer of de Azure Batch provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of Registreer de Azure Batch-provider. Als u al een Azure Batch account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u de Azure-SSIS IR maakt in de Data Factory Portal, wordt de Azure Batch provider automatisch voor u geregistreerd.) 

   1. Selecteer in het Azure Portal in het linkermenu **abonnementen**. 

   1. Selecteer uw abonnement. 

   1. Selecteer aan de linkerkant **resource providers**en controleer of **micro soft. batch** een geregistreerde provider is. 

   ![Bevestiging van de status ' geregistreerd '](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Als u **micro soft. batch** niet in de lijst ziet, [maakt u een leeg Azure batch account](../batch/batch-account-create-portal.md) in uw abonnement om het te registreren. U kunt deze later verwijderen. 

### <a name="configure-a-classic-virtual-network"></a>Een klassiek virtueel netwerk configureren

Gebruik de portal om een klassiek virtueel netwerk te configureren voordat u probeert een Azure-SSIS IR toe te voegen. 

1. Start micro soft Edge of Google Chrome. Momenteel ondersteunen alleen deze webbrowsers de Data Factory-gebruikers interface. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 

1. Selecteer **meer services**. Filter voor en selecteer **virtuele netwerken (klassiek)** . 

1. Filter en selecteer uw virtuele netwerk in de lijst. 

1. Selecteer op de pagina **virtueel netwerk (klassiek)** de optie **Eigenschappen**. 

   ![Resource-ID van klassiek virtueel netwerk](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecteer de knop kopiëren voor **resource-id** om de resource-id voor het klassieke netwerk naar het klem bord te kopiëren. Sla de ID op in het klem bord in OneNote of in een bestand. 

1. Selecteer **subnetten**in het menu links. Zorg ervoor dat het aantal beschik bare adressen groter is dan de knoop punten in uw Azure-SSIS IR. 

   ![Aantal beschik bare adressen in het virtuele netwerk](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Voeg **MicrosoftAzureBatch** toe aan de rol **Inzender voor klassieke virtuele machines** voor het virtuele netwerk. 

   1. Selecteer **toegangs beheer (IAM)** in het menu links en selecteer het tabblad **roltoewijzingen** . 

   ![De knoppen toegangs beheer en toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selecteer **roltoewijzing toevoegen**.

   1. Selecteer op de pagina **roltoewijzing toevoegen** , voor **rol**, de **Inzender voor klassieke virtuele machines**. Plak **ddbf3205-c6bd-46ae-8127-60eb93363864**in het **selectie** vakje en selecteer vervolgens **Microsoft Azure batch** in de lijst met zoek resultaten. 

   ![Zoek resultaten op de pagina functie toewijzing toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Selecteer **Opslaan** om de instellingen op te slaan en de pagina te sluiten. 

   ![Toegangs instellingen opslaan](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Controleer of **Microsoft Azure batch** wordt weer geven in de lijst met inzenders. 

   ![Azure Batch toegang bevestigen](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Controleer of de Azure Batch provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of Registreer de Azure Batch-provider. Als u al een Azure Batch account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u de Azure-SSIS IR maakt in de Data Factory Portal, wordt de Azure Batch provider automatisch voor u geregistreerd.) 

   1. Selecteer in het Azure Portal in het linkermenu **abonnementen**. 

   1. Selecteer uw abonnement. 

   1. Selecteer aan de linkerkant **resource providers**en controleer of **micro soft. batch** een geregistreerde provider is. 

   ![Bevestiging van de status ' geregistreerd '](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Als u **micro soft. batch** niet in de lijst ziet, [maakt u een leeg Azure batch account](../batch/batch-account-create-portal.md) in uw abonnement om het te registreren. U kunt deze later verwijderen. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>De Azure-SSIS IR toevoegen aan een virtueel netwerk

Nadat u uw Azure Resource Manager virtuele netwerk of het klassieke virtuele netwerk hebt geconfigureerd, kunt u de Azure-SSIS IR toevoegen aan het virtuele netwerk:

1. Start micro soft Edge of Google Chrome. Momenteel ondersteunen alleen deze webbrowsers de Data Factory-gebruikers interface. 

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het linkermenu **gegevens fabrieken**. Als er geen **gegevens fabrieken** worden weer gegeven in het menu, selecteert u **meer services**en selecteert u in de sectie **Intelligence en analyse** **gegevens fabrieken**. 

   ![Lijst met gegevens fabrieken](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecteer uw data factory met de Azure-SSIS IR in de lijst. U ziet de start pagina voor uw data factory. Selecteer de tegel **auteur & implementeren** . U ziet de Data Factory gebruikers interface op een afzonderlijk tabblad. 

   ![Startpagina van de gegevensfactory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Ga in de Data Factory-gebruikers interface naar het tabblad **bewerken** , selecteer **verbindingen**en schakel over naar het tabblad **Integration Runtimes** . 

   ![Tabblad Integration Runtimes](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Als uw Azure-SSIS IR wordt uitgevoerd, selecteert u in de kolom **acties** van de lijst met **integratie-Runtimes** de knop **stoppen** voor uw Azure-SSIS IR. U kunt uw Azure-SSIS IR pas bewerken nadat u het hebt gestopt. 

   ![De IR stoppen](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Selecteer de knop **bewerken** voor uw Azure-SSIS IR in de kolom **acties** van de lijst **Integration Runtimes** . 

   ![De Integration runtime bewerken](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Ga in het configuratie venster voor Integration runtime naar de secties **algemene instellingen** en **SQL-instellingen** en selecteer de knop **volgende** . 

1. In het gedeelte **Geavanceerde instellingen** : 

   1. Selecteer **een VNet selecteren voor uw Azure-SSIS Integration runtime om toe te voegen, ADF toestaan om bepaalde netwerk resources te maken en eventueel uw eigen statische open bare IP-adressen** in te stellen. 

   1. Selecteer bij **abonnement**het Azure-abonnement met uw virtuele netwerk.

   1. Voor **locatie**is dezelfde locatie van de Integration runtime geselecteerd.

   1. Selecteer bij **type**het type van uw virtuele netwerk: klassiek of Azure Resource Manager. We raden u aan om een Azure Resource Manager virtueel netwerk te selecteren, omdat klassieke virtuele netwerken binnenkort worden afgeschaft.

   1. Selecteer voor **VNet-naam**de naam van het virtuele netwerk. Dit moet hetzelfde zijn als die voor uw Azure SQL Database-Server met virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt om SSISDB te hosten. Of het moet hetzelfde zijn als het account dat is verbonden met uw on-premises netwerk. Anders kan het een virtueel netwerk zijn om uw eigen statische open bare IP-adressen te maken voor Azure-SSIS IR.

   1. Selecteer bij **subnet naam**de naam van het subnet voor het virtuele netwerk. Dit moet hetzelfde zijn als die voor uw Azure SQL Database-Server met virtuele netwerk service-eind punten om SSISDB te hosten. Of het moet een ander subnet zijn dat wordt gebruikt voor uw beheerde exemplaar met een persoonlijk eind punt om SSISDB te hosten. Anders kan het een wille keurige subnet zijn om uw eigen statische open bare IP-adressen voor Azure-SSIS IR te brengen.

   1. Schakel het selectie vakje **statische open bare IP-adressen voor uw Azure-SSIS Integration runtime** in om te kiezen of u uw eigen statische open bare IP-adressen wilt meenemen voor Azure-SSIS IR, zodat u ze op de firewall voor uw gegevens bronnen kunt toestaan.

      Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

      1. Voor het **eerste statische open bare IP-adres**selecteert u het eerste statische open bare IP-adres dat [voldoet aan de vereisten](#publicIP) voor uw Azure-SSIS IR. Als u er geen hebt, klikt u op **nieuwe koppeling maken** om statische open bare IP-adressen te maken op Azure Portal en klikt u vervolgens op de knop Vernieuwen hier, zodat u ze kunt selecteren.
      
      1. Voor het **tweede statische open bare IP-adres**selecteert u het tweede statische open bare IP-adres dat [voldoet aan de vereisten](#publicIP) voor uw Azure-SSIS IR. Als u er geen hebt, klikt u op **nieuwe koppeling maken** om statische open bare IP-adressen te maken op Azure Portal en klikt u vervolgens op de knop Vernieuwen hier, zodat u ze kunt selecteren.

   1. Selecteer **VNet-validatie**. Als de validatie is geslaagd, selecteert u **door gaan**. 

   ![Geavanceerde instellingen met een virtueel netwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Controleer in de sectie **samen vatting** alle instellingen voor uw Azure-SSIS IR. Selecteer vervolgens **bijwerken**.

1. Start uw Azure-SSIS IR door de knop **Start** te selecteren in de kolom **acties** voor uw Azure-SSIS IR. Het duurt ongeveer 20 tot 30 minuten om de Azure-SSIS IR die aan een virtueel netwerk zijn gekoppeld, te starten. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Een virtueel netwerk configureren

Voordat u uw Azure-SSIS IR kunt toevoegen aan een virtueel netwerk, moet u het virtuele netwerk configureren. Als u de machtigingen en instellingen van het virtuele netwerk voor uw Azure-SSIS IR automatisch wilt configureren om lid te worden van het virtuele netwerk, voegt u het volgende script toe:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Een Azure-SSIS IR maken en koppelen aan een virtueel netwerk

U kunt op hetzelfde moment een Azure-SSIS IR maken en koppelen aan een virtueel netwerk. Zie [een Azure-SSIS IR maken](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)voor het volledige script en de instructies.

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Een bestaande Azure-SSIS IR toevoegen aan een virtueel netwerk

In het artikel [een Azure-SSIS IR maken](create-azure-ssis-integration-runtime.md) ziet u hoe u een Azure-SSIS IR maakt en deze koppelt aan een virtueel netwerk in hetzelfde script. Als u al een Azure-SSIS IR hebt, volgt u deze stappen om het aan het virtuele netwerk toe te voegen: 
1. Stop de Azure-SSIS IR. 
1. Configureer de Azure-SSIS IR om lid te worden van het virtuele netwerk. 
1. Start de Azure-SSIS IR. 

### <a name="define-the-variables"></a>De variabelen definiëren

```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>De Azure-SSIS IR stoppen

U moet de Azure-SSIS IR stoppen voordat u deze kunt koppelen aan een virtueel netwerk. Met deze opdracht worden alle knoop punten vrijgegeven en wordt de facturering gestopt:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configureer de instellingen van het virtuele netwerk voor de Azure-SSIS IR om lid te worden

Als u instellingen wilt configureren voor het virtuele netwerk waaraan de Azure-SSIS wordt toegevoegd, gebruikt u dit script: 

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

Voer de `Set-AzDataFactoryV2IntegrationRuntime` opdracht uit om uw Azure-SSIS IR toe te voegen aan een virtueel netwerk: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>De Azure-SSIS IR starten

Als u de Azure-SSIS IR wilt starten, voert u de volgende opdracht uit: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Het volt ooien van deze opdracht duurt 20 tot 30 minuten.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-SSIS IR: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dit artikel bevat algemene conceptuele informatie over IRs, met inbegrip van Azure-SSIS IR. 
- [Zelf studie: SSIS-pakketten implementeren in azure](tutorial-create-azure-ssis-runtime-portal.md). Deze zelf studie bevat stapsgewijze instructies voor het maken van uw Azure-SSIS IR. Er wordt gebruikgemaakt van Azure SQL Database voor het hosten van de SSIS-catalogus. 
- [Maak een Azure-SSIS IR](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid. U vindt hier instructies over het gebruik van Azure SQL Database met virtuele netwerk service-eind punten of beheerde exemplaren in een virtueel netwerk om de SSIS-catalogus te hosten. U ziet hoe u uw Azure-SSIS IR kunt koppelen aan een virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over uw Azure-SSIS IR kunt ophalen. Het bevat status beschrijvingen voor de geretourneerde informatie. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u uw Azure-SSIS IR kunt stoppen, starten of verwijderen. U ziet ook hoe u uw Azure-SSIS IR kunt uitschalen door knoop punten toe te voegen.