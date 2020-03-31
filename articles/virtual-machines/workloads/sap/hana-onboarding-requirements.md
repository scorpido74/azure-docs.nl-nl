---
title: Onboarding-vereisten voor SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Onboarding-vereisten voor SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 141a75a75a214ff4a6f136df7570d6e81f7f4e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617007"
---
# <a name="onboarding-requirements"></a>Voorbereidingsvereisten

In deze lijst worden vereisten verzameld voor het uitvoeren van SAP HANA op Azure (Grotere exemplaren).

**Microsoft Azure**

- Een Azure-abonnement dat kan worden gekoppeld aan SAP HANA op Azure (Large Instances).
- Microsoft Premier-ondersteuningscontract. Zie [SAP Support Note #2015553 – SAP op Microsoft Azure: Vereiste ondersteuning voor](https://launchpad.support.sap.com/#/notes/2015553)specifieke informatie met betrekking tot het uitvoeren van SAP in Azure. Als u HANA Large Instance-eenheden met 384 en meer CPU's gebruikt, moet u ook het Premier-ondersteuningscontract verlengen met Azure Rapid Response.
- Bewustzijn van de HANA Large Instance SKU's die je nodig hebt nadat je een maatoefening met SAP hebt uitgevoerd.

**Netwerkconnectiviteit**

- ExpressRoute tussen on-premises naar Azure: als u uw on-premises datacenter wilt verbinden met Azure, moet u ten minste een verbinding van 1 Gbps bestellen vanaf uw ISP. Connectiviteit tussen HANA Large Instance-eenheden en Azure maakt ook gebruik van ExpressRoute-technologie. Deze ExpressRoute-verbinding tussen de HANA Large Instance-eenheden en Azure is inbegrepen in de prijs van de HANA Large Instance-eenheden, inclusief alle gegevensin- en uitgangskosten voor dit specifieke ExpressRoute-circuit. Daarom vindt u als klant geen extra kosten te maken buiten uw ExpressRoute-koppeling tussen on-premises en Azure.

**Besturingssysteem**

- Licenties voor SUSE Linux Enterprise Server 12 voor SAP-toepassingen.

   > [!NOTE] 
   > Het besturingssysteem geleverd door Microsoft is niet geregistreerd bij SUSE. Het is niet verbonden met een instantie voor abonnementsbeheer.

- SUSE Linux Subscription Management Tool geïmplementeerd in Azure op een VM. Deze tool biedt de mogelijkheid voor SAP HANA op Azure (Large Instances) te worden geregistreerd en respectievelijk bijgewerkt door SUSE. (Er is geen internettoegang binnen het HANA Large Instance datacenter.) 
- Licenties voor Red Hat Enterprise Linux 6.7 of 7.x voor SAP HANA.

   > [!NOTE]
   > Het besturingssysteem geleverd door Microsoft is niet geregistreerd bij Red Hat. Het is niet verbonden met een red hat-abonnementsbeheer-exemplaar.

- Red Hat Subscription Manager geïmplementeerd in Azure op een VM. De Red Hat Subscription Manager biedt de mogelijkheid voor SAP HANA op Azure (Large Instances) te worden geregistreerd en respectievelijk bijgewerkt door Red Hat. (Er is geen directe internettoegang vanuit de tenant die is geïmplementeerd op het Azure Large Instance-stempel.)
- SAP vereist dat u ook een ondersteuningscontract hebt met uw Linux-provider. Deze vereiste wordt niet verwijderd door de oplossing van HANA Large Instance of het feit dat u Linux in Azure uitvoert. In tegenstelling tot sommige van de Linux Azure-galerijafbeeldingen, worden de servicekosten *niet* opgenomen in het oplossingsaanbod van HANA Large Instance. Het is uw verantwoordelijkheid om te voldoen aan de eisen van SAP met betrekking tot ondersteuningscontracten met de Linux-distributeur. 
   - Voor SUSE Linux, zoek de vereisten van ondersteuning contracten in [SAP Note #1984787 - SUSE Linux Enterprise Server 12: Installatie notities](https://launchpad.support.sap.com/#/notes/1984787) en SAP Note #1056161 - [SUSE prioriteit ondersteuning voor SAP-toepassingen](https://launchpad.support.sap.com/#/notes/1056161).
   - Voor Red Hat Linux moet u de juiste abonnementsniveaus hebben die ondersteuning en service-updates voor de besturingssystemen van HANA Large Instance bevatten. Red Hat raadt het Red Hat Enterprise Linux-abonnement voor SAP-oplossing aan. Doorverwijzen https://access.redhat.com/solutions/3082481. 

Voor de ondersteuningsmatrix van de verschillende SAP HANA-versies met de verschillende Linux-versies, zie [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Raadpleeg [OS Upgrade voor HLI voor](os-upgrade-hana-large-instance.md)de compatibiliteitsmatrix van het besturingssysteem en hli-firmware/driverversies.


> [!IMPORTANT] 
> Voor type II-eenheden wordt op dit moment alleen de SLES 12 SP2 OS-versie ondersteund. 


**Database**

- Licenties en software-installatiecomponenten voor SAP HANA (platform of enterprise edition).

**Toepassingen**

- Licenties en software-installatiecomponenten voor alle SAP-toepassingen die verbinding maken met SAP HANA en gerelateerde SAP-ondersteuningscontracten.
- Licenties en software-installatiecomponenten voor niet-SAP-toepassingen die worden gebruikt met SAP HANA op Azure-omgevingen (Large Instances) en gerelateerde ondersteuningscontracten.

**Vaardigheden**

- Ervaring met en kennis van Azure IaaS en de onderdelen daarvan.
- Ervaring met en kennis van het implementeren van een SAP-workload in Azure.
- SAP HANA installatie gecertificeerd persoonlijk.
- VAARDIGHEDEN van SAP-architecten om hoge beschikbaarheid en disaster recovery rond SAP HANA te ontwerpen.

**SAP**

- De verwachting is dat u een SAP-klant bent en een ondersteuningscontract hebt met SAP.
- Speciaal voor implementaties van de Type II-klasse van HANA Large Instance SKU's, overleg met SAP over versies van SAP HANA en de uiteindelijke configuraties op grote schaal-up hardware.

**Volgende stappen**
- [Sap HANA-architectuur (Large Instances) verwijzen op Azure](hana-architecture.md)