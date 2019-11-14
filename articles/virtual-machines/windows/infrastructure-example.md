---
title: Voor beeld van een Azure-infrastructuur scenario
description: Meer informatie over de belangrijkste richt lijnen voor het ontwerpen en implementeren van een voorbeeld infrastructuur in Azure.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038570"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Voor beeld van een Azure-infrastructuur scenario voor Windows-Vm's
In dit artikel wordt uitgelegd hoe u een voorbeeld toepassings infrastructuur bouwt. We beschrijven een infra structuur voor een eenvoudige online winkel met alle richt lijnen en beslissingen rond naam conventies, beschikbaarheids sets, virtuele netwerken en load balancers, en daad werkelijk de implementatie van uw virtuele machines (Vm's).

## <a name="example-workload"></a>Voorbeeld workload
Adventure Works Cycles willen een Online Store-toepassing bouwen in azure die bestaat uit:

* Twee IIS-servers waarop de client front-end in een weblaag wordt uitgevoerd
* Twee IIS-servers die gegevens en orders in een toepassingslaag verwerken
* Twee Microsoft SQL Server exemplaren met AlwaysOn-beschikbaarheids groepen (twee SQL-servers en een hoofd knooppunt-Witness) voor het opslaan van product gegevens en-orders in een database laag
* Twee Active Directory domein controllers voor klant accounts en leveranciers in een verificatie-laag
* Alle servers bevinden zich in twee subnetten:
  * een front-end-subnet voor de webservers 
  * een back-end-subnet voor de toepassings servers, het SQL-cluster en de domein controllers

![Diagram van verschillende lagen voor toepassings infrastructuur](./media/infrastructure-example/example-tiers.png)

Het binnenkomende beveiligde webverkeer moet gelijkmatig worden verdeeld over de webservers als klanten door de online winkel bladeren. Verkeer voor order verwerking in de vorm van HTTP-aanvragen van de webservers moet in evenwicht zijn tussen de toepassings servers. Daarnaast moet de infra structuur worden ontworpen voor hoge Beschik baarheid.

Het ontwerp moet het volgende omvatten:

* Een Azure-abonnement en-account
* Één resource groep
* Azure Managed Disks
* Een virtueel netwerk met twee subnetten
* Beschikbaarheids sets voor de virtuele machines met een vergelijk bare rol
* Virtuele machines

Alle bovenstaande instructies volgen deze naamgevings regels:

* Adventure Works Cycles maakt gebruik van **[it-workload]-[locatie]-[Azure resource]** als voor voegsel
  * Voor dit voor beeld is '**azos**' (Azure Online Store) de naam van de IT-workload en '**use**' (VS-Oost 2) is de locatie
* Virtuele netwerken gebruiken AZOS-VN **[number]**
* Beschikbaarheids sets gebruiken azos-as- **[Role]**
* Namen van virtuele machines gebruik azos-VM- **[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-abonnementen en-accounts
Adventure Works Cycles maakt gebruik van het Enter prise-abonnement met de naam Adventure Works Enter prise, om facturering voor deze IT-workload te bieden.

## <a name="storage"></a>Storage
Adventure Works Cycles hebben bepaald dat ze Azure Managed Disks moeten gebruiken. Bij het maken van Vm's worden beide beschik bare opslag lagen gebruikt:

* **Standaard opslag** voor de webservers, toepassings servers en domein controllers en hun gegevens schijven.
* **Premium-opslag** voor de SQL Server vm's en hun gegevens schijven.

## <a name="virtual-network-and-subnets"></a>Virtueel netwerk en subnetten
Omdat het virtuele netwerk niet de actieve connectiviteit met het on-premises netwerk van Adventure work nodig heeft, hebben ze besloten op een virtueel netwerk in de Cloud.

Ze hebben een virtueel netwerk in de Cloud gemaakt met de volgende instellingen met behulp van de Azure Portal:

* Name: AZOS-USE-VN01
* Locatie: VS-Oost 2
* Adres ruimte van virtueel netwerk: 10.0.0.0/8
* Eerste subnet:
  * Naam: front-end
  * Adres ruimte: 10.0.1.0/24
* Tweede subnet:
  * Naam: back-end
  * Adres ruimte: 10.0.2.0/24

## <a name="availability-sets"></a>Beschikbaarheidssets
Voor een hoge Beschik baarheid van alle vier de lagen van de online winkel hebben Adventure Works-cycli besloten voor vier beschikbaarheids sets:

* **azos-as-Web** voor de webservers
* **azos-use-as-app** voor de toepassings servers
* **azos-as-SQL-gebruik** voor de SQL-servers
* **azos-as-DC** voor de domein controllers

## <a name="virtual-machines"></a>Virtuele machines
Adventure Works Cycles heeft besloten over de volgende namen voor hun Azure-Vm's:

* **azos-use-VM-web01** voor de eerste webserver
* **azos-use-VM-web02** voor de tweede webserver
* **azos-use-VM-app01** voor de eerste toepassings server
* **azos-use-VM-app02** voor de tweede toepassings server
* **azos-use-VM-sql01** voor de eerste SQL Server server in het cluster
* **azos-use-VM-sql02** voor de tweede SQL Server server in het cluster
* **azos-use-VM-DC01** voor de eerste domein controller
* **azos-use-VM-dc02** voor de tweede domein controller

Dit is de resulterende configuratie.

![Definitieve toepassings infrastructuur geïmplementeerd in azure](./media/infrastructure-example/example-config.png)

Deze configuratie is in het algemeen:

* Een virtueel netwerk in de Cloud met twee subnetten (front-end en back-end)
* Azure Managed Disks met standaard-en Premium-schijven
* Vier beschikbaarheids sets, één voor elke laag van de online winkel
* De virtuele machines voor de vier lagen
* Een externe set met gelijke taak verdeling voor op HTTPS gebaseerde webverkeer van Internet naar de webservers
* Een set met interne gelijke taak verdeling voor niet-versleuteld webverkeer van de webservers naar de toepassings servers
* Één resource groep
