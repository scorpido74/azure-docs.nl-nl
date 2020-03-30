---
title: Operations-model van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Operations-model van SAP HANA op Azure (Large Instances).
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
ms.openlocfilehash: e147e4a5f104ca4cd1a10a776c907e3f9f1d6128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616978"
---
# <a name="operations-model-and-responsibilities"></a>Bewerkingsmodel en verantwoordelijkheden

De service met SAP HANA op Azure (Large Instances) is afgestemd op Azure IaaS-services. U krijgt een exemplaar van een HANA Large Instance met een geïnstalleerd besturingssysteem dat is geoptimaliseerd voor SAP HANA. Net als bij Azure IaaS VM's, de meeste van de taken van het verharden van het OS, het installeren van extra software, het installeren van HANA, het bedienen van het OS en HANA, en het bijwerken van het OS en HANA is uw verantwoordelijkheid. Microsoft forceert geen OS-updates of HANA-updates op je.

![Verantwoordelijkheden van SAP HANA op Azure (grote exemplaren)](./media/hana-overview-architecture/image2-responsibilities.png)

Zoals in het diagram wordt weergegeven, is SAP HANA op Azure (Large Instances) een IaaS-aanbieding met meerdere tenantn. Voor het grootste deel, de verdeling van de verantwoordelijkheid is op de OS-infrastructuur grens. Microsoft is verantwoordelijk voor alle aspecten van de service onder de lijn van het besturingssysteem. Je bent verantwoordelijk voor alle aspecten van de dienstverlening boven de streep. Het OS is jouw verantwoordelijkheid. U de meeste recente on-premises methoden blijven gebruiken die u mogelijk gebruikt voor compliance, beveiliging, applicatiebeheer, basis en OS-beheer. De systemen lijken in alle opzichten in uw netwerk te bevinden.

Deze service is geoptimaliseerd voor SAP HANA, dus er zijn gebieden waar u met Microsoft moet samenwerken om de onderliggende infrastructuurmogelijkheden te gebruiken voor de beste resultaten.

De volgende lijst bevat meer details over elk van de lagen en uw verantwoordelijkheden:

**Netwerken**: Alle interne netwerken voor de Large Instance stempel met SAP HANA. Uw verantwoordelijkheid omvat toegang tot opslag, connectiviteit tussen de instanties (voor scale-out en andere functies), connectiviteit met het landschap en connectiviteit met Azure waar de SAP-toepassingslaag wordt gehost in VM's. Het bevat ook WAN-connectiviteit tussen Azure Datacenters voor disaster recovery doeleinden replicatie. Alle netwerken worden verdeeld door de tenant en hebben de kwaliteit van de service toegepast.

**Opslag:** de gevirtualiseerde partitieopslag voor alle volumes die nodig zijn voor de SAP HANA-servers, evenals voor momentopnamen. 

**Servers:** De dedicated fysieke servers voor het uitvoeren van de SAP HANA DB's die aan tenants zijn toegewezen. De servers van de klasse Type I van SKU's zijn hardware geabstraheerd. Bij dit soort servers wordt de serverconfiguratie verzameld en onderhouden in profielen, die van de ene fysieke hardware naar de andere fysieke hardware kunnen worden verplaatst. Een dergelijke (handmatige) verplaatsing van een profiel door bewerkingen kan een beetje worden vergeleken met Azure-servicehealing. De servers van de Type II klasse SKU's bieden niet een dergelijke mogelijkheid.

**SDDC**: De beheersoftware die wordt gebruikt om datacenters te beheren als softwaregedefinieerde entiteiten. Hiermee kan Microsoft resources bundelen om redenen van schaal, beschikbaarheid en prestaties.

**O/S**: Het besturingssysteem dat u kiest (SUSE Linux of Red Hat Linux) dat op de servers draait. De OS-afbeeldingen die u wordt geleverd met werden geleverd door de individuele Linux-leverancier aan Microsoft voor het uitvoeren van SAP HANA. U moet een abonnement hebben bij de Linux-leverancier voor de specifieke SAP HANA-geoptimaliseerde afbeelding. U bent verantwoordelijk voor het registreren van de afbeeldingen bij de os-leverancier. 

Vanaf het moment van overdracht door Microsoft bent u verantwoordelijk voor verdere patching van het Linux-besturingssysteem. Deze patching bevat extra pakketten die nodig kunnen zijn voor een succesvolle SAP HANA-installatie en die niet zijn opgenomen door de specifieke Linux-leverancier in hun SAP HANA geoptimaliseerde OS-afbeeldingen. (Zie de HANA-installatiedocumentatie van SAP en SAP Notes voor meer informatie.) 

U bent verantwoordelijk voor het patchen van het besturingssysteem als gevolg van storingen of optimalisatie van het besturingssysteem en de stuurprogramma's ten opzichte van de specifieke serverhardware. Je bent ook verantwoordelijk voor de beveiliging of functionele patching van het OS. 

Uw verantwoordelijkheid omvat ook monitoring en capaciteitsplanning van:

- CPU-bronverbruik.
- Geheugenverbruik.
- Schijfvolumes met betrekking tot vrije ruimte, IOPS en latentie.
- Netwerkvolumeverkeer tussen HANA Large Instance en de SAP-toepassingslaag.

De onderliggende infrastructuur van HANA Large Instance biedt functionaliteit voor back-up en herstel van het OS-volume. Het gebruik van deze functionaliteit is ook uw verantwoordelijkheid.

**Middleware**: De SAP HANA Instance, voornamelijk. Administratie, operaties en monitoring zijn uw verantwoordelijkheid. U de meegeleverde functionaliteit gebruiken om opslagmomentopnamen te gebruiken voor back-up- en herstel- en hersteldoeleinden. Deze mogelijkheden worden geleverd door de infrastructuur. Uw verantwoordelijkheden omvatten ook het ontwerpen van hoge beschikbaarheid of disaster recovery met deze mogelijkheden, het benutten ervan en het monitoren om te bepalen of storage snapshots succesvol zijn uitgevoerd.

**Gegevens:** uw gegevens die worden beheerd door SAP HANA en andere gegevens, zoals back-ups van bestanden die zich op volumes of bestandsshares bevinden. Uw verantwoordelijkheden omvatten het bewaken van schijfvrije ruimte en het beheren van de inhoud op de volumes. U bent ook verantwoordelijk voor het bewaken van de succesvolle uitvoering van back-ups van schijfvolumes en opslagmomentopnamen. Succesvolle uitvoering van gegevensreplicatie naar sites voor noodherstel is de verantwoordelijkheid van Microsoft.

**Toepassingen:** De SAP-toepassingsinstanties of, in het geval van niet-SAP-toepassingen, de toepassingslaag van die toepassingen. Uw verantwoordelijkheden omvatten implementatie, beheer, bewerkingen en monitoring van deze toepassingen. U bent verantwoordelijk voor capaciteitsplanning van cpu-bronverbruik, geheugenverbruik, Azure-opslagverbruik en netwerkbandbreedteverbruik binnen virtuele netwerken. U bent ook verantwoordelijk voor capaciteitsplanning voor resourceverbruik van virtuele netwerken tot SAP HANA op Azure (Large Instances).

**WANs:** de verbindingen die u maakt van on-premises naar Azure-implementaties voor workloads. Alle klanten met HANA Large Instance gebruiken Azure ExpressRoute voor connectiviteit. Deze verbinding maakt geen deel uit van de SAP HANA on Azure (Large Instances) oplossing. U bent verantwoordelijk voor het opzetten van deze verbinding.

**Archief**: U er de voorkeur aan geven om kopieën van gegevens te archiveren met behulp van uw eigen methoden in opslagaccounts. Archivering vereist beheer, naleving, kosten en bewerkingen. U bent verantwoordelijk voor het genereren van archiefkopieën en back-ups op Azure en het opslaan ervan op een compatibele manier.

Zie de [SLA voor SAP HANA op Azure (Large Instances)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Volgende stappen**
- [Sap HANA-architectuur (Large Instances) verwijzen op Azure](hana-architecture.md)
