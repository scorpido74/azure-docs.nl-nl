---
title: Ethereum Proof-of-Authority consortiumoplossingsjabloon implementeren op Azure
description: Gebruik de Ethereum Proof-of-Authority consortiumoplossing voor het implementeren en configureren van een consortium met meerdere leden, Ethereum-netwerk op Azure
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387504"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Ethereum-sjabloon voor een consortiumoplossing voor proof-of-authority implementeren op Azure

U [de Azure-oplossingssjabloon Ethereum Proof-of-Authority Consortium](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) gebruiken voor het implementeren, configureren en beheren van een ethereum-netwerk met meerdere leden met minimale Azure- en Ethereum-kennis.

De oplossingssjabloon kan door elk consortiumlid worden gebruikt om een blockchain-netwerkvoetafdruk in te richten met behulp van Microsoft Azure-compute-, netwerk- en opslagservices. De netwerkvoetafdruk van elk consortiumlid bestaat uit een set load-balanced validator-knooppunten waarmee een toepassing of gebruiker kan communiceren om Ethereum-transacties in te dienen.

## <a name="choose-an-azure-blockchain-solution"></a>Kies een Azure Blockchain-oplossing

Voordat u ervoor kiest om de ethereum proof-of-authority consortiumoplossingssjabloon te gebruiken, vergelijkt u uw scenario met de algemene gebruiksvoorbeelden van beschikbare Azure Blockchain-opties.

Optie | Servicemodel | Algemene gebruiksaanvraag
-------|---------------|-----------------
Oplossingssjablonen | IaaS | Oplossingssjablonen zijn Azure Resource Manager-sjablonen die u gebruiken om een volledig geconfigureerde blockchain-netwerktopologie in te richten. De sjablonen implementeren en configureren Microsoft Azure compute-, netwerk- en opslagservices voor een bepaald blockchain-netwerktype.
[Azure Blockchain-service](../service/overview.md) | PaaS | Azure Blockchain Service Preview vereenvoudigt de vorming, het beheer en het beheer van blockchainnetwerken van consortiums. Gebruik Azure Blockchain Service voor oplossingen waarvoor PaaS, consortiumbeheer of contract- en transactieprivacy vereist zijn.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS en PaaS | Azure Blockchain Workbench Preview is een verzameling Azure-services en -mogelijkheden die zijn ontworpen om u te helpen blockchain-toepassingen te maken en te implementeren om bedrijfsprocessen en gegevens te delen met andere organisaties. Gebruik Azure Blockchain Workbench voor het prototypen van een blockchain-oplossing of een blockchain-toepassingsproof of concept.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

Met behulp van de Ethereum-oplossingssjabloon u een consortiumnetwerk met meerdere of meerdere regio's implementeren in ethereum proof-of-authority.

![implementatiearchitectuur](./media/ethereum-poa-deployment/deployment-architecture.png)

Elke implementatie van consortiumleden omvat:

* Virtuele machines voor het uitvoeren van de PoA validators
* Azure Load Balancer voor het distribueren van RPC-, peering- en governance-DApp-aanvragen
* Azure Key Vault voor het beveiligen van de validatoridentiteiten
* Azure Storage voor het hosten van permanente netwerkinformatie en het coördineren van leasing
* Azure Monitor voor het aggregeren van logboeken en prestatiestatistieken
* VNet Gateway (optioneel) voor het toestaan van VPN-verbindingen via private VNets

Standaard zijn de RPC- en peering-eindpunten toegankelijk via openbare IP om vereenvoudigde connectiviteit tussen abonnementen en clouds mogelijk te maken. Voor toegangsbesturingselementen op toepassingsniveau u de [machtigingscontracten van Parity](https://wiki.parity.io/Permissioning)gebruiken. Netwerken die worden geïmplementeerd achter VPN's, die gebruikmaken van VNet-gateways voor connectiviteit met abonnementen voor abonnementen, worden ondersteund. Aangezien VPN- en VNet-implementaties complexer zijn, u beginnen met een openbaar IP-model wanneer u een oplossing prototypen.

Docker containers worden gebruikt voor betrouwbaarheid en modulariteit. Azure Container Registry wordt gebruikt om versieafbeeldingen te hosten en te serveren als onderdeel van elke implementatie. De containerafbeeldingen bestaan uit:

* Orchestrator - Genereert identiteiten en governance contracten. Slaat identiteiten op in een identiteitswinkel.
* Pariteitsclient - Leases identiteit van de identiteitswinkel. Ontdekt en maakt verbinding met collega's.
* EthStats Agent - Verzamelt lokale logboeken en statistieken via RPC en pusht informatie naar Azure Monitor.
* Governance DApp - Webinterface voor interactie met Governance-contracten.

### <a name="validator-nodes"></a>Validatorknooppunten

In het proof-of-authority protocol nemen validatorknooppunten de plaats in van traditionele mijnwerkerknooppunten. Elke validator heeft een unieke Ethereum identiteit waardoor het kan deelnemen aan het blok creatie proces. Elk consortiumlid kan twee of meer validatorknooppunten in vijf regio's inrichten voor georedundantie. Validatorknooppunten communiceren met andere validatorknooppunten om tot consensus te komen over de status van het onderliggende gedistribueerde grootboek. Om een eerlijke deelname aan het netwerk te garanderen, is het elk consortiumlid verboden om meer validators te gebruiken dan het eerste lid op het netwerk. Als het eerste lid bijvoorbeeld drie validators implementeert, kan elk lid maximaal drie validators hebben.

### <a name="identity-store"></a>Identiteitswinkel

Er wordt een identiteitsarchief geïmplementeerd in het abonnement van elk lid dat veilig de gegenereerde Ethereum-identiteiten bevat. Voor elke validator genereert de orchestration-container een Ethereum-privésleutel en slaat deze op in Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Ethereum consortiumnetwerk implementeren

In deze wandeling door, laten we aannemen dat u het creëren van een multi-party Ethereum consortium netwerk. De volgende stroom is een voorbeeld van een implementatie met meerdere partijen:

1. Drie leden genereren elk een Ethereum-account met MetaMask
1. *Lid A* zet Ethereum PoA in, dat hun ethereum-toespraak
1. *Lid A* verstrekt de URL van het consortium aan *lid B* en *lid C*
1. *Lid B* en *lid C* implementeren, Ethereum PoA, met hun Ethereum Public Address en *Member A's*consortium URL
1. *Lid A* stemt in *lid B* als beheerder
1. *Lid A* en *lid B* stemmen beiden *lid C* als beheerder

In de volgende secties ziet u hoe u de voetafdruk van het eerste lid in het netwerk configureert.

### <a name="create-resource"></a>Bron maken

Selecteer in de [Azure-portal](https://portal.azure.com)De optie **Een resource maken** in de linkerbovenhoek.

Selecteer **Blockchain** > **Ethereum Proof-of-Authority Consortium (preview)**.

### <a name="basics"></a>Basisbeginselen

Geef **onder Basisbeginselen**waarden op voor standaardparameters voor elke implementatie.

![Basisbeginselen](./media/ethereum-poa-deployment/basic-blade.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Een nieuw netwerk maken of lid worden van een bestaand netwerk | U een nieuw consortiumnetwerk maken of lid worden van een reeds bestaand consortiumnetwerk. Het samenvoegen van een bestaand netwerk vereist extra parameters. | Nieuwe maken
E-mailadres | U ontvangt een e-mailmelding wanneer uw implementatie is voltooid met informatie over uw implementatie. | Een geldig e-mailadres
VM-gebruikersnaam | Gebruikersnamen van beheerders van elke geïmplementeerde VM | 1-64 alfanumerieke tekens
Verificatietype | De methode om te verifiëren naar de virtuele machine. | Wachtwoord
Wachtwoord | Het wachtwoord voor het beheerdersaccount voor elk van de virtuele machines die zijn geïmplementeerd. Alle VM's hebben in eerste instantie hetzelfde wachtwoord. U het wachtwoord wijzigen na inrichten. | 12-72 tekens 
Abonnement | Het abonnement om het consortiumnetwerk te implementeren |
Resourcegroep| De resourcegroep waarmee het consortiumnetwerk moet worden geïmplementeerd. | myResourceGroup
Locatie | De Azure-regio voor resourcegroep. | VS - west 2

Selecteer **OK**.

### <a name="deployment-regions"></a>Implementatieregio's

Geef *onder Implementatieregio's*het aantal regio's en locaties voor elk op. U implementeren in maximaal vijf regio's. De eerste regio moet overeenkomen met de locatie resourcegroep van de sectie *Basisbeginselen.* Voor ontwikkelings- of testnetwerken u één regio per lid gebruiken. Voor productie u twee of meer regio's implementeren voor hoge beschikbaarheid.

![implementatieregio's](./media/ethereum-poa-deployment/deployment-regions.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Aantal regio's|Aantal regio's om het consortiumnetwerk te implementeren| 2
Eerste regio | Eerste regio die het consortiumnetwerk implementeert | VS - west 2
Tweede regio | Tweede regio om het consortiumnetwerk te implementeren. Extra regio's zijn zichtbaar wanneer het aantal regio's twee of meer is. | VS - oost 2

Selecteer **OK**.

### <a name="network-size-and-performance"></a>Netwerkgrootte en prestaties

Geef *onder Netwerkgrootte en -prestaties*ingangen op voor de grootte van het consortiumnetwerk. De validator node opslaggrootte bepaalt de potentiële grootte van de blockchain. De grootte kan na implementatie worden gewijzigd.

![Netwerkgrootte en prestaties](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Aantal load balanced validator nodes | Het aantal validatorknooppunten dat moet worden ingerichte als onderdeel van het netwerk. | 2
Validator node opslag prestaties | Het type beheerde schijf voor elk van de geïmplementeerde validatorknooppunten. Zie [opslagprijzen](https://azure.microsoft.com/pricing/details/managed-disks/) voor meer informatie over de prijzen | Standard - SSD
Validator knooppunt virtuele machine grootte | De grootte van de virtuele machine die wordt gebruikt voor validatorknooppunten. Zie prijzen voor [virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) voor meer informatie over de prijzen | Standaard D2 v3

Virtuele machine en opslaglaag beïnvloeden de netwerkprestaties.  Gebruik de volgende tabel om kostenefficiëntie te kiezen:

Virtuele machine SKU|Opslaglaag|Prijs|Doorvoer|Latentie
---|---|---|---|---
F1|Standard - SSD|Lage|Lage|hoog
D2_v3|Standard - SSD|gemiddeld|gemiddeld|gemiddeld
F16's|Premium - SSD|hoog|hoog|Lage

Selecteer **OK**.

### <a name="ethereum-settings"></a>Ethereum-instellingen

Geef *onder Ethereum-instellingen*ethereum-gerelateerde configuratie-instellingen op.

![Ethereum-instellingen](./media/ethereum-poa-deployment/ethereum-settings.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Consortium-lid-id | De ID die is gekoppeld aan elk lid dat deelneemt aan het consortiumnetwerk. Het wordt gebruikt om IP-adresruimten te configureren om botsingen te voorkomen. Voor een privénetwerk moet de leden-id uniek zijn voor verschillende organisaties in hetzelfde netwerk.  Een unieke leden-ID is nodig, zelfs wanneer dezelfde organisatie wordt geïmplementeerd in meerdere regio's. Noteer de waarde van deze parameter, omdat u deze moet delen met andere leden die lid worden om ervoor te zorgen dat er geen botsing is. Het geldige bereik is 0 tot en met 255. | 0
Netwerk-id | De netwerk-ID voor het consortium Ethereum netwerk wordt ingezet. Elk Ethereum-netwerk heeft zijn eigen netwerk-ID, waarbij 1 de ID voor het openbare netwerk is. Het geldige bereik is 5 tot en met 999.999.999 | 10101010
Adres beheerder Ethereum | Het Ethereum-accountadres dat wordt gebruikt voor deelname aan PoA-governance. U MetaMask gebruiken om een Ethereum-adres te genereren. |
Geavanceerde opties | Geavanceerde opties voor Ethereum-instellingen | Inschakelen
Implementeren met openbaar IP-adres | Als Private VNet is geselecteerd, wordt het netwerk geïmplementeerd achter een VNet-gateway en wordt peering-toegang verwijderd. Voor Private VNet moeten alle leden een VNet-gateway gebruiken om de verbinding compatibel te maken. | Openbare IP
Gaslimiet blokkeren | De startblokgaslimiet van het netwerk. | 50000000
Blokresealperiode (sec) | De frequentie waarmee lege blokken worden gemaakt wanneer er geen transacties op het netwerk zijn. Een hogere frequentie zal een snellere finaliteit hebben, maar hogere opslagkosten. | 15
Contract voor transactiemachtigingen | Bytecode voor het contract voor transactiemachtigingen. Beperkt de implementatie en uitvoering van slimme contracten tot een toegestane lijst met Ethereum-accounts. |

Selecteer **OK**.

### <a name="monitoring"></a>Bewaking

Met monitoring u een logboekbron voor uw netwerk configureren. De bewakingsagent verzamelt en surfaces nuttige statistieken en logboeken van uw netwerk, zodat u snel de netwerkstatus of foutopsporingsproblemen controleren.

![Azure-monitor](./media/ethereum-poa-deployment/azure-monitor.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Bewaking | Optie om bewaking in te schakelen | Inschakelen
Verbinding maken met bestaande Azure Monitor-logboeken | Optie om een nieuwe Azure Monitor-logboekeninstantie te maken of lid te worden van een bestaande instantie | Nieuwe maken
Locatie | De regio waar de nieuwe instantie wordt geïmplementeerd | VS - oost
Bestaande werkruimte-id voor logboekanalyse (Verbinding maken met bestaande Azure-monitorlogboeken = Bestaande deelnemen)|Werkruimte-id van de bestaande instantie Azure Monitor-logboeken||N.v.t.
Primaire sleutel voor logboekanalyses (Verbinding maken met bestaande Azure Monitor-logboeken = Bestaande deelnemen)|De primaire sleutel die wordt gebruikt om verbinding te maken met de bestaande instantie Azure Monitor-logboeken||N.v.t.

Selecteer **OK**.

### <a name="summary"></a>Samenvatting

Klik door het overzicht om de opgegeven invoer te bekijken en de basisvalidatie voor pre-deployment uit te voeren. Voordat u wordt geïmplementeerd, u de sjabloon en parameters downloaden.

Selecteer **Maken** om te implementeren.

Als de implementatie VNet-gateways bevat, kan de implementatie 45 tot 50 minuten duren.

## <a name="deployment-output"></a>Implementatieuitvoer

Zodra de implementatie is voltooid, hebt u toegang tot de benodigde parameters via de Azure-portal.

### <a name="confirmation-email"></a>Bevestigingse-mail

Als u een e-mailadres[(sectie Basisbeginselen)](#basics)opgeeft, wordt een e-mail verzonden met de implementatiegegevens en koppelingen naar deze documentatie.

![implementatie-e-mail](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Zodra de implementatie is voltooid en alle resources zijn ingericht, u de uitvoerparameters in uw resourcegroep weergeven.

1. Ga naar uw resourcegroep in de portal.
1. Selecteer **Overzicht > implementaties**.

    ![Overzicht van resourcegroepen](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selecteer de **implementatie van microsoft-azure-blockchain.azure-blockchain-ether-....**
1. Selecteer de sectie **Uitvoer.**

    ![Implementatie-uitvoer](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Groei van het consortium

Als u uw consortium wilt uitbreiden, moet u eerst het fysieke netwerk verbinden. Als u achter een VPN implementeert, raadpleegt u de sectie [VNet Gateway](#connecting-vnet-gateways) verbinden de netwerkverbinding configureren als onderdeel van de implementatie van het nieuwe lid. Zodra uw implementatie is voltooid, gebruikt u de [Governance DApp](#governance-dapp) om netwerkbeheerder te worden.

### <a name="new-member-deployment"></a>Nieuwe implementatie van leden

Deel de volgende informatie met het lid. De informatie is te vinden in uw e-mail na de implementatie of in de implementatievan de portal.

* URL van consortiumgegevens
* Het aantal knooppunten dat u hebt geïmplementeerd
* VNet Gateway Resource ID (bij gebruik van VPN)

Het implementerende lid moet dezelfde Ethereum Proof-of-Authority consortiumoplossingssjabloon gebruiken bij het implementeren van zijn netwerkaanwezigheid met behulp van de volgende richtlijnen:

* Selecteer **Bestaande deelnemen**
* Kies hetzelfde aantal validatorknooppunten als de rest van de leden in het netwerk om een eerlijke vertegenwoordiging te garanderen
* Hetzelfde Adres van Beheerder Ethereum gebruiken
* De opgegeven *url van consortiumgegevens gebruiken* in de *Ethereum-instellingen*
* Als de rest van het netwerk achter een VPN zit, selecteert u **Private VNet** onder de geavanceerde sectie

### <a name="connecting-vnet-gateways"></a>VNet-gateways verbinden

Deze sectie is alleen vereist als u een privé VNet hebt geïmplementeerd. U deze sectie overslaan als u openbare IP-adressen gebruikt.

Voor een privénetwerk worden de verschillende leden verbonden via VNet-gatewayverbindingen. Voordat een lid lid lid kan toetreden tot het netwerk en transactieverkeer kan zien, moet een bestaand lid een definitieve configuratie uitvoeren op hun VPN-gateway om de verbinding te accepteren. De Ethereum-knooppunten van het deelnemende lid worden pas uitgevoerd als er een verbinding is gemaakt. Maak redundante netwerkverbindingen in het consortium om de kans op één storingspunt te verkleinen.

Nadat het nieuwe lid is geïmplementeerd, moet het bestaande lid de bidirectionele verbinding voltooien door een VNet-gatewayverbinding met het nieuwe lid in te stellen. Het bestaande lid heeft behoefte aan:

* De VNet-gateway ResourceID van het verbindende lid. Zie [implementatieuitvoer](#deployment-output).
* De gedeelde verbindingssleutel.

Het bestaande lid moet het volgende PowerShell-script uitvoeren om de verbinding te voltooien. U Azure Cloud Shell gebruiken in de navigatiebalk rechtsboven in de portal.

![cloudshell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Servicecontrole

U uw Azure Monitor-portal vinden door de koppeling in de implementatie-e-mail te volgen of de parameter in de implementatieuitvoer te lokaliseren [OMS_PORTAL_URL].

De portal toont eerst netwerkstatistieken op hoog niveau en knooppuntoverzicht.

![Categorieën controleren](./media/ethereum-poa-deployment/monitor-categories.png)

Als u **knooppuntoverzicht** selecteert, ziet u infrastructuurstatistieken per knooppunt.

![Knooppuntstatistieken](./media/ethereum-poa-deployment/node-stats.png)

Als u **netwerkstatistieken selecteert,** ziet u ethereum-netwerkstatistieken.

![Netwerkstatistieken](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Voorbeeld van Kusto-query's

U de bewakingslogboeken opvragen om fouten of instellingsdrempelwaarschuwingen te onderzoeken. De volgende query's zijn voorbeelden die u uitvoeren in het gereedschap *Logboekzoeken:*

Lijstblokken die door meer dan één validatorquery zijn gerapporteerd, kunnen handig zijn om kettingvorken te vinden.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Ontvang het gemiddelde aantal peer voor een opgegeven validatorknooppunt gemiddeld meer dan 5 minuten buckets.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH-toegang

Om veiligheidsredenen wordt de SSH-poorttoegang standaard geweigerd door een beveiligingsregel voor netwerkgroepen. Als u toegang wilt krijgen tot de virtuele machine-exemplaren in het PoA-netwerk, moet u de volgende beveiligingsregel wijzigen om *toe te staan.*

1. Ga naar het gedeelte **Overzicht** van de geïmplementeerde resourcegroep in de Azure-portal.

    ![ssh overzicht](./media/ethereum-poa-deployment/ssh-overview.png)

1. Selecteer de **netwerkbeveiligingsgroep** voor de regio van de VM die u wilt openen.

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Selecteer de **allow-ssh-regel.**

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

1. **Actie wijzigen** om toe te **staan**

    ![ssh inschakelen mogelijk te maken](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Selecteer **Opslaan**. Het kan enkele minuten duren voordat wijzigingen zijn toegepast.

U op afstand verbinding maken met de virtuele machines voor de validatorknooppunten via SSH met uw opgegeven gebruikersnaam en wachtwoord/SSH-sleutel. De opdracht SSH om toegang te krijgen tot het eerste validatorknooppunt wordt weergegeven in de implementatieuitvoer van de sjabloon. Bijvoorbeeld:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Als u naar extra transactieknooppunten wilt gaan, verhoogt u het poortnummer met één.

Als u in meer dan één regio bent geïmplementeerd, wijzigt u de opdracht in de DNS-naam of het IP-adres van de load balancer in dat gebied. Als u de DNS-naam of het IP-adres van de andere regio's wilt vinden, zoekt u de bron met de naamgevingsconventie ** \* \* \* \* \*-lbpip-reg\# ** en bekijkt u de DNS-naam en IP-adreseigenschappen.

## <a name="azure-traffic-manager-load-balancing"></a>Problemen met de taakverdeling van Azure Traffic Manager

Azure Traffic Manager kan helpen downtime te verminderen en de reactiesnelheid van het PoA-netwerk te verbeteren door binnenkomend verkeer over meerdere implementaties in verschillende regio's te routeren. Ingebouwde statuscontroles en automatische omleidingzorgen zorgen voor een hoge beschikbaarheid van de RPC-eindpunten en de Governance DApp. Deze functie is handig als u naar meerdere regio's bent geïmplementeerd en klaar bent voor de productie.

Gebruik Traffic Manager om de beschikbaarheid van het PoA-netwerk te verbeteren met automatische failover. U Traffic Manager ook gebruiken om de reactiesnelheid van uw netwerken te vergroten door eindgebruikers te routeren naar de Azure-locatie met de laagste netwerklatentie.

Als u besluit een Traffic Manager-profiel te maken, u de DNS-naam van het profiel gebruiken om toegang te krijgen tot uw netwerk. Zodra andere consortiumleden aan het netwerk zijn toegevoegd, kan de Traffic Manager ook worden gebruikt om het saldo over hun geïmplementeerde validators te laden.

### <a name="creating-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

1. Selecteer in de [Azure-portal](https://portal.azure.com)De optie **Een resource maken** in de linkerbovenhoek.
1. Zoeken naar **traffic manager profiel**.

    ![Zoeken naar Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Geef het profiel een unieke naam en selecteer de resourcegroep die is gebruikt voor de PoA-implementatie.

1. Selecteer **Maken** om te implementeren.

    ![Verkeersbeheer maken](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Zodra deze is geïmplementeerd, selecteert u de instantie in de resourcegroep. De DNS-naam om toegang te krijgen tot de verkeersbeheerder is te vinden op het tabblad Overzicht.

    ![Dns-beheerder zoeken](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Kies het tabblad **Eindpunten** en selecteer de knop **Toevoegen.**
1. Geef het eindpunt een unieke naam.
1. Kies **voor Doelbrontype** **Openbaar IP-adres**.
1. Kies het openbare IP-adres van de load balancer van de eerste regio.

    ![Routeringsbeheer](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Herhaal dit voor elke regio in het geïmplementeerde netwerk. Zodra de eindpunten zich in de ingeschakelde status **bevinden,** worden ze automatisch geladen en regio-gebalanceerd op de DNS-naam van de verkeersbeheerder. U deze DNS-naam nu gebruiken in plaats van de parameter [CONSORTIUM_DATA_URL] in andere stappen van het artikel.

## <a name="data-api"></a>Gegevens-API

Elk consortiumlid host de benodigde informatie voor anderen om verbinding te maken met het netwerk. Om het gemak van connectiviteit mogelijk te maken, host elk lid een set verbindingsgegevens op het eindpunt van de data-API.

Het bestaande lid biedt de [CONSORTIUM_DATA_URL] vóór de implementatie van het lid. Bij de implementatie haalt een lid van de join informatie op uit de JSON-interface op het volgende eindpunt:

`<CONSORTIUM_DATA_URL>/networkinfo`

Het antwoord bevat informatie die nuttig is voor het toetreden van leden (Genesis-blok, Validator Set contract ABI, bootnodes) en informatie die nuttig is voor het bestaande lid (validatoradressen). U deze standaardisatie gebruiken om het consortium uit te breiden naar cloudproviders. Deze API retourneert een JSON-opgemaakte respons met de volgende structuur:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>Governance DApp

De kern van proof-of-authority is gedecentraliseerd bestuur. Aangezien proof-of-authority is gebaseerd op een toegestane lijst van netwerkautoriteiten om het netwerk gezond te houden, is het belangrijk om een eerlijk mechanisme te bieden om wijzigingen aan te brengen in deze machtigingslijst. Elke implementatie wordt geleverd met een reeks smart-contracts en portal voor on-chain governance van deze toegestane lijst. Zodra een voorgestelde wijziging een meerderheidsstemming door consortiumleden bereikt, wordt de wijziging vastgesteld. Door te stemmen kunnen nieuwe consensusdeelnemers worden toegevoegd of kunnen gecompromitteerde deelnemers op een transparante manier worden verwijderd die een eerlijk netwerk aanmoedigt.

De governance DApp is een set van vooraf [geïmplementeerde slimme contracten](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) en een webapplicatie die worden gebruikt om de autoriteiten op het netwerk te besturen. Autoriteiten zijn opgesplitst in admin identiteiten en validator knooppunten.
Beheerders hebben de bevoegdheid om consensusdeelname te delegeren aan een set validatorknooppunten. Beheerders kunnen ook stemmen andere admins in of uit het netwerk.

![Governance DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Gedecentraliseerde governance:** Wijzigingen in netwerkautoriteiten worden beheerd door stemmen in de keten door geselecteerde beheerders.
* **Delegatie validator:** Autoriteiten kunnen hun validatorknooppunten beheren die zijn ingesteld in elke PoA-implementatie.
* **Controleerbare wijzigingsgeschiedenis:** Elke wijziging wordt geregistreerd op de blockchain die transparantie en controleerbaarheid biedt.

### <a name="getting-started-with-governance"></a>Aan de slag met governance

Om elke vorm van transacties uit te voeren via de Governance DApp, moet u een Ethereum-portemonnee gebruiken. De meest eenvoudige aanpak is het gebruik van een in-browser portemonnee zoals [MetaMask](https://metamask.io); Omdat deze slimme contracten echter op het netwerk worden geïmplementeerd, u uw interacties met het Governance-contract ook automatiseren.

Nadat u MetaMask hebt geïnstalleerd, navigeert u naar de Governance DApp in de browser.  U de URL vinden via azure portal in de implementatie-uitvoer.  Als u geen portemonnee in de browser hebt geïnstalleerd, u geen acties uitvoeren. U echter de beheerdersstatus weergeven.  

### <a name="becoming-an-admin"></a>Beheerder worden

Als u het eerste lid bent dat op het netwerk is geïmplementeerd, wordt u automatisch beheerder en worden uw pariteitsknooppunten vermeld als validators. Als u lid wordt van het netwerk, moet u met een meerderheid (meer dan 50%) als beheerder worden gestemd. van de bestaande beheerset. Als u ervoor kiest geen beheerder te worden, synchroniseren en valideren uw knooppunten de blockchain nog steeds; ze nemen echter niet deel aan het blokcreatieproces. Als u het stemproces wilt starten om beheerder te worden, selecteert u **Nomineren** en voert u uw Ethereum-adres en alias in.

![Benoemen](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Kandidaten

Als u het tabblad **Kandidaten** selecteert, ziet u de huidige set kandidaatbeheerders.  Zodra een kandidaat een meerderheidsstem door de huidige admins bereikt, wordt de kandidaat bevorderd aan een admin.  Als u op een kandidaat wilt stemmen, selecteert u de rij en selecteert **u Stemmen in**. Als je van gedachten verandert bij een stemming, selecteer je de kandidaat en selecteer **je De stem intrekken**.

![Kandidaten](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Beheerders

Het tabblad **Beheerders** toont de huidige set beheerders en biedt u de mogelijkheid om tegen te stemmen.  Zodra een beheerder meer dan 50% ondersteuning verliest, worden ze verwijderd als beheerder op het netwerk. Elke validator-knooppunten waarvan de beheerder eigenaar is, verliezen de validatorstatus en worden transactieknooppunten op het netwerk. Een beheerder kan om verschillende redenen worden verwijderd; Het is echter aan het consortium om vooraf een beleid af te spreken.

![Beheerders](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validators

Als u het tabblad **Validators selecteert,** worden de huidige geïmplementeerde pariteitsknooppunten voor de instantie en hun huidige status (knooppunttype) weergegeven. Elk consortiumlid heeft een andere set validators in deze lijst, omdat deze weergave het huidige geïmplementeerde consortiumlid vertegenwoordigt. Als de instantie onlangs is geïmplementeerd en u uw validators niet hebt toegevoegd, krijgt u de optie **Validators toevoegen.** Het toevoegen van validators kiest automatisch een regionaal gebalanceerde set pariteitsknooppunten en wijst deze toe aan uw validatorset. Als u meer knooppunten hebt geïmplementeerd dan de toegestane capaciteit, worden de resterende knooppunten transactieknooppunten op het netwerk.

Het adres van elke validator wordt automatisch toegewezen via het [identiteitsarchief](#identity-store) in Azure.  Als een knooppunt naar beneden gaat, geeft het zijn identiteit op, waardoor een ander knooppunt in uw implementatie zijn plaats inneemt. Dit proces zorgt ervoor dat uw consensusparticipatie zeer beschikbaar is.

![Validators](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Consortiumnaam

Elke beheerder kan de naam van het consortium bijwerken.  Selecteer het tandwielpictogram linksboven om de naam van het consortium bij te werken.

### <a name="account-menu"></a>Menu Account

Rechtsboven staat je Ethereum account alias en identicon.  Als je een beheerder bent, kun je je alias bijwerken.

![Account](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Ontwikkeling Ethereum<a id="tutorials"></a>

Om slimme contracten samen te stellen, te implementeren en te testen, zijn hier een paar opties die u overwegen voor ethereum-ontwikkeling:
* [Truffel Suite](https://www.trufflesuite.com/docs/truffle/overview) - Client-based Ethereum ontwikkelomgeving
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Browser-based en lokale Ethereum ontwikkelomgeving

### <a name="compile-deploy-and-execute-smart-contract"></a>Slim contract compileren, implementeren en uitvoeren

In het volgende voorbeeld maakt u een eenvoudig slim contract. Je gebruikt Truffel om het slimme contract samen te stellen en te implementeren in je blockchain-netwerk. Eenmaal geïmplementeerd, belt u een slimme contractfunctie via een transactie.

#### <a name="prerequisites"></a>Vereisten

* [Python 2.7.15 installeren](https://www.python.org/downloads/release/python-2715/). Python is nodig voor Truffel en Web3. Selecteer de installatieoptie om Python op uw pad op te nemen.
* Installeer Truffel v5.0.5 `npm install -g truffle@v5.0.5`. Truffel vereist verschillende tools worden geïnstalleerd, waaronder [Node.js](https://nodejs.org), [Git](https://git-scm.com/). Zie [Truffeldocumentatie](https://github.com/trufflesuite/truffle)voor meer informatie.

### <a name="create-truffle-project"></a>Maak truffelproject

Voordat u een slim contract compileren en implementeren, moet u een Truffel-project maken.

1. Open een opdrachtprompt of shell.
1. Maak een map met de naam `HelloWorld`.
1. Map wijzigen in `HelloWorld` de nieuwe map.
1. Initialiseer een nieuw Truffel `truffle init`project met behulp van het commando .

    ![Maak een nieuw truffelproject](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Een slim contract toevoegen

Maak uw slimme contracten in de **contracten** subdirectory van uw Truffel project.

1. Maak een bestand `postBox.sol` in de naam in de **submap contracten** van uw Truffel-project.
1. Voeg de volgende soliditeitscode toe aan **postBox.sol**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Implementeer slim contract met truffel

Truffelprojecten bevatten een configuratiebestand voor blockchain-netwerkverbindingsgegevens. Wijzig het configuratiebestand om de verbindingsgegevens voor uw netwerk op te nemen.

> [!WARNING]
> Stuur nooit uw Ethereum privésleutel via het netwerk. Zorg ervoor dat elke transactie eerst lokaal wordt ondertekend en dat de ondertekende transactie via het netwerk wordt verzonden.

1. Je hebt de ezelsbruggetje nodig voor het [Ethereum-beheerdersaccount dat wordt gebruikt bij het implementeren van je blockchain-netwerk.](#ethereum-settings) Als u MetaMask hebt gebruikt om het account te maken, u het ezelsbruggetje ophalen bij MetaMask. Selecteer het beheerdersaccountpictogram rechtsboven in de MetaMask-extensie en selecteer **Instellingen > Beveiliging & Privacy > Onthullen Seed Words**.
1. Vervang de `truffle-config.js` inhoud van uw truffelproject door de volgende inhoud. Vervang de tijdelijke aanduiding eindpunt en ezelsbruggetje waarden.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Aangezien we de Truffel HD Wallet provider gebruiken, installeert `npm install truffle-hdwallet-provider --save`u de module in uw project met behulp van de opdracht.

Truffel gebruikt migratiescripts om slimme contracten te implementeren in een blockchain-netwerk. U hebt een migratiescript nodig om uw nieuwe slimme contract te implementeren.

1. Voeg een nieuwe migratie toe om het nieuwe contract te implementeren. Maak `2_deploy_contracts.js` bestand in de **submap migraties** van het Truffel-project.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Implementeren in het PoA-netwerk met de opdracht Truffel migreren. Voer bij de opdrachtprompt in de truffelprojectmap het als:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Een slimme contractfunctie aanroepen

Nu uw slimme contract is geïmplementeerd, u een transactie verzenden om een functie aan te roepen.

1. Maak in de map Truffelproject `sendtransaction.js`een nieuw bestand met de naam .
1. Voeg de volgende inhoud toe aan **sendtransaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Voer het script uit met de opdracht Truffel uitvoeren.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Script-to-callfunctie uitvoeren via transactie](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>WebAssembly (WASM) ondersteuning

WebAssembly-ondersteuning is al voor u ingeschakeld op nieuw geïmplementeerde PoA-netwerken. Het maakt smart-contract ontwikkeling mogelijk in elke taal die overgaat naar Web-Assembly (Rust, C, C++). Zie voor meer informatie: [Parity Overview of WebAssembly](https://wiki.parity.io/WebAssembly-Home) and [Tutorial from Parity Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>Veelgestelde vragen

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Ik merk dat er veel transacties op het netwerk zijn die ik niet heb verzonden. Waar komen deze vandaan?

Het is onveilig om de [persoonlijke API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)te ontgrendelen. Bots luisteren naar ontgrendelde Ethereum-accounts en proberen de fondsen leeg te lopen. De bot gaat ervan uit dat deze accounts bevatten real-ether en proberen om de eerste te zijn om het saldo overhevelen. Schakel de persoonlijke API in het netwerk niet in. In plaats daarvan vooraf ondertekenen van de transacties, hetzij handmatig met behulp van een portemonnee zoals MetaMask of programmatisch.

### <a name="how-to-ssh-onto-a-vm"></a>Hoe SSH op een VM?

De SSH-poort wordt om veiligheidsredenen niet blootgesteld. Volg [deze handleiding om de SSH-poort in te schakelen.](#ssh-access)

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hoe stel ik een auditlid of transactieknooppunten in?

Transactieknooppunten zijn een set pariteitsclients die met het netwerk zijn gekeken, maar niet deelnemen aan consensus. Deze knooppunten kunnen nog steeds worden gebruikt om Ethereum-transacties in te dienen en de slimme contractstatus te lezen. Dit mechanisme werkt voor het verstrekken van controleerbaarheid aan niet-gezagconsortiumleden op het netwerk. Om dit te bereiken, volg de stappen in [de groei van het consortium](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Waarom duren MetaMask-transacties lang?

Om ervoor te zorgen transacties worden ontvangen in de juiste volgorde, elke Ethereum transactie wordt geleverd met een incrementele nonce. Als u een account in MetaMask op een ander netwerk hebt gebruikt, moet u de nonce-waarde opnieuw instellen. Klik op het pictogram Instellingen (drie balken), Instellingen, Account opnieuw instellen. De transactiegeschiedenis wordt gewist en nu u de transactie opnieuw indienen.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Moet ik gaskosten opgeven in MetaMask?

Ether dient geen doel in proof-of-authority consortium. Daarom is het niet nodig om gaskosten te specificeren bij het indienen van transacties in MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Wat moet ik doen als mijn implementatie mislukt omdat azure OMS niet is ingericht?

Monitoring is een optionele functie. In enkele zeldzame gevallen waarin uw implementatie mislukt vanwege het niet meer mogelijk om Azure Monitor-bron in te richten, u opnieuw worden geïmplementeerd zonder Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Zijn openbare IP-implementaties compatibel met implementaties van privénetwerken?

Nee. Peering vereist tweerichtingscommunicatie, zodat het hele netwerk openbaar of privé moet zijn.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Wat is de verwachte transactiedoorvoer van Proof-of-Authority?

De transactiedoorvoer is sterk afhankelijk van de typen transacties en de netwerktopologie. Met behulp van eenvoudige transacties hebben we gemiddeld 400 transacties per seconde gebenchmarkt met een netwerk dat in meerdere regio's wordt geïmplementeerd.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hoe kan ik me abonneren op slimme contractevenementen?

Ethereum Proof-of-Authority ondersteunt nu web-sockets.  Controleer uw implementatieuitvoer om de URL en poort van de websocket te vinden.

## <a name="next-steps"></a>Volgende stappen

Zie de [Azure Blockchain-documentatie](https://docs.microsoft.com/azure/blockchain/)voor meer Azure Blockchain-oplossingen.
