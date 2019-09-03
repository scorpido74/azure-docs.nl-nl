---
title: Welke workloads kunt u met Azure Site Recovery beveiligen? | Microsoft Docs
description: Hierin worden de workloads beschreven die met behulp van herstel na noodgevallen kunnen worden beveiligd met de service Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: raynew
ms.openlocfilehash: f3ff6e5e05cab9aab5257d810c6785e7691bae45
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232186"
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Welke workloads kunt u met Azure Site Recovery beveiligen?

In dit artikel worden werk belastingen en toepassingen beschreven die u kunt beveiligen voor herstel na nood gevallen met de [Azure site Recovery](site-recovery-overview.md) -service.



## <a name="overview"></a>Overzicht

Organisaties hebben een BCDR-strategie (strategie voor bedrijfscontinuïteit en noodherstel) nodig om workloads en gegevens veilig en beschikbaar te houden tijdens geplande en ongeplande uitval en om ervoor te zorgen dat deze zo snel mogelijk worden hersteld naar een normale manier van functioneren.

Site Recovery is een Azure-service die deel uitmaakt van uw BCDR-strategie. Met Site Recovery kunt u de toepassingsbewuste replicatie naar de cloud of naar een secundaire site implementeren. U kunt Site Recovery gebruiken om replicatie, noodhersteltests, failovers en failbacks uit te voeren. Hierbij maakt het niet uit of u Windows-apps of Linux-apps gebruikt, en of deze worden uitgevoerd op fysieke servers of op virtuele VMware- of Hyper-V-machines.

Site Recovery kan worden geïntegreerd met diverse Microsoft-toepassingen, waaronder SharePoint, Exchange, Dynamics, SQL Server en Active Directory. Microsoft werkt ook nauw samen met toonaangevende leveranciers zoals Oracle, SAP en Red Hat. U kunt replicatie-oplossingen per app aanpassen.

## <a name="why-use-site-recovery-for-application-replication"></a>Waarom u Site Recovery moet gebruiken voor het repliceren van toepassingen

Site Recovery biedt als volgt beveiliging op toepassingsniveau en herstel:

* Toepassingsonafhankelijk, met replicatie voor alle workloads die worden uitgevoerd op een ondersteunde machine.
* Bijna-synchrone replicatie met RPO’s van slechts 30 seconden. Hiermee wordt voldaan aan de behoeften van zelfs de meest kritieke zakelijke apps.
* Toepassingsconsistente momentopnamen voor toepassingen met één of meer lagen.
* Integratie met SQL Server AlwaysOn en samen werking met andere replicatie technologieën op toepassings niveau, waaronder AD-replicatie, SQL AlwaysOn, Exchange data base Availability groups (Dag's).
* Flexibele herstelplannen waarmee u een volledige reeks toepassingen kunt herstellen met één klik. U kunt in uw plan ook externe scripts en handmatige acties opnemen.
* Geavanceerd netwerkbeheer in Site Recovery en Azure om de netwerkvereisten voor apps te vereenvoudigen. Dit omvat onder andere de mogelijkheid om IP-adressen te reserveren, om load balancing te configureren en om te integreren met Azure Traffic Manager, zodat u met een laag RTO kunt schakelen tussen netwerken.
* Een uitgebreide Automation-bibliotheek met toepassingsspecifieke scripts die klaar zijn voor gebruik, en kunnen worden gedownload en geïntegreerd met herstelplannen.

## <a name="workload-summary"></a>Workloadoverzicht
Met Site Recovery kan elke app die wordt uitgevoerd op een ondersteunde machine, worden gerepliceerd. Daarnaast hebben we samen met product teams extra tests uitgevoerd voor de apps die in de tabel zijn opgegeven.

| **Workload** |**Virtuele Azure-machines repliceren naar Azure** |**Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Virtuele VMware-machines repliceren naar Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |J |J |J |J |J|
| Web-apps (IIS, SQL) |J |J |J |J |J|
| System Center Operations Manager |J |J |J |J |J|
| SharePoint |J |J |J |J |J|
| SAP<br/><br/>SAP-site repliceren naar Azure voor niet-cluster |J (door Microsoft getest) |J (door Microsoft getest) |J (door Microsoft getest) |J (door Microsoft getest) |J (door Microsoft getest)|
| Exchange (niet-DAG) |J |J |J |J |J|
| Extern bureaublad/VDI |J |J |J |J |J|
| Linux (besturingssysteem en apps) |J (door Microsoft getest) |J (door Microsoft getest) |J (door Microsoft getest) |J (door Microsoft getest) |J (door Microsoft getest)|
| Dynamics AX |J |J |J |J |J|
| Windows-bestandsserver |J |J |J |J |J|
| Citrix XenApp en XenDesktop |J|N/A |J |N/A |J |

## <a name="replicate-active-directory-and-dns"></a>Active Directory en DNS repliceren
Voor de meeste zakelijke apps zijn een Active Directory- en DNS-infrastructuur essentieel. Tijdens het herstel na noodgevallen moet u deze infrastructuuronderdelen beveiligen en herstellen voordat u uw workloads en apps herstelt.

U kunt Site Recovery gebruiken om een plan voor volledig geautomatiseerd noodherstel te maken voor Active Directory en DNS. Als u bijvoorbeeld voor SharePoint en SAP een failover wilt uitvoeren van een primaire naar een secundaire site, kunt u een herstelplan opstellen waarin eerst een failover wordt uitgevoerd voor Active Directory, met een aanvullend toepassingsspecifiek herstelplan om daarna een failover uit te voeren voor de apps die afhankelijk zijn van Active Directory.

[Meer informatie](site-recovery-active-directory.md) over het beveiligen van Active Directory en DNS.

## <a name="protect-sql-server"></a>SQL Server beveiligen
SQL Server biedt een Data Services-basis voor Data Services voor vele zakelijke apps in een on-premises datacenter.  Site Recovery kan in combinatie met SQL Server HA-/DR-technologieën worden gebruikt om zakelijke apps met meerdere lagen te beveiligen die gebruikmaken van SQL Server. Site Recovery biedt:

* Een eenvoudige en voordelige oplossing voor herstel na noodgevallen voor SQL Server. Replicatie van meerdere versies en edities van zelfstandige SQL Server-servers en -clusters naar Azure of een secundaire site.  
* Integratie met SQL AlwaysOn-beschikbaarheidsgroepen voor het beheren van failovers en failbacks met Azure Site Recovery-herstelplannen.
* End-to-endherstelplannen voor alle lagen in een toepassing, met inbegrip van de SQL Server-databases.
* Het schalen van SQL Server voor tijdelijk grote workloads met Site Recovery door ze door te sturen naar grotere virtuele IaaS-machines in Azure.
* Eenvoudig testen van SQL Server-noodherstel. U kunt testfailovers uitvoeren om gegevens te analyseren en om nalevingscontroles uit te voeren zonder dat dit invloed heeft op de productieomgeving.

[Meer informatie](site-recovery-sql.md) over het beveiligen van SQL Server.

## <a name="protect-sharepoint"></a>SharePoint beveiligen
Met Azure Site Recovery kunnen SharePoint-implementaties als volgt worden beveiligd:

* Er is geen stand-byfarm voor noodherstel meer nodig, zodat u ook geen kosten meer maakt voor de bijbehorende infrastructuur. Met Site Recovery kunt u een volledige farm (met web-, app- en databaselagen) repliceren naar Azure of naar een secundaire site.
* Site Recovery vereenvoudigt het beheer en de implementatie van toepassingen. Updates die worden geïmplementeerd op de primaire site, worden automatisch gerepliceerd en zijn dus na uitvoering van een failover en herstel van een farm beschikbaar op een secundaire site. Site Recovery vermindert bovendien de beheercomplexiteit en verlaagt de kosten die samenhangen met het up-to-date houden van een stand-byfarm.
* Site Recovery vereenvoudigt het ontwikkelen en testen van SharePoint-toepassingen, omdat er een op de productieomgeving lijkende replica-omgeving wordt gemaakt, die naar wens kan worden gebruikt voor testen en foutopsporing.
* Door Site Recovery te gebruiken om SharePoint-implementaties te migreren naar Azure, wordt de overstap naar de cloud vereenvoudigd.

[Meer informatie](site-recovery-sharepoint.md) over het beveiligen van SharePoint.

## <a name="protect-dynamics-ax"></a>Dynamics AX beveiligen
Met Azure Site Recovery kunt u op de volgende manier uw Dynamics AX ERP-oplossing beveiligen:

* Site Recovery organiseert de replicatie van uw volledige Dynamics AX-omgeving (web- en AOS-lagen, databaselagen, SharePoint) naar Azure of een secundaire site.
* Site Recovery vereenvoudigt de migratie van Dynamics AX-implementaties naar de cloud (Azure).
* Site Recovery vereenvoudigt de ontwikkeling en het testen van Dynamics AX-toepassingen door een op de productieomgeving lijkende replica-omgeving te bieden, zodat u naar wens kunt testen en fouten kunt opsporen.

[Meer informatie](site-recovery-dynamicsax.md) over het beveiligen van Dynamic AX.

## <a name="protect-rds"></a>Extern bureaublad-services beveiligen
Met Extern bureaublad-services (ook wel RDS genoemd) worden virtuele-desktopinfrastructuren (VDI) en op sessies gebaseerde bureaubladen en toepassingen ingeschakeld, waardoor gebruikers waar dan ook kunnen werken. Met Azure Site Recovery kunt u het volgende doen:

* Beheerde of onbeheerde gepoolde virtuele bureaubladen repliceren naar een secundaire site, en externe toepassingen en sessies naar een secundaire site of Azure.

* Dit is wat u kunt repliceren:

| **RDS** |**Virtuele Azure-machines repliceren naar Azure** | **Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Virtuele VMware-machines repliceren naar Azure** | **Fysieke servers repliceren naar een secundaire site** | **Fysieke servers repliceren naar Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Gepoold virtueel bureaublad (onbeheerd)** |Nee|Ja |Nee |Ja |Nee |Ja |Nee |
| **Gepoold virtueel bureaublad (beheerd en zonder UDP)** |Nee|Ja |Nee |Ja |Nee |Ja |Nee |
| **Externe toepassingen en bureaubladsessies (zonder UDP)** |Ja|Ja |Ja |Ja |Ja |Ja |Ja |

[Herstel na noodgevallen voor RDS met Azure Site Recovery](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

[Meer informatie](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) over het beveiligen van RDS.

## <a name="protect-exchange"></a>Exchange beveiligen
Met Site Recovery wordt Exchange als volgt beveiligd:

* Bij kleine implementaties van Exchange, met één server of met zelfstandige servers, kan Site Recovery een replicatie verzorgen en een failover uitvoeren naar Azure of een secundaire site.
* Bij grotere implementaties kan Site Recovery worden geïntegreerd met Exchange DAG’s.
* Exchange DAG's zijn de aanbevolen oplossing voor Exchange-noodherstel binnen grote bedrijven.  De Site Recovery-herstelplannen kunnen DAG’s bevatten, zodat er DAG-failovers kunnen worden uitgevoerd tussen verschillende sites.

[Meer informatie](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) over het beveiligen van Exchange.

## <a name="protect-sap"></a>SAP beveiligen
Gebruik Site Recovery om uw SAP-implementatie als volgt te beveiligen:

* Schakel beveiliging van SAP NetWeaver en niet-NetWeaver-productietoepassingen die on-premises worden uitgevoerd in door onderdelen naar Azure te repliceren.
* Schakel beveiliging van SAP NetWeaver en niet-NetWeaver-productietoepassingen die in Azure worden uitgevoerd in door onderdelen naar een ander Azure-datacenter te repliceren.
* Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om uw SAP-implementatie te migreren naar Azure.
* Vereenvoudig SAP-projectupgrades, tests en het maken van prototypen door een on-demand een productiekloon te maken voor het testen van SAP-toepassingen.

[Meer informatie](site-recovery-sap.md) over het beveiligen van SAP.

## <a name="protect-iis"></a>IIS beveiligen
Gebruik Site Recovery om uw IIS-implementatie als volgt te beveiligen:

Azure Site Recovery biedt herstel na noodgevallen door de belangrijke onderdelen in uw omgeving te repliceren naar een koude externe site of een openbare cloud, zoals Microsoft Azure. Omdat de virtuele machines met de webserver en de database worden gerepliceerd naar de herstelsite, hoeft er geen afzonderlijke back-up te worden gemaakt van configuratiebestanden of certificaten. De toepassingstoewijzingen en -bindingen die afhankelijk zijn van omgevingsvariabelen die na failover zijn gewijzigd, kunnen worden bijgewerkt via scripts die zijn geïntegreerd in de plannen voor herstel na noodgevallen. Virtuele machines worden alleen in het geval van een failover naar de herstelsite gebracht. Door u de volgende mogelijkheden te bieden, helpt Azure Site Recovery daarnaast ook bij de organisatie van een complete failover:

-   Sequentiëring van het afsluiten en opstarten van virtuele machines in de verschillende categorieën.
-   Het toevoegen van scripts voor het bijwerken van toepassingsafhankelijkheden en -bindingen op de virtuele machines nadat deze zijn gestart. De scripts kunnen ook worden gebruikt om de DNS-server zo bij te werken dat deze naar de herstelsite wijst.
-   Het vóór de failover toewijzen van IP-adressen aan virtuele machines door de primaire en herstelnetwerken toe te wijzen, zodat scripts kunnen worden gebruikt die na failover niet bijgewerkt hoeven te worden.
-   De mogelijkheid van een failover met één klik voor meerdere webtoepassingen op de webservers, zodat er geen verwarring meer is in het geval van een noodgeval.
-   De mogelijkheid om de herstelplannen in een geïsoleerde omgeving te testen voor details voor DR.

[Meer informatie](https://aka.ms/asr-iis) over het beveiligen van IIS-webfarm.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp en XenDesktop beveiligen
Gebruik Site Recovery om de Citrix XenApp- en XenDesktop-implementaties te beveiligen. Dit werkt als volgt:

* Schakel beveiliging voor de Citrix XenApp- en XenDesktop-implementaties in door verschillende implementatielagen te repliceren in Azure, inclusief (AD DNS-server, SQL Database-server, Citrix Delivery Controller, StoreFront-server, XenApp Master (VDA), Citrix XenApp-licentieserver).
* Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om de Citrix XenApp- en XenDesktop-implementaties te migreren naar Azure.
* Vereenvoudig Citrix XenApp-/XenDesktop-tests door een op de productieomgeving lijkende replica-omgeving op aanvraag te maken voor tests en foutopsporing.
* Deze oplossing is alleen van toepassing op virtuele bureaubladen en virtuele niet-clientbureaubladen in een Windows Server-besturingssysteem. Dit komt omdat voor virtuele clientbureaubladen licentieverlening nog niet wordt ondersteund in Azure.
[Meer informatie](https://azure.microsoft.com/pricing/licensing-faq/) over licentieverlening voor clientbureaubladen en serverdesktops in Azure.

[Meer informatie](site-recovery-citrix-xenapp-and-xendesktop.md) over het beveiligen van Citrix XenApp- en XenDesktop-implementaties. U kunt ook [dit technische document van Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr) raadplegen, waarin deze informatie eveneens is opgenomen.

## <a name="next-steps"></a>Volgende stappen

[Aan de slag](azure-to-azure-quickstart.md) met Azure-VM-replicatie.
