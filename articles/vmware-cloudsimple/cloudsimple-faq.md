---
title: Veelgestelde vragen-Azure VMware-oplossingen (AVS)
description: Veelgestelde vragen over Azure VMware-oplossingen (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025058"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>Veelgestelde vragen over VMware-oplossing per AVS

## <a name="avs-service"></a>AVS-service

**Wat is Azure VMware Solutions (AVS)?**

Met Azure VMware-oplossingen (AVS) worden VMware-workloads in enkele minuten getransformeerd en uitgebreid naar persoonlijke, specifieke Clouds op Azure. AVS zorgt voor de inrichting, het beheer van de infra structuur en het organiseren van werk belastingen tussen on-premises en Azure. Omdat uw apps precies dezelfde on-premises en in Azure worden uitgevoerd, kunt u profiteren van de elasticiteit en de services van de Cloud, zonder de complexiteit van het opnieuw ontwerpen van uw apps. AVS verlaagt uw total cost of ownership met een model voor Cloud verbruik dat op aanvraag voorziet van inrichtingen, betalen per gebruik en capaciteits optimalisatie. Zie [Wat is VMware-oplossing op Azure door AVS](cloudsimple-vmware-solutions-overview.md) voor functies, voor delen en scenario's.

**Wat is een Privécloud in de Cloud?**

Een AVS-privécloud is een persoonlijke, toegewezen Cloud die bestaat uit een hoogwaardige compute-, opslag-en netwerk omgeving die is geïmplementeerd op de Microsoft Azure infrastructuur (hardware-en datacenter ruimte) in azure-locaties. Een AVS-Privécloud biedt een systeem eigen VMware-platform als een service. In VMware-voor waarden bevat elke AVS-Privécloud precies één exemplaar van de vCenter Server. De vCenter Server beheert meerdere ESXi-knoop punten in een of meer vSphere-clusters, samen met de bijbehorende virtuele SAN-opslag (vSAN). Een AVS-service kan meerdere AVS-persoonlijke Clouds in uw Azure-abonnement bevatten. Zie voor meer informatie overzicht van de [persoonlijke cloud van AVS](cloudsimple-private-cloud.md).

**Waar is de AVS-service beschikbaar?**

AVS is beschikbaar in VS-Oost, VS-West en Europa-west regio's met extra regio's binnenkort.

**Mijn abonnement voor AVS Hoe kan ik inschakelen?**

U kunt contact opnemen met uw Microsoft-account-vertegenwoordiger op [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) om uw abonnement voor de AVS-service in te scha kelen. Geef uw abonnements-ID op in het e-mail adres waarvoor u de AVS-service wilt inschakelen. 

**Hoe kan ik toegang tot de AVS-Portal?**

U opent de AVS-Portal vanuit de Azure Portal. Zie voor meer informatie [toegang tot de AVS-Portal (VMware Solutions) van de Azure Portal](access-cloudsimple-portal.md).

**Hoe kan ik capaciteit verhogen voor een Privécloud in de Cloud?**

Als u de capaciteit wilt verg Roten, koopt u extra knoop punten van het Azure Portal en gebruikt u vervolgens de knoop punten om uw AVS-privécloud uit de AVS-Portal uit te breiden. U kunt extra knoop punten toevoegen aan een bestaand vSphere-cluster of toevoegen aan een nieuw vSphere-cluster. Zie voor meer informatie [een AVS-privécloud uitvouwen](expand-private-cloud.md).

**Wat gebeurt er met mijn mijn AVS-privécloud tijdens onderhoud?**

AVS biedt meldingen enkele dagen vóór een gepland onderhouds interval. Onderhoud wordt uitgevoerd op een niet-storende manier om te zorgen voor de beschik baarheid van de Privécloud van uw AVS. Onderhoud kan van de volgende typen zijn:

* **AVS-infra structuur**. De AVS-infra structuur is zo ontworpen dat deze Maxi maal beschikbaar is. Tijdens dit type onderhouds interval worden redundante onderdelen een voor een bijgewerkt om onderbreking van de service te voor komen. U beheert de toegang tot uw AVS-persoonlijke Cloud-vCenter, alle virtuele machines, de Internet verbinding van uw Privécloud en verbindingen met on-premises of Azure.
* **AVS-Portal**. Tijdens dit type onderhouds interval zijn sommige functies op de AVS-Portal mogelijk uitgeschakeld of niet toegankelijk. De melding vóór het onderhouds interval bevat details over de beperkingen van de functie terwijl er onderhoud wordt uitgevoerd.

## <a name="connectivity"></a>Connectiviteit

**Wat zijn mijn connectiviteits opties voor het AVS-regio netwerk?**

AVS biedt de volgende connectiviteits opties om verbinding te maken met uw AVS-regio netwerk. Meerdere opties kunnen tegelijkertijd worden gebruikt.

* **ExpressRoute-verbinding van uw on-premises Data Center naar het AVS-regio netwerk**. Dit is een beveiligde privé verbinding met hoge snelheid en lage latentie die gebruikmaakt van Global Reach om uw on-premises ExpressRoute-circuit naar uw AVS ExpressRoute-circuit te overbruggen. Zie [verbinding maken tussen on-premises en AVS via ExpressRoute](on-premises-connection.md)voor instructies voor het instellen van de verbinding.
* **ExpressRoute-verbinding van uw virtuele Azure-netwerk naar uw AVS-regio netwerk**. Dit is een beveiligde persoonlijke particuliere verbinding met hoge snelheid en lage latentie die gebruikmaakt van virtuele netwerk gateways voor het overbruggen van uw virtuele netwerk op Azure naar uw AVS ExpressRoute-circuit. Zie voor instructies voor het instellen van de verbinding [verbinding maken met uw cloud omgeving van uw AVS in het virtuele Azure-netwerk met behulp van ExpressRoute](azure-expressroute-connection.md).
* **Site-naar-site-VPN-verbinding van uw on-premises Data Center naar uw AVS-regio netwerk**. Dit is een beveiligd virtueel particulier netwerk van uw on-premises VPN-apparaat naar uw Privécloud-regio. Zie [VPN-gateways instellen op het AVS-netwerk](vpn-gateway.md)voor meer informatie.

**Hoe kan ik verbinding maken met een nieuwe AVS-Cloud?**

U kunt de details van uw AVS-Privécloud in de AVS-Portal bekijken. Als u verbinding wilt maken met de vCenter die overeenkomt met de Privécloud van uw AVS, controleert u eerst of een netwerk verbinding tot stand is gebracht met behulp van site-naar-site VPN, punt-naar-site-VPN of ExpressRoute. Start vervolgens de AVS-Portal vanuit de Azure Portal en klik op de start pagina op **vSphere-client starten** of op de pagina Details van de gegevens van de AVS-privécloud.

**Wat is het voor deel van ExpressRoute-circuits?**

Een Azure ExpressRoute-circuit is een beveiligde verbinding met hoge snelheid en lage latentie. AVS biedt een toegewezen ExpressRoute-circuit per regio per klant. Met dit circuit kunt u een beveiligde verbinding tot stand brengen vanuit een on-premises of uw Azure-abonnement.

**Wat zijn de netwerk kosten om verbinding te maken met AVS? Gelden er uitstaande kosten tussen AVS en Azure, of in verschillende regio's?**

Er zijn geen AVS-kosten voor netwerk uitgaand verkeer. De standaard tarieven van Azure gelden voor elk uitgaand verkeer van uw virtuele netwerk of van uw on-premises ExpressRoute-circuit.

## <a name="networking"></a>Networking

**Welke netwerk functies zijn beschikbaar voor de Privécloud van mijn AVS?**

U kunt VLAN'S (en hun subnetten) en firewall tabellen inrichten en open bare IP-adressen toewijzen die worden toegewezen aan een virtuele machine die wordt uitgevoerd in de Privécloud van uw AVS. Zie voor meer informatie over netwerk functies [vlan's en subnetten Overview](cloudsimple-vlans-subnets.md), overzicht van [firewall tabellen](cloudsimple-firewall-tables.md)en [overzicht van open bare IP-adressen](cloudsimple-public-ip-address.md).

**Hoe kan ik verschillende subnetten instellen voor mijn toepassingen in mijn AVS-privécloud?**

U maakt VLAN'S op uw AVS-Privécloud vanuit de AVS-Portal. Nadat u een VLAN hebt gemaakt, kunt u een gedistribueerde poort groep maken op uw AVS-Privécloud, met behulp van het VLAN en virtuele machines maken die zijn verbonden met de gedistribueerde poort groep. U kunt Firewall tabellen inschakelen voor het VLAN/subnet en firewall regels definiëren om netwerk verkeer te beveiligen.

**Welke firewall instellingen zijn beschikbaar voor mijn AVS-persoonlijke Clouds?**

U kunt regels voor Noord-Zuid-en Oost-West-verkeer configureren. De regels worden gedefinieerd in een firewall tabel. De firewall tabel kan worden gekoppeld aan VLAN'S in de Privécloud van uw AVS. Zie voor meer informatie [firewall tabellen en-regels instellen voor persoonlijke Clouds van AVS](firewall.md).

**Kan ik open bare IP-adressen toewijzen aan virtuele machines in mijn AVS Privécloud?**

In de AVS-Portal kunt u een nieuw openbaar IP-adres toewijzen en dit koppelen aan het privé-IP-adres van een virtuele machine of een apparaat. U kunt ook nieuwe firewall regels maken of bestaande firewall regels Toep assen om verkeer toe te staan van specifieke poorten en IP-adressen in de portal. Zie voor meer informatie [open bare IP-adressen toewijzen voor de privécloud-cloud omgeving](public-ips.md).

## <a name="security"></a>Beveiliging

**Wat zijn mijn beveiligings opties voor AVS?**

AVS biedt de volgende beveiligings functies voor het beveiligen van uw cloud omgeving in de Privécloud:

* **Data-at-rest-versleuteling**. U kunt gegevens in rust versleutelen die zich op de vSAN-opslag in de Privécloud van uw AVS bevinden. vSAN ondersteunt externe-sleutel beheerser vers, die kunnen worden geïmplementeerd in uw Azure vNet-of on-premises omgeving. Zie [vSAN-versleuteling configureren voor de privécloud van uw AVS](vsan-encryption.md)voor meer informatie.
* **Netwerk beveiliging**. Beheer netwerk verkeer met firewall regels die van toepassing zijn tussen uw automatische AVS-Cloud en Internet, uw automatische AVS-Cloud en on-premises omgeving, of binnen subnetten van de Privécloud van uw AVS.
* **Beveiligde, particuliere verbinding**. Er wordt een beveiligde, persoonlijke verbinding tot stand gebracht tussen uw on-premises netwerk en uw Azure-abonnement.

## <a name="compute"></a>Computing

**Wat voor soort hosts zijn er beschikbaar?**

AVS biedt deze typen host:

* **CS28-knoop punt:** CPU: 2x 2,2 GHz, totaal 28 kern geheugens, 48 HT.  RAM: 256 GB.  Opslag: 1600 GB NVMe-cache, 5760 GB gegevens (alle-Flash). Netwerk: 4x25Gbe NIC
* **CS36-knoop punt:** CPU 2x 2,3 GHz, totale 36 kern geheugens 72 HT.  RAM: 512 GB.  Opslag: 3200 GB NVMe cache 11520 GB gegevens (alle-Flash).  Netwerk: 4x25Gbe NIC
* **CS36m-knoop punt:** CPU 2x 2,3 GHz, totale 36 kern geheugens 72 HT.  RAM: 576 GB.  Opslag: 3200 GB NVMe cache 13360 GB gegevens (alle-Flash).  Netwerk: 4x25Gbe NIC

**Hoe worden hardwarestoringen verwerkt?**

De AVS-infra structuur wordt voortdurend bewaakt door het AVS-platform en onze Service Operations-teams. Als er een hardwarefout wordt gedetecteerd, wordt een nieuw knoop punt toegevoegd aan de Privécloud en wordt het knoop punt met de fout verwijderd.

## <a name="storage"></a>Storage

**Welk type opslag wordt ondersteund op een AVS-Privécloud?**

AVS biedt alle Flash VMware vSAN-opslag voor elke AVS-privécloud. Elke vSphere wordt gemaakt met een eigen vSAN-gegevens opslag. Zie voor meer informatie [AVS Privécloud Private Cloud VMware-onderdelen-vSAN-opslag](vmware-components.md#vsan-storage).

**Wordt versleuteling van gegevens ondersteund?**
Ja. U kunt de vSAN-opslag op uw AVS-Privécloud instellen voor het gebruik van een KMS (Key Management Server) die on-premises of in Azure is geïmplementeerd om gegevens te versleutelen die zijn opgeslagen op vSAN.

**Hoe worden defecte schijven afgehandeld?**

AVS bewaakt voortdurend alle hardwareonderdelen van de automatische AVS-Cloud. Als er een schijf fout wordt gedetecteerd of een schijf wordt geïdentificeerd als mislukt (op basis van heuristiek), wordt er automatisch een nieuw knoop punt toegevoegd aan de nieuwe AVS-Cloud. Het knoop punt met de mislukte of niet-werkende schijf wordt verwijderd uit de Privécloud.

## <a name="vmware"></a>VMware

**Hoe kan ik grootschalige upload-of migratie van toepassingen en gegevens van on-premises uitvoeren?**

AVS biedt een systeem eigen VMware vSphere-oplossing. Alle VMware-hulpprogram ma's voor het bulksgewijs migreren van gegevens kunnen worden gebruikt in combi natie met de Privécloud van uw AVS. Een aantal opties:

* VMware HCX voor het bulksgewijs migreren van gegevens.
* Koud migreren van gegevens met behulp van Storage vMotion van on-premises naar AVS.

**Kan ik VMware-hulpprogram ma's installeren?**

AVS biedt een systeem eigen VMware vSphere-oplossing. Alle VMware-hulpprogram ma's die worden gebruikt voor het beheren van uw on-premises vSphere-omgeving, kunnen worden gebruikt op AVS. AVS ondersteunt een model voor het maken van een eigen licentie (BYOL) voor het installeren van VMware-hulpprogram ma's.

**Hoe worden updates en upgrades beheerd?**

Met AVS worden alle onderdelen van de infra structuur van uw AVS-Privécloud op een naadloze, niet-storende manier beheerd en bijgewerkt. Alle updates en beveiligings patches die zijn uitgegeven door VMware of infrastructuur leveranciers, worden gepland voor een update zodra ze door AVS worden gekwalificeerd.

AVS voert geen upgrades of updates van toepassingen uit die zijn geïnstalleerd op de AVS-Privécloud.

## <a name="azure-integration"></a>Azure-integratie

**Welke Azure-Services worden ondersteund?**

AVS biedt een Azure ExpressRoute-verbinding met uw abonnement op Azure. Alle services die in uw abonnement worden uitgevoerd, kunnen verbinding maken met uw Privécloud. Voorbeelden:

* **Azure Active Directory** als een identiteits bron voor uw AVS vCenter.
* **Azure Storage** voor het opslaan van back-ups, afbeeldingen en andere gegevens vanuit de privécloud van uw AVS.
* **Hybride toepassingen** met een toepassings architectuur die persoonlijke Clouds van open bare en AVS omspant. U kunt bijvoorbeeld webservers maken in azure die toegang hebben tot toepassings-en database servers op uw AVS-Privécloud.
* **Azure monitor** en **Azure Security Center** voor workloads die worden uitgevoerd in de VMware-ondersteunings registratie, metrische gegevens over prestaties en beveiligings beheer.

**Hoe kan ik mijn VMware-tenants aan Azure toe te wijzen?**

AVS biedt de unieke mogelijkheid om uw VMware-Vm's te beheren in de Privécloud van de Azure Portal. Een vCenter-resource groep die is geconfigureerd met de gewenste resource beperkingen, kan door de globale beheerder aan uw abonnement worden toegewezen. 

**Welke licentie voordelen krijg ik bij Azure?**

Met AVS kunt u profiteren van het voor deel van Azure Hybrid Usage en bespaart u tot 90% op licenties. Dit voor deel blijft uw investering in micro soft-licenties behouden en verlaagt uw TCO ten opzichte van andere cloud oplossingen. U krijgt ook uitgebreide beveiligings updates voor Windows Server 2008 en Microsoft SQL Server 2008. Met het model van uw eigen licentie (BYOL) kunt u kosten besparen voor algemene apps, zoals Veeam en Zerto. 
