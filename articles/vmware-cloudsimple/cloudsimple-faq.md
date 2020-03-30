---
title: Veelgestelde vragen - Azure VMware-oplossing door CloudSimple
description: Veelgestelde vragen voor Azure VMware Solution van CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025058"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Veelgestelde vragen over VMware Solution van CloudSimple

## <a name="cloudsimple-service"></a>CloudSimple-service

**Wat is Azure VMware Solution by CloudSimple?**

Azure VMware Solution by CloudSimple transformeert en breidt VMware-workloads binnen enkele minuten uit naar private, dedicated clouds op Azure. CloudSimple zorgt voor het inrichten, beheren van de infrastructuur en het orkestreren van workloads tussen on-premises en Azure. Omdat uw apps exact hetzelfde on-premises en in Azure draaien, profiteert u van de elasticiteit en services van de cloud zonder de complexiteit van het opnieuw ontwerpen van uw apps. CloudSimple verlaagt uw totale eigendomskosten met een cloudverbruiksmodel dat on-demand provisioning, pay-as-you-grow en capaciteitsoptimalisatie biedt.  Zie [Wat is VMware-oplossing op Azure by CloudSimple](cloudsimple-vmware-solutions-overview.md) voor functies, voordelen en scenario's.

**Wat is een CloudSimple Private Cloud?**

Een CloudSimple Private Cloud is een private, dedicated cloud die bestaat uit een krachtige compute-, opslag- en netwerkomgeving die wordt geïmplementeerd op Microsoft Azure-infrastructuur (hardware- en datacenterruimte) op Azure-locaties.  Een Private Cloud biedt een native VMware 'platform as a service'. In VMware-termen bevat elke Private Cloud precies één exemplaar van de vCenter-server. De vCenter Server beheert meerdere ESXi-knooppunten in een of meer vSphere-clusters, samen met de bijbehorende Virtual SAN-opslag (vSAN). Een CloudSimple-service kan meerdere Private Clouds bevatten in uw Azure-abonnement.  Zie [Overzicht van Private Cloud](cloudsimple-private-cloud.md)voor meer informatie.

**Waar is cloudsimple-service beschikbaar?**

CloudSimple is beschikbaar in oost-AMERIKAANSE, West-Amerikaanse en West-Europese regio's met extra regio's binnenkort.

**Hoe schakel ik mijn abonnement voor CloudSimple in?**

U contact opnemen [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) met uw Microsoft-accountvertegenwoordiger om uw abonnement voor de CloudSimple-service in te schakelen. Geef uw abonnements-ID op in de e-mail waarvoor u cloudsimple-service wilt inschakelen.  

**Hoe krijg ik toegang tot de CloudSimple-portal?**

U hebt toegang tot de CloudSimple-portal vanuit de Azure-portal.  Zie Toegang [tot de VMware-oplossing door CloudSimple-portal vanaf de Azure-portal](access-cloudsimple-portal.md)voor meer informatie.

**Hoe verhoog ik de capaciteit voor een Private Cloud?**

Als u de capaciteit wilt vergroten, koopt u extra knooppunten uit de Azure-portal en gebruikt u de knooppunten om uw Private Cloud uit te breiden vanuit de CloudSimple-portal.  U extra knooppunten toevoegen aan een bestaand vSphere-cluster of deze toevoegen aan een nieuw vSphere-cluster.  Zie Een [CloudSimple Private Cloud uitvouwen](expand-private-cloud.md)voor meer informatie.

**Wat gebeurt er met mijn Private Cloud tijdens het onderhoud?**

CloudSimple biedt een melding enkele dagen voorafgaand aan een gepland onderhoudsinterval.  Onderhoud gebeurt op een niet-storende manier om de beschikbaarheid van uw Private Cloud te garanderen.  Onderhoud kan van de volgende typen zijn:

* **CloudSimple-infrastructuur**.  CloudSimple-infrastructuur is ontworpen om zeer beschikbaar te zijn.  Tijdens dit type onderhoudsinterval worden redundante componenten één voor één bijgewerkt om onderbreking van de service te voorkomen. U behoudt toegang tot uw Private Cloud vCenter, alle virtuele machines, de internetverbinding van uw Private Cloud en verbindingen met on-premises of Azure.
* **CloudSimple portal**. Tijdens dit type onderhoudsinterval kunnen sommige functies op de CloudSimple-portal zijn uitgeschakeld of ontoegankelijk zijn.  De melding voorafgaand aan het onderhoudsinterval bevat details over functiebeperkingen tijdens het onderhoud.

## <a name="connectivity"></a>Connectiviteit

**Wat zijn mijn connectiviteitsopties voor het CloudSimple-regionetwerk?**

CloudSimple biedt de volgende connectiviteitsopties om verbinding te maken met uw CloudSimple-regionetwerk. Meerdere opties kunnen tegelijkertijd worden gebruikt.

* **ExpressRoute-verbinding van uw on-premises datacenter naar cloudeenvoudig regionetwerk.** Dit is een snelle, lage latentie, veilige privéverbinding die gebruikmaakt van Global Reach om uw on-premises ExpressRoute-circuit te overbruggen naar uw CloudSimple ExpressRoute-circuit. Zie [Verbinding maken van on-premises naar CloudSimple met ExpressRoute](on-premises-connection.md)voor instructies over het instellen van de verbinding.
* **ExpressRoute-verbinding van uw Azure-virtuele netwerk met uw CloudSimple-regionetwerk.** Dit is een snelle, lage latentie, veilige privéverbinding die gebruikmaakt van virtuele netwerkgateways om uw virtuele netwerk op Azure te overbruggen naar uw CloudSimple ExpressRoute-circuit. Zie [Uw CloudSimple Private Cloud-omgeving verbinden met het virtuele Azure-netwerk via ExpressRoute](azure-expressroute-connection.md)voor instructies over het instellen van de verbinding.
* **Site-to-Site VPN-verbinding van uw on-premises datacenter naar uw CloudSimple regionetwerk.** Dit is een veilig virtueel privénetwerk van uw on-premises VPN-apparaat naar uw CloudSimple Private Cloud-regio.  Zie [VPN-gateways instellen op cloudeenvoudignetwerk](vpn-gateway.md)voor meer informatie.

**Hoe maak ik verbinding met een Private Cloud?**

U details van uw Private Cloud bekijken in de CloudSimple-portal. Als u verbinding wilt maken met het vCenter dat overeenkomt met uw Private Cloud, controleert u eerst of er een netwerkverbinding tot stand is gekomen met behulp van Site-to-Site VPN, Point-to-Site VPN of ExpressRoute. Start vervolgens de CloudSimple-portal vanaf de Azure-portal en klik op **VSphere-client starten** op de startpagina of op de pagina Met details van de privécloud.

**Wat is het voordeel van ExpressRoute circuits?**

Een Azure ExpressRoute-circuit is een snelle, lage latentie, beveiligde verbinding.  CloudSimple biedt een speciaal ExpressRoute-circuit per regio per klant.  Met dit circuit u een beveiligde verbinding tot stand brengen vanaf on-premises of uw Azure-abonnement.

**Wat zijn de netwerkkosten om verbinding te maken met CloudSimple?  Zijn er uitgaande kosten van toepassing tussen CloudSimple en Azure, of tussen regio's?**

Er zijn geen CloudSimple-kosten voor netwerkuitgangen.  Azure-standaardtarieven zijn van toepassing op elk uitgangsverkeer van uw virtuele netwerk of vanaf uw on-premises ExpressRoute-circuit.

## <a name="networking"></a>Netwerken

**Welke netwerkfuncties zijn beschikbaar voor mijn Private Cloud?**

U VLAN's (en hun subnetten) en firewalltabellen inrichten en openbare IP-adressen toewijzen die worden toegewezen aan een virtuele machine die in uw private cloud wordt uitgevoerd. Zie VOOR meer informatie over netwerkfuncties het [overzicht van VLAN's en subnetten](cloudsimple-vlans-subnets.md), [overzicht van firewalltabellen](cloudsimple-firewall-tables.md)en [overzicht van openbare IP-adressen](cloudsimple-public-ip-address.md).

**Hoe stel ik verschillende subnetten in voor mijn applicaties in mijn Private Cloud?**

U maakt VLAN's op uw Private Cloud via de CloudSimple-portal.  Nadat u een VLAN hebt gemaakt, u een gedistribueerde poortgroep op uw Private Cloud vCenter maken met behulp van de VLAN en virtuele machines maken die zijn verbonden met de gedistribueerde poortgroep.  U firewalltabellen voor het VLAN/subnet inschakelen en firewallregels definiëren om netwerkverkeer te beveiligen.

**Welke firewall-instellingen zijn beschikbaar voor mijn Private Clouds?**

U regels configureren voor noord-zuid- en oost-westverkeer.  De regels worden gedefinieerd in een firewalltabel.  De firewalltabel kan worden gekoppeld aan VLAN's op uw private cloud.  Zie [Firewalltabellen en regels voor privéwolken instellen voor](firewall.md)meer informatie.

**Kan ik openbare IP-adressen toewijzen aan VM's in mijn Private Cloud-omgeving?**

In de CloudSimple-portal u een nieuw openbaar IP-adres toewijzen en koppelen aan het privé-IP-adres van een virtuele machine of een toestel.  U ook nieuwe firewallregels maken of bestaande firewallregels toepassen om verkeer van specifieke poorten en IP-adressen in de portal toe te staan. Zie Openbare [IP-adressen toewijzen voor private cloudomgeving voor](public-ips.md)meer informatie .

## <a name="security"></a>Beveiliging

**Wat zijn mijn beveiligingsopties op CloudSimple?**

CloudSimple biedt de volgende beveiligingsfuncties voor het beveiligen van uw Private Cloud-omgeving:

* **Gegevens in rustversleuteling**. U gegevens in rust versleutelen die zich bevinden in de vSAN-opslag in uw private cloud. vSAN ondersteunt externe sleutelbeheerservers, die kunnen worden geïmplementeerd in uw Azure vNet- of on-premises omgeving.  Zie [VSAN-versleuteling configureren voor uw CloudSimple Private Cloud voor](vsan-encryption.md)meer informatie.
* **Netwerkbeveiliging.** Beheer de netwerkverkeersstroom met firewallregels die van toepassing zijn tussen uw Private Cloud en het internet, uw Private Cloud en on-premises omgeving, of binnen subnetten van uw Private Cloud.
* **Veilige, privéverbinding.** Er wordt een veilige, privéverbinding tot stand gebracht tussen uw on-premises netwerk en uw Azure-abonnement.

## <a name="compute"></a>Compute

**Wat voor soort hosts zijn beschikbaar?**

CloudSimple biedt deze hosttypen:

* **CS28-knooppunt:** CPU:2x 2,2 GHz, totaal 28 cores, 48 HT.  RAM: 256 GB.  Opslag: 1600 GB NVMe-cache, 5760 GB gegevens (All-Flash). Netwerk: 4x25Gbe NIC
* **CS36-knooppunt:** CPU 2x 2,3 GHz, totaal 36 cores, 72 HT.  RAM: 512 GB.  Opslag: 3200 GB NVMe-cache met 11520 GB (All-Flash).  Netwerk: 4x25Gbe NIC
* **CS36m-knooppunt:** CPU 2x 2,3 GHz, totaal 36 cores, 72 HT.  RAM: 576 GB.  Opslag: 3200 GB NVMe-cache met 13360 GB (All-Flash).  Netwerk: 4x25Gbe NIC

**Hoe worden hardwarefouten verwerkt?**

Alle CloudSimple-infrastructuur wordt continu bewaakt door het CloudSimple-platform en onze serviceoperationsteams.  Als er een hardwarefout wordt gedetecteerd, wordt er een nieuw knooppunt aan uw privécloud toegevoegd en wordt het mislukte knooppunt verwijderd.

## <a name="storage"></a>Storage

**Welk type opslag wordt ondersteund op een Private Cloud?**

CloudSimple biedt all-flash VMware vSAN-opslag met elke Private Cloud.  Elke vSphere is gemaakt met een eigen vSAN datastore.  Zie Private [Cloud VMware-componenten - vSAN-opslag voor](vmware-components.md#vsan-storage)meer informatie .

**Wordt versleuteling van gegevens ondersteund?**
Ja.  U de vSAN-opslag op uw Private Cloud instellen om een key management server (KMS) te gebruiken die on-premises of op Azure wordt geïmplementeerd om gegevens die zijn opgeslagen op vSAN te versleutelen.

**Hoe worden mislukte schijven verwerkt?**

CloudSimple bewaakt continu alle hardwarecomponenten van de Private Cloud.  Als een schijffout wordt gedetecteerd of als een schijf wordt geïdentificeerd als mislukt (op basis van heuristiek), wordt er automatisch een nieuw knooppunt toegevoegd aan de Private Cloud.  Het knooppunt met de defecte of falende schijf wordt verwijderd uit de Private Cloud.

## <a name="vmware"></a>VMware

**Hoe kan ik op grote schaal toepassingen en gegevens van on-premises uploaden of migreren?**

CloudSimple biedt een native VMware vSphere-oplossing.  Alle VMware-tools voor bulkgegevensmigratie kunnen worden gebruikt met uw Private Cloud.  Een aantal opties:

* VMware HCX voor bulkmigratie van gegevens.
* Koude migratie van gegevens met Storage vMotion van on-premises naar CloudSimple.

**Kan ik VMware-tools installeren?**

CloudSimple biedt een native VMware vSphere-oplossing.  Alle VMware-tools die worden gebruikt voor het beheren van uw on-premises vSphere-omgeving, kunnen worden gebruikt op CloudSimple.  CloudSimple ondersteunt een byol-model (bring-your-own-license) voor het installeren van VMware-tools.

**Hoe worden updates en upgrades beheerd?**

CloudSimple beheert en werkt alle infrastructuuronderdelen van uw Private Cloud op een naadloze niet-storende manier.  Alle updates en beveiligingspatches die zijn uitgebracht door VMware- of infrastructuurleveranciers, worden bijgewerkt zodra ze door CloudSimple zijn gekwalificeerd.

CloudSimple voert geen upgrades of updates uit van toepassingen die zijn geïnstalleerd in de Private Cloud.

## <a name="azure-integration"></a>Azure-integratie

**Welke Azure-services worden ondersteund?**

CloudSimple biedt een Azure ExpressRoute-verbinding met uw abonnement op Azure.  Alle services die in uw abonnement worden uitgevoerd, kunnen verbinding maken met uw Private Cloud.  Voorbeelden zijn:

* **Azure Active Directory** als identiteitsbron voor uw CloudSimple vCenter.
* **Azure-opslag** voor het opslaan van back-ups, afbeeldingen en andere gegevens uit uw private cloud.
* **Hybride toepassingen** met een toepassingsarchitectuur die openbare en private clouds omvat.  U bijvoorbeeld webservers in Azure maken die toegang hebben tot toepassings- en databaseservers in uw Private Cloud.
* **Azure-monitor** en **Azure-beveiligingscentrum** voor workloads die worden uitgevoerd op VMware-ondersteuningslogboekregistratie, prestatiestatistieken en beveiligingsbeheer.

**Hoe stel ik mijn VMware-tenants in kaart met Azure?**

CloudSimple biedt de unieke mogelijkheid om uw VMware VM's op Private Cloud te beheren vanuit de Azure-portal.  Een vCenter-resourcegroep die is geconfigureerd met de gewenste resourcebeperkingen, kan door uw globale beheerder aan uw abonnement worden toegewezen.  

**Welke licentievoordelen krijg ik met Azure?**

Met CloudSimple u profiteren van het Azure Hybrid Usage Benefit en tot 90% besparen op licenties. Dit voordeel behoudt uw investering in Microsoft-licenties en verlaagt uw TCO ten opzichte van andere cloudoplossingen. U krijgt ook uitgebreide beveiligingsupdates voor Windows Server 2008 en Microsoft SQL Server 2008.  Het BYOL-model (Bring-your-own-license) helpt u de kosten laag te houden voor veelgebruikte apps zoals Veeam en Zerto.  
