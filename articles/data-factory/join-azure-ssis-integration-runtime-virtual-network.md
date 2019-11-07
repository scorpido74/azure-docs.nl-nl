---
title: Een Azure-SSIS-integratie-runtime toevoegen aan een virtueel netwerk
description: Meer informatie over hoe u een Azure-SSIS-integratie-runtime kunt koppelen aan een virtueel Azure-netwerk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d36900a1ce05eaf022637a6ef6b866fe0d190b17
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672734"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Een Azure-SSIS-integratie-runtime toevoegen aan een virtueel netwerk
Wanneer u SQL Server Integration Services (SSIS) in Azure Data Factory gebruikt, moet u uw Azure-SSIS Integration runtime (IR) koppelen aan een virtueel Azure-netwerk in de volgende scenario's: 

- U wilt verbinding maken met on-premises gegevens archieven uit SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR zonder een zelf-hostende IR te hoeven configureren of beheren als proxy. 

- U wilt verbinding maken met Azure-service bronnen die worden ondersteund door service-eind punten van het virtuele netwerk van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR.

- U host een SSIS-catalogus database (SSISDB) in Azure SQL Database met Service-eind punten voor virtuele netwerken of een beheerd exemplaar in een virtueel netwerk. 

Met Data Factory kunt u uw Azure-SSIS IR toevoegen aan een virtueel netwerk dat is gemaakt via het klassieke implementatie model of het implementatie model van Azure Resource Manager. 

> [!IMPORTANT]
> Het klassieke virtuele netwerk wordt afgeschaft. gebruik in plaats daarvan het Azure Resource Manager virtuele netwerk.  Als u het klassieke virtuele netwerk al gebruikt, schakelt u zo snel mogelijk over naar het Azure Resource Manager virtuele netwerk.

## <a name="access-to-on-premises-data-stores"></a>Toegang tot on-premises gegevens archieven
Als uw SSIS-pakketten toegang hebben tot on-premises gegevens opslag, kunt u uw Azure-SSIS IR koppelen aan een virtueel netwerk dat is verbonden met het on-premises netwerk. U kunt ook een zelf-hostende IR configureren of beheren als proxy voor uw Azure-SSIS IR. Zie [een zelf-hostende IR configureren als proxy voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)voor meer informatie. 

Onthoud de volgende belang rijke punten wanneer u uw Azure-SSIS IR lid maakt van een virtueel netwerk: 

- Als er geen virtueel netwerk is verbonden met uw on-premises netwerk, moet u eerst een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken om uw Azure-SSIS IR te koppelen. Configureer vervolgens een site-naar-site [-VPN-gateway verbinding](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) of een [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) -verbinding van het virtuele netwerk naar uw on-premises netwerk. 

- Als een Azure Resource Manager virtueel netwerk al is verbonden met uw on-premises netwerk op dezelfde locatie als uw Azure-SSIS IR, kunt u de IR toevoegen aan het virtuele netwerk. 

- Als er al een klassiek virtueel netwerk is verbonden met uw on-premises netwerk op een andere locatie dan uw Azure-SSIS IR, kunt u een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken om aan uw Azure-SSIS IR deel te nemen. Configureer vervolgens een [klassieke-naar-Azure Resource Manager virtuele netwerk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) verbinding. 
 
- Als er al een Azure Resource Manager virtueel netwerk is verbonden met uw on-premises netwerk op een andere locatie dan uw Azure-SSIS IR, kunt u eerst een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md##create-a-virtual-network) maken om uw Azure-SSIS IR te koppelen. Configureer vervolgens een Azure Resource Manager-to-Azure Resource Manager virtuele netwerk verbinding. 

## <a name="access-to-azure-services"></a>Toegang tot Azure-services
Als uw SSIS-pakketten toegang krijgen tot Azure-service bronnen die worden ondersteund door [service-eind punten van virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md) en u deze bronnen wilt beveiligen Azure-SSIS IR, kunt u uw Azure-SSIS IR toevoegen aan het subnet van het virtuele netwerk dat is geconfigureerd met het virtuele netwerk Service-eind punten. U kunt ondertussen een regel voor het virtuele netwerk toevoegen aan de Azure-service resources om toegang vanaf hetzelfde subnet toe te staan.

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>De SSIS-catalogus in SQL Database hosten
Als u uw SSIS-catalogus host in Azure SQL Database met virtuele netwerk service-eind punten, moet u uw Azure-SSIS IR toevoegen aan hetzelfde virtuele netwerk en subnet.

Als u uw Azure-SSIS IR wilt toevoegen aan hetzelfde virtuele netwerk als het beheerde exemplaar, moet u ervoor zorgen dat de Azure-SSIS IR zich in een ander subnet bevindt dan het beheerde exemplaar. Als u uw Azure-SSIS IR wilt toevoegen aan een ander virtueel netwerk dan het beheerde exemplaar, kunt u het beste de peering van het virtuele netwerk (dit is beperkt tot dezelfde regio) of een verbinding tussen een virtueel netwerk en een virtueel netwerk. Zie [verbinding maken tussen uw toepassing en Azure SQL database Managed instance](../sql-database/sql-database-managed-instance-connect-app.md)(Engelstalig) voor meer informatie.

In alle gevallen kan het virtuele netwerk alleen worden geïmplementeerd via het Azure Resource Manager-implementatie model.

In de volgende secties vindt u meer informatie. 

## <a name="virtual-network-configuration"></a>Configuratie van virtueel netwerk

Stel uw virtuele netwerk in om aan deze vereisten te voldoen: 

-   Zorg ervoor dat `Microsoft.Batch` een geregistreerde provider is onder het abonnement van het subnet van het virtuele netwerk dat als host fungeert voor de Azure-SSIS IR. Als u een klassiek virtueel netwerk gebruikt, moet u ook `MicrosoftAzureBatch` toevoegen aan de rol Inzender voor klassieke virtuele machines voor dat virtuele netwerk. 

-   Zorg ervoor dat u over de vereiste machtigingen beschikt. Zie [machtigingen instellen](#perms)voor meer informatie.

-   Selecteer het juiste subnet om de Azure-SSIS IR te hosten. Zie [het subnet selecteren](#subnet)voor meer informatie. 

-   Als u uw eigen Domain Name System (DNS)-server in het virtuele netwerk gebruikt, raadpleegt u [de DNS-server instellen](#dns_server). 

-   Als u een netwerk beveiligings groep (NSG) in het subnet gebruikt, raadpleegt u [een NSG instellen](#nsg). 

-   Als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt, raadpleegt u [Azure ExpressRoute of een UDR gebruiken](#route). 

-   Zorg ervoor dat de resource groep van het virtuele netwerk bepaalde Azure-netwerk resources kan maken en verwijderen. Zie [de resource groep instellen](#resource-group)voor meer informatie. 

-   Als u uw Azure-SSIS IR aanpast zoals beschreven in de [aangepaste installatie voor Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup), krijgen uw Azure-SSIS IR-knoop punten privé-IP-adressen van een vooraf gedefinieerd bereik van 172.16.0.0 tot 172.31.255.255. Zorg er dus voor dat de privé-IP-adresbereiken van uw virtuele of on-premises netwerken niet conflicteren met dit bereik.

Dit diagram toont de vereiste verbindingen voor uw Azure-SSIS IR:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Machtigingen instellen

De gebruiker die de Azure-SSIS IR maakt, moet de volgende machtigingen hebben:

- Als u uw SSIS-IR koppelt aan een Azure Resource Manager virtueel netwerk, hebt u twee opties:

  - Gebruik de ingebouwde rol netwerk bijdrager. Deze rol wordt geleverd met de machtiging _micro soft. Network/\*_ , die een veel groter bereik heeft dan nodig is.

  - Maak een aangepaste rol die alleen de benodigde _micro soft. Network/virtualNetworks/\*-_ machtiging voor/join/Action bevat. 

- Als u uw SSIS-IR aan een klassiek virtueel netwerk koppelt, wordt u aangeraden de ingebouwde rol van de klassieke virtuele machine te gebruiken. Anders moet u een aangepaste rol definiëren die de machtiging bevat om lid te worden van het virtuele netwerk.

### <a name="subnet"></a>Het subnet selecteren

Bij het kiezen van een subnet: 

-   Selecteer de GatewaySubnet voor het implementeren van een Azure-SSIS IR niet. Het is toegewezen voor virtuele netwerk gateways. 

-   Zorg ervoor dat het door u geselecteerde subnet voldoende beschik bare adres ruimte heeft om de Azure-SSIS IR te gebruiken. Houd de beschik bare IP-adressen voor ten minste twee keer het IR-knooppunt nummer. Azure reserveert een aantal IP-adressen binnen elk subnet. Deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit en er worden nog drie adressen voor Azure-Services gebruikt. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie. 

-   Gebruik geen subnet dat uitsluitend wordt ingen Omen door andere Azure-Services (bijvoorbeeld SQL Database beheerde instantie, App Service, enzovoort). 

### <a name="dns_server"></a>De DNS-server instellen 
Als u uw eigen DNS-server moet gebruiken in een virtueel netwerk dat is gekoppeld aan uw Azure-SSIS IR, moet u ervoor zorgen dat de globale namen van Azure-hosts worden omgezet (bijvoorbeeld een Azure Storage-blob met de naam `<your storage account>.blob.core.windows.net`). 

De volgende stappen worden aanbevolen: 

-   Configureer de aangepaste DNS-server voor het door sturen van aanvragen naar Azure DNS. U kunt niet-omgezette DNS-records door sturen naar het IP-adres van de recursieve resolvers van Azure (168.63.129.16) op uw eigen DNS-server. 

-   Stel de aangepaste DNS in als de primaire DNS-server voor het virtuele netwerk. Azure DNS instellen als de secundaire DNS-server. Registreer het IP-adres van de Azure recursieve resolver (168.63.129.16) als een secundaire DNS-server voor het geval uw eigen DNS-server niet beschikbaar is. 

Zie [naam omzetting die gebruikmaakt van uw eigen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)voor meer informatie. 

### <a name="nsg"></a>Een NSG instellen
Als u een NSG wilt implementeren voor het subnet dat door uw Azure-SSIS IR wordt gebruikt, kunt u inkomend en uitgaand verkeer via de volgende poorten toestaan: 

| Richting | Transport Protocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel | Opmerkingen |
|---|---|---|---|---|---|---|
| Inkomend | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (als u de IR koppelt aan een virtueel netwerk van Resource Manager) <br/><br/>10100, 20100, 30100 (als u de IR koppelt aan een klassiek virtueel netwerk)| De Data Factory-service gebruikt deze poorten om te communiceren met de knoop punten van uw Azure-SSIS IR in het virtuele netwerk. <br/><br/> Of u nu een NSG op subnetniveau maakt, Data Factory altijd een NSG configureert op het niveau van de netwerk interface kaarten (Nic's) die zijn gekoppeld aan de virtuele machines die de Azure-SSIS IR hosten. Alleen binnenkomend verkeer van Data Factory IP-adressen op de opgegeven poorten is toegestaan door deze NSG op NIC-niveau. Zelfs als u deze poorten opent op het Internet verkeer op subnetniveau, wordt verkeer van IP-adressen die niet Data Factory IP-adressen worden geblokkeerd op het niveau van de NIC. |
| Uitgaand | TCP | VirtualNetwork | * | AzureCloud | 443 | De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort voor toegang tot Azure-Services, zoals Azure Storage en Azure Event Hubs. |
| Uitgaand | TCP | VirtualNetwork | * | Internet | 80 | De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort om een certificaatintrekkingslijst van Internet te downloaden. |
| Uitgaand | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poorten om toegang te krijgen tot een SSISDB die wordt gehost door uw SQL Database-Server. Als uw SQL Database Server-verbindings beleid is ingesteld op **proxy** in plaats van de **omleiding**, is alleen poort 1433 vereist. Deze uitgaande beveiligings regel is niet van toepassing op een SSISDB die wordt gehost door uw beheerde instantie in het virtuele netwerk. |
||||||||

### <a name="route"></a>Azure ExpressRoute of een UDR gebruiken
Wanneer u een [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -circuit verbindt met uw virtuele netwerk infrastructuur om uw on-premises netwerk naar Azure uit te breiden, gebruikt een gemeen schappelijke configuratie geforceerde tunneling (adverteren van een BGP-route, 0.0.0.0/0, naar het virtuele netwerk). Deze tunneling dwingt uitgaand Internet verkeer van de virtuele netwerk stroom naar een on-premises netwerk apparaat voor inspectie en logboek registratie. 
 
Of u kunt [udr's](../virtual-network/virtual-networks-udr-overview.md) definiëren voor het afdwingen van uitgaand Internet verkeer van het subnet dat als host fungeert voor de Azure-SSIS IR op een ander subnet dat als host fungeert voor een virtueel netwerk apparaat (NVA) als een firewall of Azure Firewall voor inspectie en logboek registratie. 

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
   -   Een Azure-load balancer, met de naam *\<Guid >-azurebatch-cloudserviceloadbalancer*.
   -   Een openbaar IP-adres van Azure, met de naam *\<Guid >-azurebatch-cloudservicepublicip*.
   -   Een netwerk beveiligings groep, met de naam *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*. 

Deze resources worden gemaakt wanneer de IR wordt gestart. Ze worden verwijderd wanneer de IR wordt gestopt. Om te voor komen dat de IR-stop wordt geblokkeerd, moet u deze netwerk bronnen niet opnieuw gebruiken in uw andere resources. 

Zorg ervoor dat er geen resource vergrendeling is voor de resource groep of het abonnement waartoe het virtuele netwerk behoort. Als u een alleen-lezen vergrendeling of een verwijderings vergrendeling configureert, kan het starten en stoppen van de IR mislukken, of kan de IR niet meer reageren. 

Zorg ervoor dat u geen Azure-beleid hebt waarmee wordt voor komen dat de volgende resources worden gemaakt onder de resource groep of het abonnement waartoe het virtuele netwerk behoort: 
   -   Micro soft. Network/LoadBalancers 
   -   Micro soft. Network/NetworkSecurityGroups 
   -   Micro soft. Network/PublicIPAddresses 

### <a name="faq"></a>FAQ

- Hoe kan ik het open bare IP-adres dat wordt weer gegeven op de Azure-SSIS IR voor een binnenkomende verbinding beveiligen? Is het mogelijk om het open bare IP-adres te verwijderen?
 
    Nu wordt er automatisch een openbaar IP-adres gemaakt wanneer de Azure-SSIS IR lid wordt van het virtuele netwerk. We hebben een NSG op NIC-niveau waarmee alleen Azure Batch-beheer Services als inkomend-verbinding kunnen maken met de Azure-SSIS IR. U kunt ook een NSG op subnetniveau opgeven voor inkomende beveiliging.

    Als u niet wilt dat het open bare IP-adres wordt weer gegeven, kunt u overwegen om [de zelf-hostende IR te configureren als proxy voor de Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) in plaats van het virtuele netwerk als dit van toepassing is op uw scenario.
 
- Kan ik het statische IP-adres van de Azure-SSIS IR toevoegen aan de acceptatie lijst van de firewall voor de gegevens bron?
 
    - Als uw gegevens bron on-premises is, nadat u het virtuele netwerk hebt verbonden met uw on-premises netwerk en uw Azure-SSIS IR toevoegt aan het subnet van het virtuele netwerk, kunt u het IP-adres bereik van dat subnet toevoegen aan de acceptatie lijst.
    - Als uw gegevens bron een Azure-service is die wordt ondersteund door een service-eind punt van een virtueel netwerk, kunt u een service punt voor een virtueel netwerk configureren op het virtuele netwerk en uw Azure-SSIS IR toevoegen aan het subnet van het virtuele netwerk. Vervolgens kunt u toegang toestaan met behulp van de virtuele-netwerk regel van de Azure-Services in plaats van het IP-bereik.
    - Als uw gegevens bron een ander type gegevens bron voor de Cloud is, kunt u UDR gebruiken om uitgaand verkeer van de Azure-SSIS IR te routeren naar de NVA of om te Azure Firewall met behulp van een statisch openbaar IP-adres. U kunt het open bare IP-adres van de NVA of Azure Firewall toevoegen aan de acceptatie lijst.
    - Als de vorige antwoorden niet aan uw behoeften voldoen, kunt u de toegang tot gegevens bronnen verschaffen door [een zelf-hostende IR te configureren als proxy voor de Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Vervolgens kunt u het IP-adres van de computer die als host fungeert voor de zelf-hostende IR toevoegen aan de acceptatie lijst in plaats van de Azure-SSIS IR aan het virtuele netwerk toe te voegen.

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

   a. Selecteer in het Azure Portal in het linkermenu **abonnementen**. 

   b. Selecteer uw abonnement. 

   c. Selecteer aan de linkerkant **resource providers**en controleer of **micro soft. batch** een geregistreerde provider is. 

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

    a. Selecteer **toegangs beheer (IAM)** in het menu links en selecteer het tabblad **roltoewijzingen** . 

    ![De knoppen toegangs beheer en toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Selecteer **roltoewijzing toevoegen**.

    c. Selecteer op de pagina **roltoewijzing toevoegen** , voor **rol**, de **Inzender voor klassieke virtuele machines**. Plak **ddbf3205-c6bd-46ae-8127-60eb93363864**in het **selectie** vakje en selecteer vervolgens **Microsoft Azure batch** in de lijst met zoek resultaten. 

    ![Zoek resultaten op de pagina functie toewijzing toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Selecteer **Opslaan** om de instellingen op te slaan en de pagina te sluiten. 

    ![Toegangs instellingen opslaan](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Controleer of **Microsoft Azure batch** wordt weer geven in de lijst met inzenders. 

    ![Azure Batch toegang bevestigen](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Controleer of de Azure Batch provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of Registreer de Azure Batch-provider. Als u al een Azure Batch account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u de Azure-SSIS IR maakt in de Data Factory Portal, wordt de Azure Batch provider automatisch voor u geregistreerd.) 

   a. Selecteer in het Azure Portal in het linkermenu **abonnementen**. 

   b. Selecteer uw abonnement. 

   c. Selecteer aan de linkerkant **resource providers**en controleer of **micro soft. batch** een geregistreerde provider is. 

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

1. Als uw Azure-SSIS IR wordt uitgevoerd, selecteert u in de kolom **acties** van de lijst met **integratie-Runtimes** de knop **stoppen** voor uw Azure-SSIS IR. U kunt een IR pas bewerken nadat u deze hebt gestopt. 

   ![De IR stoppen](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Selecteer de knop **bewerken** voor uw Azure-SSIS IR in de kolom **acties** van de lijst **Integration Runtimes** . 

   ![De Integration runtime bewerken](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Ga in **het configuratie** venster van Integration runtime naar de pagina **algemene instellingen** en **SQL-instellingen** en selecteer de knop **volgende** . 

1. Op de pagina **Geavanceerde instellingen** : 

   a. Schakel het selectie vakje in naast **een VNet selecteren**. 

   b. Selecteer uw Azure-abonnement voor het **abonnement**. Onder het abonnement kunt u een bestaand virtueel netwerk selecteren. 
  
   c. Selecteer voor **VNet-naam**het virtuele netwerk. 

   d. Selecteer bij **subnet naam**het subnet in het virtuele netwerk. 

   e. Als u ook een zelf-hostende IR wilt configureren of beheren als een proxy voor uw Azure-SSIS IR, schakelt u het selectie vakje **zelf-hosten instellen** in. Zie [een zelf-hostende IR configureren als proxy voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)voor meer informatie.

   f. Selecteer de knop **VNet-validatie** . Als de validatie is geslaagd, selecteert u de knop **volgende** . 

   ![Geavanceerde instellingen voor IR-installatie](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Controleer op de pagina **samen vatting** alle instellingen voor uw Azure-SSIS IR. Selecteer vervolgens de knop **bijwerken** .

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
Als u de Azure-SSIS IR wilt configureren om lid te worden van het virtuele netwerk, voert u de `Set-AzDataFactoryV2IntegrationRuntime`-opdracht uit: 

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
