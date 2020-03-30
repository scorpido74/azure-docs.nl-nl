---
title: Scenario's voor privézones - Azure DNS
description: Lees in dit artikel over veelvoorkomende scenario's voor het gebruik van Azure DNS Private Zones.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939346"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS-scenario's voor privézones

Azure DNS Private Zones bieden naamomzetting binnen een virtueel netwerk en tussen virtuele netwerken. In dit artikel bekijken we enkele veelvoorkomende scenario's die met deze functie kunnen worden gerealiseerd.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenario: Naamoplossing in één virtueel netwerk
In dit scenario hebt u een virtueel netwerk in Azure met een aantal Azure-resources, waaronder virtuele machines (VM's). U wilt de bronnen vanuit het virtuele netwerk oplossen via een specifieke domeinnaam (DNS-zone) en u hebt de naamomzetting nodig om privé te zijn en niet toegankelijk vanaf het internet. Bovendien hebt u azure voor de VM's binnen het VNET nodig om ze automatisch te registreren in de DNS-zone. 

Dit scenario is hieronder afgebeeld. Virtueel netwerk met de naam "A" bevat twee VM's (VNETA-VM1 en VNETA-VM2). Elk van deze hebben Prive-IP's gekoppeld. Zodra u een privézone met de naam contoso.com maakt en dit virtuele netwerk koppelt als een virtueel registratienetwerk, maakt Azure DNS automatisch twee A-records in de zone zoals afgebeeld. Nu ontvangen DNS-query's van VNETA-VM1 om VNETA-VM2.contoso.com op te lossen een DNS-antwoord met het privé-IP-ip van VNETA-VM2. Bovendien ontvangt een Reverse DNS-query (PTR) voor het privé-IP-ip van VNETA-VM1 (10.0.0.1) die is uitgegeven door VNETA-VM2 een DNS-antwoord met de naam VNETA-VM1, zoals verwacht. 

![Eén virtuele netwerkresolutie](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenario: Naamoplossing voor virtuele netwerken

Dit scenario is het meest voorkomende geval waarin u een privézone moet koppelen aan meerdere virtuele netwerken. Dit scenario past in architecturen zoals het Hub-and-Spoke-model waarbij er een centraal virtueel hubnetwerk is waarmee meerdere andere Virtuele Spoke-netwerken zijn aangesloten. Het centrale virtuele netwerk hub kan worden gekoppeld als het virtuele registratienetwerk aan een privézone en de virtuele netwerken van Spoke kunnen worden gekoppeld als virtuele netwerken resolutie. 

Het volgende diagram toont een eenvoudige versie van dit scenario waarin er slechts twee virtuele netwerken zijn - A en B. A is aangewezen als een registratie virtueel netwerk en B is aangewezen als een resolutie virtueel netwerk. Het is de bedoeling dat beide virtuele netwerken een gemeenschappelijke zone delen contoso.com. Wanneer de zone wordt gemaakt en de virtuele netwerken Resolutie en Registratie zijn gekoppeld aan de zone, registreert Azure automatisch DNS-records voor de VM's (VNETA-VM1 en VNETA-VM2) van het virtuele netwerk A. U ook handmatig DNS-records toevoegen aan de zone voor VM's in het virtuele netwerk Resolutie B. Met deze instelling observeert u het volgende gedrag voor doorsturen en reverse DNS-query's:
* Een DNS-query van VNETB-VM1 in het virtuele netwerk Resolutie B ontvangt voor VNETA-VM1.contoso.com een DNS-antwoord met het privé-IP-ip van VNETA-VM1.
* Een Reverse DNS (PTR) query van VNETB-VM2 in het resolutie virtuele netwerk B, voor 10.1.0.1, ontvangt een DNS-antwoord met de FQDN VNETB-VM1.contoso.com.  
* Een Reverse DNS (PTR) query van VNETB-VM3 in het resolutie virtuele netwerk B, voor 10.0.0.1, ontvangt NXDOMAIN. De reden hiervoor is dat Reverse DNS-query's alleen worden beperkt tot hetzelfde virtuele netwerk. 


![Meerdere virtuele netwerkresoluties](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenario: Split-Horizon-functionaliteit

In dit scenario hebt u een use case waarbij u verschillende DNS-resolutiegedrag wilt realiseren, afhankelijk van waar de client zich bevindt (binnen in Azure of op internet), voor dezelfde DNS-zone. U bijvoorbeeld een privé- en openbare versie van uw toepassing hebben met verschillende functionaliteit of gedrag, maar u wilt dezelfde domeinnaam voor beide versies gebruiken. Dit scenario kan worden gerealiseerd met Azure DNS door een openbare DNS-zone en een privézone met dezelfde naam te maken.

Het volgende diagram toont dit scenario. U hebt een virtueel netwerk A met twee VM's (VNETA-VM1 en VNETA-VM2) die zowel particuliere IP's als openbare IP's hebben toegewezen. U maakt een openbare DNS-zone met de naam contoso.com en registreert de openbare IP's voor deze VM's als DNS-records binnen de zone. U maakt ook een privé-DNS-zone die ook contoso.com wordt genoemd, waarin A wordt opgegeven als het virtuele netwerk Registratie. Azure registreert de VM's automatisch als A-records in de privézone en wijst naar hun privé-IP's.

Wanneer een internetclient nu een DNS-query uitgeeft om VNETA-VM1.contoso.com op te zoeken, retourneert Azure de openbare IP-record uit de openbare zone. Als dezelfde DNS-query wordt uitgegeven vanuit een andere VM (bijvoorbeeld VNETA-VM2) in hetzelfde virtuele netwerk A, retourneert Azure de privé-IP-record uit de privézone. 

![Split Brian resolutie](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over privé-DNS-zones raadpleegt u [Using Azure DNS for private domains](private-dns-overview.md) (Azure DNS gebruiken voor privédomeinen).

Meer informatie over het [maken van een privé-DNS-zone](./private-dns-getstarted-powershell.md) in Azure DNS.

Meer informatie over DNS-zones en -records door te bezoeken: [DNS-zones en recordsoverzicht](dns-zones-records.md).

Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.

