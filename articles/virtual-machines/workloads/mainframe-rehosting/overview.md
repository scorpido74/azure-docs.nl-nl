---
title: Mainframe rehosting op virtuele Azure-machines
description: Host uw mainframeworkloads, zoals IBM Z-gebaseerde systemen, opnieuw met virtuele machines (VM's) op Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289795"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe rehosting op virtuele Azure-machines

Door workloads te migreren van mainframeomgevingen naar de cloud, u uw infrastructuur moderniseren en vaak kosten besparen. Veel workloads kunnen worden overgebracht naar Azure met slechts kleine codewijzigingen, zoals het bijwerken van de namen van databases.

Over het algemeen betekent de term *mainframe* een groot computersysteem. Concreet gaat het om ibm System Z-servers of IBM-plugcompatibele systemen die MVS, DOS, VSE, OS/390 of z/OS draaien.

Een virtuele Azure-machine (VM) wordt gebruikt om de resources voor een specifieke toepassing op één exemplaar te isoleren en te beheren. Mainframes zoals IBM z/OS gebruiken hiervoor Logische Partities (LPARS). Een mainframe kan één LPAR gebruiken voor een CICS-regio met bijbehorende COBOL-programma's en een aparte LPAR voor IBM Db2-database. Een typische [n-tier-toepassing op Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implementeert Azure VM's in een virtueel netwerk dat voor elke laag kan worden gesegmenteerd in subnetten.

Azure VM's kunnen mainframe-emulatieomgevingen en compilers uitvoeren die lift- en shiftscenario's ondersteunen. Ontwikkeling en testen behoren vaak tot de eerste workloads die worden gemigreerd van een mainframe naar een Azure dev/testomgeving. Veelgebruikte servercomponenten die u emuleren, zijn onder andere online transactieproces (OLTP), batch- en gegevensopnamesystemen, zoals de volgende figuur laat zien.

![Met emulatieomgevingen op Azure u op z/os gebaseerde systemen uitvoeren.](media/01-overview.png)

Sommige mainframeworkloads kunnen relatief eenvoudig naar Azure worden gemigreerd, terwijl andere met een partneroplossing opnieuw kunnen worden gehost op Azure. Voor gedetailleerde richtlijnen over het kiezen van een partneroplossing kan het [Azure Mainframe Migration Center](https://azure.microsoft.com/migration/mainframe/) helpen.

## <a name="mainframe-migration"></a>Mainframe-migratie

Opnieuw hosten, herbouwen, vervangen of met pensioen gaan? IaaS of PaaS? Zie de [migratiehandleiding mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) in het Azure Architecture Center om de juiste migratiestrategie voor uw mainframetoepassing te bepalen.

## <a name="micro-focus-rehosting-platform"></a>Micro Focus rehosting platform

Micro Focus Enterprise Server is een van de grootste mainframe rehosting platforms beschikbaar. U het gebruiken uw z / OS-workloads uitvoeren op een goedkoper x86-platform op Azure.

Aan de slag:

- [Enterprise Server en Enterprise Developer installeren op Azure](./microfocus/set-up-micro-focus-azure.md)
- [CICS BankDemo voor enterprise-ontwikkelaar instellen op Azure](./microfocus/demo.md)
- [Enterprise Server uitvoeren in een Dockercontainer op Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame op Azure

TmaxSoft OpenFrame is een populaire mainframe rehosting oplossing die wordt gebruikt in lift-and-shift scenario's. Een OpenFrame-omgeving op Azure is geschikt voor productie-, demo's, tests of productieworkloads.

Aan de slag:

- [Aan de slag met TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Het ebook downloaden](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z Development and Test Environment (IBM zD&T) stelt een niet-productieomgeving op Azure in die u gebruiken voor de ontwikkeling, testen en demo's van z/OS-gebaseerde toepassingen.

De emulatieomgeving op Azure kan verschillende soorten Z-exemplaren hosten via ADCDs (Application Developers Controlled Distributions). U zD&T Personal Edition, zD&T Parallel Sysplex en zD&T Enterprise Edition uitvoeren op Azure en Azure Stack.

Aan de slag:

- [IBM zD-&T 12.0 instellen op Azure](./ibm/install-ibm-z-environment.md)
- [ADCD instellen op zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale op Azure

De IBM DB2 pureScale-omgeving biedt een databasecluster voor Azure. Het is niet identiek aan de oorspronkelijke omgeving, maar het levert vergelijkbare beschikbaarheid en schaal als IBM DB2 voor z / OS draait in een Parallel Sysplex setup.

Zie [IBM DB2 pureScale op Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure)om aan de slag te gaan.

## <a name="considerations"></a>Overwegingen

Wanneer u mainframeworkloads migreert naar Azure infrastructure as a service (IaaS), u kiezen uit verschillende soorten on-demand, schaalbare computerbronnen, waaronder Azure VM's. Azure biedt een reeks [Linux-](/azure/virtual-machines/linux/overview) en Windows-VM's. [Windows](/azure/virtual-machines/windows/overview)

### <a name="compute"></a>Compute

Azure compute power is gunstig te vergelijken met de capaciteit van een mainframe. Als u overweegt een mainframeworkload naar Azure te verplaatsen, vergelijkt u de mainframestatistiek van een miljoen instructies per seconde (MIPS) met virtuele CPU's. 

Meer informatie over het [verplaatsen van mainframecompute naar Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Hoge beschikbaarheid en failover

Azure biedt op betrokkenheid gebaseerde service-level agreements (SLA's). Beschikbaarheid van meerdere negens is de standaardbeschikbaarheid en SLA's kunnen worden geoptimaliseerd met lokale of geogebaseerde replicatie van services. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

Met Azure IaaS, zoals een VM, bieden specifieke systeemfuncties failoverondersteuning, bijvoorbeeld failoverclustering-exemplaren en beschikbaarheidssets. Wanneer u Azure-platform als serviceresources (PaaS) gebruikt, verwerkt het platform failover automatisch. Voorbeelden hiervan zijn [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) en Azure Cosmos [DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Schaalbaarheid

Mainframes worden doorgaans opgeschaald, terwijl cloudomgevingen worden opgeschaald. Azure biedt een reeks [Linux-](/azure/virtual-machines/linux/sizes) en [Windows-formaten](/azure/virtual-machines/windows/sizes) om aan uw behoeften te voldoen. De cloud schaalt ook op of omlaag om aan de exacte gebruikersspecificaties te voldoen. Rekenkracht, opslag en services [schalen](/azure/architecture/best-practices/auto-scaling) op aanvraag onder een factureringsmodel op basis van gebruik.

### <a name="storage"></a>Storage

In de cloud hebt u een scala aan flexibele, schaalbare opslagopties en betaalt u alleen voor wat u nodig hebt. [Azure Storage](/azure/storage/common/storage-introduction) biedt een enorm schaalbare objectstore voor gegevensobjecten, een bestandssysteemservice voor de cloud, een betrouwbare berichtenwinkel en een NoSQL-archief. Voor VM's bieden beheerde en onbeheerde schijven permanente, veilige schijfopslag.

Meer informatie over het [verplaatsen van mainframeopslag naar Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Back-ups maken en herstellen

Het onderhouden van uw eigen ramp recovery site kan een dure propositie zijn. Azure heeft eenvoudig te implementeren en kosteneffectieve opties voor [back-up,](/azure/backup/backup-introduction-to-azure-backup) [herstel](/azure/site-recovery/site-recovery-overview)en [redundantie](/azure/storage/common/storage-redundancy) op lokaal of regionaal niveau of via georedundantie.

## <a name="azure-government-for-mainframe-migrations"></a>Azure-overheid voor mainframemigraties

Veel overheidsinstanties zouden hun mainframetoepassingen graag verplaatsen naar een moderner, flexibeler platform. Microsoft Azure Government is een fysiek gescheiden instantie van het wereldwijde Microsoft Azure-platform, verpakt voor federale, staats- en lokale overheidssystemen. Het biedt beveiligings-, beschermings- en nalevingsservices van wereldklasse, specifiek voor Amerikaanse overheidsinstanties en hun partners.

Azure Government heeft een Provisional Authority to Operate (P-ATO) verdiend voor FedRAMP High Impact voor systemen die dit type omgeving nodig hebben.

Download [microsoft azure government cloud voor mainframetoepassingen](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)om aan de slag te gaan.

## <a name="next-steps"></a>Volgende stappen

Vraag onze [partners](partner-workloads.md) om u te helpen bij het migreren of opnieuw hosten van uw mainframetoepassingen. 

Zie ook:

- [Whitepapers over mainframeonderwerpen](mainframe-white-papers.md)
- [Mainframemigratie](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Probleemoplossing](/azure/virtual-machines/troubleshooting/)
- [Demystifying mainframe to Azure migration Demyststifying mainframe to Azure migration Demyststifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
