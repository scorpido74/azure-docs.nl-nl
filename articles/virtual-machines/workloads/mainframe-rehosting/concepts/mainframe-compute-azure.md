---
title: Mainframecompute verplaatsen naar Azure Virtual Machines
description: Azure compute resources vergelijken gunstig met mainframecapaciteit, zodat u IBM z14-toepassingen migreren en moderniseren.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288928"
---
# <a name="move-mainframe-compute-to-azure"></a>Mainframecompute verplaatsen naar Azure

Mainframes hebben een reputatie voor hoge betrouwbaarheid en beschikbaarheid en blijven de vertrouwde ruggengraat van veel ondernemingen. Er wordt vaak gedacht dat ze ook bijna onbeperkte schaalbaarheid en rekenkracht hebben. Echter, sommige ondernemingen zijn ontgroeid de mogelijkheden van de grootste beschikbare mainframes. Als dit klinkt als u, azure biedt flexibiliteit, bereik, en infrastructuur besparingen.

Als u mainframeworkloads wilt uitvoeren op Microsoft Azure, moet u weten hoe de rekenmogelijkheden van uw mainframe zich verhouden tot Azure. Op basis van een IBM z14 mainframe (het meest recente model vanaf dit schrijven), dit artikel vertelt u hoe u vergelijkbare resultaten op Azure te krijgen.

Om aan de slag te gaan, moet u rekening houden met de omgevingen naast elkaar. In het volgende cijfer wordt een mainframeomgeving voor het uitvoeren van toepassingen vergeleken met een Azure-hostingomgeving.

![Azure-services en emulatieomgevingen bieden vergelijkbare ondersteuning en stroomlijnt migratie](media/mainframe-compute-azure.png)

De kracht van mainframes wordt vaak gebruikt voor oltp-systemen (online transactieverwerking) die miljoenen updates voor duizenden gebruikers verwerken. Deze toepassingen maken vaak gebruik van software voor transactieverwerking, schermafhandeling en formulierinvoer. Ze kunnen gebruik maken van een Customer Information Control System (CICS), Information Management System (IMS) of Transaction Interface Package (TIP).

Zoals uit de figuur blijkt, kan een TPM-emulator op Azure CICS- en IMS-workloads verwerken. Een batchsysteememulator op Azure vervult de rol van Job Control Language (JCL). Mainframegegevens worden gemigreerd naar Azure-databases, zoals Azure SQL Database. Azure-services of andere software die wordt gehost in Azure Virtual Machines kunnen worden gebruikt voor systeembeheer.

## <a name="mainframe-compute-at-a-glance"></a>Mainframe compute in één oogopslag

In het z14 mainframe zijn processors gerangschikt in maximaal vier *laden.* Een *lade* is gewoon een cluster van processors en chipsets. Elke lade kan zes actieve centrale processor (CP) chips hebben, en elke CP heeft 10 system controller (SC) chips. In Intel x86-terminologie zijn er zes sockets per lade, 10 cores per socket en vier laden. Deze architectuur biedt het ruwe equivalent van 24 sockets en 240 cores, maximaal, voor een z14.

De snelle z14 CP heeft een kloksnelheid van 5,2 GHz. Typisch, een z14 wordt geleverd met alle cps in de doos. Ze worden geactiveerd als dat nodig is. Een klant wordt vaak in rekening gebracht voor ten minste vier uur rekentijd per maand, ondanks het werkelijke gebruik.

Een mainframeprocessor kan worden geconfigureerd als een van de volgende typen:

- Gp-processor voor algemeen gebruik
- Geïntegreerde informatieprocessor systeem z (zIIP)
- Geïntegreerde faciliteit voor Linux (IFL) processor
- System Assist Processor (SAP)
- Geïntegreerde koppelingsinstallatie (ICF)-processor

## <a name="scaling-mainframe-compute-up-and-out"></a>Mainframe compute up and out schalen

IBM mainframes bieden de mogelijkheid om op te schalen tot 240 cores (de huidige z14 grootte voor een enkel systeem). Daarnaast kunnen IBM mainframes uitschalen via een functie genaamd de Coupling Facility (CF). De CF maakt het mogelijk meerdere mainframesystemen tegelijkertijd toegang te geven tot dezelfde gegevens. Met behulp van de CF groepeert de mainframe Parallel Sysplex-technologie mainframeprocessors in clusters. Toen deze handleiding werd geschreven, ondersteunde de Parallel Sysplex-functie 32 groeperingen van elk 64 processors. Tot 2.048 processors kunnen op deze manier worden gegroepeerd om de rekencapaciteit uit te schalen.

Met een CF kunnen de compute clusters gegevens delen met directe toegang. Het wordt gebruikt voor het vergrendelen van informatie, cache-informatie en de lijst met gedeelde gegevensbronnen. Een Parallel Sysplex met behulp van een of meer CD's kan worden gezien als een "gedeelde alles" scale-out compute cluster. Zie [Parallel Sysplex op IBM Z op](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) de website van IBM voor meer informatie over deze functies.

Toepassingen kunnen deze functies gebruiken om zowel scale-out prestaties als hoge beschikbaarheid te bieden. Voor informatie over hoe CICS Parallel Sysplex met CF kan gebruiken, download t.w.v. [de IBM CICS en de Coupling Facility: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Azure-gegevens in één oogopslag

Sommige mensen denken ten onrechte dat Intel-gebaseerde servers zijn niet zo krachtig als mainframes. Echter, de nieuwe core-dense, Intel-gebaseerde systemen hebben net zoveel rekencapaciteit als mainframes. In dit gedeelte worden de IaaS-opties (Azure infrastructure-as-a-service) voor computers en opslag beschreven. Azure biedt ook PaaS-opties (Platform-as-a-Service), maar dit artikel richt zich op de IaaS-keuzes die vergelijkbare mainframecapaciteit bieden.

Azure Virtual Machines bieden rekenkracht in verschillende formaten en typen. In Azure komt een virtuele CPU (vCPU) ruwweg overeen met een kern op een mainframe.

Momenteel biedt het bereik van Azure Virtual Machine-formaten 1 tot 128 vCPU's. Vm-typen (Virtual machine) zijn geoptimaliseerd voor bepaalde workloads. In de volgende lijst worden bijvoorbeeld de VM-typen weergegeven (actueel vanaf dit schrijven) en de aanbevolen toepassingen ervan:

| Grootte     | Type en beschrijving                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D-serie | Algemene doeleinden met 64 vCPU en een kloksnelheid van maximaal 3,5 GHz                           |
| E-serie | Geheugen geoptimaliseerd met maximaal 64 vCPU's                                                 |
| F-serie | Compute geoptimaliseerd met maximaal 64 vCPU's en een kloksnelheid van 3,7 GHz                       |
| H-serie | Geoptimaliseerd voor HPC-toepassingen (high-performance computing)                          |
| L-serie | Opslag geoptimaliseerd voor toepassingen met hoge doorvoer die worden ondersteund door databases zoals NoSQL |
| M-serie | Grootste computer- en geheugengeoptimaliseerde VM's met maximaal 128 vCPU's                        |

Zie [Virtual Machine-serie](https://azure.microsoft.com/pricing/details/virtual-machines/series/)voor meer informatie over beschikbare VM's.

Een z14 mainframe kan maximaal 240 cores hebben. Z14 mainframes gebruiken echter bijna nooit alle cores voor één toepassing of workload. In plaats daarvan scheidt een mainframe workloads in logische partities (LPARs) en hebben de LPARs classificaties: MIPS (Millions Of Instructions Per Second) of MSU (Million Service Unit). Bij het bepalen van de vergelijkbare VM-grootte die nodig is om een mainframeworkload op Azure uit te voeren, moet u rekening houden met de MIPS-classificatie (of MSU).

Hieronder volgen algemene schattingen:

-   150 MIPS per vCPU

-   1.000 MIPS per processor

Als u de juiste VM-grootte voor een bepaalde werkbelasting in een LPAR wilt bepalen, optimaliseert u eerst de VM voor de werkbelasting. Bepaal vervolgens het aantal vCPU's dat nodig is. Een conservatieve schatting is 150 MIPS per vCPU. Op basis van deze schatting kan bijvoorbeeld een VM uit de F-serie met 16 vCPU's gemakkelijk een IBM Db2-workload van een LPAR met 2.400 MIPS ondersteunen.

## <a name="azure-compute-scale-up"></a>Azure compute scale-up

De Vm's uit de M-serie kunnen worden opgeschaald naar 128 vCPU's (op het moment dat dit artikel werd geschreven). Met behulp van de conservatieve schatting van 150 MIPS per vCPU, de M-serie VM komt neer op ongeveer 19.000 MIPS. De algemene regel voor het schatten van MIPS voor een mainframe is 1.000 MIPS per processor. Een z14 mainframe kan maximaal 24 processors hebben en ongeveer 24.000 MIPS leveren voor een enkel mainframesysteem.

Het grootste enkel z14 mainframe heeft ongeveer 5.000 MIPS meer dan de grootste VM die beschikbaar is in Azure. Toch is het belangrijk om te vergelijken hoe workloads worden geïmplementeerd. Als een mainframesysteem zowel een toepassing als een relationele database heeft, worden ze meestal geïmplementeerd op hetzelfde fysieke mainframe, elk in zijn eigen LPAR. Dezelfde oplossing op Azure wordt vaak geïmplementeerd met één VM voor de toepassing en een afzonderlijke VM met voldoende grootte voor de database.

Als bijvoorbeeld een M64 vCPU-systeem de toepassing ondersteunt en een M96-vCPU wordt gebruikt voor de database, zijn ongeveer 150 vCPU's nodig, of ongeveer 24.000 MIPS, zoals het volgende cijfer laat zien.

![Workload-implementaties van 24.000 MIPS vergelijken](media/mainframe-compute-mips.png)

De aanpak is om LPARs te migreren naar individuele VM's. Vervolgens schaalt Azure eenvoudig op naar de grootte die nodig is voor de meeste toepassingen die worden geïmplementeerd op één mainframesysteem.

## <a name="azure-compute-scale-out"></a>Azure compute scale-out

Een van de voordelen van een Azure-gebaseerde oplossing is de mogelijkheid om uit te schalen. Schalen maakt bijna onbeperkte rekencapaciteit beschikbaar voor een toepassing. Azure ondersteunt meerdere methoden om rekenkracht uit te schalen:

- **Taakverdeling in een cluster.** In dit scenario kan een toepassing een [load balancer](/azure/load-balancer/load-balancer-overview) of resourcemanager gebruiken om de werkbelasting over meerdere VM's in een cluster te spreiden. Als er meer rekencapaciteit nodig is, worden extra VM's aan het cluster toegevoegd.

- **Virtuele machineschaalsets.** In dit burst-scenario kan een toepassing worden geschaald naar aanvullende [rekenbronnen](/azure/virtual-machine-scale-sets/overview) op basis van vm-gebruik. Wanneer de vraag daalt, kan het aantal VM's in een schaalset ook dalen, waardoor een efficiënt gebruik van rekenkracht wordt gegarandeerd.

- **PaaS-schaling.** Azure PaaS-aanbiedingen schalen rekenresources. [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) wijst bijvoorbeeld rekenresources toe om te voldoen aan de toename van het aantal aanvragen.

- **Kubernetes clusters.** Toepassingen op Azure kunnen [Kubernetes-clusters](/azure/aks/concepts-clusters-workloads) gebruiken voor compute services voor bepaalde resources. Azure Kubernetes Service (AKS) is een beheerde service die Kubernetes-knooppunten, pools en clusters op Azure orkestreert.

Als u de juiste methode wilt kiezen voor het uitschalen van rekenresources, is het belangrijk om te begrijpen hoe Azure en mainframes verschillen. De sleutel is hoe of als gegevens worden gedeeld door rekenbronnen. In Azure worden gegevens (standaard) doorgaans niet door meerdere VM's gedeeld. Als het delen van gegevens vereist is door meerdere VM's in een schaal-out compute cluster, moeten de gedeelde gegevens zich bevinden in een bron die deze functionaliteit ondersteunt. Op Azure omvat het delen van gegevens opslag zoals in de volgende sectie wordt besproken.

## <a name="azure-compute-optimization"></a>Azure-compute-optimalisatie

U elke verwerkingslaag optimaliseren in een Azure-architectuur. Gebruik het meest geschikte type VM's en functies voor elke omgeving. De volgende afbeelding toont een potentieel patroon voor het implementeren van VM's in Azure ter ondersteuning van een CICS-toepassing die Db2 gebruikt. Op de primaire locatie worden de VM's voor productie, preproductie en het testen van VM's met hoge beschikbaarheid geïmplementeerd. De secundaire site is voor back-up en disaster recovery.

Elke laag kan ook de juiste noodherstelservices bieden. Productie- en database-VM's vereisen bijvoorbeeld een warm of warm herstel, terwijl de VM's voor de ontwikkeling en het testen van VM's een koud herstel ondersteunen.

![Zeer beschikbare implementatie die noodherstel ondersteunt](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Volgende stappen

- [Mainframemigratie](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting op Azure Virtual Machines](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mainframeopslag verplaatsen naar Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM-bronnen

- [Parallel Sysplex op IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS en de koppelingsfaciliteit: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Vereiste gebruikers maken voor een Db2 pureScale Feature-installatie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Instantie maken, opdracht](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale Clustered Database-oplossing](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government cloud voor mainframetoepassingen](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft en FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Meer migratiebronnen

- [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
