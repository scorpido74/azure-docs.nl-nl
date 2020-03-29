---
title: IBM DB2 pureScale op Azure
description: In dit artikel tonen we een architectuur voor het uitvoeren van een IBM DB2 pureScale-omgeving op Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945053"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale op Azure

De IBM DB2 pureScale-omgeving biedt een databasecluster voor Azure met hoge beschikbaarheid en schaalbaarheid op Linux-besturingssystemen. In dit artikel wordt een architectuur weergegeven voor het uitvoeren van DB2 pureScale op Azure.

## <a name="overview"></a>Overzicht

Ondernemingen gebruiken al lang traditionele relationele database management system (RDBMS) platforms om tegemoet te komen aan hun online transactieverwerking (OLTP) behoeften. Tegenwoordig migreren velen hun databaseomgevingen op basis van mainframe naar Azure als een manier om de capaciteit uit te breiden, de kosten te verlagen en een stabiele operationele kostenstructuur te behouden. Migratie is vaak de eerste stap in het moderniseren van een legacy platform. 

Onlangs heeft een zakelijke klant zijn IBM DB2-omgeving opnieuw gehost op z/OS naar IBM DB2 pureScale op Azure. De Db2 pureScale database cluster oplossing biedt hoge beschikbaarheid en schaalbaarheid op Linux besturingssystemen. De klant heeft Db2 met succes uitgevoerd als een zelfstandige, scale-up instantie op een enkele virtuele machine (VM) in een grootschalig scale-up systeem op Azure voorafgaand aan de installatie van Db2 pureScale. 

Hoewel niet identiek aan de oorspronkelijke omgeving, ibm DB2 pureScale op Linux biedt vergelijkbare hoge beschikbaarheid en schaalbaarheid functies als IBM DB2 voor z / OS draait in een Parallel Sysplex configuratie op het mainframe. In dit scenario wordt het cluster via iSCSI verbonden met een gedeeld opslagcluster. We gebruikten het GlusterFS-bestandssysteem, een gratis, schaalbaar, open source gedistribueerd bestandssysteem dat speciaal is geoptimaliseerd voor cloudopslag. IBM ondersteunt deze oplossing echter niet meer. Om uw ondersteuning van IBM te behouden, moet u een ondersteund iSCSI-compatibel bestandssysteem gebruiken. Microsoft biedt Storage Spaces Direct (S2D) als optie aan

In dit artikel wordt de architectuur beschreven die wordt gebruikt voor deze Azure-migratie. De klant gebruikte Red Hat Linux 7.4 om de configuratie te testen. Deze versie is beschikbaar op de Azure Marketplace. Voordat u een Linux-distributie kiest, moet u de momenteel ondersteunde versies verifiëren. Zie voor meer informatie de documentatie voor [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) en [GlusterFS.](https://docs.gluster.org/en/latest/)

Dit artikel is een startpunt voor uw DB2 implementatieplan. Uw bedrijfsvereisten verschillen, maar hetzelfde basispatroon is van toepassing. U dit architecturale patroon ook gebruiken voor OLAP-toepassingen (Online Analytical Processing) op Azure.

Dit artikel heeft geen betrekking op verschillen en mogelijke migratietaken voor het verplaatsen van een IBM DB2 voor z/OS-database naar IBM DB2 pureScale die op Linux draait. En het biedt geen maatschattingen en werkbelastinganalyses voor de overgang van DB2 z/OS naar DB2 pureScale. 

Om u te helpen beslissen over de beste DB2 pureScale architectuur voor uw omgeving, raden we u aan om de grootte volledig in te schatten en een hypothese te maken. Op het bronsysteem moet u db2 z/OS Parallel Sysplex overwegen met architectuur voor het delen van gegevens, configuratie van de koppelingsfaciliteit en ddf-gebruiksstatistieken (Distributed Data Facility).

> [!NOTE]
> In dit artikel wordt één benadering van DB2-migratie beschreven, maar er zijn er nog meer. DB2 pureScale kan bijvoorbeeld ook worden uitgevoerd in gevirtualiseerde on-premises omgevingen. IBM ondersteunt DB2 op Microsoft Hyper-V in verschillende configuraties. Zie [DB2 pureScale virtualisatiearchitectuur](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) voor meer informatie in het IBM Knowledge Center.

## <a name="architecture"></a>Architectuur

Om hoge beschikbaarheid en schaalbaarheid op Azure te ondersteunen, u een scale-out, gedeelde gegevensarchitectuur gebruiken voor DB2 pureScale. De klantmigratie gebruikte de volgende voorbeeldarchitectuur.

![DB2 pureScale op virtuele Azure-machines met opslag en netwerken](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale op virtuele Azure-machines met opslag en netwerken")


Het diagram toont de logische lagen die nodig zijn voor een DB2 pureScale-cluster. Deze omvatten virtuele machines voor een client, voor beheer, voor caching, voor de database-engine en voor gedeelde opslag. 

Naast de databaseengineknooppunten bevat het diagram twee knooppunten die worden gebruikt voor clustercaching-faciliteiten (CFs). Voor de databaseengine zelf worden minimaal twee knooppunten gebruikt. Een DB2-server die deel uitmaakt van een pureScale-cluster wordt lid genoemd. 

Het cluster is via iSCSI verbonden met een gedeeld opslagcluster met drie delen om schaalopslag en hoge beschikbaarheid te bieden. DB2 pureScale is geïnstalleerd op virtuele Azure-machines met Linux.

Deze benadering is een sjabloon die u wijzigen voor de grootte en schaal van uw organisatie. Het is gebaseerd op het volgende:

-   Twee of meer databaseleden worden gecombineerd met ten minste twee CF-knooppunten. De knooppunten beheren een globale bufferpool (GBP) voor gedeelde geheugen- en GLM-services (Global Lock Manager) om gedeelde toegang te beheren en stelling van actieve leden te vergrendelen. Het ene CF-knooppunt fungeert als het primaire en het andere als het secundaire CF-knooppunt. Om één storingspunt in de omgeving te voorkomen, heeft een DB2 pureScale-cluster ten minste vier knooppunten nodig.

-   Krachtige gedeelde opslag (weergegeven in P30-formaat in het diagram). Elk knooppunt maakt gebruik van deze opslag.

-   Krachtige netwerken voor de gegevensleden en gedeelde opslag.

### <a name="compute-considerations"></a>Rekenoverwegingen berekenen

Met deze architectuur worden de toepassings-, opslag- en gegevenslagen uitgevoerd op virtuele Azure-machines. Met [de scripts voor implementatie-instellingen](https://aka.ms/db2onazure) wordt het volgende gemaakt:

-   Een DB2 pureScale-cluster. Het type rekenresources dat u op Azure nodig hebt, is afhankelijk van uw installatie. In het algemeen u twee benaderingen gebruiken:

    -   Gebruik een multi-node, high-performance computing (HPC)-stijl netwerk waar kleine tot middelgrote exemplaren toegang tot gedeelde opslag. Voor dit HPC-type configuratie bieden voor Azure-geheugen geoptimaliseerde E-serie of voor opslag geoptimaliseerde [virtuele machines](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) uit de L-serie de benodigde rekenkracht.

    -   Gebruik minder grote virtuele machine-exemplaren voor de gegevensengines. Voor grote exemplaren zijn de grootste voor geheugen geoptimaliseerde [virtuele m-serie](https://azure.microsoft.com/pricing/details/virtual-machines/series/) machines ideaal voor zware workloads in het geheugen. Mogelijk hebt u een speciale instantie nodig, afhankelijk van de grootte van de logische partitie (LPAR) die wordt gebruikt om DB2 uit te voeren.

-   De DB2 CF maakt gebruik van voor geheugen geoptimaliseerde virtuele machines, zoals E-series of L-series.

-   Een gedeeld opslagcluster\_dat gebruik\_maakt van virtuele DS4 v2-machines met Linux.

-   Het management jumpbox\_is een\_standaard DS2 v2 virtuele machine met Linux.  Een alternatief is Azure Bastion, een service die een veilige RDP/SSH-ervaring biedt voor alle VM's in uw virtuele netwerk.

-   De client is\_een\_standaard DS3 v2 virtuele machine met Windows (gebruikt voor het testen).

-   *Optioneel*. Een getuigenserver. Dit is alleen nodig met bepaalde eerdere versies van Db2 pureScale. In dit voorbeeld\_wordt\_gebruik gemaakt van een standaard DS3 v2 virtuele machine met Linux (gebruikt voor DB2 pureScale).

> [!NOTE]
> Een DB2 pureScale-cluster vereist ten minste twee DB2-exemplaren. Het vereist ook een cache-instantie en een voorbeeld van een vergrendelingsmanager.

### <a name="storage-considerations"></a>Opslagoverwegingen

Net als Oracle RAC is DB2 pureScale een high-performance block I/O, scale-out database. We raden u aan de grootste [Azure premium SSD-optie](disks-types.md) te gebruiken die aan uw behoeften voldoet. Kleinere opslagopties kunnen geschikt zijn voor ontwikkelings- en testomgevingen, terwijl productieomgevingen vaak meer opslagcapaciteit nodig hebben. De voorbeeldarchitectuur maakt gebruik van [P30](https://azure.microsoft.com/pricing/details/managed-disks/) vanwege de verhouding tussen IOPS en grootte en prijs. Ongeacht de grootte, gebruik Premium Storage voor de beste prestaties.

DB2 pureScale maakt gebruik van een gedeelde architectuur, waarbij alle gegevens toegankelijk zijn vanaf alle clusterknooppunten. Premium-opslag moet worden gedeeld in meerdere instanties, zowel op aanvraag als op specifieke instanties.

Een groot DB2 pureScale-cluster kan 200 terabyte (TB) of meer premium gedeelde opslag vereisen, met IOPS van 100.000. DB2 pureScale ondersteunt een iSCSI-blokinterface die u op Azure gebruiken. De iSCSI-interface vereist een gedeeld opslagcluster dat u implementeren met S2D of een ander hulpmiddel. Met dit type oplossing wordt een vSAN-apparaat (virtual storage area network) in Azure. DB2 pureScale gebruikt het vSAN om het geclusterde bestandssysteem te installeren dat wordt gebruikt om gegevens tussen virtuele machines te delen.

### <a name="networking-considerations"></a>Aandachtspunten voor netwerken

IBM raadt InfiniBand-netwerken aan voor alle leden in een DB2 pureScale-cluster. DB2 pureScale maakt ook gebruik van remote direct memory access (RDMA), indien beschikbaar, voor de CF's.

Tijdens de installatie maakt u een [Azure-brongroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) met alle virtuele machines. In het algemeen groepeert u resources op basis van hun levensduur en wie ze beheert. De virtuele machines in deze architectuur vereisen [versnelde netwerken.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) Het is een Azure-functie die consistente, ultralage netwerklatentie biedt via single-root I/O-virtualisatie (SR-IOV) naar een virtuele machine.

Elke Azure virtuele machine wordt geïmplementeerd in een virtueel netwerk dat subnetten heeft: main, Gluster FS front end (gfsfe), Gluster FS back end (bfsbe), DB2 pureScale (db2be) en DB2 pureScale front end (db2fe). Het installatiescript maakt ook de primaire [NIC's](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) op de virtuele machines in het hoofdsubnet.

Gebruik [netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) om het netwerkverkeer binnen het virtuele netwerk te beperken en de subnetten te isoleren.

Op Azure moet DB2 pureScale TCP/IP gebruiken als netwerkverbinding voor opslag.

## <a name="next-steps"></a>Volgende stappen

-   [Deze architectuur implementeren op Azure](deploy-ibm-db2-purescale-azure.md)
