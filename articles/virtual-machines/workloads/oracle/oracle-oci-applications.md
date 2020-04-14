---
title: Architecturen om Oracle-apps te implementeren op Azure Virtual Machines | Microsoft Documenten
description: Toepassingsarchitecturen voor het implementeren van Oracle-apps, waaronder E-Business Suite, JD Edwards EnterpriseOne en PeopleSoft op virtuele Microsoft Azure-machines met databases in Azure of in Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: mimckitt
ms.custom: ''
ms.openlocfilehash: 20e751b322d06ac176ee5c634d92e0efe874baac
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263298"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architecturen om Oracle-toepassingen op Azure te implementeren

Microsoft en Oracle hebben samengewerkt om klanten in staat te stellen Oracle-toepassingen zoals Oracle E-Business Suite, JD Edwards EnterpriseOne en PeopleSoft in de cloud te implementeren. Met de introductie van de [preview private network-interconnectiviteit](configure-azure-oci-networking.md) tussen Microsoft Azure en Oracle Cloud Infrastructure (OCI) kunnen Oracle-toepassingen nu worden geïmplementeerd op Azure met hun back-enddatabases in Azure of OCI. Oracle-toepassingen kunnen ook worden geïntegreerd met Azure Active Directory, zodat u één aanmelding instellen, zodat gebruikers zich bij de Oracle-toepassing kunnen aanmelden met hun Azure Active Directory-referenties (Azure AD).

OCI biedt meerdere Oracle-databaseopties voor Oracle-toepassingen, waaronder DBaaS, Exadata Cloud Service, Oracle RAC en Infrastructure-as-a-Service (IaaS). Op dit moment is Autonomous Database geen ondersteunde back-end voor Oracle-toepassingen.

Er zijn [meerdere opties](oracle-overview.md) voor het implementeren van Oracle-toepassingen in Azure, ook op een zeer beschikbare en veilige manier. Azure biedt ook [Oracle-databaseVM-afbeeldingen](oracle-vm-solutions.md) die u implementeren als u ervoor kiest uw Oracle-toepassingen volledig op Azure uit te voeren.

In de volgende secties worden architectuuraanbevelingen van zowel Microsoft als Oracle beschreven om Oracle E-Business Suite, JD Edwards EnterpriseOne en PeopleSoft te implementeren in een cross-cloudconfiguratie of volledig in Azure. Microsoft en Oracle hebben deze toepassingen getest en bevestigd dat de prestaties voldoen aan de normen die Oracle voor deze toepassingen heeft vastgesteld.

## <a name="architecture-considerations"></a>Architectuuroverwegingen

Oracle-toepassingen bestaan uit meerdere services, die kunnen worden gehost op dezelfde of meerdere virtuele machines in Azure en optioneel in OCI. 

Toepassingsinstanties kunnen worden ingesteld met privé- of openbare eindpunten. Microsoft en Oracle raden aan een *bastionhost-VM* met een openbaar IP-adres in een apart subnet in te stellen voor het beheer van de toepassing. Wijs vervolgens alleen privé-IP-adressen toe aan de andere machines, inclusief de databaselaag. 

Bij het instellen van een toepassing in een cross-cloudarchitectuur is planning vereist om ervoor te zorgen dat de IP-adresruimte in het virtuele Azure-netwerk de privé-IP-adresruimte in het virtuele VIRTUELE-cloudnetwerk van OCI niet overlapt.

Voor extra beveiliging u netwerkbeveiligingsgroepen instellen op subnetniveau om ervoor te zorgen dat alleen verkeer op specifieke poorten en IP-adressen is toegestaan. Machines in de middelste laag mogen bijvoorbeeld alleen verkeer ontvangen vanuit het virtuele netwerk. Geen extern verkeer mag de machines van de middelste laag rechtstreeks bereiken.

Voor hoge beschikbaarheid u redundante exemplaren van de verschillende servers instellen in dezelfde beschikbaarheidsset of verschillende beschikbaarheidszones. Met beschikbaarheidszones u een SLA van 99,99% bereiken, terwijl u met beschikbaarheidssets een SLA van 99,95% in de regio realiseren. Voorbeeldarchitecturen die in dit artikel worden weergegeven, worden geïmplementeerd in twee beschikbaarheidszones.

Wanneer u een toepassing implementeert met de interconnectie met meerdere clouden, u een bestaand ExpressRoute-circuit blijven gebruiken om uw Azure-omgeving te verbinden met uw on-premises netwerk. U hebt echter een apart ExpressRoute-circuit nodig voor de interconnect to OCI dan degene die verbinding maakt met uw on-premises netwerk.

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) is een reeks toepassingen, waaronder Supply Chain Management (SCM) en Customer Relationship Management (CRM). Om gebruik te maken van het beheerde databaseportfolio van OCI, kan EBS worden geïmplementeerd met behulp van de cross-cloud-interconnect tussen Microsoft Azure en OCI. In deze configuratie worden de presentatie- en toepassingslagen uitgevoerd in Azure en de databaselaag in OCI, zoals geïllustreerd in het volgende architectuurdiagram (figuur 1).

![E-Business Suite cross-cloud architectuur](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figuur 1: E-Business Suite cross-cloud architectuur* 

In deze architectuur is het virtuele netwerk in Azure verbonden met een virtueel cloudnetwerk in OCI met behulp van de cross-cloud interconnect. De toepassingslaag is ingesteld in Azure, terwijl de database is ingesteld in OCI. Het wordt aanbevolen om elk onderdeel te implementeren in zijn eigen subnet met netwerkbeveiligingsgroepen om verkeer alleen van specifieke subnetten op specifieke poorten toe te staan.

De architectuur kan ook volledig worden aangepast voor implementatie op Azure met zeer beschikbare Oracle-databases die zijn geconfigureerd met Oracle Data Guard in twee beschikbaarheidszones in een regio. Het volgende diagram (figuur 2) is een voorbeeld van dit architectonische patroon:

![E-Business Suite Azure-architectuur](media/oracle-oci-applications/ebs-arch-azure.png)

*Figuur 2: E-Business Suite Azure-architectuur*

De volgende secties beschrijven de verschillende componenten op een hoog niveau.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Toepassingslaag (middelste)

De toepassingslaag is geïsoleerd in zijn eigen subnet. Er zijn meerdere virtuele machines ingesteld voor fouttolerantie en eenvoudig patchbeheer. Deze VM's kunnen worden ondersteund door gedeelde opslag, die wordt aangeboden door Azure NetApp-bestanden en Ultra SSD's. Deze configuratie zorgt voor een eenvoudigere implementatie van patches zonder downtime. De machines in de toepassingslaag moeten worden gefronted door een openbare load balancer, zodat aanvragen voor de EBS-toepassingslaag worden verwerkt, zelfs als één machine in de laag offline is vanwege een fout.

### <a name="load-balancer"></a>Load balancer

Met een Azure load balancer u verkeer distribueren over meerdere exemplaren van uw werkbelasting om een hoge beschikbaarheid te garanderen. In dit geval wordt een openbare load balancer ingesteld, omdat gebruikers toegang hebben tot de EBS-toepassing via het web. De load balancer verdeelt de belasting over beide machines in de middelste laag. Voor extra beveiliging u alleen verkeer toestaan van gebruikers die toegang hebben tot het systeem vanuit uw bedrijfsnetwerk via een site-to-site VPN- of ExpressRoute- en netwerkbeveiligingsgroepen.

### <a name="database-tier"></a>Databaselaag

Deze laag host de Oracle-database en is gescheiden in zijn eigen subnet. Het wordt aanbevolen om netwerkbeveiligingsgroepen toe te voegen die alleen verkeer van de toepassingslaag toestaan aan de databaselaag op de Oracle-specifieke databasepoort 1521.

Microsoft en Oracle raden een hoge beschikbaarheid setup. Hoge beschikbaarheid in Azure kan worden bereikt door twee Oracle-databases in te stellen in twee beschikbaarheidszones met Oracle Data Guard of door Oracle Database Exadata Cloud Service in OCI te gebruiken. Wanneer u Oracle Database Exadata Cloud Service gebruikt, wordt uw database geïmplementeerd in twee subnetten. U Oracle Database ook instellen in VM's in OCI in twee beschikbaarheidsdomeinen met Oracle Data Guard.


### <a name="identity-tier"></a>Identiteitslaag

De identiteitslaag bevat de EBS Asserter VM. Met EBS Asserter u identiteiten synchroniseren via Oracle Identity Cloud Service (IDCS) en Azure AD. De EBS Asserter is nodig omdat EBS geen enkele aanmeldingsprotocollen zoals SAML 2.0 of OpenID Connect ondersteunt. De EBS Asserter verbruikt het OpenID connect-token (gegenereerd door IDCS), valideert het en maakt vervolgens een sessie voor de gebruiker in EBS. 

Hoewel deze architectuur IDCS-integratie toont, kunnen azure AD-uniforme toegang en single sign-on ook worden ingeschakeld met Oracle Access Manager met Oracle Internet Directory of Oracle Unified Directory. Zie voor meer informatie de whitepapers over [het implementeren van Oracle EBS met IDCS-integratie](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) of het implementeren van Oracle [EBS met OAM-integratie.](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)

Voor hoge beschikbaarheid is het raadzaam om redundante servers van de EBS Asserter te implementeren in meerdere beschikbaarheidszones met een load balancer ervoor.

Zodra uw infrastructuur is ingesteld, kan E-Business Suite worden geïnstalleerd door de installatiehandleiding van Oracle te volgen.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle's JD Edwards EnterpriseOne is een geïntegreerde applicaties suite van uitgebreide enterprise resource planning software. Het is een multi-tiered applicatie die kan worden ingesteld met een Oracle of SQL Server database backend. In deze sectie worden details besproken over het implementeren van JD Edwards EnterpriseOne met een back-end back-end van de Oracle-database in OCI of in Azure.

In de volgende aanbevolen architectuur (figuur 3) worden de beheer-, presentatie- en middelste lagen geïmplementeerd in het virtuele netwerk in Azure. De database wordt geïmplementeerd in een virtueel cloudnetwerk in OCI.

Net als bij E-Business Suite u een optionele bastionlaag instellen voor veilige administratieve doeleinden. Gebruik de bastion VM-host als een jumpserver om toegang te krijgen tot de toepassings- en database-exemplaren.

![JD Edwards EnterpriseOne cross-cloud architectuur](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figuur 3: JD Edwards EnterpriseOne cross-cloud architectuur*

In deze architectuur is het virtuele netwerk in Azure verbonden met het virtuele cloudnetwerk in OCI met behulp van de cross-cloud interconnect. De toepassingslaag is ingesteld in Azure, terwijl de database is ingesteld in OCI. Het wordt aanbevolen om elk onderdeel te implementeren in zijn eigen subnet met netwerkbeveiligingsgroepen om verkeer alleen van specifieke subnetten op specifieke poorten toe te staan.

De architectuur kan ook volledig worden aangepast voor implementatie op Azure met zeer beschikbare Oracle-databases die zijn geconfigureerd met Oracle Data Guard in twee beschikbaarheidszones in een regio. Het volgende diagram (figuur 4) is een voorbeeld van dit architectonische patroon:

![JD Edwards EnterpriseOne Azure-architectuur](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figuur 4: JD Edwards EnterpriseOne Azure-architectuur*

De volgende secties beschrijven de verschillende componenten op een hoog niveau.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Administratieve laag

Zoals de naam al doet vermoeden, wordt deze laag gebruikt voor administratieve taken. U een apart subnet voor de administratieve laag uitsnijden. De services en servers in deze laag worden voornamelijk gebruikt voor de installatie en het beheer van de toepassing. Daarom zijn afzonderlijke exemplaren van deze servers voldoende. Redundante exemplaren zijn niet vereist voor de hoge beschikbaarheid van uw toepassing.

De onderdelen van deze laag zijn als volgt:
    
 - **Inrichtingsserver** - Deze server wordt gebruikt voor end-to-end implementatie van de verschillende onderdelen van de toepassing. Het communiceert met de instanties in de andere lagen, inclusief de exemplaren in de databaselaag, via poort 22. Het host de Server Manager Console voor JD Edwards EnterpriseOne.
 - **Implementatieserver** - Deze server is voornamelijk vereist voor de installatie van JD Edwards EnterpriseOne. Tijdens het installatieproces fungeert deze server als de centrale opslagplaats voor vereiste bestanden en installatiepakketten. De software wordt gedistribueerd of geïmplementeerd naar andere servers en clients van deze server.
 - **Ontwikkelclient** - Deze server bevat componenten die zowel in een webbrowser als in native toepassingen worden uitgevoerd.

### <a name="presentation-tier"></a>Presentatielaag

Deze laag bevat verschillende componenten zoals Application Interface Services (AIS), Application Development Framework (ADF) en Java Application Servers (JAS). De servers in deze laag communiceren met de servers in de middelste laag. Ze worden voorgeleid door een load balancer die verkeer naar de benodigde server leidt op basis van het poortnummer en de URL waarop het verkeer wordt ontvangen. Het wordt aanbevolen dat u meerdere exemplaren van elk servertype implementeert voor hoge beschikbaarheid.

De volgende onderdelen in deze laag zijn:
    
- **Application Interface Services (AIS)** - De AIS-server biedt de communicatie-interface tussen JD Edwards EnterpriseOne mobiele bedrijfsapplicaties en JD Edwards EnterpriseOne.
- **Java Application Server (JAS)** - De JAS ontvangt aanvragen van de load balancer en geeft deze door aan de middelste laag om ingewikkelde taken uit te voeren. De JAS heeft de mogelijkheid om eenvoudige bedrijfslogica uit te voeren.
- **BI Publisher Server (BIP)** - Deze server presenteert rapporten op basis van de gegevens die zijn verzameld door de JD Edwards EnterpriseOne-toepassing. U ontwerpen en bepalen hoe het rapport de gegevens presenteert op basis van verschillende sjablonen.
- **Business Services Server (BSS)** - Het BSS maakt informatie-uitwisseling en interoperabiliteit met andere Oracle-toepassingen mogelijk.
- **Real-Time Events Server (RTE)** - Met de RTE Server u meldingen instellen voor externe systemen over transacties die plaatsvinden in het JDE EnterpriseOne-systeem. Het maakt gebruik van een abonneemodel en stelt systemen van derden in staat zich te abonneren op evenementen. Als u saldoaanvragen wilt laden voor beide RTE-servers, moet u ervoor zorgen dat de servers zich in een cluster bevinden.
- **Application Development Framework (ADF) Server** - De ADF-server wordt gebruikt om JD Edwards EnterpriseOne-toepassingen uit te voeren die zijn ontwikkeld met Oracle ADF. Dit wordt geïmplementeerd op een Oracle WebLogic-server met ADF-runtime.

### <a name="middle-tier"></a>Middelste laag

De middelste laag bevat de logische server en batchserver. In dit geval worden beide servers op dezelfde virtuele machine geïnstalleerd. Voor productiescenario's wordt echter aanbevolen om logische server en batchserver op afzonderlijke servers te implementeren. Meerdere servers worden geïmplementeerd in de middelste laag in twee beschikbaarheidszones voor een hogere beschikbaarheid. Er moet een Azure load balancer worden gemaakt en deze servers moeten in de backend-groep worden geplaatst om ervoor te zorgen dat beide servers actief zijn en aanvragen verwerken.

De servers in de middelste laag ontvangen alleen aanvragen van de servers in de presentatielaag en alleen de public load balancer. Netwerkbeveiligingsgroepregels moeten worden ingesteld om verkeer te weigeren van een ander adres dan het subnet van de presentatielaag en de load balancer. Er kan ook een NSG-regel worden ingesteld om verkeer op poort 22 van de bastionhost mogelijk te maken voor beheerdoeleinden. Mogelijk u de public load balancer gebruiken om saldoaanvragen tussen de VM's in de middelste laag te laden.

De volgende twee componenten bevinden zich in de middelste laag:

- **Logische server** - Bevat de bedrijfslogica of zakelijke functies.
- **Batchserver** - Gebruikt voor batchverwerking

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>Peoplesoft

Oracle's PeopleSoft applicatie suite bevat software voor human resources en financieel beheer. De applicatiesuite is multi-tiered en toepassingen omvatten human resource management systemen (HRMS), customer relationship management (CRM), financials en supply chain management (FSCM) en enterprise performance management (EPM).

Het wordt aanbevolen dat elke laag van de softwaresuite wordt geïmplementeerd in een eigen subnet. Een Oracle-database of Microsoft SQL Server is vereist als backend-database voor de toepassing. In deze sectie worden details besproken over het implementeren van PeopleSoft met een backend van de Oracle-database.

Het volgende is een canonieke architectuur voor het implementeren van de PeopleSoft-toepassingssuite in een cross-cloudarchitectuur (figuur 5).

![PeopleSoft cross-cloud architectuur](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figuur 5: PeopleSoft cross-cloud architectuur*

In deze voorbeeldarchitectuur is het virtuele netwerk in Azure verbonden met het virtuele cloudnetwerk in OCI met behulp van de cross-cloud interconnect. De toepassingslaag is ingesteld in Azure, terwijl de database is ingesteld in OCI. Het wordt aanbevolen om elk onderdeel te implementeren in zijn eigen subnet met netwerkbeveiligingsgroepen om verkeer alleen van specifieke subnetten op specifieke poorten toe te staan.

De architectuur kan ook volledig worden aangepast voor implementatie op Azure met zeer beschikbare Oracle-databases die zijn geconfigureerd met Oracle Data Guard in twee beschikbaarheidszones in een regio. Het volgende diagram (figuur 6) is een voorbeeld van dit architectonische patroon:

![PeopleSoft Azure-architectuur](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figuur 6: PeopleSoft Azure-architectuur*

De volgende secties beschrijven de verschillende componenten op een hoog niveau.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Toepassingslaag

De toepassingslaag bevat exemplaren van de PeopleSoft-toepassingsservers, PeopleSoft-webservers, elastisch zoeken en PeopleSoft Process Scheduler. Een Azure load balancer is ingesteld om aanvragen van gebruikers te accepteren die worden doorgestuurd naar de juiste server in de toepassingslaag.

Voor hoge beschikbaarheid u overwegen redundante exemplaren van elke server in de toepassingslaag in verschillende beschikbaarheidszones in te stellen. De Azure load balancer kan worden ingesteld met meerdere back-endpools om elk verzoek naar de juiste server te leiden.

### <a name="peopletools-client"></a>PeopleTools Klant

De PeopleTools-client wordt gebruikt voor het uitvoeren van beheeractiviteiten, zoals ontwikkeling, migratie en upgrade. Omdat de PeopleTools-client niet nodig is voor het bereiken van een hoge beschikbaarheid van uw toepassing, zijn redundante servers van PeopleTools Client niet nodig.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Volgende stappen

Gebruik [Terraform-scripts](https://github.com/microsoft/azure-oracle) om Oracle-apps in Azure in te stellen en cross-cloudconnectiviteit met OCI tot stand te brengen.

Zie de [Oracle Cloud-documentatie](https://docs.cloud.oracle.com/iaas/Content/home.htm) voor meer informatie en whitepapers over OCI.
