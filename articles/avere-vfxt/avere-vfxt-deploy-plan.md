---
title: Uw avere vFXT-systeem plannen-Azure
description: Geeft een uitleg over het plannen van voordat avere vFXT voor Azure wordt geïmplementeerd
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256232"
---
# <a name="plan-your-avere-vfxt-system"></a>Uw Avere vFXT-systeem plannen

In dit artikel wordt uitgelegd hoe u een nieuw avere vFXT voor Azure-cluster plant dat op de juiste wijze is gepositioneerd en aangepast aan uw behoeften. 

Voordat u naar de Azure Marketplace gaat of een virtuele machine maakt, moet u nadenken hoe het cluster met andere elementen in Azure zal werken. Plan waar cluster bronnen zich bevinden in uw particuliere netwerk en subnetten, en bepaal waar uw back-end-opslag zal worden uitgevoerd. Zorg ervoor dat de cluster knooppunten die u maakt krachtig genoeg zijn voor de ondersteuning van uw werk stroom. 

Lees verder voor meer informatie.

## <a name="resource-group-and-network-infrastructure"></a>Resource groep en netwerk infrastructuur

Denk na over waar de elementen van uw avere vFXT voor Azure-implementatie zullen worden. In het onderstaande diagram ziet u een mogelijke schema voor de avere vFXT voor Azure-onderdelen:

![Diagram waarin de cluster controller en de cluster-Vm's binnen één subnet worden weer gegeven. Rond de grens van het subnet bevindt zich een vnet-grens. Binnen het vnet is dit een zeshoek die het service-eind punt van de opslag aangeeft. deze is verbonden met een onderbroken pijl naar een Blob-opslag buiten het vnet.](media/avere-vfxt-components-option.png)

Volg deze richt lijnen bij het plannen van de netwerk infrastructuur van uw avere vFXT-systeem:

* Alle elementen moeten worden beheerd met een nieuw abonnement dat is gemaakt voor de avere vFXT-implementatie. De voordelen zijn: 
  * Eenvoudiger kosten bijhouden: Bekijk en controleer alle kosten van resources, infra structuur en reken cycli in één abonnement.
  * Eenvoudiger opschonen: u kunt het hele abonnement verwijderen wanneer u klaar bent met het project.
  * Handig partitioneren van resource quota: Beveilig andere kritieke werk belastingen van mogelijke resource beperking door de avere vFXT-clients en het cluster in één abonnement te isoleren. Dit voor komt een conflict wanneer er een groot aantal clients wordt gemaakt voor een werk stroom met hoge prestaties.

* Zoek uw client Compute-systemen dicht bij het vFXT-cluster. Back-end-opslag kan extern zijn.  

* Het vFXT-cluster en de virtuele machine van de cluster controller moeten zich in hetzelfde virtuele netwerk (vnet) bevinden, in dezelfde resource groep en hetzelfde opslag account gebruiken. De sjabloon voor het automatisch maken van het cluster zorgt voor de meeste situaties.

* Het cluster moet zich in een eigen subnet bevinden om IP-adres conflicten met clients of reken bronnen te voor komen. 

* De sjabloon voor het maken van het cluster kan de meeste benodigde infrastructuur resources voor het cluster maken, met inbegrip van resource groepen, virtuele netwerken, subnetten en opslag accounts. Als u resources wilt gebruiken die al bestaan, moet u ervoor zorgen dat deze voldoen aan de vereisten in deze tabel. 

  | Bron | Bestaande gebruiken? | Vereisten |
  |----------|-----------|----------|
  | Resourcegroep | Ja, indien leeg | Moet leeg zijn| 
  | Opslagaccount | Ja als er verbinding wordt gemaakt met een bestaande BLOB-container na het maken van het cluster <br/>  Nee, als u een nieuwe BLOB-container maakt tijdens het maken van het cluster | Bestaande BLOB-container moet leeg zijn <br/> &nbsp; |
  | Virtueel netwerk | Ja | Moet een service-eind punt voor opslag bevatten als er een nieuwe Azure Blob-container wordt gemaakt | 
  | Subnet | Ja |   |

## <a name="ip-address-requirements"></a>Vereisten voor IP-adressen 

Zorg ervoor dat het subnet van het cluster een groot voldoende IP-adres bereik heeft voor de ondersteuning van het cluster. 

Het avere vFXT-cluster maakt gebruik van de volgende IP-adressen:

* Eén IP-adres voor cluster beheer. Dit adres kan van het ene naar het andere knoop punt in het cluster worden verplaatst, maar is altijd beschikbaar, zodat u verbinding kunt maken met het configuratie hulpprogramma voor avere configuratie scherm.
* Voor elk cluster knooppunt:
  * Ten minste één client gerichte IP-adres. (Alle client adressen worden beheerd door de *vserver*van het cluster, waardoor ze naar behoefte kunnen worden verplaatst tussen knoop punten.)
  * Eén IP-adres voor cluster communicatie
  * Een IP-adres van een exemplaar (toegewezen aan de virtuele machine)

Als u Azure Blob Storage gebruikt, kan het ook IP-adressen van het vnet van uw cluster vereisen:  

* Een Azure Blob-opslag account vereist ten minste vijf IP-adressen. Houd deze vereiste in acht als u Blob Storage vindt in hetzelfde vnet als uw cluster.
* Als u Azure Blob Storage gebruikt die zich buiten het virtuele netwerk van uw cluster bevindt, moet u een opslag service-eind punt in het vnet maken. Dit eind punt maakt geen gebruik van een IP-adres.

U hebt de mogelijkheid om netwerk bronnen en Blob-opslag (indien gebruikt) in verschillende resource groepen van het cluster te vinden.

## <a name="vfxt-node-size"></a>grootte van vFXT-knoop punt

De virtuele machines die fungeren als cluster knooppunten bepalen de door Voer van de aanvraag en de opslag capaciteit van uw cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Elk vFXT-knoop punt is identiek. Dat wil zeggen, als u een cluster met drie knoop punten maakt, u drie Vm's hebt van hetzelfde type en dezelfde grootte. 

| Type instantie | Vcpu's | Geheugen  | Lokale SSD-opslag  | Maximaal aantal gegevensschijven | Niet in cache opgeslagen schijf doorvoer | NIC (aantal) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

Schijf cache per knoop punt kan worden geconfigureerd en kan rage van 1000 GB tot 8000 GB. 4 TB per knoop punt is de aanbevolen cache grootte voor Standard_E32s_v3-knoop punten.

Lees de Microsoft Azure-documentatie voor meer informatie over deze Vm's:

* [Grootte van virtuele machines geoptimaliseerd voor geheugen](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Account quotum

Zorg ervoor dat uw abonnement de capaciteit heeft om het avere vFXT-cluster uit te voeren, evenals alle computers of client systemen die worden gebruikt. Lees [quotum voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) voor meer informatie.

## <a name="back-end-data-storage"></a>Back-end-gegevens opslag

Waar moet het avere vFXT-cluster uw gegevens opslaan wanneer deze zich niet in de cache bevinden? Bepaal of uw werkset lange termijn wordt opgeslagen in een nieuwe BLOB-container of in een bestaand opslag systeem voor de Cloud of hardware. 

Als u Azure Blob Storage voor de back-end wilt gebruiken, moet u een nieuwe container maken als onderdeel van het maken van het vFXT-cluster. Met deze optie maakt en configureert u de nieuwe container, zodat deze gereed is om te worden gebruikt zodra het cluster gereed is. 

Lees [de avere-vFXT voor Azure maken](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) voor meer informatie.

> [!NOTE]
> Alleen lege Blob Storage-containers kunnen worden gebruikt als kern bestanden voor het avere vFXT-systeem. De vFXT moet de object opslag kunnen beheren zonder dat bestaande gegevens moeten worden bewaard. 
>
> Lees [gegevens verplaatsen naar het vFXT-cluster](avere-vfxt-data-ingest.md) voor meer informatie over het efficiënt kopiëren van gegevens naar de nieuwe container van het cluster met behulp van client computers en de avere vFXT-cache.

Als u een bestaand on-premises opslag systeem wilt gebruiken, moet u het toevoegen aan het vFXT-cluster nadat het is gemaakt. Lees [opslag configureren](avere-vfxt-add-storage.md) voor gedetailleerde instructies over het toevoegen van een bestaand opslag systeem aan het avere vFXT-cluster.

## <a name="cluster-access"></a>Cluster toegang 

Het avere vFXT voor Azure-cluster bevindt zich in een privé subnet en het cluster heeft geen openbaar IP-adres. U moet een van de volgende methoden gebruiken om toegang te krijgen tot het privé subnet voor cluster beheer en client verbindingen. 

De toegangs opties zijn onder andere:

* Jump host: wijs een openbaar IP-adres toe aan een afzonderlijke virtuele machine in het particuliere netwerk en gebruik het om een SSL-tunnel te maken voor de cluster knooppunten. 

  > [!TIP]
  > Als u een openbaar IP-adres op de cluster controller instelt, kunt u het gebruiken als de Jump host. Lees de [cluster controller als Jump host](#cluster-controller-as-jump-host) voor meer informatie.

* Virtueel particulier netwerk (VPN): Configureer een punt-naar-site-of site-naar-site-VPN naar uw particuliere netwerk.

* Azure-ExpressRoute: Configureer een particuliere verbinding via een ExpressRoute-partner. 

Lees de [documentatie van Azure Virtual Network over Internet communicatie](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)voor meer informatie over deze opties.

### <a name="cluster-controller-as-jump-host"></a>Cluster controller als Jump host

Als u een openbaar IP-adres op de cluster controller instelt, kunt u dit als een Jump host gebruiken om contact op te nemen met het avere vFXT-cluster van buiten het particuliere subnet. Omdat de controller echter toegangs rechten heeft om cluster knooppunten te wijzigen, maakt dit een klein beveiligings risico.  

Ter verbetering van de beveiliging van een controller met een openbaar IP-adres, wordt door het implementatie script automatisch een netwerk beveiligings groep gemaakt waarmee de toegang tot alleen poort 22 wordt beperkt. U kunt het systeem verder beveiligen door de toegang tot uw bereik van IP-bron adressen te vergren delen, dat wil zeggen, alleen verbindingen toestaan van computers die u wilt gebruiken voor toegang tot het cluster.

Wanneer u het cluster maakt, kunt u kiezen of u een openbaar IP-adres op de cluster controller wilt maken. 

* Als u een nieuw vnet of een nieuw subnet maakt, wordt aan de cluster controller een openbaar IP-adres toegewezen.
* Als u een bestaand vnet en subnet selecteert, heeft de cluster controller alleen particuliere IP-adressen. 

## <a name="vm-access-roles"></a>VM-toegangs rollen 

Azure gebruikt op [rollen gebaseerd toegangs beheer](../role-based-access-control/index.yml) (RBAC) om de cluster-vm's te autoriseren om bepaalde taken uit te voeren. Zo moet de cluster controller autorisatie hebben om de virtuele machines van het cluster knooppunt te maken en te configureren. De cluster knooppunten moeten IP-adressen toewijzen of opnieuw toewijzen aan andere cluster knooppunten.

Er worden twee ingebouwde Azure-rollen gebruikt voor de virtuele avere-machines van vFXT: 

* De cluster controller maakt gebruik van de ingebouwde rol [Inzender voor avere](../role-based-access-control/built-in-roles.md#avere-contributor). 
* Cluster knooppunten gebruiken de ingebouwde rol [avere operator](../role-based-access-control/built-in-roles.md#avere-operator)

Als u toegangs rollen voor avere vFXT-onderdelen wilt aanpassen, moet u uw eigen rol definiëren en deze vervolgens toewijzen aan de virtuele machines op het moment dat ze worden gemaakt. U kunt de implementatie sjabloon niet gebruiken in azure Marketplace. Neem contact op met de klanten service van micro soft door een ticket in de Azure Portal te openen, zoals wordt beschreven in [hulp vragen bij uw systeem](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Volgende stap: inzicht in het implementatie proces

[Implementatie overzicht](avere-vfxt-deploy-overview.md) geeft de grote afbeelding van alle stappen die nodig zijn voor het maken van een avere vFXT voor het Azure-systeem en het voorbereiden van de gegevens.  