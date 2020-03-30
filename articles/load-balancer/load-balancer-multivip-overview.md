---
title: Meerdere frontends - Azure Load Balancer
description: Ga met dit leerpad aan de slag met een overzicht van meerdere frontends op Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0a54416a70a8561edfad5915944100e0ce686bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771254"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Meerdere frontends voor Azure Load Balancer

Met Azure Load Balancer u balansservices laden op meerdere poorten, meerdere IP-adressen of beide. U openbare en interne load balancer-definities gebruiken om balansstromen over een set VM's te laden.

Dit artikel beschrijft de fundamenten van dit vermogen, belangrijke concepten en beperkingen. Als u alleen services op één IP-adres wilt blootstellen, u vereenvoudigde instructies vinden voor [openbare](load-balancer-get-started-internet-portal.md) of [interne](load-balancer-get-started-ilb-arm-portal.md) configuraties van de load balancer. Het toevoegen van meerdere frontends is incrementeel naar één frontendconfiguratie. Met behulp van de concepten in dit artikel u op elk gewenst moment een vereenvoudigde configuratie uitbreiden.

Wanneer u een Azure Load Balancer definieert, zijn een frontend- en een backendpoolconfiguratie verbonden met regels. De statussonde waarnaar naar verwijst met de regel wordt gebruikt om te bepalen hoe nieuwe stromen worden verzonden naar een knooppunt in de backendpool. De frontend (aka VIP) wordt gedefinieerd door een 3-tuple bestaande uit een IP-adres (openbaar of intern), een transportprotocol (UDP of TCP) en een poortnummer van de regel voor het balanceren van de lasten. De backend pool is een verzameling Virtual Machine IP-configuraties (onderdeel van de NIC-bron) die verwijzen naar de backendpool Load Balancer.

De volgende tabel bevat enkele voorbeeldfrontendconfiguraties:

| Front-end | IP-adres | Protocol | poort |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

De tabel toont vier verschillende frontends. Frontends #1, #2 en #3 zijn één frontend met meerdere regels. Hetzelfde IP-adres wordt gebruikt, maar de poort of het protocol is verschillend voor elke frontend. Frontends #1 en #4 zijn een voorbeeld van meerdere frontends, waarbij hetzelfde frontend protocol en dezelfde poort worden hergebruikt over meerdere frontends.

Azure Load Balancer biedt flexibiliteit bij het definiëren van de regels voor het balanceren van de lasten. Een regel geeft aan hoe een adres en poort aan de frontend wordt toegewezen aan het bestemmingsadres en de poort op de backend. Of backendpoorten worden hergebruikt voor alle regels, hangt af van het type regel. Elk type regel heeft specifieke vereisten die van invloed kunnen zijn op de configuratie van de host en het ontwerp van de sonde. Er zijn twee soorten regels:

1. De standaardregel zonder hergebruik van backendpoorten
2. De Floating IP-regel waarbij backendpoorten worden hergebruikt

Met Azure Load Balancer u beide regeltypen in dezelfde configuratie van de load balancer mengen. De load balancer kan ze tegelijkertijd gebruiken voor een bepaalde VM, of een combinatie, zolang u zich houdt aan de beperkingen van de regel. Welk regeltype u kiest, is afhankelijk van de vereisten van uw toepassing en de complexiteit van het ondersteunen van die configuratie. U moet evalueren welke regeltypen het beste zijn voor uw scenario.

We verkennen deze scenario's verder door te beginnen met het standaardgedrag.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regeltype #1: Geen backendpoorthergebruik

![Veelvoudige frontendillustratie met groene en purpere frontend](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

In dit scenario worden de frontends als volgt geconfigureerd:

| Front-end | IP-adres | Protocol | poort |
| --- | --- | --- | --- |
| ![groene frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![paarse frontend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

De DIP is de bestemming van de inkomende stroom. In de backend pool legt elke VM de gewenste service bloot op een unieke poort op een DIP. Deze service is gekoppeld aan de frontend door middel van een regeldefinitie.

We definiëren twee regels:

| Regel | Frontend van kaart | Naar backend pool |
| --- | --- | --- |
| 1 |![groene frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

De volledige toewijzing in Azure Load Balancer is nu als volgt:

| Regel | Frontend IP-adres | Protocol | poort | Doel | poort |
| --- | --- | --- | --- | --- | --- |
| ![groene regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP-adres |80 |
| ![paarse regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP-adres |81 |

Elke regel moet een stroom opleveren met een unieke combinatie van het IP-adres en de bestemmingspoort. Door de bestemmingspoort van de stroom te variëren, kunnen meerdere regels stromen naar dezelfde DIP op verschillende poorten leveren.

Health probes worden altijd gericht op de DIP van een VM. U moet ervoor zorgen dat uw sonde de status van de VM weerspiegelt.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regeltype #2: hergebruik van backendpoorten met zwevend IP-adres

Azure Load Balancer biedt de flexibiliteit om de frontend-poort over meerdere frontends te hergebruiken, ongeacht het gebruikte regeltype. Bovendien geven sommige toepassingsscenario's de voorkeur aan of vereisen dezelfde poort die door meerdere toepassingsinstanties op één vm in de backendgroep moet worden gebruikt. Veelvoorkomende voorbeelden van hergebruik van poorten zijn clustering voor hoge beschikbaarheid, virtuele netwerkapparaten en het blootstellen van meerdere TLS-eindpunten zonder herversleuteling.

Als u de backend-poort voor meerdere regels opnieuw wilt gebruiken, moet u Zwevend IP inschakelen in de regeldefinitie.

"Floating IP" is de terminologie van Azure voor een deel van wat bekend staat als Direct Server Return (DSR). DSR bestaat uit twee delen: een flow topologie en een IP-adresmappingschema. Op platformniveau werkt Azure Load Balancer altijd in een DSR-stroomtopologie, ongeacht of Floating IP is ingeschakeld of niet. Dit betekent dat het uitgaande deel van een stroom altijd correct wordt herschreven om direct terug te stromen naar de oorsprong.

Met het standaardregeltype stelt Azure een traditioneel taakverdelings-IP-adrestoewijzingsschema bloot voor gebruiksgemak. Als u Floating IP inschakelt, wordt het IP-adrestoewijzingsschema gewijzigd om extra flexibiliteit mogelijk te maken, zoals hieronder wordt uitgelegd.

In het volgende diagram wordt deze configuratie geïllustreerd:

![Veelvoudige frontendillustratie met groene en purpere frontend met DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Voor dit scenario heeft elke VM in de backendpool drie netwerkinterfaces:

* DIP: een virtuele nic die is gekoppeld aan de VM (IP-configuratie van de NIC-bron van Azure)
* Frontend 1: een loopback-interface binnen gastbesturingssysteem die is geconfigureerd met IP-adres van Frontend 1
* Frontend 2: een loopback-interface binnen gastbesturingssysteem die is geconfigureerd met IP-adres van Frontend 2

Voer voor elke vm in de backendgroep de volgende opdrachten uit op een Windows-opdrachtprompt.

Als u de lijst met interfacenamen op uw vm wilt krijgen, typt u deze opdracht:

    netsh interface show interface 

Typ deze opdracht voor de VM NIC (Azure managed):

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
   (interfacenaam vervangen door de naam van deze interface)

Herhaal deze opdrachten voor elke loopback-interface die u hebt toegevoegd:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
   (interfacenaam vervangen door de naam van deze loopback-interface)
     
    netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
   (interfacenaam vervangen door de naam van deze loopback-interface)

> [!IMPORTANT]
> De configuratie van de loopback-interfaces wordt uitgevoerd binnen het besturingssysteem van de gast. Deze configuratie wordt niet uitgevoerd of beheerd door Azure. Zonder deze configuratie werken de regels niet. Definities van statussondes gebruiken de DIP van de VM in plaats van de loopback-interface die de DSR Frontend vertegenwoordigt. Daarom moet uw service sondereacties geven op een DIP-poort die de status weergeeft van de service die wordt aangeboden op de loopback-interface die de DSR Frontend vertegenwoordigt.


Laten we uitgaan van dezelfde frontend-configuratie als in het vorige scenario:

| Front-end | IP-adres | Protocol | poort |
| --- | --- | --- | --- |
| ![groene frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![paarse frontend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

We definiëren twee regels:

| Regel | Front-end | Kaart naar backend pool |
| --- | --- | --- |
| 1 |![Regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (in VM1 en VM2) |
| 2 |![Regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (in VM1 en VM2) |

In de volgende tabel ziet u de volledige toewijzing in de load balancer:

| Regel | Frontend IP-adres | Protocol | poort | Doel | poort |
| --- | --- | --- | --- | --- | --- |
| ![groene regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |gelijk aan frontend (65.52.0.1) |hetzelfde als frontend (80) |
| ![paarse regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |gelijk aan frontend (65.52.0.2) |hetzelfde als frontend (80) |

De bestemming van de binnenkomende stroom is het IP-adres aan de frontend op de loopback-interface in de VM. Elke regel moet een stroom opleveren met een unieke combinatie van het IP-adres en de bestemmingspoort. Door het doel-IP-adres van de stroom te variëren, is poorthergebruik mogelijk op dezelfde VM. Uw service wordt blootgesteld aan de load balancer door deze te binden aan het IP-adres en de poort van de respectievelijke loopback-interface.

Dit voorbeeld wijzigt de doelpoort niet. Hoewel dit een Floating IP-scenario is, ondersteunt Azure Load Balancer ook het definiëren van een regel om de backend-bestemmingspoort te herschrijven en deze te laten verschillen van de frontend-bestemmingspoort.

Het floating IP-regeltype is de basis van verschillende configuratiepatronen van de load balancer. Een voorbeeld dat momenteel beschikbaar is, is de [SQL AlwaysOn met configuratie met meerdere listeners.](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) Na verloop van tijd zullen we meer van deze scenario's documenteren.

## <a name="limitations"></a>Beperkingen

* Meerdere frontendconfiguraties worden alleen ondersteund met IaaS VM's.
* Met de Floating IP-regel moet uw toepassing de primaire IP-configuratie gebruiken voor uitgaande SNAT-stromen. Als uw toepassing wordt gekoppeld aan het IP-adres aan de voorzijde dat is geconfigureerd op de lusback-interface in het gastbesturingssysteem, is de uitgaande SNAT van Azure niet beschikbaar om de uitgaande stroom te herschrijven en mislukt de stroom.  Uitgaande [scenario's bekijken](load-balancer-outbound-connections.md).
* Openbare IP-adressen hebben een effect op facturering. Zie [IP-adresprijzen voor](https://azure.microsoft.com/pricing/details/ip-addresses/) meer informatie
* Er gelden abonnementslimieten. Zie [Servicelimieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) voor meer informatie voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Controleer [uitgaande verbindingen](load-balancer-outbound-connections.md) om de impact van meerdere frontends op uitgaand verbindingsgedrag te begrijpen.
