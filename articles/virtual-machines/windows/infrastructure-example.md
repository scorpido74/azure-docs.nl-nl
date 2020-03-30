---
title: Voorbeeld Azure Infrastructure Walkthrough
description: Meer informatie over de richtlijnen voor het ontwerp en de implementatie voor het implementeren van een voorbeeldinfrastructuur in Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab6f304d78357e261c68ebbcfcb3746844edce8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038570"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Voorbeeld Azure-infrastructuur-walkthrough voor Windows VM's
Dit artikel loopt door het bouwen van een voorbeeld applicatie-infrastructuur. We detail ontwerpen van een infrastructuur voor een eenvoudige online winkel die alle richtlijnen en beslissingen rond het benoemen van conventies, beschikbaarheid sets, virtuele netwerken en load balancers, en daadwerkelijk de inzet van uw virtuele machines (VM's) samenbrengt.

## <a name="example-workload"></a>Voorbeeldwerkbelasting
Adventure Works Cycles wil een online winkeltoepassing bouwen in Azure die bestaat uit:

* Twee IIS-servers waarop de client front-end in een weblaag wordt uitgevoerd
* Twee IIS-servers die gegevens en orders in een toepassingslaag verwerken
* Twee Microsoft SQL Server-exemplaren met AlwaysOn-beschikbaarheidsgroepen (twee SQL-servers en een meerderheidsknooppuntgetuige) voor het opslaan van productgegevens en -orders in een databaselaag
* Twee Active Directory-domeincontrollers voor klantaccounts en leveranciers in een verificatielaag
* Alle servers bevinden zich in twee subnetten:
  * een front-end subnet voor de webservers 
  * een back-endsubnet voor de toepassingsservers, SQL-cluster en domeincontrollers

![Diagram van verschillende lagen voor toepassingsinfrastructuur](./media/infrastructure-example/example-tiers.png)

Binnenkomend veilig webverkeer moet load-balanced zijn tussen de webservers als klanten door de online winkel bladeren. Orderverwerkingsverkeer in de vorm van HTTP-aanvragen van de webservers moet worden gebalanceerd tussen de toepassingsservers. Daarnaast moet de infrastructuur worden ontworpen voor hoge beschikbaarheid.

Het resulterende ontwerp moet voorzien zijn van:

* Een Azure-abonnement en -account
* Eén resourcegroep
* Azure Managed Disks
* Een virtueel netwerk met twee subnetten
* Beschikbaarheidssets voor de VM's met een vergelijkbare rol
* Virtuele machines

Al het bovenstaande volgt deze naamgevingsconventies:

* Adventure Works Cycles gebruikt **[IT-workload]-[locatie]-[Azure-bron]** als voorvoegsel
  * In dit voorbeeld is "**azos**" (Azure Online Store) de naam van de IT-werkbelasting en "**gebruik**" (Oost-VS 2) is de locatie
* Virtuele netwerken gebruiken AZOS-USE-VN **[nummer]**
* Beschikbaarheidssets maken gebruik van azos-gebruik-als-**[rol]**
* Virtuele machinenamen gebruiken azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-abonnementen en -accounts
Adventure Works Cycles gebruikt hun Enterprise-abonnement, genaamd Adventure Works Enterprise Subscription, om facturering te bieden voor deze IT-workload.

## <a name="storage"></a>Storage
Adventure Works Cycles bepaald dat ze Azure Managed Disks moeten gebruiken. Bij het maken van VM's worden beide beschikbare opslaglagen gebruikt:

* **Standaardopslag** voor de webservers, toepassingsservers en domeincontrollers en hun gegevensschijven.
* **Premium opslag** voor de SQL Server VM's en hun gegevensschijven.

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
Om de hoge beschikbaarheid van alle vier de lagen van hun online winkel te behouden, heeft Adventure Works Cycles besloten tot vier beschikbaarheidssets:

* **azos-use-as-web** voor de webservers
* **azos-gebruik-als-app** voor de toepassingsservers
* **azos-use-as-sql** voor de SQL-servers
* **azos-use-as-dc** voor de domeincontrollers

## <a name="virtual-machines"></a>Virtuele machines
Adventure Works Cycles heeft de volgende namen voor hun Azure VM's gekozen:

* **azos-use-vm-web01** voor de eerste webserver
* **azos-use-vm-web02** voor de tweede webserver
* **azos-use-vm-app01** voor de eerste toepassingsserver
* **azos-use-vm-app02** voor de tweede toepassingsserver
* **azos-use-vm-sql01** voor de eerste SQL Server-server in het cluster
* **azos-use-vm-sql02** voor de tweede SQL Server-server in het cluster
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
