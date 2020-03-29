---
title: Voorbeeld Azure-infrastructuur-walkthrough
description: Meer informatie over de richtlijnen voor het ontwerp en de implementatie voor het implementeren van een voorbeeldinfrastructuur in Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6040bf8862131f5a8a5564cd2f5d845fa0490a95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944905"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Voorbeeld Azure-infrastructuurwalkthrough voor Linux VM's
Dit artikel loopt door het bouwen van een voorbeeld applicatie-infrastructuur. We detailontwerpen van een infrastructuur voor een eenvoudige on-line winkel die alle richtlijnen en beslissingen rond het benoemen van conventies, beschikbaarheidsets, virtuele netwerken en load balancers samenbrengt en uw virtuele machines (VM's) daadwerkelijk implementeert.

## <a name="example-workload"></a>Voorbeeldwerkbelasting
Adventure Works Cycles wil een on-line store-toepassing bouwen in Azure die bestaat uit:

* Twee nginx-servers waarop de client front-end in een weblaag wordt uitgevoerd
* Twee nginx-servers die gegevens en orders in een toepassingslaag verwerken
* Twee MongoDB-servers maken deel uit van een geshard cluster voor het opslaan van productgegevens en -orders in een databaselaag
* Twee Active Directory-domeincontrollers voor klantaccounts en leveranciers in een verificatielaag
* Alle servers bevinden zich in twee subnetten:
  * een front-end subnet voor de webservers 
  * een back-end subnet voor de toepassingsservers, het MongoDB-cluster en domeincontrollers

![Diagram van verschillende lagen voor toepassingsinfrastructuur](./media/infrastructure-example/example-tiers.png)

Binnenkomend veilig webverkeer moet load-balanced zijn tussen de webservers als klanten door de on-line winkel bladeren. Orderverwerkingsverkeer in de vorm van HTTP-aanvragen van de webservers moet load-balanced zijn tussen de toepassingsservers. Daarnaast moet de infrastructuur worden ontworpen voor hoge beschikbaarheid.

Het resulterende ontwerp moet voorzien zijn van:

* Een Azure-abonnement en -account
* Eén resourcegroep
* Azure Managed Disks
* Een virtueel netwerk met twee subnetten
* Beschikbaarheidssets voor de VM's met een vergelijkbare rol
* Virtuele machines

Al het bovenstaande volgt deze naamgevingsconventies:

* Adventure Works Cycles gebruikt **[IT-workload]-[locatie]-[Azure-bron]** als voorvoegsel
  * In dit voorbeeld is "**azos**" (Azure On-line Store) de naam van de IT-werkbelasting en "**gebruik**" (Oost-VS 2) is de locatie
* Virtuele netwerken gebruiken AZOS-USE-VN<strong>[nummer]</strong>
* Beschikbaarheidssets maken gebruik van azos-gebruik-als-**[rol]**
* Virtuele machinenamen gebruiken azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-abonnementen en -accounts
Adventure Works Cycles gebruikt hun Enterprise-abonnement, genaamd Adventure Works Enterprise Subscription, om facturering te bieden voor deze IT-workload.

## <a name="storage"></a>Storage
Adventure Works Cycles bepaald dat ze Azure Managed Disks moeten gebruiken. Bij het maken van VM's worden beide opslaglagen voor opslag gebruikt:

* **Standaardopslag** voor de webservers, toepassingsservers en domeincontrollers en hun gegevensschijven.
* **Premium opslag** voor de MongoDB geshard clusterservers en hun dataschijven.

## <a name="virtual-network-and-subnets"></a>Virtueel netwerk en subnetten
Omdat het virtuele netwerk geen permanente connectiviteit met het on-premises netwerk van Adventure Work Cycles nodig heeft, hebben ze gekozen voor een virtueel netwerk dat alleen in de cloud is.

Ze hebben een virtueel netwerk gemaakt met alleen de volgende instellingen met behulp van de Azure-portal:

* Naam: AZOS-USE-VN01
* Locatie: East US 2
* Virtuele netwerkadresruimte: 10.0.0.0/8
* Eerste subnet:
  * Naam: FrontEnd
  * Adresruimte: 10.0.1.0/24
* Tweede subnet:
  * Naam: BackEnd
  * Adresruimte: 10.0.2.0/24

## <a name="availability-sets"></a>Beschikbaarheidssets
Om de hoge beschikbaarheid van alle vier de lagen van hun online winkel te behouden, heeft Adventure Works Cycles gekozen voor vier beschikbaarheidssets:

* **azos-use-as-web** voor de webservers
* **azos-gebruik-als-app** voor de toepassingsservers
* **azos-use-as-db** voor de servers in het MongoDB sharded cluster
* **azos-use-as-dc** voor de domeincontrollers

## <a name="virtual-machines"></a>Virtuele machines
Adventure Works Cycles heeft de volgende namen voor hun Azure VM's gekozen:

* **azos-use-vm-web01** voor de eerste webserver
* **azos-use-vm-web02** voor de tweede webserver
* **azos-use-vm-app01** voor de eerste toepassingsserver
* **azos-use-vm-app02** voor de tweede toepassingsserver
* **azos-use-vm-db01** voor de eerste MongoDB-server in het cluster
* **azos-use-vm-db02** voor de tweede MongoDB-server in het cluster
* **azos-use-vm-dc01** voor de eerste domeincontroller
* **azos-use-vm-dc02** voor de tweede domeincontroller

Hier is de resulterende configuratie.

![Definitieve toepassingsinfrastructuur geïmplementeerd in Azure](./media/infrastructure-example/example-config.png)

Deze configuratie bevat:

* Een virtueel cloudnetwerk met twee subnetten (FrontEnd en BackEnd)
* Azure Managed Disks met zowel standaard- als Premium-schijven
* Vier beschikbaarheidssets, één voor elke laag van de online winkel
* De virtuele machines voor de vier lagen
* Een externe load balanced set voor HTTPS-gebaseerd webverkeer van internet naar de webservers
* Een interne load balanced set voor onversleuteld webverkeer van de webservers naar de toepassingsservers
* Eén resourcegroep
