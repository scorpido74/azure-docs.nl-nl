---
title: 'Zelfstudie: Het azure FXT Edge Filer-cachecluster maken'
description: Een cluster met hybride opslagcache maken met de Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: bfe1d1aeeac55039acf0c7eb295001277be9cd2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239208"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Zelfstudie: Het Azure FXT Edge Filer-cluster maken

Nadat u de Azure FXT Edge Filer-hardwareknooppunten voor uw cache hebt geïnstalleerd en geïnitialiseerd, gebruikt u de FXT-clustersoftware om het cachecluster te maken. 

In deze zelfstudie u de stappen doorlopen om uw hardwareknooppunten als een cluster te configureren. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Welke informatie is nodig voordat u begint met het maken van het cluster
> * Het verschil tussen het beheernetwerk van het cluster, het clusternetwerk en het netwerk dat met de client wordt geconfronteerd
> * Verbinding maken met een clusterknooppunt 
> * Een eerste cluster maken met één Azure FXT Edge Filer-knooppunt
> * Aanmelden bij het clusterconfiguratiescherm om de clusterinstellingen te configureren

Deze procedure duurt tussen de 15 en 45 minuten, afhankelijk van hoeveel onderzoek u moet doen om IP-adressen en netwerkbronnen te identificeren.

## <a name="prerequisites"></a>Vereisten

Voltooi deze vereisten voordat u deze zelfstudie start:

* Installeer uw Azure FXT Edge Filer-hardwaresystemen in uw datacenter 

  U hebt slechts één knooppunt nodig om het cluster te maken, maar u moet [nog minstens twee knooppunten toevoegen](fxt-add-nodes.md) voordat u het cluster configureren en klaar maken voor gebruik. 

* Sluit de juiste stroom- en netwerkkabels aan op het systeem  
* Ten minste één Azure FXT Edge Filer-knooppunt inschakelen en [het hoofdwachtwoord instellen](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Informatie verzamelen voor het cluster 

U hebt de volgende informatie nodig om het Azure FXT Edge Filer-cluster te maken:

* Clusternaam

* Administratief wachtwoord in te stellen voor het cluster

* IP-adressen:

  * Eén IP-adres voor clusterbeheer en de netmask en router die u voor het beheernetwerk moet gebruiken
  * De eerste en laatste IP-adressen in een aaneengesloten bereik van IP-adressen voor clustercommunicatie (knooppunt tot knooppunt). Zie [IP-adres distributie](#ip-address-distribution), hieronder, voor meer informatie. 
  * (Clientgerichte IP-adressen worden ingesteld na het maken van het cluster.)

* Informatie over netwerkinfrastructuur:

  * Het IP-adres van een DNS-server voor het cluster
  * De naam van het DNS-domein voor het cluster
  * De naam of het IP-adres voor de CLUSTER NTP-servers (één server of drie of meer) 
  * Of u nu ieee 802.1AX-2008-koppelingsaggregatie wilt inschakelen op de interfaces van het cluster
  * Als u koppelingsaggregatie inschakelt, ongeacht of u ieee 802.3ad (LACP) dynamische aggregatie wilt gebruiken

U deze netwerkinfrastructuuritems configureren nadat u het cluster hebt gemaakt, maar het is beter om dit te doen tijdens het maken. 

### <a name="ip-address-distribution"></a>Ip-adresdistributie

Het azure FXT Edge Filer-cluster voor hybride opslagcache maakt gebruik van IP-adressen in drie categorieën:

* Beheer-IP: één IP-adres voor clusterbeheer

  Dit adres dient als toegangspunt voor toegang tot de clusterconfiguratiehulpprogramma's (het webgebaseerde configuratiescherm of de XML-RPC-API). Dit adres wordt automatisch toegewezen aan het primaire knooppunt in het cluster en wordt automatisch verplaatst als het primaire knooppunt verandert.

  Andere IP-adressen kunnen worden gebruikt om toegang te krijgen tot het configuratiescherm, maar het IP-adres van het beheer is ontworpen om toegang te bieden, zelfs als afzonderlijke knooppunten mislukken.

* Clusternetwerk: een reeks IP-adressen voor clustercommunicatie

  Het clusternetwerk wordt gebruikt voor communicatie tussen clusterknooppunten en om bestanden op te halen uit de backend-opslag (core filers).

  **Beste praktijken:** Wijs één IP-adres toe per fysieke poort die wordt gebruikt voor clustercommunicatie op elk Azure FXT Edge Filer-knooppunt. Het cluster wijst de adressen in het opgegeven bereik automatisch toe aan afzonderlijke knooppunten.

* Clientgericht netwerk: het bereik van IP-adressen dat clients gebruiken om bestanden op te vragen en te schrijven

  De clientnetwerkadressen worden door clients gebruikt om toegang te krijgen tot de kernfilergegevens via het cluster. Een NFS-client kan bijvoorbeeld een van deze adressen monteren.

  **Beste praktijken:** Wijs één IP-adres toe per fysieke poort die wordt gebruikt voor clientcommunicatie op elk knooppunt van de FXT-serie.

  Het cluster distribueert clientgerichte IP-adressen zo gelijkmatig mogelijk over de samenstellende knooppunten.

  Voor de eenvoud configureren veel beheerders één DNS-naam met rrdns-configuratie (round-robin DNS) om het gemakkelijker te maken clientaanvragen over het adresbereik te distribueren. Met deze instelling kunnen alle clients dezelfde opdracht voor het instellen gebruiken om toegang te krijgen tot het cluster. Lees [DNS configureren](fxt-configure-network.md#configure-dns-for-load-balancing) voor meer informatie.

Het IP-adres van het beheer en een reeks clusternetwerkadressen moeten worden opgegeven om een nieuw cluster te maken. Clientgerichte adressen worden opgegeven na het maken van het cluster.

## <a name="connect-to-the-first-node"></a>Verbinding maken met het eerste knooppunt

U verbinding maken met een van de geïnstalleerde FXT-knooppunten en de OS-software gebruiken om het cluster in te stellen.

Als u dit nog niet hebt gedaan, u ten minste één van de FXT-knooppunten voor uw cluster inschakelen en ervoor zorgen dat het een netwerkverbinding en een IP-adres heeft. U moet een nieuw hoofdwachtwoord instellen om het knooppunt te activeren, dus volg de stappen in [Hardware-wachtwoorden instellen](fxt-node-password.md) als u dit nog niet hebt gedaan.

Om de netwerkverbinding te controleren, moet u ervoor zorgen dat de netwerkkoppeling-LED's van het knooppunt worden verlicht (en, indien nodig, de indicatoren op de netwerkschakelaar waaraan het is bevestigd). Indicator-LED's worden beschreven in [de hardwarestatus monitor Azure FXT Edge Filer.](fxt-monitor.md)

Wanneer het knooppunt wordt opgestart, wordt een IP-adres aangevraagd. Als deze is verbonden met een DHCP-server, accepteert het ip-adres van DHCP. (Dit IP-adres is tijdelijk. Het zal veranderen wanneer u het cluster maakt.)

Als het niet is verbonden met een DHCP-server of geen antwoord ontvangt, gebruikt het knooppunt Bonjour-software om een zelftoegewezen IP-adres in te stellen in het formulier 169.254. \*. \*. U moet echter een tijdelijk statisch IP-adres instellen op een van de netwerkkaarten van het knooppunt voordat u het gebruikt om een cluster te maken. Instructies zijn opgenomen in dit oude document; Neem contact op met Microsoft Service en ondersteuning voor bijgewerkte informatie: [aanhangsel A: Een statisch IP-adres instellen op een FXT-knooppunt](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Het IP-adres zoeken

Maak verbinding met het Azure FXT Edge Filer-knooppunt om het IP-adres te vinden. U gebruik maken van een seriële kabel, directe verbinding met de USB- en VGA-poorten of verbinding maken via een KVM-switch. (Zie [Eerste wachtwoorden instellen](fxt-node-password.md)voor poortverbindingsgegevens .)

Nadat u verbinding hebt gemaakt, meldt u zich aan met de gebruikersnaam `root` en het wachtwoord dat u instelt toen u het knooppunt voor de eerste keer hebt opgestart.  

Nadat u zich hebt aanmeldt, moet u het IP-adres van het knooppunt bepalen.

Gebruik de `ifconfig` opdracht om de adressen te zien die aan dit systeem zijn toegewezen.

De opdracht `ifconfig | grep -B5 inet` zoekt bijvoorbeeld naar poorten met internetadressen en geeft vijf contextregels om de poort-id weer te geven.

Noteer een IP-adres dat in het ifconfig-rapport wordt weergegeven. Adressen die worden vermeld met poortnamen zoals e0a of e0b zijn goede opties. Gebruik geen IP-adressen die worden vermeld met e7*-namen, omdat deze namen alleen worden gebruikt voor iDRAC/IPMI-servicepoorten.  

## <a name="load-the-cluster-configuration-wizard"></a>De wizard clusterconfiguratie laden

Gebruik het op de browser gebaseerde clusterconfiguratieprogramma om het cluster te maken. 

Voer het IP-adres voor het knooppunt in een webbrowser in. Als de browser een bericht geeft over de site die niet wordt vertrouwd, ga dan toch naar de site. (Afzonderlijke Azure FXT Edge Filer-knooppunten hebben geen door CA geleverde beveiligingscertificaten.)

![Aanmeldingspagina van het Configuratiescherm in het browservenster](media/fxt-cluster-create/unconfigured-node-gui.png)

Laat de velden **Gebruikersnaam** en **Wachtwoord** leeg. Klik **op Aanmelden** om de pagina voor het maken van het cluster te laden.

![Eerste installatiescherm voor een niet-geconfigureerd knooppunt in het configuratiescherm van de browser. Er worden opties weergegeven om software bij te werken, een cluster handmatig te configureren of een cluster te configureren vanuit een installatiebestand.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Het cluster maken

Het hulpprogramma voor clusterconfiguratie leidt u door een reeks schermen om het Azure FXT Edge Filer-cluster te maken. Zorg ervoor dat u de [vereiste informatie](#gather-information-for-the-cluster) bij de hand hebt voordat u begint. 

### <a name="creation-options"></a>Opties voor maken

Het eerste scherm geeft drie opties. Gebruik de handmatige configuratieoptie, tenzij u speciale instructies van het ondersteuningspersoneel hebt.

Klik **op Ik configureer het cluster handmatig** om het scherm met de nieuwe clusterconfiguratieopties te laden. 

De andere opties worden zelden gebruikt:

* "Update de systeemafbeelding" vraagt u om nieuwe OS-software te installeren voordat u het cluster maakt. (De momenteel geïnstalleerde softwareversie wordt boven aan het scherm weergegeven.) U moet het softwarepakketbestand opgeven - een URL en gebruikersnaam/wachtwoord, of door een bestand van uw computer te uploaden. 

* De clusterinstallatie-bestandsoptie wordt soms gebruikt door Microsoft Customer Service and Support. 

## <a name="cluster-options"></a>Clusteropties

In het volgende scherm wordt u gevraagd opties voor het nieuwe cluster te configureren.

De pagina is onderverdeeld in twee hoofdsecties, **Basisconfiguratie** en **netwerkconfiguratie.** De sectie netwerkconfiguratie bevat ook subsecties: een voor het **beheernetwerk** en een voor het **clusternetwerk.**

### <a name="basic-configuration"></a>Basisconfiguratie

Vul in het bovenste gedeelte basisgegevens in voor het nieuwe cluster.

![Detail van de sectie 'Basisconfiguratie' op de PAGINA GUI in de browser. Het toont drie velden (clusternaam, beheerderswachtwoord, wachtwoord bevestigen)](media/fxt-cluster-create/basic-configuration.png) 

* **Clusternaam** - Voer een unieke naam voor het cluster in.

  De clusternaam moet aan de volgende criteria voldoen:
  
  * Lengte van 1 tot 16 tekens
  * Kan letters, cijfers en het streepje (-) en ondermaat (_) tekens bevatten 
  * Mag geen andere interpunctie of speciale tekens bevatten
  
  U deze naam later wijzigen op de **configuratiepagina Cluster** > **Algemene installatie.** (Lees de [clusterconfiguratiehandleiding,](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)die geen deel uitmaakt van deze documentatieset, voor meer informatie over clusterinstellingen.)

  > [!NOTE] 
  > Uw clusternaam wordt gebruikt om systeemgegevens te identificeren die zijn geüpload ter ondersteuning van monitoring of probleemoplossing, dus het is handig om uw bedrijfsnaam op te nemen.

* **Beheerderswachtwoord** - Stel het wachtwoord in `admin`voor de standaardgebruiker.
  
  U moet afzonderlijke gebruikersaccounts instellen voor elke persoon die het cluster `admin`beheert, maar u de gebruiker niet verwijderen. Meld u `admin` aan alsof u extra gebruikers moet maken.
 
  U het `admin` wachtwoord wijzigen op de pagina Instellingen **beheergebruikers** > **Users** in het configuratiescherm van het cluster. Lees voor meer informatie de documentatie **gebruikers** in de [clusterconfiguratiehandleiding.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html)

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Netwerkconfiguratie

In de sectie **Netwerken** wordt u gevraagd de netwerkinfrastructuur op te geven die het cluster zal gebruiken. 

Er zijn twee afzonderlijke netwerken om te configureren:

* Het *beheernetwerk* biedt beheerderstoegang tot het cluster voor configuratie en bewaking. Het IP-adres dat hier is opgegeven, wordt gebruikt bij het maken van verbinding met het Configuratiescherm of voor SSH-toegang. 

  De meeste clusters gebruiken slechts één IP-adres voor beheer, maar als u interfaces wilt toevoegen, u dit doen nadat u het cluster hebt gemaakt.

* Het *clusternetwerk* wordt gebruikt voor communicatie tussen clusterknooppunten en tussen clusterknooppunten en back-endopslag (core filers).

Het clientgerichte netwerk wordt later geconfigureerd nadat het cluster is gemaakt.

Deze sectie bevat ook configuratie voor DNS- en NTP-servers die door beide netwerken worden gebruikt.

### <a name="configure-the-management-network"></a>Het beheernetwerk configureren

Instellingen in de sectie **Beheer** zijn voor het netwerk dat beheerderstoegang biedt tot het cluster.

![detail van de sectie "Beheer", met velden voor 5 opties en een selectievakje voor 1 Gb beheernetwerk](media/fxt-cluster-create/management-network-filled.png)

* **Beheer-IP** - Geef het IP-adres op dat u gebruikt om toegang te krijgen tot het configuratiescherm van het cluster. Dit adres wordt geclaimd door het primaire knooppunt van het cluster, maar wordt automatisch verplaatst naar een gezond knooppunt als het oorspronkelijke primaire knooppunt niet beschikbaar is.

  De meeste clusters gebruiken slechts één beheer-IP-adres. Als u er meer dan één wilt, u ze toevoegen nadat u het cluster hebt gemaakt met behulp van de pagina > **Clusterbeheernetwerkinstellingen.** **Cluster** Lees meer in de [clusterconfiguratiehandleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Netmask** - Geef het netmasker voor het beheernetwerk op.

* **Router** : voer het standaardgatewayadres in dat door het beheernetwerk wordt gebruikt.

* **VLAN-tag (optioneel)** - Als uw cluster VLAN-tags gebruikt, geeft u de tag voor het beheernetwerk op.

  Extra VLAN-instellingen zijn geconfigureerd op de pagina **Cluster** > **VLAN-instellingen.** Lees [Werken met VLAN's](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) en [cluster> VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) in de clusterconfiguratiehandleiding voor meer informatie.

* **MTU** - Pas indien nodig de maximale transmissie-eenheid (MTU) aan voor het beheernetwerk van uw cluster.

* **Gebruik 1 Gb mgmt-netwerk** - Schakel dit selectievakje in als u de twee 1GbE-netwerkpoorten alleen aan uw FXT-knooppunten aan het beheernetwerk wilt toewijzen. (U moet 25GbE/10GbE-poorten beschikbaar hebben voor alle andere verkeer.) Als u dit selectievakje niet aanvinkt, gebruikt het beheernetwerk de beschikbare poort met de hoogste snelheid. 

### <a name="configure-the-cluster-network"></a>Het clusternetwerk configureren 

De clusternetwerkinstellingen zijn van toepassing op verkeer tussen de clusterknooppunten en tussen clusterknooppunten en kernbestanden.

![detail van de sectie "Cluster", met velden om zes waarden in te voeren](media/fxt-cluster-create/cluster-network-filled.png)

* **Eerste IP** en **laatste IP** - Voer de IP-adressen in die het bereik definiëren dat moet worden gebruikt voor interne clustercommunicatie. De IP-adressen die hier worden gebruikt, moeten aaneengesloten zijn en niet door DHCP zijn toegewezen.

  U meer IP-adressen toevoegen na het maken van het cluster. Gebruik de pagina > **Clusterclusternetwerken** [(clusterconfiguratiehandleidingdocumentatie).](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks) **Cluster**

  De waarde in **Aantal IP's in bereik** wordt automatisch berekend en weergegeven.

* **Niet-mgmt netmask (optioneel)** - Geef het netmasker voor het clusternetwerk op. 

  Het systeem stelt automatisch de nettomaskerwaarde voor die u voor het beheernetwerk hebt ingevoerd; indien nodig te veranderen.

* **Clusterrouter (optioneel)** - Geef het standaardgatewayadres op dat door het clusternetwerk wordt gebruikt. 

  Het systeem stelt automatisch hetzelfde gatewayadres voor dat u voor het beheernetwerk hebt opgegeven.

* **Cluster VLAN-tag (optioneel)** - Als uw cluster VLAN-tags gebruikt, geeft u de tag voor het clusternetwerk op.

* **Niet-mgmt MTU (optioneel)** - Pas indien nodig de maximale transmissie-eenheid (MTU) aan voor uw clusternetwerk.

### <a name="configure-cluster-dns-and-ntp"></a>Cluster DNS en NTP configureren 

Onder de sectie **Cluster** staan velden voor het opgeven van DNS- en NTP-servers en voor het inschakelen van koppelingsaggregatie. Deze instellingen zijn van toepassing op alle netwerken die het cluster gebruikt.

![Detail van sectie voor DNS/NTP-configuratie, met drie velden voor DNS-servers, velden voor DNS-domein- en DNS-zoekopdrachten, drie velden voor NTP-servers en een vervolgkeuzemenu voor koppelingsopties](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS-server(s)** - Voer het IP-adres van een of meer DNS-servers (Domain Name System) in.

  DNS wordt aanbevolen voor alle clusters en vereist als u SMB, AD of Kerberos wilt gebruiken. 
  
  Configureer de DNS-server van het cluster voor round-robin load balancing zoals beschreven in [DNS configureren voor het Azure FXT Edge Filer-cluster.](fxt-configure-network.md#configure-dns-for-load-balancing)

* **DNS-domein** - Voer de netwerkdomeinnaam in die het cluster zal gebruiken.

* **DNS-zoekopdracht** - Voer optioneel extra domeinnamen in die het systeem moet zoeken om DNS-query's op te lossen. U maximaal zes domeinnamen toevoegen, gescheiden door spaties.

* **NTP-server(s)** - Geef één of drie NTP-servers (Network Time Protocol) op in de opgegeven velden. U hostnamen of IP-adressen gebruiken.

* **Koppelingsaggregatie** - Met koppelingsaggregatie u aanpassen hoe de ethernetpoorten op de FXT-knooppunten van het cluster omgaan met verschillende soorten verkeer. Lees [Koppelingsaggregatie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) voor meer informatie in de clusterconfiguratiehandleiding.

### <a name="click-the-create-button"></a>Klik op de knop Maken

Nadat u alle vereiste instellingen in het formulier hebt weergegeven, klikt u op de knop **Cluster maken.**

![onderkant van ingevuld formulier met cursor over de knop maken rechtsonder](media/fxt-cluster-create/create-cluster.png)

Het systeem geeft een bericht weer tijdens het maken van het cluster.

![clusterconfiguratiestatusbericht in browser: "Het FXT-knooppunt maakt nu het cluster. Dit duurt enkele minuten. Wanneer het cluster wordt gemaakt, gaat u naar deze koppeling om de configuratie te voltooien." met hyperlink op "bezoek deze link"](media/fxt-cluster-create/creating-message.png)

Na enkele ogenblikken u op de koppeling in het bericht klikken om naar het configuratiescherm van het cluster te gaan. (Met deze koppeling gaat u naar het IP-adres dat u hebt opgegeven in **het IP-beheer**.) Het duurt 15 seconden tot één minuut voordat de koppeling actief is nadat u op de knop Maken hebt geklikt. Als de webinterface niet wordt geladen, wacht u nog enkele seconden en klikt u opnieuw op de koppeling. 

Het maken van clusteren duurt een minuut of langer, maar u zich aanmelden bij het Configuratiescherm terwijl het proces aan de gang is. Het is normaal dat de dashboardpagina van het configuratiescherm waarschuwingen weergeeft totdat het proces voor het maken van het cluster is voltooid. 

## <a name="open-the-settings-pages"></a>De pagina's Instellingen openen 

Nadat u het cluster hebt gemaakt, moet u de configuratie voor uw netwerk en werkstroom aanpassen. 

Gebruik de webinterface van het Configuratiescherm om uw nieuwe cluster in te stellen. Volg de koppeling vanuit het statusscherm van het clustermaken of blader naar het management-IP-adres dat u op het cluster instelt.

Meld u aan bij de `admin` webinterface met de gebruikersnaam en het wachtwoord dat u instelt bij het maken van het cluster.

![webbrowser met aanmeldingsvelden van het configuratiescherm](media/fxt-cluster-create/admin-login.png)

Het Configuratiescherm wordt geopend en toont de **dashboardpagina.** Als de clustercreatie is afgelopen, moeten eventuele waarschuwingsberichten uit het display worden gewist.

Klik op het tabblad **Instellingen** om het cluster te configureren.

Op het tabblad **Instellingen** toont de linkerzijbalk een menu met configuratiepagina's. De pagina's worden ingedeeld per categorie. Klik op het besturingselement + of - boven aan de categorienaam om de afzonderlijke pagina's uit te vouwen of te verbergen.

![Tabblad Instellingen van het configuratiescherm (in de browser) met de pagina Cluster > Algemene instelling geladen](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Stappen voor clusterinstellingen

Op dit punt in het proces bestaat uw cluster, maar het heeft slechts één knooppunt, geen ip-adressen die naar de client zijn gericht en geen back-endopslag. Er zijn extra installatiestappen nodig om van een nieuw gemaakt cluster naar een cachesysteem te gaan dat klaar is om uw werkstroom te verwerken.

### <a name="required-configuration"></a>Vereiste configuratie

Deze stappen zijn nodig voor de meeste of alle clusters. 

* Knooppunten toevoegen aan het cluster 

  Drie knooppunten zijn standaard, maar veel productieclusters hebben meer - tot een maximum van 24 knooppunten.

  Lees [Clusterknooppunten toevoegen](fxt-add-nodes.md) voor meer informatie over het toevoegen van andere Azure FXT Edge Filer-eenheden aan uw cluster en om hoge beschikbaarheid in te schakelen.

* Back-endopslag opgeven

  Voeg *kernfilerdefinities* toe voor elk back-endopslagsysteem dat het cluster zal gebruiken. Lees [Back-endopslag toevoegen en configureer virtuele naamruimte](fxt-add-storage.md#about-back-end-storage) voor meer informatie.

* Clienttoegang en de virtuele naamruimte instellen 

  Maak ten minste één virtuele server (vserver) en wijs deze een IP-adresbereik toe voor clientmachines om te gebruiken. U moet ook de clusternaamruimte (ook wel de globale naamruimte of GNS genoemd) configureren, een functie voor een virtueel bestandssysteem waarmee u back-endopslagexport toewijzen aan virtuele paden. De clusternaamruimte biedt clients een consistente en toegankelijke bestandssysteemstructuur, zelfs als u back-endopslagmedia overschakelt. De naamruimte kan ook een gebruiksvriendelijke virtuele opslaghiërarchie bieden voor Azure Blob-containers of andere ondersteunde opslag van cloudobjecten.

  Lees [De naamruimte](fxt-add-storage.md#configure-the-namespace) configureren voor meer informatie. Deze stap omvat:
  * Vservers maken
  * Knooppunten instellen tussen de clientnetwerkweergave en back-endopslag 
  * Definiëren welke client IP-adressen worden bediend door elke vserver

  > [!Note] 
  > Een belangrijke planning wordt aanbevolen voordat u begint met het opzetten van het GNS van het cluster. Lees de secties [Het gebruik van een globale naamruimte](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) en het maken en werken met [VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) in de clusterconfiguratiehandleiding voor hulp.

* [Netwerkinstellingen aanpassen](fxt-configure-network.md)

  Er zijn verschillende netwerkgerelateerde instellingen die moeten worden geverifieerd of aangepast voor een nieuw cluster. Lees [Netwerkinstellingen aanpassen](fxt-configure-network.md) voor meer informatie over deze items:

  * DNS- en NTP-configuratie verifiëren 
  * Indien nodig directoryservices configureren 
  * VLAN's instellen
  * Proxyservers configureren
  * IP-adressen toevoegen aan het clusternetwerk
  * Versleutelingscertificaten opslaan

* [Ondersteuningsbewaking instellen](#enable-support)

  U moet het privacybeleid voor de configuratietool accepteren en u moet tegelijkertijd uw instellingen voor het uploaden van ondersteuning configureren.

  Het cluster kan automatisch gegevens over probleemoplossing over uw cluster uploaden, inclusief statistieken en foutopsporingsbestanden. Met deze uploads kunnen microsoft-klantenservice en -ondersteuning de best mogelijke service bieden. U aanpassen wat wordt gecontroleerd en optioneel de proactieve ondersteunings- en probleemoplossingsservice op afstand inschakelen.  

### <a name="optional-configuration"></a>Optionele configuratie

Deze stappen zijn niet vereist voor alle clusters. Ze zijn nodig voor bepaalde typen werkstromen of voor bepaalde clusterbeheerstijlen. 

* Knooppuntinstellingen aanpassen

  U knooppuntnamen instellen en knooppunt-IPMI-poorten configureren op clusterbreed niveau of afzonderlijk. Als u deze instellingen configureert voordat u knooppunten aan het cluster toevoegt, kunnen de nieuwe knooppunten de instellingen automatisch oppikken wanneer ze lid worden. De opties worden beschreven in het document Voor het maken van een document [voor het maken van een document voor het aanpassen van knooppuntinstellingen](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Sommige documentatie voor dit product is nog niet beschikbaar op de Microsoft Azure-documentatiesite. Links naar de [clusterconfiguratiehandleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) en de verouderde versie van de [clustercreatiegids](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) brengen u naar een afzonderlijke door GitHub gehoste website. 

* SMB configureren

  Als u smb-toegang tot uw cluster en NFS wilt toestaan, moet u SMB configureren en inschakelen. SMB (ook wel CIFS genoemd) wordt meestal gebruikt om Microsoft Windows-clients te ondersteunen.

  Bij het plannen en configureren van SMB wordt meer gedaan dan op een paar knoppen in het Configuratiescherm klikken. Afhankelijk van de vereisten van uw systeem kan SMB invloed uitoefenen op de manier waarop u kernbestandsbestanden definieert, hoeveel vservers u maakt, hoe u uw knooppunten en naamruimte configureert, toegangsmachtigingen en andere instellingen.

  Lees voor meer informatie de sectie Clusterconfiguratiehandleiding [Configureren van smb-toegang.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html)

* Extra licenties installeren

  Als u cloudopslag anders dan Azure Blob wilt gebruiken, moet u een extra functielicentie installeren. Neem contact op met uw Microsoft-vertegenwoordiger voor meer informatie over de aankoop van een FlashCloud<sup>TM-licentie.</sup> Details worden uitgelegd in [Back-end opslag toevoegen en virtuele naamruimte configureren.](fxt-add-storage.md#about-back-end-storage)


### <a name="enable-support"></a>Ondersteuning inschakelen

Het Azure FXT Edge Filer-cluster kan automatisch ondersteuningsgegevens over uw cluster uploaden. Met deze uploads kunnen medewerkers de best mogelijke klantenservice bieden.

Volg deze stappen om ondersteuningsuploads in te stellen.

1. Navigeer naar de pagina Instellingen**voor clusterondersteuning.** **Cluster** >  Accepteer het privacybeleid. 

   ![Schermafbeelding van het Configuratiescherm en het pop-upvenster met de knop Bevestigen om het privacybeleid te accepteren](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Klik op het driehoekje links van **klantgegevens** om de sectie uit te vouwen.
1. Klik op de knop **Uploadgegevens opnieuw valideren.**
1. Stel de ondersteuningsnaam van het cluster in **unieke clusternaam** in - zorg ervoor dat het uw cluster uniek identificeert om medewerkers te ondersteunen.
1. Schakel de selectievakjes voor **statistiekenbewaking,** **algemene informatie uploaden**en **crashinformatie uploaden**in .
1. Klik **op Verzenden**.  

   ![Schermafbeelding van de sectie voltooide pagina met klantgegevens van de pagina Ondersteuningsinstellingen](media/fxt-cluster-create/fxt-support-info.png)

1. Klik op het driehoekje links van **Secure Proactive Support (SPS)** om de sectie uit te vouwen.
1. Schakel het selectievakje in voor **SPS-koppeling inschakelen**.
1. Klik **op Verzenden**.

   ![Schermafbeelding van de voltooide sectie Secure Proactive Support op de pagina ondersteuningsinstellingen](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u het basiscluster hebt gemaakt en het privacybeleid hebt geaccepteerd, voegt u de rest van de clusterknooppunten toe. 

> [!div class="nextstepaction"]
> [Clusterknooppunten toevoegen](fxt-add-nodes.md)
