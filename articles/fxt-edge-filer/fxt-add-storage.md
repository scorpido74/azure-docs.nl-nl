---
title: 'Zelf studie: opslag toevoegen aan een Azure FXT Edge-bestands cluster'
description: Back-end-opslag en de client gerichte pseudonamespace voor de Azure FXT Edge-Bestandsr configureren
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239215"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Zelf studie: back-end-opslag toevoegen en de virtuele naam ruimte configureren 

In deze zelf studie wordt uitgelegd hoe u opslag van back-Edge kunt toevoegen voor uw cache en hoe u het client gerichte virtuele bestands systeem kunt instellen. 

Het cluster maakt verbinding met back-endsystemen om toegang te krijgen tot de gegevens van de client en om wijzigingen permanent op te slaan dan in de cache. 

De naam ruimte is het op de client gerichte pseudo-bestands systeem waarmee u back-end-opslag kunt vervangen zonder dat er aan de client zijde werk stromen worden gewijzigd. 

In deze zelfstudie leert u: 

> [!div class="checklist"]
> * Back-end-opslag toevoegen aan het Azure FXT Edge-bestands cluster 
> * Het client gerichte pad voor opslag definiëren

## <a name="about-back-end-storage"></a>Over back-end-opslag

Het Azure FXT Edge-bestands cluster maakt gebruik van een kern definitie van een *bestand* om een back-end-opslag systeem te koppelen aan het FXT-cluster.

De Azure FXT Edge-Bestandsr is compatibel met verschillende populaire NAS-hardwareapparaten en kan lege containers van Azure Blob of andere Cloud opslag gebruiken. 

Cloud-opslag containers moeten leeg zijn wanneer ze worden toegevoegd, zodat het FXT-besturings systeem alle gegevens op het opslag volume in de Cloud volledig kan beheren. U kunt de bestaande gegevens naar de Cloud container verplaatsen nadat u de container als een kern bestand aan het cluster hebt toegevoegd.

Gebruik het configuratie scherm om een kern bestand aan uw systeem toe te voegen.

> [!NOTE]
> 
> Als u Amazon AWS of Google Cloud Storage wilt gebruiken, moet u een FlashCloud<sup>TM</sup> -functie licentie installeren. Neem contact op met uw micro soft-vertegenwoordiger voor een licentie code en volg de instructies in de verouderde configuratie handleiding voor het [toevoegen of verwijderen van functie licenties](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Ondersteuning voor Azure Blob Storage is opgenomen in de Azure FXT Edge-software licentie. 

Lees deze secties van de cluster configuratie handleiding voor meer informatie over het toevoegen van basis bestanden:

* Lees voor meer informatie over het kiezen en voorbereiden van het toevoegen van een kern bestand het [werken met kern bestanden](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Lees de volgende artikelen voor gedetailleerde vereisten en stapsgewijze instructies:

  * [Een nieuwe NAS-kern bestand toevoegen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Een nieuwe Cloud core-Bestandsr toevoegen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Nadat u een kern bestand hebt toegevoegd, kunt u de instellingen ervan bijwerken op de pagina met details van de core-bestands extensie.

## <a name="add-a-core-filer"></a>Een kern bestand toevoegen

Definieer een kern bestand door te klikken op de knop **maken** op de pagina **kern bestand** > instellingen voor **kern bestanden beheren** .

![Klik op de knop maken boven de lijst met kern bestanden op de pagina kern bestanden beheren](media/fxt-cluster-config/create-core-filer-button.png)

De wizard **nieuwe Core-bestand toevoegen** leidt u door het proces van het maken van een kern bestand dat is gekoppeld aan uw back-end-opslag. De cluster configuratie handleiding bevat stapsgewijze beschrijvingen van het proces, dat verschilt van NFS-en NAS-opslag en voor Cloud opslag (koppelingen hierboven). 

Subtaken zijn onder andere:

* Geef het type kern bestand (NAS of Cloud) op

  ![Eerste pagina van de hardware NAS-wizard nieuwe Core-bestand. De optie ' Cloud core-bestand ' is uitgeschakeld en er wordt een fout bericht over de ontbrekende licentie weer gegeven.](media/fxt-cluster-config/new-nas-1.png)

* Stel de naam van de kern bestand in. Kies een naam die de cluster beheerders helpt te begrijpen welk opslag systeem het vertegenwoordigt.

* Geef voor NAS-kern bestanden de Fully Qualified Domain Name (FQDN) of het IP-adres op. FQDN wordt aanbevolen voor alle kern bestanden en is vereist voor SMB-toegang.

* Een cache beleid selecteren: de tweede pagina van de wizard bevat een lijst met de beschik bare cache beleidsregels voor de nieuwe kern bestand. Lees het [gedeelte cache beleid van de cluster configuratie handleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)voor meer informatie. 

  ![Tweede pagina van een hardware NAS-wizard nieuwe Core-bestand. de vervolg keuzelijst cache beleid is geopend, met verschillende uitgeschakelde opties en drie geldige cache beleids opties (bypass, lees cache en lezen/schrijven in cache).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Voor Cloud opslag moet u de Cloud service en toegangs referenties opgeven, onder andere para meters. Lees de [Cloud service en het protocol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) in de cluster configuratie handleiding voor meer informatie.

  ![Informatie over Cloud core-bestanden in de wizard nieuwe kern bestand](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Als u de Cloud toegangs referenties voor dit cluster al hebt toegevoegd, worden ze weer gegeven in de lijst. Referenties bijwerken en toevoegen op de pagina **Cluster** > instellingen voor **Cloud referenties** . 

Nadat u alle vereiste instellingen in de wizard hebt ingevuld, klikt u op de knop **bestand toevoegen** om de wijziging in te dienen.

Na enkele ogen blikken wordt het opslag systeem weer gegeven in de lijst met kern bestanden van het **dash board** en is toegankelijk via de pagina instellingen van de kern bestand.

![kern bestand ' Flurry-NAS ' op de pagina instellingen voor kern bestanden beheren, met de detail weergave van de bestands extensie uitgevouwen](media/fxt-cluster-config/core-filer-in-manage-page.png)

Er ontbreekt een vserver voor de kern bestand in deze scherm opname. U moet de kern bestand koppelen aan een vserver en een verbinding maken zodat clients toegang hebben tot de opslag. Deze stappen worden hieronder beschreven in [de naam ruimte configureren](#configure-the-namespace).

## <a name="configure-the-namespace"></a>De naam ruimte configureren

Het Azure FXT Edge-bestands cluster maakt een virtueel bestands systeem met de naam van de *cluster naam ruimte* die de client toegang vereenvoudigt tot gegevens die zijn opgeslagen op verschillende back-endsystemen. Omdat clients bestanden aanvragen via een virtueel pad, kunnen opslag systemen worden toegevoegd of vervangen zonder dat de werk stroom van de client hoeft te worden gewijzigd. 

Met de cluster naam ruimte kunt u ook Cloud-en NAS-opslag systemen in een vergelijk bare bestands structuur presen teren. 

De vservers van het cluster behouden de naam ruimte en leveren inhoud aan clients. Er zijn twee stappen voor het maken van de cluster naam ruimte: 

1. Een vserver maken 
1. Tussen de back-end-opslag systemen en de client gerichte bestandssysteem paden instellen 

### <a name="create-a-vserver"></a>Een vserver maken

VServers zijn virtuele bestands servers die bepalen hoe gegevens stromen tussen de client en de kern bestanden van het cluster:

* IP-adressen van VServers host-client
* VServers maakt u de naam ruimte en definieert u koppelingen die de client gerichte virtuele mappen structuur toewijzen aan de export op back-end-opslag
* VServers dwingen bestands toegangs controles uit te voeren, waaronder kern bestanden exporteren beleid en gebruikers verificatie systemen
* VServers bieden SMB-infra structuur

Lees voordat u begint met het configureren van een cluster vserver de gekoppelde documentatie en neem contact op met uw micro soft-vertegenwoordiger voor meer informatie over naam ruimte en vservers. Als u VLAN'S gebruikt, [maakt u deze](fxt-configure-network.md#adjust-network-settings) voordat u de vserver maakt. 

In deze secties van de cluster configuratie handleiding vindt u meer informatie over de functies FXT vserver en Global Namespace:

* [VServers maken en gebruiken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Een globale naam ruimte gebruiken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Een VServer maken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

U hebt ten minste één vserver voor uw cluster nodig. 

Als u een nieuwe vserver wilt maken, hebt u de volgende informatie nodig:

* De naam die moet worden ingesteld voor de vserver

* Het bereik van client gerichte IP-adressen die door de vserver worden verwerkt

  U moet één bereik van aaneengesloten IP-adressen opgeven wanneer u de vserver maakt. U kunt later meer adressen toevoegen via de pagina **netwerk** instellingen van de client.

* Als uw netwerk VLAN'S heeft, welk VLAN moet worden gebruikt voor deze vserver

Gebruik de pagina **VServer** > VServers-instellingen **beheren** om een nieuwe vserver te maken. Lees voor meer informatie [een vserver maken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) in de cluster configuratie handleiding. 

![pop-upvenster voor het maken van een nieuwe vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Een verbinding maken

Een *koppeling* wijst een back-end-opslagpad toe aan de naam ruimte die zichtbaar is voor de client.

U kunt dit systeem gebruiken voor het vereenvoudigen van het pad dat wordt gebruikt in client koppel punten en om de capaciteit naadloos te schalen, omdat één virtueel pad opslag van meerdere kern bestanden kan bevatten.

![Pagina Wizard nieuwe verbinding toevoegen met instellingen ingevuld](media/fxt-cluster-config/add-junction-full.png)

Raadpleeg [ **vserver** > **naam ruimte** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) in de cluster configuratie handleiding voor meer informatie over het maken van een naam ruimte koppeling.

![De pagina VServer > naam ruimte-instellingen geeft Details voor een verbinding weer](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Export regels configureren

Wanneer u zowel een vserver als een kern bestand hebt, moet u de export regels aanpassen en beleids regels exporteren die bepalen hoe clients toegang hebben tot bestanden op de kern bestands export.

Gebruik eerst de pagina **VServer** > **export regels** om nieuwe regels toe te voegen, het standaard beleid te wijzigen of uw eigen aangepaste export beleid te maken.

Gebruik vervolgens de pagina **VServer** > - **beleid exporteren** om het aangepaste beleid toe te passen op de export van uw kern bestand wanneer dit wordt geopend via die vserver.

Raadpleeg het artikel over de cluster configuratie handleiding voor meer informatie over het beheren van de [toegang tot de kern bestands export](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) .


## <a name="next-steps"></a>Volgende stappen

Nadat u opslag hebt toegevoegd en de client gerichte naam ruimte hebt geconfigureerd, voltooit u de eerste installatie van het cluster: 

> [!div class="nextstepaction"]
> [De netwerkinstellingen van het cluster configureren](fxt-configure-network.md)
