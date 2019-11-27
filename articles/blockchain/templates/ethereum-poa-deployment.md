---
title: Ethereum proof-of-Authority consortium-oplossings sjabloon implementeren in azure
description: Gebruik de Ethereum proof-of-Authority consortium-oplossing voor het implementeren en configureren van een consortium Ethereum Network voor meerdere leden op Azure
ms.date: 04/08/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: c3e449c1d6ebaf7c6cb2c35dc9f91d55f569447a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326174"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Ethereum proof-of-Authority consortium-oplossings sjabloon implementeren in azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[De sjabloon Ethereum proof-of-Authority consortium Azure-oplossing](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) is ontworpen om het eenvoudiger te maken, configureren en beheren van een Ethereum netwerk van een consortium met meerdere leden met minimale Azure-en Ethereum-kennis.

Met een aantal gebruikers invoer en een implementatie met één klik via de Azure Portal, kan elk lid een netwerk footprint inrichten, met behulp van Microsoft Azure compute-, netwerk-en opslag Services over de hele wereld. De netwerk footprint van elk lid bestaat uit een verzameling knoop punten met gelijke taak verdeling waarmee een toepassing of gebruiker Ethereum-trans acties kan verzenden.

## <a name="concepts"></a>Concepten

### <a name="terminology"></a>Terminologie

-   **Consensus** : de handeling van het synchroniseren van gegevens in het gedistribueerde netwerk via blok validatie en maken.

-   **Lid** van het consortium: een entiteit die deel uitmaakt van consensus op het block chain-netwerk.

-   **Beheerder** : een Ethereum-account dat wordt gebruikt om de deelname van een gegeven consortium te beheren.

-   **Validator** : een computer die is gekoppeld aan een Ethereum-account dat deel uitmaakt van consensus namens een beheerder.

### <a name="proof-of-authority"></a>Bewijs van certificering

Voor degenen die geen ervaring hebben met de Block Chain-Community, is de release van deze oplossing een uitstekende kans om meer te weten te komen over de technologie op een gemakkelijke en configureer bare manier op Azure. Proof-of-work is een Sybil mechanisme dat gebruikmaakt van reken kosten voor het zelf reguleren van het netwerk en het toestaan van eerlijke deelname. Dit werkt prima in anonieme, open Block chain-netwerken waarbij de concurrentie voor cryptocurrency beveiliging op het netwerk bevordert. In particuliere/consortium netwerken is de onderliggende ether echter geen waarde. Een alternatief protocol, bewijs van certificering, is geschikter voor toegestane netwerken waarbij alle deel nemers van de consensus bekend zijn en betrouwbaar zijn. Zonder dat de analyse nodig is, is de controle van de instantie efficiënter en blijft de Byzantine-fout tolerantie behouden.

### <a name="consortium-governance"></a>Consortium governance

Omdat de controle van de instantie afhankelijk is van een lijst met netwerk instanties om het netwerk in orde te laten blijven, is het belang rijk om een redelijk mechanisme te bieden voor het wijzigen van de machtigingen lijst. Elke implementatie wordt geleverd met een set slimme contracten en een portal voor beheer op basis van de keten van deze toegestane lijst. Zodra een voorgestelde wijziging een meerderheids stem heeft bereikt door consortium leden, wordt de wijziging aangenomen. Op deze manier kunnen deel nemers aan nieuwe consensus worden toegevoegd of geknoeid, zodat ze op transparante wijze kunnen worden verwijderd.

### <a name="admin-account"></a>Beheerders account

Tijdens de implementatie van de knoop punten van de controle-instanties wordt u gevraagd om een Ethereum-adres voor de beheerder. U kunt verschillende methoden gebruiken om dit Ethereum-account te genereren en te beveiligen. Zodra dit adres als een autoriteit op het netwerk is toegevoegd, kunt u dit account gebruiken om deel te nemen aan governance. Dit beheerders account wordt ook gebruikt voor het delegeren van consensus deelname aan de validatie knooppunten die zijn gemaakt als onderdeel van deze implementatie. Omdat alleen het open bare Ethereum-adres wordt gebruikt, heeft elke beheerder de flexibiliteit om hun persoonlijke sleutels te beveiligen op een manier die het gewenste beveiligings model volgt.

### <a name="validator-node"></a>Validatie knooppunt

In het test-of-Authority-protocol nemen validatie knooppunten de plaats van traditionele Miner-knoop punten. Elke validator heeft een unieke Ethereum-identiteit die wordt toegevoegd aan een lijst met machtigingen voor een Smart-contract. Zodra een validatie functie is opgenomen in deze lijst, kan deze deel nemen aan het proces voor het maken van een blok kering. Zie de documentatie van de pariteit over de [autoriteit Round consensus](https://wiki.parity.io/Aura)voor meer informatie over dit proces. Elk consortium kan twee of meer validatie knooppunten voor vijf regio's inrichten voor geo-redundantie. Validatie knooppunten communiceren met andere knoop punten voor validatie om tot consensus te komen in de status van het onderliggende gedistribueerde groot boek.
Om te zorgen voor een billijke deelname aan het netwerk, mag elk consortium geen meer validatie functies gebruiken dan het eerste lid van het netwerk (als het eerste lid drie validatie functies implementeert, kan elk lid slechts Maxi maal drie validatie functies hebben).

### <a name="identity-store"></a>Identiteits opslag

Omdat elk lid meerdere knoop punten voor validatie gelijktijdig uitvoert en elk knoop punt moet een toegestane identiteit hebben, is het belang rijk dat de validators veilig een unieke actieve identiteit in het netwerk kunnen verkrijgen. Om dit eenvoudiger te maken, hebben we een identiteits opslag gemaakt die wordt geïmplementeerd in het abonnement van elk lid waar de gegenereerde Ethereum-identiteiten veilig worden bewaard. Tijdens de implementatie genereert de Orchestration-container een persoonlijke sleutel Ethereum voor elke validator en slaat deze op in Azure Key Vault. Voordat het pariteits knooppunt wordt gestart, haalt het eerst een lease op voor een ongebruikte identiteit om ervoor te zorgen dat de identiteit niet wordt opgehaald door een ander knoop punt. De identiteit wordt aan de client verstrekt, waarmee IT de bevoegdheid krijgt om blokken te maken. Als de hosting-VM een storing ondervindt, wordt de identiteits lease vrijgegeven, waardoor een vervangend knoop punt zijn identiteit in de toekomst kan hervatten.

### <a name="bootnode-registrar"></a>Registratie van Bootnode

Om het gemak van de connectiviteit mogelijk te maken, host elk lid een set verbindings gegevens op het [Data API-eind punt](#data-api). Deze gegevens bevatten een lijst met bootnodes die als peering-knoop punten voor het lid worden geleverd. Als onderdeel van deze gegevens-API houden we deze bootnode-lijst up-to-date

### <a name="bring-your-own-operator"></a>Uw eigen operator meenemen

Een consortium wil vaak deel nemen aan netwerk beheer, maar u hoeft de infra structuur niet te beheren en te onderhouden. In tegens telling tot traditionele systemen kan een enkele operator in het netwerk worden gebruikt voor het gedecentraliseerde model van Block Chain-systemen. In plaats van een gecentraliseerde tussen persoon in te huren voor het uitvoeren van een netwerk, kan elk consortium-lid het infrastructuur beheer delegeren aan de operator van hun keuze. Hierdoor kan een hybride model waarin elk lid kan kiezen om hun eigen infra structuur of een gedelegeerde bewerking naar een andere partner uit te voeren. De werk stroom gedelegeerde bewerking werkt als volgt:

1.  Het **consortium-lid** genereert een Ethereum-adres (houdt een persoonlijke sleutel)

2.  **Lid** van het consortium biedt een open bare Ethereum-adres voor **operator**

3.  **Operator** implementeert en configureert de PoA validator-knoop punten met behulp van onze Azure Resource Manager-oplossing

4.  **Operator** levert het RPC-en beheer eindpunt aan het **consortium**

5.  **Lid** van het consortium gebruikt hun persoonlijke sleutel voor het ondertekenen van een aanvraag die de **operator** voor validatie knooppunten accepteert en die is geïmplementeerd voor deelname aan hun naam

### <a name="azure-monitor"></a>Azure Monitor

Deze oplossing wordt ook geleverd met Azure Monitor voor het bijhouden van knoop punten en netwerk statistieken. Voor ontwikkel aars van toepassingen biedt dit inzicht in de onderliggende Block chain om statistieken voor het genereren van blokken bij te houden. Netwerk operators kunnen Azure Monitor gebruiken om snel netwerk storingen te detecteren en te voor komen via infrastructuur statistieken en query bare Logboeken. Zie [service monitoring](#service-monitoring)voor meer informatie.

### <a name="deployment-architecture"></a>Implementatie architectuur

#### <a name="description"></a>Beschrijving

Met deze oplossing kunt u een multi-Ethereum-consortium op basis van één regio of meerdere regio's implementeren. De RPC-en peering-eind punten zijn standaard toegankelijk via het open bare IP-adres om vereenvoudigde connectiviteit tussen abonnementen en Clouds mogelijk te maken. We raden u aan [de machtigings contracten van de pariteit](https://wiki.parity.io/Permissioning) te gebruiken voor toegangs beheer op toepassings niveau. We bieden ook ondersteuning voor netwerken die zijn geïmplementeerd achter Vpn's, die gebruikmaken van VNet-gateways voor connectiviteit tussen abonnementen. Deze implementaties zijn complexer. het wordt daarom aangeraden eerst te beginnen met het open bare IP-model.

#### <a name="consortium-member-overview"></a>Overzicht van het consortium

De implementatie van elk consortium omvat:

-   Virtual Machines voor het uitvoeren van de PoA-validatie functies

-   Azure Load Balancer voor het distribueren van RPC-, peering-en governance DApp-aanvragen

-   Azure Key Vault voor het beveiligen van de validator-identiteiten

-   Azure Storage voor het hosten van permanente netwerk gegevens en het coördineren van leasing

-   Azure Monitor voor het samen voegen van Logboeken en prestatie statistieken

-   VNet-gateway (optioneel) voor het toestaan van VPN-verbindingen tussen privé-VNets

![Implementatie architectuur](./media/ethereum-poa-deployment/deployment-architecture.png)

We maken gebruik van docker-containers voor betrouw baarheid en modulariteit. We gebruiken Azure Container Registry om geversiete installatie kopieën te hosten en te leveren als onderdeel van elke implementatie. De container installatie kopieën bestaan uit:

-   Orchestrator

    -   Wordt eenmaal uitgevoerd tijdens de implementatie

    -   Hiermee worden identiteits-en beheer contracten gegenereerd

    -   Slaat identiteiten op in identiteits opslag

-   Pariteits-client

    -   Lease-identiteit uit identiteits archief

    -   Detecteert en maakt verbinding met peers

-   EthStats-agent

    -   Verzamelt lokale logboeken en statistieken via RPC en pushes naar Azure Monitor

-   Governance DApp

    -   Webinterface voor interactie met Beheer contracten

## <a name="how-to-guides"></a>Handleidingen
### <a name="governance-dapp"></a>Governance DApp

De kern van het controle bewijs is gedecentraliseerd bestuur. De governance DApp is een reeks vooraf geïmplementeerde [slimme contracten](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) en een webtoepassing die wordt gebruikt om de autoriteiten op het netwerk te beheren.
Instanties worden opgedeeld in beheerders identiteiten en validatie knooppunten.
Beheerders hebben de bevoegdheid om consensus te delegeren aan een set validatie knooppunten. Beheerders kunnen ook andere beheerders aan of uit het netwerk stemmen.

![governance dapp](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Gedecentraliseerde governance-** Wijzigingen in de netwerk instanties worden beheerd via de stemming op de keten door beheerders te selecteren.

-   Controle van **validatie functies-** Instanties kunnen de validatie knooppunten beheren die in elke PoA-implementatie zijn ingesteld.

-   Bewaking van **controle bare wijzigingen-** Elke wijziging wordt vastgelegd op de Block chain die transparantie en controle biedt.

#### <a name="getting-started-with-governance"></a>Aan de slag met governance
Als u trans acties wilt uitvoeren via de governance DApp, moet u gebruikmaken van een Ethereum wallet.  De meest eenvoudige aanpak is het gebruik van een in-browser wallet zoals het deel [masker](https://metamask.io). omdat deze bijvoorbeeld slimme contracten op het netwerk zijn geïmplementeerd, kunt u ook uw interacties voor het beheer contract automatiseren.

Nadat u het DApp hebt geïnstalleerd, gaat u naar het beheer beleid in de browser.  U kunt de URL vinden in het bevestigings bericht voor de implementatie of via Azure Portal in de implementatie-uitvoer.  Als er geen in-browser wallet is geïnstalleerd, kunt u geen acties uitvoeren. u kunt echter wel de beheerders status lezen.  

#### <a name="becoming-an-admin"></a>Een beheerder worden
Als u het eerste lid bent dat op het netwerk is geïmplementeerd, wordt u automatisch een beheerder en worden uw pariteits knooppunten weer gegeven als validatie functies.  Als u lid wordt van het netwerk, moet u worden afgestemd als beheerder met een meerderheid (groter dan 50%) van de bestaande beheerder.  Als u ervoor kiest geen beheerder te worden, zullen uw knoop punten de Block chain nog steeds synchroniseren en valideren. ze zullen echter niet deel nemen aan het proces voor het maken van een blok kering. Als u het stem proces wilt starten om een beheerder te worden, klikt u op __benoemen__ en voert u uw Ethereum-adres en-alias in.

![Benoemen](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Uitgesloten
Als u het tabblad __kandidaten__ selecteert, wordt de huidige set kandidaten beheerders weer gegeven.  Zodra een kandidaat een meerderheids stem heeft bereikt door de huidige beheerders, wordt de kandidaat gepromoveerd naar een beheerder.  Als u wilt stemmen op een kandidaat, selecteert u de rij en klikt u bovenaan op ' stemmen '.  Als u van gedachten verandert in een stem, kunt u de kandidaat selecteren en op ' Rescind stemmen ' klikken.

![Uitgesloten](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Beheerders
Het tabblad __beheerders__ toont de huidige set beheerders en biedt u de mogelijkheid om te stemmen.  Zodra een beheerder meer dan 50% van de ondersteuning verliest, wordt deze verwijderd als beheerder op het netwerk.  Alle validatie knooppunten waarvan deze beheerder eigenaar is, verliezen de validatie status en worden transactie knooppunten in het netwerk.  Een beheerder kan om verschillende redenen worden verwijderd. het is echter wel aan het consortium om op voor hand akkoord te gaan met een beleid.

![Beheerders](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Controles
Als u het tabblad __validators__ in het linkermenu selecteert, worden de huidige geïmplementeerde pariteits knooppunten voor deze instantie en hun huidige status (knooppunt type) weer gegeven.  Elk consortium heeft een andere set validatie functies in deze lijst, aangezien deze weer gave het huidige geïmplementeerde consortium-lid vertegenwoordigt.  Als dit een pas geïmplementeerd exemplaar is en u nog geen validatie functies hebt toegevoegd, wordt u de optie voor het toevoegen van validatie functies weer gegeven.  Als u deze optie selecteert, wordt automatisch een regio met pariteits knooppunt verdeling gekozen en toegewezen aan de validatieset.  Als u meer knoop punten hebt geïmplementeerd dan de toegestane capaciteit, worden de andere knoop punten transactie knooppunten in het netwerk.

Het adres van elke validator wordt automatisch toegewezen via het [identiteits archief](#identity-store) in Azure.  Als een knoop punt uitvalt, wordt de identiteit ervan relinquish, zodat er een ander knoop punt in uw implementatie kan worden geplaatst.  Zo zorgt u ervoor dat de deelname van consensus Maxi maal beschikbaar is.

![Controles](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Consortium naam
Elke beheerder kan de naam van het consortium bijwerken, dat boven aan de pagina wordt weer gegeven.  Selecteer het tandwiel pictogram in de linkerbovenhoek om de naam van het consortium bij te werken.

#### <a name="account-menu"></a>Account menu
In de rechter bovenhoek vindt u de alias van uw Ethereum-account en identicon.  Als u een beheerder bent, hebt u de mogelijkheid om uw alias bij te werken.

![Account](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Ethereum-test-of-Authority implementeren

Hier volgt een voor beeld van een implementatie stroom met meerdere partijen:

1.  Drie leden genereren een Ethereum-account met behulp van het gebruik van een gebruikers sjabloon

2.  *Lid A* implements Ethereum PoA, dat hun Ethereum open bare adres biedt

3.  *Lid A* levert de consortium-URL naar *lid B* en *lid C*

4.  *Lid B* en *lid C* implementeren, Ethereum PoA, voorzien van hun Ethereum open bare adres en de consortium-URL van *een lid*

5.  *Lid A een* stemmen van *lid B* als beheerder

6.  *Lid A* en *lid B* beide stemmen *lid C* als beheerder

Dit proces vereist een Azure-abonnement dat ondersteuning biedt voor het implementeren van verschillende virtuele machines en beheerde schijven. Maak, indien nodig, [een gratis Azure-account](https://azure.microsoft.com/free/) om te beginnen.

Wanneer een abonnement is beveiligd, gaat u naar Azure Portal. Selecteer +, Marketplace (' Zie alle ') en zoek naar Ethereum PoA consortium.

In de volgende sectie wordt u begeleid bij het configureren van de footprint van het eerste lid in het netwerk. De implementatie stroom is onderverdeeld in vijf stappen: basis beginselen, implementatie regio's, netwerk grootte en-prestaties, Ethereum-instellingen, Azure Monitor.

#### <a name="basics"></a>Basisbeginselen

Geef onder **basis beginselen**waarden op voor de standaard parameters voor elke implementatie, zoals abonnement, resource groep en basis eigenschappen van de virtuele machine.

Een gedetailleerde beschrijving van elke para meter volgt:

Parameternaam|Beschrijving|Toegestane waarden|Standaardwaarden
---|---|---|---
Een nieuw netwerk maken of lid worden van een bestaand netwerk?|Een nieuw netwerk maken of lid worden van een bestaand consortium netwerk|Nieuwe lid maken bestaande|Nieuwe maken
E-mail adres (optioneel)|U ontvangt een e-mail melding wanneer uw implementatie is voltooid met informatie over uw implementatie.|Geldig e-mail adres|N.v.t.
VM-gebruikers naam|Gebruikers naam van de beheerder van elke geïmplementeerde VM (alleen alfanumerieke tekens)|1-64 tekens|N.v.t.
Verificatietype|De methode voor verificatie bij de virtuele machine.|Wachtwoord of openbare SSH-sleutel|Wachtwoord
Wacht woord (verificatie type = wacht woord)|Het wachtwoord voor het beheerdersaccount voor elk van de geïmplementeerde virtuele machines.  Het wacht woord moet drie van de volgende tekens bevatten: 1 hoofd letter, 1 kleine letter, 1 cijfer en 1 speciaal teken. Hoewel alle Vm's in eerste instantie hetzelfde wacht woord hebben, kunt u het wacht woord na het inrichten wijzigen.|12-72 tekens|N.v.t.
SSH-sleutel (verificatie type = open bare sleutel)|De Secure shell-sleutel die wordt gebruikt voor externe aanmelding.||N.v.t.
Abonnement|Het abonnement waarop het consortium netwerk moet worden geïmplementeerd||N.v.t.
Resourcegroep|De resource groep waarvoor het consortium netwerk moet worden geïmplementeerd.||N.v.t.
Locatie|De Azure-regio voor de resource groep.||N.v.t.

Hieronder ziet u een voor beeld van de implementatie: ![Basic-Blade](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Implementatie regio's

Geef vervolgens onder implementatie regio's invoer op voor het aantal regio's voor het implementeren van het consortium netwerk en de selectie van Azure-regio's op basis van het aantal opgegeven regio's. De gebruiker kan Maxi maal vijf regio's implementeren. U kunt het beste de eerste regio kiezen die overeenkomt met de locatie van de resource groep uit de sectie basis beginselen. Voor ontwikkel-en test netwerken wordt één regio per lid aanbevolen. Voor productie raden we u aan om te implementeren in twee of meer regio's voor hoge Beschik baarheid.

Een gedetailleerde beschrijving van elke para meter volgt:

  Parameternaam|Beschrijving|Toegestane waarden|Standaardwaarden
  ---|---|---|---
  Aantal regio's|Aantal regio's voor de implementatie van het consortium netwerk|1, 2, 3, 4, 5|1
  Eerste regio|Eerste regio voor het implementeren van het consortium netwerk|Alle toegestane Azure-regio's|N.v.t.
  Tweede regio|Tweede regio voor het implementeren van het consortium netwerk (alleen zichtbaar wanneer het aantal regio's is geselecteerd als 2)|Alle toegestane Azure-regio's|N.v.t.
  Derde regio|Derde regio voor het implementeren van het consortium netwerk (alleen zichtbaar wanneer het aantal regio's is geselecteerd als 3)|Alle toegestane Azure-regio's|N.v.t.
  Vierde regio|Vierde regio voor het implementeren van het consortium netwerk (alleen zichtbaar wanneer het aantal regio's is geselecteerd als 4)|Alle toegestane Azure-regio's|N.v.t.
  Vijfde regio|Vijfde regio voor het implementeren van het consortium netwerk (alleen zichtbaar wanneer het aantal regio's is geselecteerd als 5)|Alle toegestane Azure-regio's|N.v.t.

Hieronder ziet u een voor beeld van de implementatie: ![implementatie regio's](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Netwerk grootte en-prestaties

Geef vervolgens onder netwerk grootte en prestaties de invoer voor de grootte van het consortium netwerk op, zoals het aantal en de grootte van de validatie knooppunten.
De opslag grootte van het validatie knooppunt bepaalt de mogelijke grootte van de Block chain. Dit kan na de implementatie worden gewijzigd.

Een gedetailleerde beschrijving van elke para meter volgt:

  Parameternaam|Beschrijving|Toegestane waarden|Standaardwaarden
  ---|---|---|---
  Aantal validator-knoop punten met gelijke taak verdeling|Het aantal validatie knooppunten dat moet worden ingericht als onderdeel van het netwerk|2-15|2
  Prestaties van het knoop punt opslag valideren|Het type beheerde schijf dat elk van de geïmplementeerde knoop punten voor validatie back-ups maakt.|Standard-SSD of Premium|Standard - SSD
  Grootte van de virtuele machine van het validatie knooppunt|De grootte van de virtuele machine die wordt gebruikt voor validatie knooppunten.|Standaard A, standaard D, standaard D-v2, Standard F-serie, Standard DS en Standard FS|Standard D1 v2

[Prijs informatie voor opslag](https://azure.microsoft.com/pricing/details/managed-disks/)

[Prijs informatie voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

De virtuele machine en de opslaglaag zijn van invloed op de netwerk prestaties.  We raden de volgende Sku's aan op basis van de gewenste kosten efficiëntie:

  SKU van virtuele machine|Opslaglaag|Prijs|Doorvoer|Latentie
  ---|---|---|---|---
  F1|Standard - SSD|gebrek|gebrek|hogesnelheidsnet
  D2_v3|Standard - SSD|drager|drager|drager
  F16s|Premium SSD|hogesnelheidsnet|hogesnelheidsnet|gebrek

Hieronder ziet u een voor beeld van de implementatie: de grootte en prestaties van het netwerk ![](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum-instellingen

Geef vervolgens onder Ethereum-instellingen Ethereum configuratie-instellingen op, zoals de netwerk-ID en het Ethereum-account wachtwoord of de Genesis-blok kering.

Een gedetailleerde beschrijving van elke para meter volgt:

  Parameternaam|Beschrijving|Toegestane waarden|Standaardwaarden
  ---|---|---|---
Lid-ID van consortium|De ID die is gekoppeld aan elk lid dat deel uitmaakt van het consortium netwerk dat wordt gebruikt om IP-adres ruimten te configureren om conflicten te voor komen. In het geval van een particulier netwerk moet de lid-ID uniek zijn voor verschillende organisaties in hetzelfde netwerk.  Een unieke lid-ID is vereist, zelfs wanneer dezelfde organisatie op meerdere regio's wordt geïmplementeerd. Noteer de waarde van deze para meter, omdat u deze moet delen met andere join-leden om ervoor te zorgen dat er geen conflicten zijn.|0-255|N.v.t.
Netwerk-ID|De netwerk-ID voor het consortium Ethereum-netwerk dat wordt geïmplementeerd.  Elk Ethereum-netwerk heeft een eigen netwerk-ID, waarbij 1 de ID is van het open bare netwerk.|5-999.999.999|10101010
Ethereum-adres van de beheerder|Het Ethereum-account adres dat wordt gebruikt voor deelname aan PoA governance.  We raden u aan het gebruik van het Ethereum voor het genereren van een adres.|42 alfanumerieke tekens die beginnen met 0x|N.v.t.
Geavanceerde opties|Geavanceerde opties voor Ethereum-instellingen|In-of uitschakelen|Uitschakelen
Openbaar IP (geavanceerde opties = inschakelen)|Implementeert het netwerk achter een VNet-gateway en verwijdert peering-toegang. Als deze optie is geselecteerd, moeten alle leden een VNet-gateway gebruiken om de verbinding compatibel te maken.|Privé-VNet openbaar IP|Openbare IP
Limiet voor blok-gas (geavanceerde opties = inschakelen)|De limiet voor het blok-gas van het netwerk wordt gestart|Wille keurig numeriek|50000000
Verzegelings periode blok keren (SEC)|De frequentie waarmee lege blokken worden gemaakt wanneer er geen trans acties op het netwerk zijn. Een hogere frequentie heeft een snellere eind verhouding, maar verhoogde opslag kosten.|Wille keurig numeriek|15
Transactie machtigings contract (geavanceerde opties = inschakelen)|Byte code voor het transactie machtigings contract. Hiermee worden de implementatie van het slimme contract en de uitvoering van een lijst met Ethereum-accounts beperkt.|Byte code van contract|N.v.t.

Hieronder ziet u een voor beeld van de implementatie: ![ethereum-instellingen](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Bewaking

Op de Blade bewaking kunt u een Azure Monitor logboek bron configureren voor uw netwerk. De bewakings agent verzamelt en bewaart bruikbare metrische gegevens en logboeken van uw netwerk, zodat u snel de problemen met de netwerk status of de fout kunt controleren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Parameternaam|Beschrijving|Toegestane waarden|Standaardwaarden
  ---|---|---|---
Bewaking|Optie om bewaking in te scha kelen|In-of uitschakelen|Inschakelen
Verbinding maken met bestaande Azure Monitor-logboeken|Een nieuw exemplaar van Azure Monitor logboeken maken of lid worden van een bestaand exemplaar|Nieuwe maken of een bestaande koppelen|Nieuwe maken
Bewaak locatie (verbinding maken met bestaande Azure Monitor logs = nieuwe maken)|De regio waar de instantie van de nieuwe Azure Monitor-Logboeken wordt geïmplementeerd|Alle Azure Monitor-logboeken regio's|N.v.t.
Bestaande log Analytics-werk ruimte-ID (verbinding maken met bestaande Azure Monitor logs = deel nemen aan bestaande bestanden)|Werk ruimte-ID van het bestaande exemplaar van Azure Monitor logboeken||N.v.t.
Bestaande primaire sleutel voor logboek analyse (verbinding maken met bestaande Azure Monitor logs = lid worden van bestaande)|De primaire sleutel die wordt gebruikt om verbinding te maken met het bestaande exemplaar van Azure Monitor logboeken||N.v.t.


Hieronder ziet u een voor beeld van de implementatie: ![Azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Samenvatting

Klik op de Blade samen vatting om de opgegeven invoer te controleren en de basis validatie vóór de implementatie uit te voeren. Voordat u implementeert, kunt u de sjabloon en de para meters downloaden.

Bekijk juridische en privacy-voor waarden en klik op kopen om te implementeren. Als de implementatie VNet-gateways bevat, zal de implementatie 45 tot 50 minuten duren.

#### <a name="post-deployment"></a>Post-implementatie

##### <a name="deployment-output"></a>Implementatie-uitvoer

Zodra de implementatie is voltooid, kunt u de benodigde para meters openen via de bevestigings-e-mail of via de Azure Portal. In deze para meters vindt u het volgende:

-   RPC-eind punt Ethereum

-   URL van governance-dash board

-   Azure Monitor URL

-   Gegevens-URL

-   Resource-ID van VNet-gateway (optioneel)

##### <a name="confirmation-email"></a>Bevestigings-e-mail

Als u een e-mail adres ([sectie met basis](#basics)informatie) opgeeft, wordt er een e-mail bericht verzonden naar het e-mail adres met de informatie over de implementatie-uitvoer.

![e-mail over implementatie](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portal

Zodra de implementatie is voltooid en alle resources zijn ingericht, kunt u de uitvoer parameters weer geven in de resource groep.

1.  Zoek de resource groep in de portal

2.  Navigeren naar *implementaties*

3.  Selecteer de eerste implementatie met dezelfde naam als de resource groep

4.  *Uitvoer* selecteren

### <a name="growing-the-consortium"></a>Het consortium uitbreiden

Als u uw consortium wilt uitbreiden, moet u eerst verbinding maken met het fysieke netwerk.
Deze eerste stap is naadloos met behulp van de open bare op IP gebaseerde implementatie. Als u zich achter een VPN-verbinding bevindt, raadpleegt u de sectie [een VNet-gateway verbinden](#connecting-vnet-gateways) om de netwerk verbinding te maken als onderdeel van de nieuwe leden implementatie.  Nadat de implementatie is voltooid, gebruikt u de [governance DApp](#governance-dapp) om een netwerk beheerder te worden.

#### <a name="new-member-deployment"></a>Nieuwe leden implementatie

1.  Deel de volgende informatie met het lid join. Deze informatie vindt u in uw e-mail bericht na de implementatie of in de portal-implementatie-uitvoer.

    -  URL van consortium gegevens

    -  Het aantal knoop punten dat u hebt geïmplementeerd

    -  Resource-ID van de VNet-gateway (als u VPN gebruikt)

2.  Het geïmplementeerde lid moet [dezelfde oplossing](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) gebruiken bij het implementeren van de netwerk aanwezigheid, waarbij het volgende in acht wordt genomen:

    -  *Bestaand lid* selecteren

    -  Kies hetzelfde aantal validator-knoop punten als de rest van de leden op het netwerk om een billijke weer gave te garanderen

    -  Gebruik hetzelfde Ethereum-adres dat in de vorige stap is gegeven

    -  Geef de verschafte *URL voor de consortium gegevens* door op het tabblad *Ethereum-instellingen*

    -  Als de rest van het netwerk zich achter een VPN bevindt, selecteert u *privé-VNet* in het gedeelte Geavanceerd

#### <a name="connecting-vnet-gateways"></a>VNet-gateways verbinden

U kunt deze stap negeren als u hebt geïmplementeerd met behulp van de standaard instellingen voor het open bare IP-adres. In het geval van een particulier netwerk zijn de verschillende leden verbonden via VNet-gateway verbindingen. Voordat een lid kan deel nemen aan het netwerk en transactie verkeer kan zien, moet een bestaand lid een definitieve configuratie op hun VPN-gateway uitvoeren om de verbinding te accepteren. Dit betekent dat de Ethereum-knoop punten van het lid join pas worden uitgevoerd als er een verbinding tot stand is gebracht. Het is raadzaam redundante netwerk verbindingen (net) in het consortium te maken om de kans op een Single Point of Failure te verminderen.

Nadat het nieuwe lid is geïmplementeerd, moet het bestaande lid de bidirectionele verbinding volt ooien door een VNet-gateway verbinding met het nieuwe lid in te stellen. Hiervoor is een bestaand lid nodig:

1.  De VNet-gateway ResourceID van het Connect-lid (Zie implementatie-uitvoer)

2.  De gedeelde verbindings sleutel

Het bestaande lid moet het volgende Power shell-script uitvoeren om de verbinding te volt ooien. U kunt het beste Azure Cloud Shell vinden in de rechter navigatie balk in de portal.

![Cloud shell](./media/ethereum-poa-deployment/cloud-shell.png)

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

### <a name="service-monitoring"></a>Servicecontrole

U kunt uw Azure Monitor-Portal vinden door de koppeling te volgen in de e-mail implementatie of door de para meter in de implementatie-uitvoer \[OMS\_PORTAL\_URL\]op te zoeken.

In de portal worden eerst netwerk statistieken en knooppunt overzicht op hoog niveau weer gegeven.

![monitor Categorieën](./media/ethereum-poa-deployment/monitor-categories.png)

Als u het **knooppunt overzicht** selecteert, wordt u naar een portal geleid om de infrastructuur statistieken per knoop punt weer te geven.

![knooppunt statistieken](./media/ethereum-poa-deployment/node-stats.png)

Als u **netwerk statistieken** selecteert, worden de Ethereum-netwerk statistieken weer gegeven.

![netwerk statistieken](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

Achter deze Dash boards bevindt zich een set queryable RAW-Logboeken. U kunt deze onbewerkte Logboeken gebruiken om de Dash boards, fouten onderzoeken of drempel waarden voor de installatie te wijzigen. Hieronder vindt u een aantal voorbeeld query's die kunnen worden uitgevoerd in het hulp programma voor zoeken in Logboeken:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Een lijst met blokken die zijn gerapporteerd door meer dan één validator. Handig om de keten vorken te helpen vinden.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Het gemiddelde aantal peers ophalen voor een opgegeven validator-knoop punt, gemiddeld meer dan vijf minuten buckets.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>SSH-toegang

Uit veiligheids overwegingen wordt de SSH-poort toegang standaard geweigerd door een regel voor de beveiliging van een netwerk groep. Als u toegang wilt krijgen tot de exemplaren van virtuele machines in het PoA-netwerk, moet u deze regel wijzigen in \"\" toestaan

1.  Start in het gedeelte Overzicht van de geïmplementeerde resource groep van Azure Portal.

    ![overzicht van SSH](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Selecteer de netwerk beveiligings groep voor de regio van de virtuele machine die u wilt openen

    ![SSH-NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Selecteer de \"regel voor het toestaan van SSH-\"

    ![SSH-toestaan](./media/ethereum-poa-deployment/ssh-allow.png)

4.  \"actie\" wijzigen om toe te staan

    ![SSH inschakelen toestaan](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Klik op \"opslaan\" (het kan enkele minuten duren om de wijzigingen toe te passen)

U kunt nu op afstand verbinding maken met de virtuele machines voor de validatie knooppunten via SSH met uw opgegeven beheerders naam en wacht woord/SSH-sleutel.
De SSH-opdracht die moet worden uitgevoerd om het eerste knoop punt voor validatie te openen, wordt weer gegeven in de sjabloon implementatie-uitvoer parameter als ' SSH-\_naar\_eerste\_VL\_knoop punt\_REGION1 ' (voor de voorbeeld implementatie: SSH-p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Als u extra transactie knooppunten wilt ophalen, verhoogt u het poort nummer met één (bijvoorbeeld het eerste transactie knooppunt bevindt zich op poort 4000).

Als u in meer dan één regio hebt geïmplementeerd, wijzigt u de bovenstaande opdracht in de DNS-naam of het IP-adres van de load balancer in die regio. Als u de DNS-naam of het IP-adres van de andere regio's wilt vinden, zoekt u de resource met de naam Conventie \*\*\*\*\*-lbpip-reg\#en bekijkt u de eigenschappen van de DNS-naam en IP-adres.

### <a name="azure-traffic-manager-load-balancing"></a>Taak verdeling van Azure Traffic Manager

Azure Traffic Manager kan u helpen downtime te reduceren en de reactie tijd van het PoA-netwerk te verbeteren door inkomend verkeer te routeren over meerdere implementaties in verschillende regio's. Ingebouwde status controles en automatische route ring zorgen ervoor dat de RPC-eind punten en de governance-DApp hoge Beschik baarheid bieden. Deze functie is handig als u hebt geïmplementeerd in meerdere regio's en gereed is voor productie.

Met Traffic Manager kunt u:

-   Verbeter de beschik baarheid van PoA-netwerken met automatische failover.

-   Verhoog de reactie snelheid van uw netwerken door eind gebruikers naar de Azure-locatie te routeren met de laagste netwerk latentie.

Als u besluit een Traffic Manager profiel te maken, kunt u de DNS-naam van het profiel gebruiken om toegang te krijgen tot uw netwerk. Zodra andere consortium leden zijn toegevoegd aan het netwerk, kan de Traffic Manager ook worden gebruikt om de taak verdeling over de geïmplementeerde validators te verdelen.

#### <a name="creating-a-traffic-manager-profile"></a>Een Traffic Manager profiel maken

Zoek en selecteer \"Traffic Manager profiel\" nadat u op de knop \"een resource maken\" in de Azure Portal hebt geklikt.

![zoeken naar Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

Geef het profiel een unieke naam en selecteer de resource groep die is gemaakt tijdens de implementatie van PoA. Klik op de knop maken om te implementeren.

![Traffic Manager maken](./media/ethereum-poa-deployment/traffic-manager-create.png)

Zodra de app is geïmplementeerd, selecteert u het exemplaar in de resource groep. De DNS-naam voor toegang tot Traffic Manager vindt u op het tabblad Overzicht

![Traffic Manager DNS zoeken](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Selecteer het tabblad eind punten en klik op de knop toevoegen. Geef het eind punt een unieke naam. Wijzig het doel bron type in openbaar IP-adres. Selecteer vervolgens het open bare IP-adres van de eerste regio\'s load balancer.

![Routering verkeer beheer](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Herhaal deze stap voor elke regio in het geïmplementeerde netwerk. Zodra de eind punten zich in de \"ingeschakelde\" status bevinden, worden ze automatisch geladen en wordt de regio binnen de DNS-naam van Traffic Manager verdeeld. U kunt deze DNS-naam nu gebruiken in plaats van de \[CONSORTIUM\_DATA\_URL\] para meter in andere stappen van het document.

### <a name="data-api"></a>Gegevens-API

Elk consortium heeft de benodigde informatie voor anderen om verbinding te maken met het netwerk. Het bestaande lid geeft de [CONSORTIUM_DATA_URL] vóór de implementatie van het lid. Na de implementatie haalt een lid van de JSON-interface op het volgende eind punt informatie op:

`<CONSORTIUM_DATA_URL>/networkinfo`

Het antwoord bevat informatie die nuttig is voor het toevoegen van leden (Genesis-blok, validator set contract ABI, bootnodes) en informatie die nuttig is voor het bestaande lid (validator-adressen). We moedigen het gebruik van deze standaardisatie aan om het consortium uit te breiden over cloud providers. Deze API retourneert een JSON-indelings antwoord met de volgende structuur:
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
## <a name="tutorials"></a>Zelfstudies

### <a name="programmatically-interacting-with-a-smart-contract"></a>Programmatisch communiceren met een slim contract

> [!WARNING]
> Stuur nooit uw persoonlijke Ethereum-sleutel via het netwerk. Zorg ervoor dat elke trans actie eerst lokaal wordt ondertekend en dat de ondertekende trans actie via het netwerk wordt verzonden.

In het volgende voor beeld gebruiken we *ethereumjs-Wallet* om een Ethereum-adres te genereren, *ethereumjs-TX* om lokaal te ondertekenen en *Web3* om de onbewerkte trans actie naar het Ethereum RPC-eind punt te verzenden.

We gebruiken dit eenvoudige, Hello-World-contract voor dit voor beeld:

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

In dit voor beeld wordt ervan uitgegaan dat het contract al is geïmplementeerd. U kunt *solc* en *Web3* gebruiken voor het programmatisch implementeren van een contract. Installeer eerst de volgende knooppunt modules:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Met dit nodeJS-script worden de volgende bewerkingen uitgevoerd:

-   Een onbewerkte trans actie maken: postMsg

-   De trans actie ondertekenen met de gegenereerde persoonlijke sleutel

-   De ondertekende trans actie verzenden naar het Ethereum-netwerk

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Slim contract implementeren met truffle

-   Benodigde bibliotheken installeren

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   In truffle. js voegt u de volgende code toe om uw te ontgrendelen en het PoA-knoop punt te configureren als ingangs punt door de instructie voor te geven (/Settings/Seed-woorden tonen)

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   Implementeren naar PoA-netwerk

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Fout opsporing voor Smart-contracten met truffle

Truffle heeft een lokaal ontwikkel netwerk dat beschikbaar is voor het opsporen van fouten in Smart contract. U kunt [hier](https://truffleframework.com/tutorials/debugging-a-smart-contract)de volledige zelf studie vinden.

### <a name="webassembly-wasm-support"></a>Ondersteuning voor webassembly (WASM)

Ondersteuning voor webassembly is al voor u ingeschakeld op nieuwe geïmplementeerde PoA-netwerken. Dit maakt het mogelijk om intelligente contracten te ontwikkelen in elke taal die wordt gestapeld naar een webassemblatie ( C++roest, C,). Zie de onderstaande koppelingen voor meer informatie

-   Overzicht van de pariteit van webassembly-<https://wiki.parity.io/WebAssembly-Home>

-   Zelf studie van pariteits tech-<https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Naslaginformatie

### <a name="faq"></a>Veelgestelde vragen

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Ik zie dat er veel trans acties op het netwerk werkt\'t worden verzonden. Waar komen ze vandaan?

Het is niet veilig om de [persoonlijke API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)te ontgrendelen. Bots Luis teren naar niet-vergrendelde Ethereum-accounts en proberen de fondsen uit te voeren. De bot gaat ervan uit dat deze accounts echte-ether bevatten en proberen de eerste om het saldo te Siphon. Schakel de persoonlijke API niet in op het netwerk. In plaats daarvan moet u de trans acties vooraf hand matig gebruiken met behulp van een wallet zoals het-verdeel masker of via een programma, zoals wordt beschreven in de sectie [programmatisch interactie met een slim contract](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>SSH naar een VM?

De SSH-poort is niet beschikbaar vanwege veiligheids redenen. Volg [deze hand leiding om de SSH-poort in te scha kelen](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hoe kan ik een audit-lid of transactie knooppunten instellen?

Transactie knooppunten zijn een set pariteits clients die zijn gekoppeld aan het netwerk, maar die niet deel uitmaken van consensus. Deze knoop punten kunnen nog steeds worden gebruikt voor het verzenden van Ethereum-trans acties en het lezen van de status van het slimme contract.
Dit werkt goed als een mechanisme voor het leveren van controle van niet-bevoegde consortium leden op het netwerk. Volg de stappen in stap 2 van het consortium groeien.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Waarom nemen trans acties met een geruime tijd in beslag?

Om ervoor te zorgen dat trans acties in de juiste volg orde worden ontvangen, wordt elke Ethereum-trans actie geleverd met een incrementele nonce. Als u een account in een ander netwerk hebt gebruikt in het submasker, moet u de nonce-waarde opnieuw instellen. Klik op het instellingen pictogram (drie balken), instellingen en account opnieuw instellen. De transactie geschiedenis wordt gewist en de trans actie kan nu opnieuw worden ingediend.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Moet ik de kosten voor het gas opgeven in het hand-outmasker?

Eer is geen doel van het controleren van het controle-of-Authority consortium. Daarom is het niet nodig om de kosten voor het gas op te geven bij het indienen van trans acties in het deel masker.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Wat moet ik doen als mijn implementatie mislukt als gevolg van een fout bij het inrichten van Azure OMS?

Bewaking is een optionele functie. In zeldzame gevallen waarin uw implementatie mislukt omdat het niet mogelijk is om Azure Monitor resource in te richten, kunt u zonder Azure Monitor opnieuw implementeren.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Zijn open bare IP-implementaties die compatibel zijn met implementaties van particuliere netwerken?

Nee, peering vereist twee richtings communicatie, zodat het hele netwerk openbaar of privé moet zijn.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Wat is de verwachte transactie doorvoer van het bewijs van de instantie?

De trans actie-door Voer is zeer afhankelijk van de typen trans acties en de netwerk topologie.  Met eenvoudige trans acties hebben we een gemiddeld aantal van 400 trans acties per seconde gebenchmarkd met een netwerk dat is geïmplementeerd in meerdere regio's.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hoe kan ik abonneren op slimme contract gebeurtenissen?

Ethereum-test-of-Authority ondersteunt nu web-sockets.  Controleer uw implementatie-e-mail of implementatie-uitvoer om de URL en poort van de Web-socket te vinden.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [Ethereum proof-of-Authority consortium-](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) oplossing.
