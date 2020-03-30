---
title: Certificering van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Certificering van SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a02f0e1b05b9de8105126d1c9e4e3f79057285f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617227"
---
# <a name="certification"></a>Certificering

Naast de NetWeaver-certificering vereist SAP een speciale certificering voor SAP HANA om SAP HANA te ondersteunen op bepaalde infrastructuren, zoals Azure IaaS.

De belangrijkste SAP-notitie op NetWeaver en tot op zekere hoogte SAP HANA-certificering is [SAP Note #1928533 – SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen.](https://launchpad.support.sap.com/#/notes/1928533)

De certificeringsrecords voor SAP HANA op Azure (Large Instances) eenheden zijn te vinden op de [SAP HANA gecertificeerde IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) site. 

De SAP HANA op Azure (Large Instances) types, genoemd in SAP HANA certified IaaS Platforms site, biedt Microsoft en SAP klanten de mogelijkheid om grote SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA of andere SAP HANA-workloads in Azure te implementeren. De oplossing is gebaseerd op de SAP-HANA gecertificeerde dedicated hardware stempel[(SAP HANA tailored datacenter integration – TDI).](https://scn.sap.com/docs/DOC-63140) Als u een SAP HANA TDI-oplossing uitvoert, werken alle SAP HANA-gebaseerde toepassingen (zoals SAP Business Suite op SAP HANA, SAP BW op SAP HANA, S4/HANA en BW4/HANA) aan de hardware-infrastructuur.

Vergeleken met het uitvoeren van SAP HANA in VM's, heeft deze oplossing een voordeel. Het zorgt voor veel grotere geheugenvolumes. Om deze oplossing in te schakelen, moet u de volgende belangrijke aspecten begrijpen:

- De SAP-toepassingslaag en niet-SAP-toepassingen worden uitgevoerd in VM's die worden gehost in de gebruikelijke Azure-hardwarestempels.
- On-premises infrastructuur, datacenters en implementaties van toepassingen zijn verbonden met het cloudplatform via ExpressRoute (aanbevolen) of een vpn (virtual private network). Active Directory en DNS worden ook uitgebreid naar Azure.
- De SAP HANA-databaseinstantie voor HANA-workload wordt uitgevoerd op SAP HANA op Azure (Large Instances). De stempel voor grote instanties is verbonden met Azure-netwerken, zodat software die in VM's wordt uitgevoerd, kan communiceren met de HANA-instantie die wordt uitgevoerd in HANA Large Instance.
- Hardware van SAP HANA op Azure (Large Instances) is speciale hardware die wordt geleverd in een IaaS met SUSE Linux Enterprise Server of Red Hat Enterprise Linux vooraf geïnstalleerd. Net als bij virtuele machines, verdere updates en onderhoud aan het besturingssysteem is uw verantwoordelijkheid.
- Installatie van HANA of eventuele extra componenten die nodig zijn om SAP HANA draaien op eenheden van HANA Large Instance is uw verantwoordelijkheid. Alle lopende bewerkingen en het beheer van SAP HANA op Azure zijn ook uw verantwoordelijkheid.
- Naast de oplossingen die hier worden beschreven, u andere onderdelen in uw Azure-abonnement installeren die verbinding maken met SAP HANA op Azure (Large Instances). Voorbeelden zijn componenten die communicatie mogelijk maken met of rechtstreeks naar de SAP HANA-database, zoals jump servers, RDP-servers, SAP HANA Studio, SAP Data Services voor SAP BI-scenario's of netwerkbewakingsoplossingen.
- Net als in Azure biedt HANA Large Instance ondersteuning voor functionaliteit met hoge beschikbaarheid en disaster recovery.

**Volgende stappen**
- Beschikbare [SKU's doorverwijzen voor HLI](hana-available-skus.md) 