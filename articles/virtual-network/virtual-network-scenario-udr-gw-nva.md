---
title: Hybride verbinding met toepassing met 2 niveaus | Microsoft Documenten
description: Meer informatie over het implementeren van virtuele apparaten en UDR om een multi-tier toepassingsomgeving in Azure te maken
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1bdc485dfb352144e8a8d0fb75965cbb78288e2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64575577"
---
# <a name="virtual-appliance-scenario"></a>Scenario voor virtuele apparaten
Een veelvoorkomend scenario onder grotere Azure-klanten is de noodzaak om een tweetrapstoepassing te bieden die is blootgesteld aan internet, terwijl toegang tot de backlaag vanuit een on-premises datacenter wordt toegestaan. Dit document leidt u door een scenario met behulp van User Defined Routes (UDR), een VPN-gateway en virtuele netwerkapparaten om een omgeving met twee lagen te implementeren die aan de volgende vereisten voldoet:

* Webtoepassing moet alleen toegankelijk zijn vanaf het openbare internet.
* Webserver die de toepassing host, moet toegang hebben tot een backend-toepassingsserver.
* Al het verkeer van het internet naar de webapplicatie moet via een virtueel firewall-toestel gaan. Dit virtuele toestel wordt alleen voor internetverkeer gebruikt.
* Al het verkeer dat naar de toepassingsserver gaat, moet via een virtueel firewall-toestel gaan. Dit virtuele toestel zal worden gebruikt voor toegang tot de backend-endserver en toegang vanuit het on-premises netwerk via een VPN-gateway.
* Beheerders moeten in staat zijn om de firewall virtuele apparaten te beheren vanaf hun on-premises computers, met behulp van een derde firewall virtuele toestel uitsluitend gebruikt voor beheerdoeleinden.

Dit is een standaard perimeter netwerk (ook bekend als DMZ) scenario met een DMZ en een beveiligd netwerk. Een dergelijk scenario kan worden geconstrueerd in Azure met behulp van NSGs, firewall virtuele apparaten, of een combinatie van beide. De onderstaande tabel toont een aantal van de voors en tegens tussen NSGs en firewall virtuele apparaten.

|  | Voordelen | Nadelen |
| --- | --- | --- |
| NSG |Geen kosten. <br/>Geïntegreerd in Azure RBAC. <br/>Regels kunnen worden gemaakt in Azure Resource Manager-sjablonen. |Complexiteit kan variëren in grotere omgevingen. |
| Firewall |Volledige controle over datavlak. <br/>Centraal beheer via firewall console. |Kosten van firewall toestel. <br/>Niet geïntegreerd met Azure RBAC. |

De onderstaande oplossing maakt gebruik van firewall virtuele apparaten om een perimeter netwerk (DMZ) / beveiligde netwerk scenario te implementeren.

## <a name="considerations"></a>Overwegingen
U de hierboven beschreven omgeving in Azure implementeren met behulp van verschillende functies die vandaag als volgt beschikbaar zijn.

* **Virtueel netwerk (VNet)**. Een Azure VNet werkt op dezelfde manier als een on-premises netwerk en kan worden gesegmenteerd in een of meer subnetten om verkeerisolatie en scheiding van problemen te bieden.
* **Virtueel toestel**. Verschillende partners bieden virtuele apparaten in de Azure Marketplace die kunnen worden gebruikt voor de drie hierboven beschreven firewalls. 
* **Gebruikers gedefinieerde routes (UDR)**. Routetabellen kunnen UDR's bevatten die door Azure-netwerken worden gebruikt om de stroom van pakketten binnen een VNet te beheren. Deze routetabellen kunnen worden toegepast op subnetten. Een van de nieuwste functies in Azure is de mogelijkheid om een routetabel toe te passen op het GatewaySubnet, zodat al het verkeer dat in het Azure VNet komt, kan worden doorgestuurd van een hybride verbinding naar een virtueel toestel.
* **IP Forwarding**. Standaard stuurt de Azure-netwerkengine pakketten alleen door naar virtuele netwerkinterfacekaarten (NIC's) als het IP-adres van de pakketbestemming overeenkomt met het NIC-IP-adres. Als een UDR daarom definieert dat een pakket naar een bepaald virtueel toestel moet worden verzonden, zou de Azure-netwerkengine dat pakket laten vallen. Om ervoor te zorgen dat het pakket wordt geleverd aan een VM (in dit geval een virtueel toestel) dat niet de werkelijke bestemming voor het pakket is, moet u IP Forwarding inschakelen voor het virtuele toestel.
* **Netwerkbeveiligingsgroepen (NSGs)**. Het onderstaande voorbeeld maakt geen gebruik van NSG's, maar u NSG's die op de subnetten en/of NIC's in deze oplossing worden toegepast, gebruiken om het verkeer in en uit die subnetten en NIC's verder te filteren.

![IPv6-connectiviteit](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

In dit voorbeeld is er een abonnement dat het volgende bevat:

* 2 resourcegroepen, niet weergegeven in het diagram. 
  * **ONPREMRG**. Bevat alle resources die nodig zijn om een on-premises netwerk te simuleren.
  * **AZURERG**. Bevat alle resources die nodig zijn voor de virtuele netwerkomgeving van Azure. 
* Een VNet met de naam **onpremvnet** gebruikt om een on-premises datacenter gesegmenteerd zoals hieronder vermeld na te bootsen.
  * **onpremsn1**. Subnet met een virtuele machine (VM) met Ubuntu om een on-premises server na te bootsen.
  * **onpremsn2**. Subnet met een VM met Ubuntu om een on-premises computer na te bootsen die door een beheerder wordt gebruikt.
* Er is een firewall virtuele toestel genaamd **OPFW** op **onpremvnet** gebruikt om een tunnel naar **azurevnet**onderhouden .
* Een VNet met de naam **azurevnet** gesegmenteerd zoals hieronder vermeld.
  * **azsn1**. Extern firewall subnet uitsluitend gebruikt voor de externe firewall. Al het internetverkeer komt binnen via dit subnet. Dit subnet bevat alleen een NIC gekoppeld aan de externe firewall.
  * **azsn2**. Front-end subnet hosting van een VM draait als een webserver die zal worden benaderd vanaf het internet.
  * **azsn3**. Backend subnet hosting een VM draait een backend applicatie server die zal worden benaderd door de front-end webserver.
  * **azsn4**. Management subnet uitsluitend gebruikt om het beheer toegang tot alle firewall virtuele apparaten. Dit subnet bevat alleen een NIC voor elke firewall virtueel toestel gebruikt in de oplossing.
  * **GatewaySubnet**. Subnet voor hybride verbindingen voor Azure vereist voor ExpressRoute en VPN Gateway om connectiviteit te bieden tussen Azure VNets en andere netwerken. 
* Er zijn 3 firewall virtuele apparaten in het **azurevnet** netwerk. 
  * **AZF1**. Externe firewall die is blootgesteld aan het openbare internet met behulp van een openbare IP-adresbron in Azure. U moet ervoor zorgen dat u een sjabloon van de Marketplace hebt, of rechtstreeks van uw toestelleverancier, die een virtueel 3-NIC-toestel voorziet.
  * **AZF2**. Interne firewall gebruikt om het verkeer tussen **azsn2** en **azsn3**controle . Dit is ook een 3-NIC virtueel toestel.
  * **AZF3**. Beheerfirewall toegankelijk voor beheerders van het on-premises datacenter en verbonden met een beheersubnet dat wordt gebruikt om alle firewall-apparaten te beheren. U 2-NIC virtuele toestelsjablonen vinden in de Marketplace of er rechtstreeks een aanvragen bij de leverancier van uw toestel.

## <a name="user-defined-routing-udr"></a>Door de gebruiker gedefinieerde routering (UDR)
Elk subnet in Azure kan worden gekoppeld aan een UDR-tabel die wordt gebruikt om te bepalen hoe het verkeer dat in dat subnet wordt gestart, wordt doorgestuurd. Als er geen UDR's zijn gedefinieerd, gebruikt Azure standaardroutes om verkeer van het ene subnet naar het andere te laten stromen. Ga naar Wat zijn [door gebruikers gedefinieerde routes en IP-forwarding](virtual-networks-udr-overview.md)om udr's beter te begrijpen.

Om ervoor te zorgen dat de communicatie via het juiste firewalltoestel wordt uitgevoerd, moet u op basis van de laatste vereiste hierboven de volgende routetabel maken met UDR's in **azurevnet.**

### <a name="azgwudr"></a>azgwudr azgwudr
In dit scenario wordt het enige verkeer dat van on-premises naar Azure stroomt, gebruikt om de firewalls te beheren door verbinding te maken met **AZF3**, en dat verkeer moet via de interne firewall, **AZF2**. Daarom is slechts één route nodig in het **GatewaySubnet** zoals hieronder wordt weergegeven.

| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Hiermee kan on-premises verkeer beheerfirewall **AZF3** bereiken |

### <a name="azsn2udr"></a>azsn2udr
| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Hiermee wordt verkeer naar het backend-subnet dat de toepassingsserver host via **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Hiermee kan al het andere verkeer worden omgeleid via **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Hiermee kan verkeer naar **azsn2** van app-server naar de webserver stromen via **AZF2** |

U moet ook routetabellen maken voor de subnetten in **onpremvnet** om het on-premises datacenter na te bootsen.

### <a name="onpremsn1udr"></a>onpremsn1udr onpremsn1udr
| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Hiermee kan verkeer **onpremsn2** via **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr onpremsn2udr
| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Hiermee wordt verkeer naar het back-net in Azure via **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Hiermee kan verkeer **onpremsn1** via **OPFW** |

## <a name="ip-forwarding"></a>Doorsturen via IP
UDR en IP Forwarding zijn functies die u in combinatie gebruiken om virtuele apparaten te gebruiken om de verkeersstroom in een Azure VNet te regelen.  Een virtueel apparaat is niets meer dan een virtuele machine waarop een toepassing wordt uitgevoerd om netwerkverkeer op een bepaalde manier af te handelen, zoals een firewall of een NAT-apparaat.

Deze VM op het virtuele apparaat moet in staat zijn om binnenkomend verkeer te ontvangen dat niet is geadresseerd aan zichzelf. Om met een VM verkeer te kunnen ontvangen dat is geadresseerd aan andere bestemmingen, moet u Doorsturen via IP inschakelen voor die VM. Dit is een Azure-instelling, niet een instelling in het gastbesturingssysteem. Uw virtuele toestel moet nog steeds een soort toepassing uitvoeren om het binnenkomende verkeer te verwerken en het op de juiste manier te routeren.

Ga voor meer informatie over IP Forwarding naar [Wat zijn door gebruikers gedefinieerde routes en IP-forwarding](virtual-networks-udr-overview.md).

Stel u bijvoorbeeld voor dat u de volgende instelling in een Azure-vnet hebt:

* Subnet **onpremsn1** bevat een VM met de naam **onpremvm1**.
* Subnet **onpremsn2** bevat een VM met de naam **onpremvm2**.
* Een virtueel toestel met de naam **OPFW** is aangesloten op **onpremsn1** en **onpremsn2.**
* Een door de gebruiker gedefinieerde route die is gekoppeld aan **onpremsn1** geeft aan dat al het verkeer naar **onpremsn2** naar **OPFW**moet worden verzonden.

Op dit punt, als **onpremvm1** probeert om een verbinding met **onpremvm2**vast te stellen, zal de UDR worden gebruikt en het verkeer zal worden verzonden naar **OPFW** als de volgende hop. Houd er rekening mee dat de werkelijke pakketbestemming niet wordt gewijzigd, maar dat er nog steeds **onpremvm2** de bestemming is. 

Zonder IP Forwarding ingeschakeld voor **OPFW**, zal de Azure virtual networking logica de pakketten laten vallen, omdat het alleen mogelijk maakt pakketten worden verzonden naar een VM als het IP-adres van de VM is de bestemming voor het pakket.

Met IP Forwarding stuurt de azure-logica voor virtuele netwerken de pakketten door naar OPFW, zonder het oorspronkelijke doeladres te wijzigen. **OPFW** moet de pakketten verwerken en bepalen wat ermee te doen.

Om het bovenstaande scenario te laten werken, moet u IP Forwarding inschakelen op de NIC's voor **OPFW,** **AZF1,** **AZF2**en **AZF3** die worden gebruikt voor routering (alle NIC's behalve die welke zijn gekoppeld aan het beheersubnet). 

## <a name="firewall-rules"></a>Firewallregels
Zoals hierboven beschreven, zorgt IP Forwarding er alleen voor dat pakketten naar de virtuele apparaten worden verzonden. Uw apparaat moet nog beslissen wat te doen met deze pakketten. In het bovenstaande scenario moet u de volgende regels in uw apparaten maken:

### <a name="opfw"></a>OPFW (OPFW)
OPFW vertegenwoordigt een on-premises apparaat met de volgende regels:

* **Route**: Al het verkeer naar 10.0.0.0/16 (**azurevnet**) moet worden verzonden door tunnel **ONPREMAZURE**.
* **Beleid**: Al het tweerichtingsverkeer tussen **poort2** en **ONPREMAZURE**toestaan .

### <a name="azf1"></a>AZF1
AZF1 vertegenwoordigt een virtueel Azure-toestel met de volgende regels:

* **Beleid**: Toestaan dat al het bidirectionele verkeer tussen **poort1** en **poort2**.

### <a name="azf2"></a>AZF2
AZF2 vertegenwoordigt een virtueel Azure-toestel met de volgende regels:

* **Route**: Al het verkeer naar 10.0.0.0/16 (**onpremvnet**) moet via **poort1**worden verzonden naar het IP-adres van de Azure-gateway (d.w.z. 10.0.0.1).
* **Beleid**: Toestaan dat al het bidirectionele verkeer tussen **poort1** en **poort2**.

## <a name="network-security-groups-nsgs"></a>Netwerkbeveiligingsgroepen (NSGs)
In dit scenario worden geen NSG's gebruikt. U echter NSGs op elk subnet toepassen om binnenkomend en uitgaand verkeer te beperken. U bijvoorbeeld de volgende NSG-regels toepassen op het externe FW-subnet.

**Binnenkomende**

* Sta al het TCP-verkeer van het internet toe tot poort 80 op elke VM in het subnet.
* Ontken al het andere verkeer van het internet.

**Uitgaande**

* Ontken al het verkeer naar het internet.

## <a name="high-level-steps"></a>Stappen op hoog niveau
Volg de onderstaande stappen op hoog niveau om dit scenario te implementeren.

1. Log in op uw Azure-abonnement.
2. Als u een VNet wilt implementeren om het on-premises netwerk na te bootsen, indient u de resources die deel uitmaken van **ONPREMRG.**
3. De middelen inrichten die deel uitmaken van **AZURERG**.
4. Inrichten van de tunnel van **onpremvnet** naar **azurevnet**.
5. Zodra alle resources zijn ingericht, meld u aan **bij onpremvm2** en ping 10.0.3.101 om de connectiviteit tussen **onpremsn2** en **azsn3**te testen.

