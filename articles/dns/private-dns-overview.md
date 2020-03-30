---
title: Wat is privé-DNS in Azure?
description: Ga in dit artikel aan de slag met een overzicht van de private DNS-hostingservice op Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 97b266398b3ea46d09b04524dad34922f21b1a95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76939292"
---
# <a name="what-is-azure-private-dns"></a>Wat is privé-DNS in Azure?

Het Domain Name System, of DNS, is verantwoordelijk voor het vertalen (of oplossen) van een servicenaam naar het IP-adres.  Azure DNS is een hostingservice voor DNS-domeinen, die naamomzetting biedt met behulp van de Microsoft Azure-infrastructuur. Naast het ondersteunen van dns-domeinen die op internet zijn gericht, ondersteunt Azure DNS ook privé-DNS-zones.

Azure Private DNS biedt een betrouwbare, veilige DNS-service voor het beheren en oplossen van domeinnamen in een virtueel netwerk zonder dat u een aangepaste DNS-oplossing hoeft toe te voegen. Door privé-DNS-zones te gebruiken, u uw eigen aangepaste domeinnamen gebruiken in plaats van de door Azure verstrekte namen die vandaag beschikbaar zijn. Het gebruik van aangepaste domeinnamen helpt u om uw virtuele netwerkarchitectuur zo goed mogelijk aan te passen aan de behoeften van uw organisatie. Het biedt naamresolutie voor virtuele machines (VM's) binnen een virtueel netwerk en tussen virtuele netwerken. Bovendien u zonesnamen configureren met een split-horizonweergave, waarmee een privé- en een openbare DNS-zone de naam kunnen delen.

Als u de records van een privé-DNS-zone wilt oplossen vanuit uw virtuele netwerk, moet u het virtuele netwerk koppelen aan de zone. Gekoppelde virtuele netwerken hebben volledige toegang en kunnen alle DNS-records oplossen die in de privézone zijn gepubliceerd. Daarnaast u ook automatisch registreren op een virtuele netwerkkoppeling inschakelen. Als u automatische registratie inschakelt op een virtuele netwerkkoppeling, worden de DNS-records voor de virtuele machines op dat virtuele netwerk geregistreerd in de privézone. Wanneer automatische registratie is ingeschakeld, werkt Azure DNS ook de zonerecords bij wanneer een virtuele machine wordt gemaakt, het IP-adres ervan wijzigt of wordt verwijderd.

![DNS-overzicht](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Gebruik als aanbevolen praktijk geen *.local* domain voor uw privé-DNS-zone. Niet alle besturingssystemen ondersteunen dit.

## <a name="benefits"></a>Voordelen

Azure Private DNS biedt de volgende voordelen:

* **Hiermee is de behoefte aan aangepaste DNS-oplossingen weg.** Voorheen maakten veel klanten aangepaste DNS-oplossingen om DNS-zones in hun virtuele netwerk te beheren. U nu DNS-zones beheren met behulp van de native Azure-infrastructuur, waardoor de last van het maken en beheren van aangepaste DNS-oplossingen wordt weggezet.

* **Gebruik alle gangbare DNS-recordstypen**. Azure DNS ondersteunt A-, AAAA-, CNAME-, MX-, PTR-, SOA-, SRV- en TXT-records.

* **Automatisch recordbeheer hostname**. Samen met het hosten van uw aangepaste DNS-records, onderhoudt Azure automatisch hostnamerecords voor de VM's in de opgegeven virtuele netwerken. In dit scenario u de domeinnamen optimaliseren die u gebruikt zonder aangepaste DNS-oplossingen te hoeven maken of toepassingen te wijzigen.

* **Hostname-resolutie tussen virtuele netwerken**. In tegenstelling tot door Azure geleverde hostnamen kunnen privé-DNS-zones worden gedeeld tussen virtuele netwerken. Deze mogelijkheid vereenvoudigt scenario's voor cross-network en servicedetectie, zoals virtueel netwerkpeeren.

* **Vertrouwde tools en gebruikerservaring.** Om de leercurve te verminderen, maakt deze service gebruik van gevestigde Azure DNS-hulpprogramma's (Azure portal, Azure PowerShell, Azure CLI, Azure Resource Manager-sjablonen en de REST-API).

* **DNS-ondersteuning voor split-horizon**. Met Azure DNS u zones met dezelfde naam maken die worden opgelost voor verschillende antwoorden vanuit een virtueel netwerk en van het openbare internet. Een typisch scenario voor split-horizon DNS is het bieden van een speciale versie van een service voor gebruik binnen uw virtuele netwerk.

* **Beschikbaar in alle Azure-regio's**. De functie Azure DNS-privézones is beschikbaar in alle Azure-regio's in de openbare Azure-cloud.

## <a name="capabilities"></a>Functionaliteit

Azure DNS biedt de volgende mogelijkheden:

* **Automatische registratie van virtuele machines uit een virtueel netwerk dat is gekoppeld aan een privézone met automatische registratie ingeschakeld.** De virtuele machines worden geregistreerd (toegevoegd) aan de privé-zone als A-records die verwijzen naar hun privé-IP-adressen. Wanneer een virtuele machine in een virtuele netwerkkoppeling met automatische registratie wordt verwijderd, verwijdert Azure DNS ook automatisch de bijbehorende DNS-record uit de gekoppelde privézone.

* **Forward DNS-resolutie wordt ondersteund in virtuele netwerken die zijn gekoppeld aan de privézone.** Voor cross-virtual network DNS-resolutie is er geen expliciete afhankelijkheid, zodat de virtuele netwerken met elkaar worden gekeken. U echter wel virtuele netwerken peeren voor andere scenario's (bijvoorbeeld HTTP-verkeer).

* **Reverse DNS lookup wordt ondersteund binnen het bereik van het virtuele netwerk.** Reverse DNS lookup for a private IP within the virtual network assigned to a private zone returns the FQDN that includes the host/record name and the zone name as the suffix.

## <a name="other-considerations"></a>Andere overwegingen

Azure DNS heeft de volgende beperkingen:

* Een specifiek virtueel netwerk kan slechts aan één privézone worden gekoppeld als automatische registratie van VM DNS-records is ingeschakeld. U echter meerdere virtuele netwerken koppelen aan één DNS-zone.
* Reverse DNS werkt alleen voor privé-IP-ruimte in het gekoppelde virtuele netwerk
* Reverse DNS voor een privé-IP-adres voor een gekoppeld virtueel netwerk retourneert *internal.cloudapp.net* als het standaard achtervoegsel voor de virtuele machine. Voor virtuele netwerken die zijn gekoppeld aan een privézone waarvoor automatische registratie is ingeschakeld, retourneert reverse DNS voor een privé-IP-adres twee FQDN's: een met standaard het achtervoegsel *internal.cloudapp.net* en een andere met het achtervoegsel privézone.
* Voorwaardelijke doorsturen wordt momenteel niet native ondersteund. Oplossing tussen Azure en on-premises netwerken inschakelen. Zie [Naamresolutie voor VM's en rolexemplaren](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Prijzen

Zie [Azure DNS Pricing](https://azure.microsoft.com/pricing/details/dns/)voor prijsinformatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privézone in Azure DNS met [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure CLI](./private-dns-getstarted-cli.md).

* Lees meer over enkele veelvoorkomende [privézonescenario's](./private-dns-scenarios.md) die kunnen worden gerealiseerd met privézones in Azure DNS.

* Zie [Veelgestelde vragen](./dns-faq-private.md)over privé-informatie over privézones in Azure DNS, inclusief specifiek gedrag dat u voor bepaalde soorten bewerkingen verwachten.

* Meer informatie over DNS-zones en -records door het overzicht van [DNS-zones en records te](dns-zones-records.md)bezoeken.

* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
