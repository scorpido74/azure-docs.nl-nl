---
title: 'Zelfstudie: Opslag toevoegen aan een Azure FXT Edge Filer-cluster'
description: Back-endopslag en de clientgerichte pseudonameruimte configureren voor Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239215"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Zelfstudie: Back-endopslag toevoegen en de virtuele naamruimte configureren 

In deze zelfstudie wordt uitgelegd hoe u back-edge-opslag voor uw cache toevoegen en hoe u het clientgerichte virtuele bestandssysteem instellen. 

Het cluster maakt verbinding met back-endopslagsystemen om toegang te krijgen tot de gegevens die clients aanvragen en om wijzigingen permanenter op te slaan dan in de cache. 

De naamruimte is het clientgerichte pseudo-bestandssysteem waarmee u back-endopslag verwisselen zonder workflows aan de clientzijde te wijzigen. 

In deze zelfstudie leert u: 

> [!div class="checklist"]
> * Back-endopslag toevoegen aan het Azure FXT Edge Filer-cluster 
> * Het clientgerichte pad voor opslag definiëren

## <a name="about-back-end-storage"></a>Over back-end opslag

Het Azure FXT Edge Filer-cluster maakt gebruik van een *kernfilerdefinitie* om een back-endopslagsysteem te koppelen aan het FXT-cluster.

Azure FXT Edge Filer is compatibel met verschillende populaire NAS-hardwaresystemen en kan lege containers gebruiken van Azure Blob of andere cloudopslag. 

Cloudopslagcontainers moeten leeg zijn wanneer ze worden toegevoegd, zodat het FXT-besturingssysteem alle gegevens op het cloudopslagvolume volledig kan beheren. U uw bestaande gegevens naar de cloudcontainer verplaatsen nadat u de container als kernfiler aan het cluster hebt toegevoegd.

Gebruik het Configuratiescherm om een core filer aan uw systeem toe te voegen.

> [!NOTE]
> 
> Als u Amazon AWS- of Google Cloud-opslag wilt gebruiken, moet u een FlashCloud<sup>TM-functielicentie</sup> installeren. Neem contact op met uw Microsoft-vertegenwoordiger voor een licentiesleutel en volg de instructies in de verouderde configuratiehandleiding voor [het toevoegen of verwijderen van functielicenties.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses)
> 
> Ondersteuning voor Azure Blob-opslag is opgenomen in de Azure FXT Edge Filer-softwarelicentie. 

Lees deze secties van de clusterconfiguratiehandleiding voor meer gedetailleerde informatie over het toevoegen van kernfilers:

* Lees [Werken met Core Filers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview)voor meer informatie over het kiezen en voorbereiden van het toevoegen van een core filer.
* Lees de volgende artikelen voor gedetailleerde vereisten en stapsgewijze instructies:

  * [Een nieuwe NAS Core Filer toevoegen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Een nieuwe Cloud Core Filer toevoegen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Nadat u een core filer hebt toegevoegd, u de instellingen bijwerken op de pagina Details van de Core Filer.

## <a name="add-a-core-filer"></a>Een kernfiler toevoegen

Definieer een kernfiler door op de knop **Maken** op de**instellingenpagina Core Filers beheren** **van** > core-bestanden te klikken.

![Klik op de knop Maken boven de lijst met kernfilers op de pagina Core Filers beheren](media/fxt-cluster-config/create-core-filer-button.png)

De wizard **Nieuwe Core Filer toevoegen** leidt u door het proces van het maken van een kernfiler die naar uw back-endopslag linkt. De clusterconfiguratiehandleiding bevat stapsgewijze beschrijvingen van het proces, wat anders is voor NFS/NAS-opslag en voor cloudopslag (koppelingen zijn hierboven). 

Subtaken zijn:

* Het type core filer opgeven (NAS of cloud)

  ![Eerste pagina van de nieuwe core filer-wizard van hardware NAS. De optie voor "cloud core filer" is uitgeschakeld en toont een foutmelding over de ontbrekende licentie.](media/fxt-cluster-config/new-nas-1.png)

* Stel de naam van de core filer in. Kies een naam waarmee de clusterbeheerders inzicht krijgen in welk opslagsysteem het vertegenwoordigt.

* Geef voor NAS-kernfilers de volledig gekwalificeerde domeinnaam (FQDN) of IP-adres op. FQDN wordt aanbevolen voor alle kernbestanden en vereist voor SMB-toegang.

* Selecteer een cachebeleid : op de tweede pagina van de wizard vindt u het beschikbare cachebeleid voor de nieuwe kernfiler. Lees voor meer informatie het [gedeelte cachebeleid in de clusterconfiguratiehandleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Tweede pagina van een nieuwe core filer-wizard van hardware NAS; het vervolgkeuzemenu Cachebeleid is geopend, met verschillende uitgeschakelde opties en drie geldige cachebeleidsopties (omzeilen, caching lezen en caching lezen/schrijven).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Voor cloudopslag moet u onder andere de cloudservice- en toegangsreferenties opgeven. Lees [cloudservice en -protocol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) voor meer informatie in de clusterconfiguratiehandleiding.

  ![Cloud core filer-informatie in de wizard Nieuwe Core Filer](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Als u al cloudtoegangsreferenties voor dit cluster hebt toegevoegd, worden deze weergegeven in de lijst. Referenties bijwerken en toevoegen op de pagina**Instellingen voor clustercloudreferenties.** **Cluster** >  

Nadat u alle vereiste instellingen in de wizard hebt ingevuld, klikt u op de knop **Filer toevoegen** om de wijziging in te dienen.

Na enkele ogenblikken wordt het opslagsysteem weergegeven in de lijst **met dashboardbestanden** en is het toegankelijk via de pagina's met de instellingen van de kernfiler.

![core filer "Flurry-NAS" op de pagina Core Filers-instellingen beheren, met de filer-detailsweergave uitgevouwen](media/fxt-cluster-config/core-filer-in-manage-page.png)

De core filer in deze screenshot mist een vserver. U moet de core filer koppelen aan een vserver en een knooppunt maken, zodat clients toegang hebben tot de opslag. Deze stappen worden hieronder beschreven in [De naamruimte configureren](#configure-the-namespace).

## <a name="configure-the-namespace"></a>De naamruimte configureren

Het Azure FXT Edge Filer-cluster maakt een virtueel bestandssysteem genaamd de *clusternaamruimte* dat clienttoegang tot gegevens die zijn opgeslagen op diverse back-endsystemen vereenvoudigt. Omdat clients bestanden aanvragen met een virtueel pad, kunnen opslagsystemen worden toegevoegd of vervangen zonder dat de clientworkflow hoeft te worden gewijzigd. 

Met de clusternaamruimte u ook cloud- en NAS-opslagsystemen in een vergelijkbare bestandsstructuur presenteren. 

De vservers van het cluster behouden de naamruimte en serveren inhoud aan clients. Er zijn twee stappen om de naamruimte van het cluster te maken: 

1. Een vserver maken 
1. Knooppunten instellen tussen de back-endopslagsystemen en de clientgerichte bestandssysteempaden 

### <a name="create-a-vserver"></a>Een vserver maken

VServers zijn virtuele bestandsservers die bepalen hoe gegevens stromen tussen de client en de kernfilers van het cluster:

* VServers host op client gerichte IP-adressen
* VServers maken de naamruimte en definiëren knooppunten die de clientgerichte virtuele mapstructuur toewijzen aan export op back-endopslag
* VServers dwingen besturingselementen voor bestandstoegang af, inclusief exportbeleid voor de core filer en gebruikersverificatiesystemen
* VServers bieden SMB-infrastructuur

Voordat u begint met het configureren van een clustervserver, leest u de gekoppelde documentatie en raadpleegt u uw Microsoft-vertegenwoordiger voor hulp bij het begrijpen van naamruimte en vservers. Als u VLAN's gebruikt, [maakt u ze](fxt-configure-network.md#adjust-network-settings) voordat u de vserver maakt. 

Deze secties van de clusterconfiguratiehandleiding helpen u vertrouwd te raken met de FXT-vserver- en algemene naamruimtefuncties:

* [Maken en werken met VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Een globale naamruimte gebruiken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Een VServer maken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Je hebt ten minste één vserver nodig voor je cluster. 

Als u een nieuwe vserver wilt maken, hebt u de volgende informatie nodig:

* De naam die u voor de vserver moet instellen

* Het bereik van clientgerichte IP-adressen die de vserver verwerkt

  U moet één reeks aaneengesloten IP-adressen leveren wanneer u de vserver maakt. U later meer adressen toevoegen met de pagina **Client Facing Network-instellingen.**

* Als uw netwerk VLAN's heeft, welke VLAN moet gebruiken voor deze vserver

Gebruik de pagina **VServer** > **Manage VServers-instellingen** om een nieuwe vserver te maken. Lees voor meer informatie [Een VServer maken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) in de clusterconfiguratiehandleiding. 

![pop-upvenster voor het maken van een nieuwe vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Een knooppunt maken

Een *knooppunt brengt* een back-endopslagpad in kaart naar de clientzichtbare naamruimte.

U dit systeem gebruiken om het pad dat wordt gebruikt in clientmountpunten te vereenvoudigen en om de capaciteit naadloos te schalen, omdat één virtueel pad geschikt is voor opslag vanuit meerdere kernbestanden.

![Wizard Pagina Nieuw knooppunt toevoegen met ingevulde instellingen](media/fxt-cluster-config/add-junction-full.png)

Raadpleeg [ **VServer-naamruimte** > ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) in de clusterconfiguratiehandleiding voor volledige details over het maken van een naamruimteknooppunt.

![De pagina VServer->-instellingen voor naamruimte met details voor een knooppunt](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Exportregels configureren

Nadat u zowel een vserver als een core filer hebt, moet u de exportregels en het exportbeleid aanpassen waarmee wordt bepalen hoe clients toegang hebben tot bestanden op de uitvoer van de kernfiler.

Gebruik eerst de pagina > **VServer-exportregels** om nieuwe regels toe te voegen, het standaardbeleid te wijzigen of om uw eigen aangepaste exportbeleid te maken. **VServer**

Ten tweede gebruikt u de pagina > **VServer-exportbeleid** om het aangepaste beleid toe te passen op de export van uw core filer wanneer deze via die vserver wordt geopend. **VServer**

Lees het artikel Clusterconfiguratiegids [Dat toegang tot Core Filer-export controleert](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Nadat u opslag hebt toegevoegd en de naamruimte voor de client hebt geconfigureerd, moet u de eerste installatie van uw cluster voltooien: 

> [!div class="nextstepaction"]
> [De netwerkinstellingen van het cluster configureren](fxt-configure-network.md)
