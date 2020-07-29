---
title: Voor beeld van een Azure-infrastructuur scenario
description: Meer informatie over de belangrijkste richt lijnen voor het ontwerpen en implementeren van een voorbeeld infrastructuur in Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 500de3f89bd041adf0b73e21762495d8c89e19c8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286288"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Voor beeld van een Azure-infrastructuur scenario voor Linux-Vm's
In dit artikel wordt uitgelegd hoe u een voorbeeld toepassings infrastructuur bouwt. We beschrijven een infra structuur voor een eenvoudige on line Store waarin alle richt lijnen en beslissingen over naam conventies, beschikbaarheids sets, virtuele netwerken en load balancers worden gecombineerd en de virtuele machines (Vm's) daad werkelijk worden geïmplementeerd.

## <a name="example-workload"></a>Voorbeeld workload
Adventure Works cycli wil een on line Store-toepassing bouwen in azure die bestaat uit:

* Twee nginx-servers waarop de client front-end wordt uitgevoerd in een weblaag
* Twee nginx-servers die gegevens en orders in een toepassings tier verwerken
* Twee MongoDB-servers maken deel uit van een Shard-cluster voor het opslaan van product gegevens en orders in een database laag
* Twee Active Directory domein controllers voor klant accounts en leveranciers in een verificatie-laag
* Alle servers bevinden zich in twee subnetten:
  * een front-end-subnet voor de webservers 
  * een back-end-subnet voor de toepassings servers, het MongoDB-cluster en de domein controllers

![Diagram van verschillende lagen voor toepassings infrastructuur](./media/infrastructure-example/example-tiers.png)

Inkomende beveiligde webverkeer moet worden verdeeld over de webservers als klanten door de online winkel bladeren. Het verwerken van verkeer in de vorm van HTTP-aanvragen van de webservers moet gelijkmatig zijn verdeeld over de toepassings servers. Daarnaast moet de infra structuur worden ontworpen voor hoge Beschik baarheid.

Het ontwerp moet het volgende omvatten:

* Een Azure-abonnement en-account
* Één resource groep
* Azure Managed Disks
* Een virtueel netwerk met twee subnetten
* Beschikbaarheids sets voor de virtuele machines met een vergelijk bare rol
* Virtuele machines

Alle bovenstaande instructies volgen deze naamgevings regels:

* Adventure Works Cycles maakt gebruik van **[it-workload]-[locatie]-[Azure resource]** als voor voegsel
  * Voor dit voor beeld is '**azos**' (Azure on-line Store) de naam van de IT-workload en '**use**' (VS-Oost 2) is de locatie
* Virtuele netwerken gebruiken AZOS-VN<strong>[number]</strong>
* Beschikbaarheids sets gebruiken azos-as-**[Role]**
* Namen van virtuele machines gebruik azos-VM-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-abonnementen en-accounts
Adventure Works Cycles maakt gebruik van het Enter prise-abonnement met de naam Adventure Works Enter prise, om facturering voor deze IT-workload te bieden.

## <a name="storage"></a>Storage
Adventure Works Cycles hebben bepaald dat ze Azure Managed Disks moeten gebruiken. Bij het maken van virtuele machines worden beide opslag lagen beschikbaar gemaakt:

* **Standaard opslag** voor de webservers, toepassings servers en domein controllers en hun gegevens schijven.
* **Premium-opslag** voor de MongoDb Shard-cluster servers en de bijbehorende gegevens schijven.

## <a name="virtual-network-and-subnets"></a>Virtueel netwerk en subnetten
Omdat het virtuele netwerk niet de actieve connectiviteit met het on-premises netwerk van Adventure work nodig heeft, hebben ze besloten op een virtueel netwerk in de Cloud.

Ze hebben een virtueel netwerk in de Cloud gemaakt met de volgende instellingen met behulp van de Azure Portal:

* Naam: AZOS-USE-VN01
* Locatie: VS-Oost 2
* Adres ruimte van virtueel netwerk: 10.0.0.0/8
* Eerste subnet:
  * Naam: front-end
  * Adres ruimte: 10.0.1.0/24
* Tweede subnet:
  * Naam: back-end
  * Adres ruimte: 10.0.2.0/24

## <a name="availability-sets"></a>Beschikbaarheidssets
Voor het behoud van de hoge Beschik baarheid van alle vier de lagen van de on line Store, hebben Adventure Works-cycli besloten voor vier beschikbaarheids sets:

* **azos-as-Web** voor de webservers
* **azos-use-as-app** voor de toepassings servers
* **azos-use-as-DB** voor de servers in het MongoDb Shard-cluster
* **azos-as-DC** voor de domein controllers

## <a name="virtual-machines"></a>Virtuele machines
Adventure Works Cycles heeft besloten over de volgende namen voor hun Azure-Vm's:

* **azos-use-VM-web01** voor de eerste webserver
* **azos-use-VM-web02** voor de tweede webserver
* **azos-use-VM-app01** voor de eerste toepassings server
* **azos-use-VM-app02** voor de tweede toepassings server
* **azos-use-VM-db01** voor de eerste MongoDb-server in het cluster
* **azos-use-VM-db02** voor de tweede MongoDb-server in het cluster
* **azos-use-VM-DC01** voor de eerste domein controller
* **azos-use-VM-dc02** voor de tweede domein controller

Dit is de resulterende configuratie.

![Definitieve toepassings infrastructuur geïmplementeerd in azure](./media/infrastructure-example/example-config.png)

Deze configuratie is in het algemeen:

* Een virtueel netwerk in de Cloud met twee subnetten (front-end en back-end)
* Azure Managed Disks het gebruik van standaard-en Premium-schijven
* Vier beschikbaarheids sets, één voor elke laag van de online winkel
* De virtuele machines voor de vier lagen
* Een externe set met gelijke taak verdeling voor op HTTPS gebaseerde webverkeer van Internet naar de webservers
* Een set met interne gelijke taak verdeling voor niet-versleuteld webverkeer van de webservers naar de toepassings servers
* Één resource groep
