---
title: FXT-cluster maken met Microsoft Azure Edge-bestands extensie
description: Een hybride opslag cache cluster maken met de Azure FXT Edge-Bestandsr
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 54d70f60d4b7290b60c864817c756648fef1f481
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256085"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Zelf studie: het Azure FXT Edge-bestands cluster maken

Nadat u de FXT Edge-hardwarecomponenten van Azure hebt geïnstalleerd en geïnitialiseerd voor uw cache, gebruikt u de FXT-cluster software om het cache cluster te maken. 

In deze zelf studie wordt u begeleid bij de stappen voor het configureren van uw hardware-knoop punten als een cluster. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Welke gegevens nodig zijn voordat u begint met het maken van het cluster
> * Het verschil tussen het beheer netwerk van het cluster, het cluster netwerk en het client-Facing netwerk
> * Verbinding maken met een cluster knooppunt 
> * Een eerste cluster maken met behulp van één Azure FXT Edge-Bestandsr knoop punt
> * Aanmelden bij het configuratie scherm van het cluster om de cluster instellingen te configureren

Deze procedure duurt tussen 15 en 45 minuten, afhankelijk van hoeveel onderzoek u moet doen om IP-adressen en netwerk bronnen te identificeren.

## <a name="prerequisites"></a>Vereisten

Voltooi deze vereisten voordat u met deze zelf studie begint:

* De hardwaresystemen van uw Azure FXT Edge-bestand installeren in uw Data Center 

  U hebt slechts één knoop punt nodig om het cluster te maken, maar u moet [ten minste twee extra knoop punten toevoegen](fxt-add-nodes.md) voordat u het cluster kunt configureren en het kan gebruiken. 

* De juiste stroom-en netwerk kabels aansluiten op het systeem  
* Schakel ten minste één Azure FXT Edge-knoop punt in en [Stel het hoofd wachtwoord](fxt-node-password.md) in

## <a name="gather-information-for-the-cluster"></a>Informatie voor het cluster verzamelen 

U hebt de volgende informatie nodig om het Azure FXT Edge-bestands cluster te maken:

* Clusternaam

* Beheerders wachtwoord om in te stellen voor het cluster

* IP-adressen:

  * Eén IP-adres voor cluster beheer en het netmasker en de router die moeten worden gebruikt voor het beheer netwerk
  * Het eerste en laatste IP-adres in een aaneengesloten bereik met IP-adressen voor de communicatie van het cluster (knoop punt naar knoop punt). Zie [IP-adres distributie](#ip-address-distribution)hieronder voor meer informatie. 
  * (Client gerichte IP-adressen worden ingesteld na het maken van het cluster.)

* Informatie over de netwerk infrastructuur:

  * Het IP-adres van een DNS-server voor het cluster
  * De naam van het DNS-domein voor het cluster
  * De naam of het IP-adres voor de cluster-NTP-servers (een server of drie of meer) 
  * Of u de koppelings aggregatie van IEEE 802.1 AX-2008 wilt inschakelen op de interfaces van het cluster
  * Als u koppelings aggregatie inschakelt, of de dynamische aggregatie van IEEE 802.3 ad (LACP) al dan niet moet worden gebruikt

U kunt deze netwerk infrastructuur items configureren nadat u het cluster hebt gemaakt, maar dit is beter tijdens het maken. 

### <a name="ip-address-distribution"></a>Distributie van IP-adressen

Het cache cluster voor hybride opslag van Azure FXT Edge maakt gebruik van IP-adressen in drie categorieën:

* Beheer-IP: één IP-adres voor cluster beheer

  Dit adres fungeert als toegangs punt voor toegang tot de hulpprogram ma's voor cluster configuratie (het op het web gebaseerde configuratie scherm of de XML-RPC-API). Dit adres wordt automatisch toegewezen aan het primaire knoop punt in het cluster en wordt automatisch verplaatst als het primaire knoop punt wordt gewijzigd.

  Andere IP-adressen kunnen worden gebruikt om toegang te krijgen tot het configuratie scherm, maar het beheer-IP-adres is ontworpen om toegang te bieden, zelfs als failover van afzonderlijke knoop punten wordt uitgevoerd.

* Cluster netwerk: een bereik met IP-adressen voor cluster communicatie

  Het cluster netwerk wordt gebruikt voor communicatie tussen cluster knooppunten en voor het ophalen van bestanden van de back-end-opslag (kern bestanden).

  **Aanbevolen procedure:** Wijs één IP-adres toe per fysieke poort die wordt gebruikt voor cluster communicatie op elk Azure FXT Edge-knoop punt. Het cluster wijst automatisch de adressen in het opgegeven bereik toe aan afzonderlijke knoop punten.

* Client gericht netwerk: het bereik van IP-adressen die clients gebruiken om bestanden aan te vragen en te schrijven

  De client-netwerk adressen worden door clients gebruikt voor toegang tot de kern bestands gegevens via het cluster. Een NFS-client kan bijvoorbeeld een van deze adressen koppelen.

  **Aanbevolen procedure:** Wijs één IP-adres toe per fysieke poort die wordt gebruikt voor client communicatie op elk FXT Series-knoop punt.

  Het cluster distribueert IP-adressen op de client zo gelijkmatig mogelijk over de samenstellende knoop punten.

  Ter vereenvoudiging: veel beheerders configureren één DNS-naam met Round-Robin DNS-configuratie (RRDNS), zodat het eenvoudiger wordt om client aanvragen over het adres bereik te distribueren. Met deze installatie kunnen alle clients ook de opdracht koppelen gebruiken om toegang te krijgen tot het cluster. Lees [DNS configureren](fxt-configure-network.md#configure-dns-for-load-balancing) voor meer informatie.

Het beheer-IP-adres en een bereik van cluster netwerk adressen moeten worden opgegeven om een nieuw cluster te maken. Client adressen worden opgegeven nadat het cluster is gemaakt.

## <a name="connect-to-the-first-node"></a>Verbinding maken met het eerste knoop punt

U kunt verbinding maken met een van de geïnstalleerde FXT-knoop punten en de bijbehorende OS-software gebruiken om het cluster in te stellen.

Als u dit nog niet hebt gedaan, schakelt u ten minste één van de FXT-knoop punten voor uw cluster uit en controleert u of deze een netwerk verbinding en een IP-adres heeft. U moet een nieuw root-wacht woord instellen om het knoop punt te activeren. Volg daarom de stappen in [Hardware-wacht woorden instellen](fxt-node-password.md) als u dit nog niet hebt gedaan.

Als u de netwerk verbinding wilt controleren, controleert u of de netwerk koppeling-Led's van het knoop punt licht zijn (en, indien nodig, de Indica tors op de netwerk switch waaraan deze is gekoppeld). Indicator-Led's worden beschreven in de [Hardware-status van Azure FXT Edge-bestanden bewaken](fxt-monitor.md).

Wanneer het knoop punt wordt opgestart, wordt een IP-adres aangevraagd. Als deze is verbonden met een DHCP-server, wordt het IP-adres geaccepteerd dat door DHCP wordt verschaft. (Dit IP-adres is tijdelijk. Het wordt gewijzigd wanneer u het cluster maakt.)

Als deze niet is verbonden met een DHCP-server of als er geen antwoord wordt ontvangen, gebruikt het knoop punt Bonjour-software om een zelf toegewezen IP-adres in de vorm 169,254. \*. \* in te stellen. U moet echter een tijdelijk statisch IP-adres op een van de netwerk kaarten van het knoop punt instellen voordat u het gebruikt om een cluster te maken. Instructies zijn opgenomen in dit oudere document. Neem contact op met de service en ondersteuning van micro soft voor bijgewerkte informatie: [bijlage A: een statisch IP-adres instellen op een FXT-knoop punt](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Het IP-adres zoeken

Maak verbinding met het knoop punt van de Azure FXT Edge-bestand om het IP-adres te vinden. U kunt een seriële kabel gebruiken, een rechtstreekse verbinding met de USB-en VGA-poorten of verbinding maken via een KVM-switch. (Zie [eerste wacht woorden instellen](fxt-node-password.md)voor details over poort verbindingen.)

Nadat u verbinding hebt gemaakt, meldt u zich aan met de gebruikers naam `root` en het wacht woord dat u hebt ingesteld toen u het knoop punt voor de eerste keer opstart.  

Nadat u zich hebt aangemeld, moet u het IP-adres van het knoop punt bepalen.

Gebruik de opdracht `ifconfig` om de adressen weer te geven die aan dit systeem zijn toegewezen.

De opdracht `ifconfig | grep -B5 inet` zoekt bijvoorbeeld naar poorten met Internet adressen en biedt vijf regels context om de poort-id weer te geven.

Noteer elk IP-adres dat wordt weer gegeven in het ifconfig-rapport. Adressen die worden weer gegeven met poort namen, zoals e0a of e0b, zijn goede opties. Gebruik geen IP-adressen die worden vermeld met E7 * names, omdat deze namen alleen worden gebruikt voor iDRAC/IPMI-service poorten.  

## <a name="load-the-cluster-configuration-wizard"></a>De wizard cluster configuratie laden

Gebruik het hulp programma voor cluster configuratie op basis van de browser om het cluster te maken. 

Voer het IP-adres voor het knoop punt in een webbrowser in. Als de browser een bericht geeft over de site die niet wordt vertrouwd, gaat u toch naar de site. (Afzonderlijke knoop punten van de FXT Edge van Azure hebben geen beveiligings certificaten door de certificerings instantie.)

![De aanmeldings pagina van het configuratie scherm in het browser venster](media/fxt-cluster-create/unconfigured-node-gui.png)

Laat de velden **gebruikers naam** en **wacht woord** leeg. Klik op **Aanmelden** om de pagina voor het maken van het cluster te laden.

![Eerste installatie scherm voor een niet-geconfigureerd knoop punt in het configuratie scherm van de browser. Het bevat opties voor het bijwerken van software, het hand matig configureren van een cluster of het configureren van een cluster vanuit een installatie bestand.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Het cluster maken

Het hulp programma cluster configuratie begeleidt u door een aantal schermen om het Azure FXT Edge-bestands cluster te maken. Zorg ervoor dat u de [vereiste gegevens](#gather-information-for-the-cluster) hebt om te beginnen. 

### <a name="creation-options"></a>Opties voor maken

Het eerste scherm bevat drie opties. Gebruik de optie hand matige configuratie tenzij u speciale instructies van ondersteunings personeel hebt.

Klik op **Ik zal het cluster hand matig configureren** om het scherm nieuwe cluster configuratie opties te laden. 

De andere opties worden zelden gebruikt:

* Als u de installatie kopie van het systeem bijwerkt, wordt u gevraagd nieuwe OS-software te installeren voordat u het cluster maakt. (De momenteel geïnstalleerde software versie wordt boven aan het scherm weer gegeven.) U moet het software pakket bestand opgeven: een URL en gebruikers naam/wacht woord of door een bestand van uw computer te uploaden. 

* De optie voor het installatie bestand van het cluster wordt soms gebruikt door de klanten service en ondersteuning van micro soft. 

## <a name="cluster-options"></a>Cluster opties

In het volgende scherm wordt u gevraagd om de opties voor het nieuwe cluster te configureren.

De pagina is onderverdeeld in twee hoofd secties: **basis configuratie** en **netwerk configuratie**. De sectie netwerk configuratie heeft ook subsecties: één voor het **beheer** netwerk en één voor het **cluster** netwerk.

### <a name="basic-configuration"></a>Basis configuratie

Vul in het bovenste gedeelte basis informatie in voor het nieuwe cluster.

![Details van de sectie ' basis configuratie ' in de browser GUI-pagina. Er worden drie velden weer gegeven (cluster naam, beheerders wachtwoord, bevestig het wacht woord)](media/fxt-cluster-create/basic-configuration.png) 

* **Cluster naam** : Voer een unieke naam in voor het cluster.

  De cluster naam moet aan de volgende criteria voldoen:
  
  * Lengte van 1 tot 16 tekens
  * Kan letters, cijfers en streepjes (-) en onderstrepings tekens (_) bevatten 
  * Mag geen andere interpunctie of speciale tekens bevatten
  
  U kunt deze naam later wijzigen op de pagina **Cluster** > **algemene installatie** configuratie. (Voor meer informatie over cluster instellingen raadpleegt u de [hand leiding voor cluster configuratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), die geen deel uitmaakt van deze documentatieset.)

  > [!NOTE] 
  > De cluster naam wordt gebruikt om systeem informatie te identificeren die is geüpload naar ondersteuning voor bewaking of probleem oplossing. het is dus handig om uw bedrijfs naam op te geven.

* **Beheerders wachtwoord** : Stel het wacht woord in voor de standaard gebruiker met beheerders rechten, `admin`.
  
  U moet afzonderlijke gebruikers accounts instellen voor elke persoon die het cluster beheert, maar u kunt de gebruiker `admin` niet verwijderen. Meld u aan als `admin` als u extra gebruikers wilt maken.
 
  U kunt het wacht woord voor `admin` wijzigen op de pagina **beheer** > **gebruikers** instellingen in het configuratie scherm van het cluster. Lees voor meer informatie de documentatie over **gebruikers** in de [cluster configuratie handleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Netwerkconfiguratie

De sectie **netwerken** vraagt u om de netwerk infrastructuur op te geven die door het cluster wordt gebruikt. 

Er zijn twee afzonderlijke netwerken die moeten worden geconfigureerd:

* Het *beheer netwerk* biedt beheerders toegang tot het cluster voor configuratie en controle. Het IP-adres dat u hier opgeeft, wordt gebruikt bij het maken van verbinding met het configuratie scherm of voor SSH-toegang. 

  Voor de meeste clusters wordt slechts één beheer-IP-adres gebruikt, maar als u interfaces wilt toevoegen, kunt u dit doen nadat u het cluster hebt gemaakt.

* Het *cluster netwerk* wordt gebruikt voor communicatie tussen cluster knooppunten en tussen cluster knooppunten en back-end-opslag (kern bestanden).

Het client gerichte netwerk wordt later geconfigureerd nadat het cluster is gemaakt.

Deze sectie bevat ook configuratie voor DNS-en NTP-servers die door beide netwerken worden gebruikt.

### <a name="configure-the-management-network"></a>Het beheer netwerk configureren

De instellingen in de sectie **beheer** zijn voor het netwerk dat beheerders toegang biedt tot het cluster.

![Details van de sectie ' beheer ', met velden voor vijf opties en een selectie vakje voor 1Gb-beheer netwerk](media/fxt-cluster-create/management-network-filled.png)

* **Beheer-IP** : Geef het IP-adres op dat u gaat gebruiken voor toegang tot het configuratie scherm van het cluster. Dit adres wordt geclaimd door het primaire knoop punt van het cluster, maar wordt automatisch verplaatst naar een goed knoop punt als het oorspronkelijke primaire knoop punt niet meer beschikbaar is.

  Voor de meeste clusters wordt slechts één IP-adres voor beheer gebruikt. Als u meer dan één wilt, kunt u ze toevoegen nadat u het cluster hebt gemaakt met behulp van de pagina **cluster** >  netwerk instellingen voor**beheer** . Meer informatie vindt u in de [hand leiding voor cluster configuratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Netmask** : Geef het netmask op voor het beheer netwerk.

* **Router** : Voer het standaard gateway adres in dat door het beheer netwerk wordt gebruikt.

* **VLAN-tag (optioneel)** : als uw cluster VLAN-Tags gebruikt, geeft u het label op voor het beheer netwerk.

  Aanvullende VLAN-instellingen worden geconfigureerd op de pagina **Cluster** > **VLAN** -instellingen. Lees meer over het [werken met vlan's](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) en [cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) in de cluster configuratie handleiding voor meer informatie.

* **MTU** : Stel, indien nodig, de MTU (maximum trans Mission Unit) voor het beheer netwerk van uw cluster in.

* **Gebruik 1gb-beheer netwerk** : Schakel dit selectie vakje in als u de twee 1GbE-netwerk poorten op uw FXT-knoop punten alleen aan het beheer netwerk wilt toewijzen. (U moet 25GbE/10GbE-poorten beschikbaar hebben voor al het andere verkeer.) Als u dit selectie vakje niet inschakelt, gebruikt het beheer netwerk de hoogste snelheids poort die beschikbaar is. 

### <a name="configure-the-cluster-network"></a>Het cluster netwerk configureren 

De cluster netwerk instellingen zijn van toepassing op verkeer tussen de cluster knooppunten en tussen cluster knooppunten en kern bestanden.

![Details van de sectie ' cluster ', met velden voor het invoeren van zes waarden](media/fxt-cluster-create/cluster-network-filled.png)

* **Eerste IP** -adres en **laatste IP** -Voer de IP-adressen in die het bereik definiëren dat moet worden gebruikt voor interne cluster communicatie. De IP-adressen die hier worden gebruikt, moeten aaneengesloten zijn en niet door DHCP worden toegewezen.

  U kunt meer IP-adressen toevoegen nadat u het cluster hebt gemaakt. Gebruik de pagina **cluster** > **cluster Networks** Settings ([documentatie over de cluster configuratie handleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  De waarde in het **aantal ip's in het bereik** wordt automatisch berekend en weer gegeven.

* **Netmask zonder beheer (optioneel)** : Geef het netmask op voor het cluster netwerk. 

  Het systeem stelt automatisch de netmask-waarde in die u voor het beheer netwerk hebt ingevoerd. Wijzig deze indien nodig.

* **Cluster router (optioneel)** : Geef het standaard gateway adres op dat wordt gebruikt door het cluster netwerk. 

  Het systeem suggereert automatisch hetzelfde gateway adres dat u hebt opgegeven voor het beheer netwerk.

* VLAN-tag voor het cluster **(optioneel)** : als uw cluster VLAN-Tags gebruikt, geeft u de tag op voor het cluster netwerk.

* **Niet-veranderings-MTU (optioneel)** : als dat nodig is, past u de MTU (maximum trans Mission Unit) voor uw cluster netwerk aan.

### <a name="configure-cluster-dns-and-ntp"></a>Cluster-DNS en NTP configureren 

De sectie **cluster** bevat velden voor het opgeven van DNS-en NTP-servers en voor het inschakelen van koppelings aggregatie. Deze instellingen zijn van toepassing op alle netwerken die het cluster gebruikt.

![Details van de sectie voor de DNS/NTP-configuratie, met drie velden voor DNS-servers, velden voor DNS-domein en DNS-zoek opdracht, drie velden voor NTP-servers en een vervolg keuzelijst voor aggregatie opties voor koppelingen](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS-server (s)** : Voer het IP-adres in van een of meer DNS-servers (Domain Name System).

  DNS wordt aanbevolen voor alle clusters en is vereist als u SMB, AD of Kerberos wilt gebruiken. 
  
  Voor optimale prestaties configureert u de DNS-server van het cluster voor Round Robin-taak verdeling, zoals beschreven in [DNS configureren voor het Azure FXT Edge-bestands cluster](fxt-configure-network.md#configure-dns-for-load-balancing).

* **DNS-domein** : Voer de naam van het netwerk domein in dat door het cluster wordt gebruikt.

* **DNS zoeken** : Geef desgewenst aanvullende domein namen op die het systeem moet zoeken om DNS-query's op te lossen. U kunt Maxi maal zes domein namen toevoegen, gescheiden door spaties.

* **NTP-server (s)** : Geef een of drie NTP-servers (Network Time Protocol) op in de opgegeven velden. U kunt hostnamen of IP-adressen gebruiken.

* **Koppelings aggregatie** : met koppelings aggregatie kunt u aanpassen hoe de Ethernet-poorten op de cluster FXT knoop punten verschillende soorten verkeer verwerken. Lees [koppelings aggregatie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) in de cluster configuratie handleiding voor meer informatie.

### <a name="click-the-create-button"></a>Klik op de knop maken

Nadat u alle vereiste instellingen in het formulier hebt opgegeven, klikt u op de knop **cluster maken** .

![onder aan voltooide formulier met cursor over de knop maken rechtsonder](media/fxt-cluster-create/create-cluster.png)

Er wordt een bericht weer gegeven tijdens het maken van het cluster.

![status bericht van cluster configuratie in browser: het FXT-knoop punt maakt nu het cluster. Dit kan enkele minuten duren. Wanneer het cluster is gemaakt, gaat u naar deze koppeling om de configuratie te volt ooien. " met een Hyper link op ' Ga naar deze koppeling '](media/fxt-cluster-create/creating-message.png)

Na enkele ogen blikken kunt u klikken op de koppeling in het bericht om naar het configuratie scherm van het cluster te gaan. (Met deze koppeling gaat u naar het IP-adres dat u hebt opgegeven in **beheer-IP**.) Het duurt 15 seconden tot één minuut voordat de koppeling actief wordt nadat u op de knop maken hebt geklikt. Als de webinterface niet wordt geladen, wacht u enkele seconden en klikt u nogmaals op de koppeling. 

Het maken van een cluster duurt een minuut of langer, maar u kunt zich aanmelden bij het configuratie scherm terwijl het proces wordt uitgevoerd. Het is normaal dat de dashboard pagina van het configuratie scherm de waarschuwingen weergeeft totdat het proces voor het maken van het cluster is voltooid. 

## <a name="open-the-settings-pages"></a>Open de pagina instellingen 

Nadat u het cluster hebt gemaakt, moet u de configuratie van het netwerk en de werk stroom aanpassen. 

Gebruik de web-interface van het configuratie scherm om uw nieuwe cluster in te stellen. Volg de koppeling in het scherm status van het maken van het cluster of blader naar het IP-adres van het beheer dat u op het cluster hebt ingesteld.

Meld u aan bij de webinterface met de gebruikers naam `admin` en het wacht woord dat u hebt ingesteld bij het maken van het cluster.

![webbrowser waarin de aanmeldings velden van het configuratie scherm worden weer gegeven](media/fxt-cluster-create/admin-login.png)

Het configuratie scherm wordt geopend en de **Dashboard** pagina wordt weer gegeven. Wanneer het maken van het cluster is voltooid, moeten waarschuwings berichten in de weer gave worden gewist.

Klik op het tabblad **instellingen** om het cluster te configureren.

Op het tabblad **instellingen** wordt in de zijbalk links een menu met configuratie pagina's weer gegeven. De pagina's zijn ingedeeld op categorie. Klik boven aan de naam van de categorie op de + of-Control om de afzonderlijke pagina's uit te vouwen of te verbergen.

![Het tabblad instellingen van het configuratie scherm (in de browser) met het cluster > pagina Algemene installatie geladen](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Installatie stappen voor het cluster

Op dit punt in het proces bestaat uw cluster, maar heeft er slechts één knoop punt, geen client gerichte IP-adressen en geen back-end-opslag. Er zijn aanvullende installatie stappen nodig om vanaf een nieuw gemaakt cluster naar een cache systeem te gaan dat klaar is om uw werk stroom te verwerken.

### <a name="required-configuration"></a>Vereiste configuratie

Deze stappen zijn nodig voor de meeste of alle clusters. 

* Knoop punten toevoegen aan het cluster 

  Drie knoop punten zijn standaard, maar veel productie clusters hebben meer tot Maxi maal 24 knoop punten.

  Lees [cluster knooppunten toevoegen](fxt-add-nodes.md) voor meer informatie over het toevoegen van andere Azure FXT Edge-eenheden aan uw cluster en om hoge Beschik baarheid in te scha kelen.

* Opslag voor back-end opgeven

  Een definitie van een *kern bestand* toevoegen voor elk back-end-opslag systeem dat door het cluster wordt gebruikt. Lees de [back-end-opslag toevoegen en configureer de virtuele naam ruimte](fxt-add-storage.md#about-back-end-storage) voor meer informatie.

* Client toegang en de virtuele naam ruimte instellen 

  Maak ten minste één virtuele server (vserver) en wijs deze toe aan een IP-adres bereik dat door client computers kan worden gebruikt. U moet ook de cluster naam ruimte (ook wel de globale naam ruimte of GNS genoemd) configureren, een virtuele bestandssysteem functie waarmee u opslag van back-endservers kunt koppelen aan virtuele paden. De cluster naam ruimte biedt clients een consistente en toegankelijke bestandssysteem structuur, zelfs als u back-end-opslag media overschakelt. De naam ruimte kan ook een gebruiks vriendelijke virtuele-opslag hiërarchie bieden voor Azure Blob-containers of andere ondersteunde Cloud object opslag.

  Lees [de naam ruimte configureren](fxt-add-storage.md#configure-the-namespace) voor meer informatie. Deze stap omvat:
  * Vservers maken
  * Koppelingen instellen tussen de weer gave van de client netwerk en de back-end-opslag 
  * Definiëren welke client-IP-adressen worden verwerkt door elke vserver

  > [!Note] 
  > Er wordt een belang rijke planning aanbevolen voordat u begint met het instellen van de GNS van het cluster. Lees de secties [een globale naam ruimte gebruiken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) en [maken en werken met VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) in de cluster configuratie handleiding voor meer informatie.

* [Netwerk instellingen aanpassen](fxt-configure-network.md)

  Er zijn verschillende netwerk instellingen die moeten worden gecontroleerd of aangepast voor een nieuw cluster. Lees de [netwerk instellingen aanpassen](fxt-configure-network.md) voor meer informatie over deze items:

  * DNS-en NTP-configuratie controleren 
  * Directory Services, indien nodig, configureren 
  * VLAN'S instellen
  * Proxy servers configureren
  * IP-adressen toevoegen aan het cluster netwerk
  * Versleutelings certificaten opslaan

* [Ondersteunings controle instellen](#enable-support)

  U moet het privacybeleid voor het configuratie programma accepteren en u moet uw instellingen voor het uploaden van ondersteuning tegelijk configureren.

  Het cluster kan automatisch gegevens over het oplossen van problemen over uw cluster uploaden, met inbegrip van statistieken en bestanden voor fout opsporing. Met deze uploads kunnen klanten service en ondersteuning van micro soft de best mogelijke service bieden. U kunt bepalen wat er wordt bewaakt en desgewenst de proactieve ondersteuning en externe probleemoplossings service inschakelen.  

### <a name="optional-configuration"></a>Optionele configuratie

Deze stappen zijn niet vereist voor alle clusters. Ze zijn nodig voor bepaalde typen werk stromen of voor bepaalde Cluster beheer stijlen. 

* Knooppunt instellingen aanpassen

  U kunt knooppunt namen instellen en knooppunt-IPMI-poorten configureren op het niveau van een cluster of afzonderlijk. Als u deze instellingen configureert voordat u knoop punten aan het cluster toevoegt, kunnen de nieuwe knoop punten automatisch de instellingen ophalen wanneer ze worden toegevoegd. De opties worden beschreven in het verouderde cluster maken document [aanpassen knooppunt instellingen](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Sommige documentatie voor dit product is nog niet beschikbaar op de Microsoft Azure-documentatie site. Met koppelingen naar de [cluster configuratie handleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) en de oudere versie van de [hand leiding](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) voor het maken van een cluster gaat u naar een afzonderlijke door github gehoste website. 

* SMB configureren

  Als u SMB-toegang tot uw cluster en NFS wilt toestaan, moet u SMB configureren en inschakelen. SMB wordt meestal gebruikt voor de ondersteuning van micro soft Windows-clients.

  Het plannen en configureren van SMB vergt meer dan door te klikken op een paar knoppen in het configuratie scherm. Afhankelijk van de vereisten van uw systeem kan SMB invloed hebben op de manier waarop u kern bestanden definieert, hoeveel vservers u maakt, hoe u uw koppelingen en naam ruimte, toegangs machtigingen en andere instellingen kunt configureren.

  Lees voor meer informatie de sectie cluster configuratie handleiding [Configuring SMB Access](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) .

* Aanvullende licenties installeren

  Als u andere Cloud opslag dan Azure Blob wilt gebruiken, moet u een extra functie licentie installeren. Neem contact op met uw micro soft-vertegenwoordiger voor meer informatie over het aanschaffen van een FlashCloud<sup>TM</sup> -licentie. Details worden uitgelegd in [back-end-opslag toevoegen en virtuele naam ruimte configureren](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Ondersteuning inschakelen

Met het Azure FXT Edge-bestands cluster kunnen automatisch ondersteunings gegevens over uw cluster worden geüpload. Met deze uploads kunnen mede werkers de best mogelijke klanten service bieden.

Volg deze stappen voor het instellen van ondersteunings-uploads.

1. Ga naar de pagina **Cluster** >  instellingen voor**ondersteuning** . Accepteer het privacybeleid. 

   ![Scherm opname van het configuratie scherm en het pop-upvenster met de knop bevestigen om het privacybeleid te accepteren](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Klik op het drie hoekje links van **klant gegevens** om de sectie uit te vouwen.
1. Klik op de knop **Upload gegevens opnieuw valideren** .
1. Stel de ondersteunings naam van het cluster in de **unieke naam** van het cluster in. Zorg ervoor dat het cluster uniek wordt geïdentificeerd ter ondersteuning van mede werkers.
1. Schakel de selectie vakjes in voor **Statistieken bewaking**, **algemene informatie uploaden**en **informatie over het uploaden van crashes**.
1. Klik op **Submit**  

   ![Scherm afbeelding met de sectie met voltooide klant gegevens van de pagina met ondersteunings instellingen](media/fxt-cluster-create/fxt-support-info.png)

1. Klik op het drie hoekje links van **beveiligde proactieve ondersteuning (SPS)** om de sectie uit te vouwen.
1. Schakel het selectie vakje in voor het inschakelen van de **SPS-koppeling**.
1. Klik op **Submit**

   ![Scherm afbeelding met de sectie beveiligde proactieve ondersteuning op de pagina ondersteunings instellingen](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u het basis cluster hebt gemaakt en het privacybeleid hebt geaccepteerd, voegt u de rest van de cluster knooppunten toe. 

> [!div class="nextstepaction"]
> [Clusterknooppunten toevoegen](fxt-add-nodes.md)
