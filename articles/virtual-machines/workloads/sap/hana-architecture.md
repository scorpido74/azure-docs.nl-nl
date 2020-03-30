---
title: Architectuur van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Architectuur voor het implementeren van SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041da4198b0bdd040a4916008a1135aa2e2a5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614525"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>SAP HANA-architectuur (Large Instances) op Azure

Op een hoog niveau heeft de SAP HANA op Azure (Large Instances) oplossing de SAP-toepassingslaag die zich in VM's bevindt. De databaselaag bevindt zich op SAP TDI-geconfigureerde hardware in een groot exemplaarstempel in dezelfde Azure-regio die is verbonden met Azure IaaS.

> [!NOTE]
> Implementeer de SAP-toepassingslaag in dezelfde Azure-regio als de SAP DBMS-laag. Deze regel is goed gedocumenteerd in gepubliceerde informatie over SAP-workloads op Azure. 

De algehele architectuur van SAP HANA op Azure (Large Instances) biedt een SAP TDI-gecertificeerde hardwareconfiguratie, een niet-gevirtualiseerde, bare metal, high-performance server voor de SAP HANA-database. Het biedt ook de mogelijkheid en flexibiliteit van Azure om resources voor de SAP-toepassingslaag te schalen om aan uw behoeften te voldoen.

![Bouwkundig overzicht van SAP HANA op Azure (Grote exemplaren)](./media/hana-overview-architecture/image1-architecture.png)

De getoonde architectuur is verdeeld in drie secties:

- **Rechts:** toont een on-premises infrastructuur die verschillende toepassingen in datacenters uitvoert, zodat eindgebruikers toegang hebben tot LOB-toepassingen, zoals SAP. Idealiter is deze on-premises infrastructuur verbonden met Azure met [ExpressRoute.](https://azure.microsoft.com/services/expressroute/)

- **Center:** toont Azure IaaS en, in dit geval, het gebruik van VM's om SAP of andere toepassingen te hosten die SAP HANA als DBMS-systeem gebruiken. Kleinere HANA-exemplaren die functioneren met het geheugen dat VM's leveren, worden geïmplementeerd in VM's samen met hun toepassingslaag. Zie [Virtuele machines voor](https://azure.microsoft.com/services/virtual-machines/)meer informatie over virtuele machines.

   Azure-netwerkservices worden gebruikt om SAP-systemen samen met andere toepassingen te groeperen in virtuele netwerken. Deze virtuele netwerken maken verbinding met on-premises systemen en met SAP HANA op Azure (Large Instances).

   Zie [SAP Support Note #1928533 – SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen](https://launchpad.support.sap.com/#/notes/1928533)voor SAP NetWeaver-toepassingen en databases die worden ondersteund om in Azure te worden uitgevoerd. Zie voor documentatie over het implementeren van SAP-oplossingen op Azure:

  -  [SAP gebruiken op virtuele Windows-machines](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [SAP-oplossingen gebruiken op virtuele Azure-machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Links**: toont de SAP HANA TDI-gecertificeerde hardware in de Azure Large Instance-stempel. De HANA Large Instance-eenheden zijn verbonden met de virtuele netwerken van uw Azure-abonnement door dezelfde technologie te gebruiken als de connectiviteit van on-premises naar Azure. Vanaf mei 2019 is er een optimalisatie geïntroduceerd die het mogelijk maakt om te communiceren tussen de HANA Large Instance-eenheden en de Azure VM's zonder tussenkomst van de ExpressRoute Gateway. Deze optimalisatie genaamd ExpressRoute Fast Path wordt weergegeven in deze architectuur (rode lijnen). 

De azure large instance-stempel zelf combineert de volgende componenten:

- **Computing:** Servers die zijn gebaseerd op verschillende generatie Intel Xeon-processors die de benodigde rekencapaciteit bieden en SAP HANA-gecertificeerd zijn.
- **Netwerk:** een uniforme high-speed netwerkstructuur die de computer-, opslag- en LAN-componenten met elkaar verbindt.
- **Opslag:** een opslaginfrastructuur die toegankelijk is via een uniforme netwerkstructuur. De specifieke opslagcapaciteit die wordt geleverd, is afhankelijk van de specifieke SAP HANA-configuratie op Azure (Large Instances) die wordt geïmplementeerd. Meer opslagcapaciteit is beschikbaar tegen een extra maandelijkse kosten.

Binnen de multi-tenant infrastructuur van de Big Instance stempel worden klanten ingezet als geïsoleerde tenants. Bij de implementatie van de tenant geeft u een Azure-abonnement een naam binnen uw Azure-inschrijving. Dit Azure-abonnement is het abonnement waarop de HANA Large Instance wordt gefactureerd. Deze tenants hebben een 1:1-relatie met het Azure-abonnement. Voor een netwerk is het mogelijk om toegang te krijgen tot een HANA Large Instance-eenheid die is geïmplementeerd in één tenant in één Azure-regio van verschillende virtuele netwerken die behoren tot verschillende Azure-abonnementen. Deze Azure-abonnementen moeten tot dezelfde Azure-inschrijving behoren. 

Net als bij VM's wordt SAP HANA op Azure (Large Instances) aangeboden in meerdere Azure-regio's. Als u mogelijkheden voor noodherstel wilt bieden, u ervoor kiezen om u aan te melden. De verschillende Grote zegels van de Instantie binnen geo-politieke regio worden verbonden aan elkaar. HANA Large Instance Stamps in het Westen en oosten van de VS zijn bijvoorbeeld verbonden via een speciale netwerkkoppeling voor noodherstelreplicatie. 

Net zoals u kiezen tussen verschillende VM-typen met Azure Virtual Machines, u kiezen uit verschillende SKU's van HANA Large Instance die zijn afgestemd op verschillende werkbelastingtypen SAP HANA. SAP past geheugen-naar-processor-socket ratio's toe voor verschillende workloads op basis van de Intel-processorgeneraties. In de volgende tabel ziet u de aangeboden SKU-typen.

U vindt beschikbare SKU's [beschikbare SKU's voor HLI.](hana-available-skus.md)

**Volgende stappen**
- Verwijs [SAP HANA (Large Instances) netwerkarchitectuur](hana-network-architecture.md)