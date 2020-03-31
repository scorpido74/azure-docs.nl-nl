---
title: Over noodherstel voor on-premises apps met Azure Site Recovery
description: Hierin worden de workloads beschreven die met behulp van herstel na noodgevallen kunnen worden beveiligd met de service Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062843"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Over herstel na noodgevallen voor on-premises apps

In dit artikel worden on-premises workloads en apps beschreven die u beschermen voor herstel na noodgevallen met de [Azure Site Recovery-service.](site-recovery-overview.md)

## <a name="overview"></a>Overzicht

Organisaties hebben een BCDR-strategie (Business Continuity and Disaster Recovery) nodig om workloads en gegevens veilig en beschikbaar te houden tijdens geplande en ongeplande downtime. En herstellen van de normale werkomstandigheden.

Site Recovery is een Azure-service die deel uitmaakt van uw BCDR-strategie. Met Site Recovery kunt u de toepassingsbewuste replicatie naar de cloud of naar een secundaire site implementeren. U Siteherstel gebruiken om replicatie te beheren, noodhersteltests uit te voeren en failovers en failback uit te voeren. Uw apps kunnen worden uitgevoerd op Windows- of Linux-computers, fysieke servers, VMware of Hyper-V.

Site recovery integreert met Microsoft-toepassingen zoals SharePoint, Exchange, Dynamics, SQL Server en Active Directory. Microsoft werkt nauw samen met toonaangevende leveranciers, waaronder Oracle, SAP en Red Hat. U kunt replicatie-oplossingen per app aanpassen.

## <a name="why-use-site-recovery-for-application-replication"></a>Waarom u Site Recovery moet gebruiken voor het repliceren van toepassingen

Site Recovery biedt als volgt beveiliging op toepassingsniveau en herstel:

- App-agnostisch en biedt replicatie voor alle workloads die op een ondersteunde machine worden uitgevoerd.
- Bijna-synchrone replicatie, met herstelpuntdoelstellingen (RPO) tot 30 seconden om te voldoen aan de behoeften van de meeste kritieke zakelijke apps.
- Toepassingsconsistente momentopnamen voor toepassingen met één of meer lagen.
- Integratie met SQL Server AlwaysOn en partnerschap met andere replicatietechnologieën op toepassingsniveau. Bijvoorbeeld Active Directory-replicatie, SQL AlwaysOn en Exchange Database Availability Groups (DAGs).
- Flexibele herstelplannen waarmee u met één klik een volledige toepassingsstack herstellen en externe scripts en handmatige acties in het plan opnemen.
- Geavanceerd netwerkbeheer in Site Recovery en Azure om de vereisten voor app-netwerk te vereenvoudigen. Netwerkbeheer, zoals de mogelijkheid om IP-adressen te reserveren, load-balancing te configureren en integratie met Azure Traffic Manager voor rto-netwerkomschakelingen (low recovery time objectives).
- Een uitgebreide Automation-bibliotheek met toepassingsspecifieke scripts die klaar zijn voor gebruik, en kunnen worden gedownload en geïntegreerd met herstelplannen.

## <a name="workload-summary"></a>Workloadoverzicht

Met Site Recovery kan elke app die wordt uitgevoerd op een ondersteunde machine, worden gerepliceerd. We werken samen met productteams om extra tests uit te voeren voor de apps die in de volgende tabel zijn opgegeven.

| **Workload** |**Virtuele Azure-machines repliceren naar Azure** |**Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Vm's vmware repliceren naar Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Ja |Ja |Ja |Ja |Ja|
| Web-apps (IIS, SQL) |Ja |Ja |Ja |Ja |Ja|
| System Center Operations Manager |Ja |Ja |Ja |Ja |Ja|
| SharePoint |Ja |Ja |Ja |Ja |Ja|
| SAP<br/><br/>SAP-site repliceren naar Azure voor niet-cluster |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft)|
| Exchange (niet-DAG) |Ja |Ja |Ja |Ja |Ja|
| Extern bureaublad/VDI |Ja |Ja |Ja |Ja |Ja|
| Linux (besturingssysteem en apps) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft)|
| Dynamics AX |Ja |Ja |Ja |Ja |Ja|
| Windows-bestandsserver |Ja |Ja |Ja |Ja |Ja|
| Citrix XenApp en XenDesktop |Ja|N.v.t. |Ja |N.v.t. |Ja |

## <a name="replicate-active-directory-and-dns"></a>Active Directory en DNS repliceren

Voor de meeste zakelijke apps zijn een Active Directory- en DNS-infrastructuur essentieel. Tijdens noodherstel moet u deze infrastructuuronderdelen beschermen en herstellen voordat u workloads en apps herstelt.

U kunt Site Recovery gebruiken om een plan voor volledig geautomatiseerd noodherstel te maken voor Active Directory en DNS. Als u bijvoorbeeld wilt falen via SharePoint en SAP van een primaire naar een secundaire site, u een herstelplan instellen dat eerst mislukt via Active Directory. Gebruik vervolgens een extra app-specifiek herstelplan om te mislukken ten opzichte van de andere apps die afhankelijk zijn van Active Directory.

[Meer informatie](site-recovery-active-directory.md) over noodherstel voor Active Directory en DNS.

## <a name="protect-sql-server"></a>SQL Server beveiligen

SQL Server biedt een basis voor gegevensservices voor veel zakelijke apps in een on-premises datacenter. Siteherstel kan worden gebruikt met SQL Server HA/DR-technologieën om multi-tiered enterprise-apps te beschermen die SQL Server gebruiken.

Site Recovery biedt:

- Een eenvoudige en voordelige oplossing voor herstel na noodgevallen voor SQL Server. Replicatie van meerdere versies en edities van zelfstandige SQL Server-servers en -clusters naar Azure of een secundaire site.
- Integratie met SQL AlwaysOn-beschikbaarheidsgroepen voor het beheren van failovers en failbacks met Azure Site Recovery-herstelplannen.
- End-to-endherstelplannen voor alle lagen in een toepassing, met inbegrip van de SQL Server-databases.
- Schalen van SQL Server voor piekbelastingen met Site Recovery, _door_ ze te laten barsten in grotere IaaS-virtuele machineformaten in Azure.
- Eenvoudig testen van SQL Server-noodherstel. U kunt testfailovers uitvoeren om gegevens te analyseren en om nalevingscontroles uit te voeren zonder dat dit invloed heeft op de productieomgeving.

[Meer informatie](site-recovery-sql.md) over disaster recovery voor SQL-server.

## <a name="protect-sharepoint"></a>SharePoint beveiligen

Met Azure Site Recovery kunnen SharePoint-implementaties als volgt worden beveiligd:

- Er is geen stand-byfarm voor noodherstel meer nodig, zodat u ook geen kosten meer maakt voor de bijbehorende infrastructuur. Gebruik Siteherstel om een hele farm (web-, app- en databaselagen) te repliceren naar Azure of naar een secundaire site.
- Site Recovery vereenvoudigt het beheer en de implementatie van toepassingen. Updates die op de primaire site worden geïmplementeerd, worden automatisch gerepliceerd. De updates zijn beschikbaar na failover en herstel van een boerderij in een secundaire site. Verlaagt de complexiteit van het beheer en de kosten in verband met het up-to-date houden van een stand-by farm.
- Site Recovery vereenvoudigt het ontwikkelen en testen van SharePoint-toepassingen, omdat er een op de productieomgeving lijkende replica-omgeving wordt gemaakt, die naar wens kan worden gebruikt voor testen en foutopsporing.
- Door Site Recovery te gebruiken om SharePoint-implementaties te migreren naar Azure, wordt de overstap naar de cloud vereenvoudigd.

[Meer informatie](site-recovery-sharepoint.md) over noodherstel voor SharePoint.

## <a name="protect-dynamics-ax"></a>Dynamics AX beveiligen

Met Azure Site Recovery kunt u op de volgende manier uw Dynamics AX ERP-oplossing beveiligen:

- Replicatie van uw gehele Dynamics AX-omgeving (web- en AOS-lagen, databaselagen, SharePoint) beheren naar Azure of naar een secundaire site.
- Site Recovery vereenvoudigt de migratie van Dynamics AX-implementaties naar de cloud (Azure).
- Site Recovery vereenvoudigt de ontwikkeling en het testen van Dynamics AX-toepassingen door een op de productieomgeving lijkende replica-omgeving te bieden, zodat u naar wens kunt testen en fouten kunt opsporen.

[Meer informatie](site-recovery-dynamicsax.md) over disaster recovery voor Dynamic AX.

## <a name="protect-remote-desktop-services"></a>Extern bureaublad-services beveiligen

Extern bureaublad-services (RDS) maakt virtuele desktopinfrastructuur (VDI), sessiegebaseerde desktops en toepassingen mogelijk waarmee gebruikers overal kunnen werken.

Met Azure Site Recovery u de volgende services repliceren:

- Beheerde of onbeheerde gepoolde virtuele bureaubladen repliceren naar een secundaire site.
- Externe toepassingen en sessies repliceren naar een secundaire site of Azure.

In de volgende tabel worden de replicatieopties weergegeven:

| **RDS** |**Virtuele Azure-machines repliceren naar Azure** | **Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Vm's vmware repliceren naar Azure** | **Fysieke servers repliceren naar een secundaire site** | **Fysieke servers repliceren naar Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Gepoold virtueel bureaublad (onbeheerd)** |Nee|Ja |Nee |Ja |Nee |Ja |Nee |
| **Gepoold virtueel bureaublad (beheerd en zonder UDP)** |Nee|Ja |Nee |Ja |Nee |Ja |Nee |
| **Externe toepassingen en bureaubladsessies (zonder UDP)** |Ja|Ja |Ja |Ja |Ja |Ja |Ja |

[Meer informatie](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) over noodherstel voor RDS.

## <a name="protect-exchange"></a>Exchange beveiligen

Met Site Recovery wordt Exchange als volgt beveiligd:

- Bij kleine implementaties van Exchange, met één server of met zelfstandige servers, kan Site Recovery een replicatie verzorgen en een failover uitvoeren naar Azure of een secundaire site.
- Bij grotere implementaties kan Site Recovery worden geïntegreerd met Exchange DAG’s.
- Exchange DAG's zijn de aanbevolen oplossing voor Exchange-noodherstel binnen grote bedrijven.  De Site Recovery-herstelplannen kunnen DAG’s bevatten, zodat er DAG-failovers kunnen worden uitgevoerd tussen verschillende sites.

Zie [Exchange DAGs](/Exchange/high-availability/database-availability-groups/database-availability-groups) en [Exchange disaster recovery](/Exchange/high-availability/disaster-recovery/disaster-recovery)voor meer informatie over noodherstel voor Exchange.

## <a name="protect-sap"></a>SAP beveiligen

Gebruik Site Recovery om uw SAP-implementatie als volgt te beveiligen:

- Schakel beveiliging van SAP NetWeaver en niet-NetWeaver-productietoepassingen die on-premises worden uitgevoerd in door onderdelen naar Azure te repliceren.
- Schakel beveiliging van SAP NetWeaver en niet-NetWeaver-productietoepassingen die in Azure worden uitgevoerd in door onderdelen naar een ander Azure-datacenter te repliceren.
- Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om uw SAP-implementatie te migreren naar Azure.
- Vereenvoudig SAP-projectupgrades, tests en het maken van prototypen door een on-demand een productiekloon te maken voor het testen van SAP-toepassingen.

[Meer informatie](site-recovery-sap.md) over disaster recovery voor SAP.

## <a name="protect-internet-information-services"></a>Internetinformatieservices beveiligen

Gebruik Siterecovery om uw IIS-implementatie (Internet Information Services) als volgt te beschermen:

Azure Site Recovery biedt herstel na noodgevallen door de belangrijke onderdelen in uw omgeving te repliceren naar een koude externe site of een openbare cloud, zoals Microsoft Azure. Aangezien de virtuele machines met de webserver en de database worden gerepliceerd naar de herstelsite, is er geen vereiste voor een aparte back-up voor configuratiebestanden of certificaten. De toepassingstoewijzingen en -bindingen die afhankelijk zijn van omgevingsvariabelen die na failover zijn gewijzigd, kunnen worden bijgewerkt via scripts die zijn geïntegreerd in de plannen voor herstel na noodgevallen. Virtuele machines worden alleen tijdens een failover op de herstelsite opgehaald. Azure Site Recovery helpt u ook bij het orkestreren van de end-to-end failover door u de volgende mogelijkheden te bieden:

- Sequentiëring van het afsluiten en opstarten van virtuele machines in de verschillende categorieën.
- Scripts toevoegen om updates van toepassingsafhankelijkheden en bindingen op de virtuele machines mogelijk te maken nadat ze zijn gestart. De scripts kunnen ook worden gebruikt om de DNS-server zo bij te werken dat deze naar de herstelsite wijst.
- Wijs IP-adressen toe aan virtuele machines pre-failover door de primaire en herstelnetwerken in kaart te brengen en scripts te gebruiken die niet hoeven te worden bijgewerkt na failover.
- Mogelijkheid voor een failover met één klik voor meerdere webtoepassingen die de ruimte voor verwarring tijdens een ramp elimineert.
- De mogelijkheid om de herstelplannen in een geïsoleerde omgeving te testen voor details voor DR.

[Meer informatie](site-recovery-iis.md) over noodherstel voor IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp en XenDesktop beveiligen

Gebruik Site Recovery om de Citrix XenApp- en XenDesktop-implementaties te beveiligen. Dit werkt als volgt:

- Bescherming van de Citrix XenApp- en XenDesktop-implementatie mogelijk maken. Repliceer de verschillende implementatielagen naar Azure: Active Directory, DNS-server, SQL-databaseserver, Citrix Delivery Controller, StoreFront-server, XenApp Master (VDA), Citrix XenApp License Server.
- Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om de Citrix XenApp- en XenDesktop-implementaties te migreren naar Azure.
- Vereenvoudig Citrix XenApp-/XenDesktop-tests door een op de productieomgeving lijkende replica-omgeving op aanvraag te maken voor tests en foutopsporing.
- Deze oplossing is alleen van toepassing op virtuele Windows Server-desktops en niet op virtuele clientdesktops. Client virtuele desktops worden nog niet ondersteund voor licenties in Azure. [Meer informatie](https://azure.microsoft.com/pricing/licensing-faq/) over licentieverlening voor clientbureaubladen en serverdesktops in Azure.

[Meer informatie](site-recovery-citrix-xenapp-and-xendesktop.md) over disaster recovery voor Citrix XenApp- en XenDesktop-implementaties. U ook verwijzen naar de [Citrix whitepaper.](https://aka.ms/citrix-xenapp-xendesktop-with-asr)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](azure-to-azure-quickstart.md) over noodherstel voor een Azure VM.
