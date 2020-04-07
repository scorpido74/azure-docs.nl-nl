---
title: Uw Avere vFXT-systeem plannen - Azure
description: Legt de planning uit voordat u Avere vFXT voor Azure implementeert
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 6acc1ffd197ddba4290ff7c0751b259d98a70927
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754387"
---
# <a name="plan-your-avere-vfxt-system"></a>Uw Avere vFXT-systeem plannen

In dit artikel wordt uitgelegd hoe u een nieuw Avere vFXT-cluster voor Azure-cluster plannen dat op de juiste manier is gepositioneerd en zo groot is voor uw behoeften.

Voordat u naar de Azure Marketplace gaat of vm's maakt, moet u rekening houden met deze gegevens:

* Hoe werkt het cluster met andere Azure-bronnen?
* Waar moeten clusterelementen zich bevinden in particuliere netwerken en subnetten?
* Welk type back-endopslag gebruikt u en hoe krijgt het cluster er toegang toe?
* Hoe krachtig moeten uw clusterknooppunten zijn om uw werkstroom te ondersteunen?

Lees verder voor meer informatie.

## <a name="learn-the-components-of-the-system"></a>Leer de onderdelen van het systeem

Het kan handig zijn om de onderdelen van het Avere vFXT voor Azure-systeem te begrijpen wanneer u begint met plannen.

* Clusterknooppunten - Het cluster bestaat uit drie of meer VM's die zijn geconfigureerd als clusterknooppunten. Meer knooppunten geven het systeem een hogere doorvoer en een grotere cache.

* Cache - De cachecapaciteit is gelijk verdeeld over de clusterknooppunten. Stel de cachegrootte per knooppunt in wanneer u het cluster maakt. de nodegroottes worden toegevoegd om de totale cachegrootte te worden.

* Clustercontroller - De clustercontroller is een extra VM in hetzelfde subnet als de clusterknooppunten. De controller is nodig om het cluster te maken en voor lopende beheertaken.

* Back-endopslag : de gegevens die u in de cache wilt hebben, worden op lange termijn opgeslagen in een hardwareopslagsysteem of een Azure Blob-container. U opslag toevoegen nadat u het Avere vFXT voor Azure-cluster hebt gemaakt, of als u blob-opslag gebruikt, u de container toevoegen en configureren tijdens het maken van het cluster.

* Clients - Clientmachines die de bestanden in de cache gebruiken, maken verbinding met het cluster via een virtueel bestandspad in plaats van rechtstreeks toegang te krijgen tot de opslagsystemen. (Lees meer in [Mount the Avere vFXT cluster](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Abonnement, brongroep en netwerkinfrastructuur

Bedenk waar de elementen van uw Avere vFXT voor Azure-implementatie zullen zijn. Het onderstaande diagram toont een mogelijke indeling voor de Avere vFXT voor Azure-componenten:

![Diagram met de clustercontroller en cluster-VM's binnen één subnet. Rond de subnetgrens is een vnetgrens. In het vnet bevindt zich een zeshoek die het eindpunt van de opslagservice vertegenwoordigt; het is verbonden met een gestippelde pijl met een Blob-opslag buiten het vnet.](media/avere-vfxt-components-option.png)

Volg deze richtlijnen bij het plannen van de netwerkinfrastructuur van uw Avere vFXT-cluster:

* Maak een nieuw abonnement voor elke Avere vFXT voor Azure-implementatie. Beheer alle onderdelen in dit abonnement.

  Voordelen van het gebruik van een nieuw abonnement voor elke implementatie zijn:
  * Eenvoudigere kostentracking : bekijk en controleer alle kosten van resources, infrastructuur en compute-cycli in één abonnement.
  * Eenvoudiger opruimen - U het hele abonnement verwijderen wanneer u klaar bent met het project.
  * Handige verdeling van resourcequota - Isoleer de Avere vFXT-clients en -cluster in één abonnement om andere kritieke workloads te beschermen tegen mogelijke beperking van resources. Deze scheiding voorkomt conflicten bij het opvoeden van een groot aantal clients voor een krachtige computerworkflow.

* Zoek uw client compute systemen dicht bij het vFXT cluster. Back-end opslag kan meer op afstand.  

* Zoek het vFXT-cluster en de clustercontroller VM samen - specifiek moeten ze zijn:

  * In hetzelfde virtuele netwerk
  * In dezelfde resourcegroep
  * Hetzelfde opslagaccount gebruiken
  
  De sjabloon voor het maken van het cluster zorgt voor deze configuratie voor de meeste situaties.

* Het cluster moet zich in een eigen subnet bevinden om conflicten met IP-adres met clients of andere rekenbronnen te voorkomen.

* Gebruik de sjabloon voor het maken van het cluster om de meeste benodigde infrastructuurbronnen voor het cluster te maken, waaronder brongroepen, virtuele netwerken, subnetten en opslagaccounts.

  Als u reeds bestaande resources wilt gebruiken, moet u ervoor zorgen dat ze voldoen aan de vereisten in deze tabel.

  | Resource | Bestaande gebruiken? | Vereisten |
  |----------|-----------|----------|
  | Resourcegroep | Ja, als het leeg is | Moet leeg zijn|
  | Storage-account | **Ja** als u een bestaande Blob-container verbindt na het maken van het cluster <br/>  **Nee** als u een nieuwe Blob-container maakt tijdens het maken van het cluster | Bestaande Blob-container moet leeg zijn <br/> &nbsp; |
  | Virtueel netwerk | Ja | Moet een eindpunt voor de opslagservice bevatten als u een nieuwe Azure Blob-container maakt |
  | Subnet | Ja | Kan geen andere bronnen bevatten |

## <a name="ip-address-requirements"></a>IP-adresvereisten

Zorg ervoor dat het subnet van uw cluster een groot genoeg IP-adresbereik heeft om het cluster te ondersteunen.

Het Avere vFXT-cluster gebruikt de volgende IP-adressen:

* Eén IP-adres voor clusterbeheer. Dit adres kan zo nodig van knooppunt naar knooppunt in het cluster gaan, zodat het altijd beschikbaar is. Gebruik dit adres om verbinding te maken met het configuratieprogramma van het Configuratiescherm van Het Configuratiescherm van Avere.
* Voor elk clusterknooppunt:
  * Ten minste één IP-adres voor klanten. (Alle clientgerichte adressen worden beheerd door de *vserver*van het cluster, die de IP-adressen naar behoefte tussen knooppunten kan verplaatsen.)
  * Eén IP-adres voor clustercommunicatie
  * Eén exemplaar-IP-adres (toegewezen aan de vm)

Als u Azure Blob-opslag gebruikt, zijn mogelijk ook IP-adressen uit het virtuele netwerk van uw cluster vereist:  

* Voor een Azure Blob-opslagaccount zijn ten minste vijf IP-adressen vereist. Houd rekening met deze vereiste als u Blob-opslag in hetzelfde virtuele netwerk als uw cluster vindt.
* Als u Azure Blob-opslag gebruikt die zich buiten het virtuele netwerk van het cluster bevindt, maakt u een eindpunt van de opslagservice binnen het virtuele netwerk. Het eindpunt gebruikt geen IP-adres.

U hebt de optie om netwerkbronnen en Blob-opslag (indien gebruikt) te zoeken in verschillende brongroepen van het cluster.

## <a name="vfxt-node-size"></a>vFXT-knooppuntgrootte

De VM's die als clusterknooppunten dienen, bepalen de doorvoer- en opslagcapaciteit van uw cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Elk vFXT-knooppunt is identiek. Dat wil zeggen, als u een cluster met drie nodes maakt, hebt u drie VM's van hetzelfde type en dezelfde grootte.

| Instantietype | vCPUs | Geheugen  | Lokale SSD-opslag  | Max. aantal gegevensschijven | Schijfdoorvoer zonder cache | NIC (telling) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

Schijfcache per knooppunt is configureerbaar en kan van 1000 GB tot 8000 GB worden verwijderd. 4 TB per knooppunt is de aanbevolen cachegrootte voor Standard_E32s_v3 knooppunten.

Lees voor meer informatie over deze VM's de Microsoft Azure-documentatie: [Geheugengeoptimaliseerde virtuele machineformaten](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Rekeningsquotum

Zorg ervoor dat uw abonnement de capaciteit heeft om het Avere vFXT-cluster uit te voeren, evenals alle computer- of clientsystemen die worden gebruikt. Lees [Quota voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) voor meer informatie.

## <a name="back-end-data-storage"></a>Back-end gegevensopslag

Back-end opslagsystemen leveren bestanden zowel aan de cache van het cluster als ontvangen ook gewijzigde gegevens uit de cache. Bepaal of uw werkset op lange termijn wordt opgeslagen in een nieuwe Blob-container of in een bestaand opslagsysteem (cloud of hardware). Deze back-end opslagsystemen worden *core filers*genoemd.

### <a name="hardware-core-filers"></a>Hardwarecore-filers

Voeg hardwareopslagsystemen toe aan het vFXT-cluster nadat u het cluster hebt gemaakt. U verschillende populaire hardwaresystemen gebruiken, waaronder on-premises systemen, zolang het opslagsysteem kan worden bereikt via het subnet van het cluster.

Lees [Opslag configureren](avere-vfxt-add-storage.md) voor gedetailleerde instructies over het toevoegen van een bestaand opslagsysteem aan het Avere vFXT-cluster.

### <a name="cloud-core-filers"></a>Cloudcore-filers

Het Avere vFXT voor Azure-systeem kan lege Blob-containers gebruiken voor back-endopslag. Containers moeten leeg zijn wanneer ze aan het cluster worden toegevoegd - het vFXT-systeem moet zijn objectopslag kunnen beheren zonder bestaande gegevens te hoeven bewaren.

> [!TIP]
> Als u Azure Blob-opslag wilt gebruiken voor de back-end, maakt u een nieuwe container als onderdeel van het maken van het vFXT-cluster. De sjabloon voor het maken van het cluster kan een nieuwe Blob-container maken en configureren, zodat deze klaar is voor gebruik zodra het cluster beschikbaar is. Het toevoegen van een container later is ingewikkelder.
>
> Lees [De Avere vFXT voor Azure maken](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) voor meer informatie.

Nadat u de lege Blob-opslagcontainer als kernfiler hebt toegevoegd, u gegevens naar de opslagcontainer kopiëren via het cluster. Gebruik een parallel, multi-threaded kopieermechanisme. Lees [Gegevens verplaatsen naar het vFXT-cluster](avere-vfxt-data-ingest.md) om te leren hoe u gegevens efficiënt kopiëren naar de nieuwe container van het cluster met behulp van clientmachines en de Avere vFXT-cache.

## <a name="cluster-access"></a>Clustertoegang

Het Avere vFXT voor Azure-cluster bevindt zich in een privésubnet en het cluster heeft geen openbaar IP-adres. U moet een manier hebben om toegang te krijgen tot het privésubnet voor clusterbeheer en clientverbindingen.

Toegangsopties zijn onder andere:

* Jump host - Wijs een openbaar IP-adres toe aan een afzonderlijke VM binnen het privénetwerk en gebruik deze om een TLS-tunnel te maken aan de clusterknooppunten.

  > [!TIP]
  > Als u een openbaar IP-adres instelt op de clustercontroller, u het gebruiken als de jump host. Lees [clustercontroller als jump host](#cluster-controller-as-jump-host) voor meer informatie.

* Vpn (Virtual private network) - Configureer een point-to-site of site-to-site VPN tussen uw privénetwerk in Azure en bedrijfsnetwerken.

* Azure ExpressRoute - Configureer een privéverbinding via een ExpressRoute-partner.

Lees de documentatie van [het Azure Virtual Network over internetcommunicatie voor](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)meer informatie over deze opties.

### <a name="cluster-controller-as-jump-host"></a>Clustercontroller als jumphost

Als u een openbaar IP-adres instelt op de clustercontroller, u het gebruiken als een jumphost om contact op te nemen met het Avere vFXT-cluster van buiten het privésubnet. Omdat de controller echter toegangsrechten heeft om clusterknooppunten te wijzigen, ontstaat er een klein beveiligingsrisico.

Om de beveiliging van een controller met een openbaar IP-adres te verbeteren, maakt het implementatiescript automatisch een netwerkbeveiligingsgroep die de binnenkomende toegang tot poort 22 beperkt. U het systeem verder beschermen door de toegang tot uw bereik van IP-bronadressen te vergrendelen- dat wil zeggen, alleen verbindingen toestaan van machines die u wilt gebruiken voor clustertoegang.

Bij het maken van het cluster u kiezen of u een openbaar IP-adres op de clustercontroller wilt maken.

* Als u een **nieuw virtueel netwerk** of een nieuw **subnet maakt,** krijgt de clustercontroller een **openbaar** IP-adres toegewezen.
* Als u een bestaand virtueel netwerk en subnet **private** selecteert, heeft de clustercontroller alleen privé-IP-adressen.

## <a name="vm-access-roles"></a>VM-toegangsrollen

Azure gebruikt [rbac (role-based access control)](../role-based-access-control/index.yml) om de clusterVM's te autoriseren om bepaalde taken uit te voeren. De clustercontroller heeft bijvoorbeeld autorisatie nodig om de VM's van het clusterknooppunt te maken en te configureren. Clusterknooppunten moeten IP-adressen aan andere clusterknooppunten kunnen toewijzen of opnieuw toewijzen.

Twee ingebouwde Azure-rollen worden gebruikt voor de virtuele AverE-virtuele machines:

* De clustercontroller maakt gebruik van de ingebouwde rol [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor).
* Clusterknooppunten maken gebruik van de ingebouwde rol [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator).

Als u toegangsrollen voor Avere vFXT-componenten moet aanpassen, moet u uw eigen rol definiëren en deze vervolgens toewijzen aan de VM's op het moment dat ze worden gemaakt. U de implementatiesjabloon niet gebruiken in de Azure Marketplace. Raadpleeg de klantenservice en ondersteuning van Microsoft door een ticket te openen in de Azure-portal zoals beschreven in [Hulp bij uw systeem.](avere-vfxt-open-ticket.md)

## <a name="next-steps"></a>Volgende stappen

[Implementatieoverzicht](avere-vfxt-deploy-overview.md) geeft een overzicht van de grote afbeeldingen van de stappen die nodig zijn om een Avere vFXT voor Azure-systeem te maken en het klaar te maken om gegevens te serveren.
