---
title: Mainframeopslag verplaatsen naar Azure Storage
description: Enorm schaalbare Azure-opslagbronnen kunnen mainframegebaseerde organisaties helpen bij het migreren en moderniseren van IBM z14-toepassingen.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288911"
---
# <a name="move-mainframe-storage-to-azure"></a>Mainframeopslag verplaatsen naar Azure

Als u mainframeworkloads wilt uitvoeren op Microsoft Azure, moet u weten hoe de mogelijkheden van uw mainframe zich verhouden tot Azure. De enorm schaalbare opslagbronnen kunnen organisaties helpen te moderniseren zonder de toepassingen waar ze op vertrouwen te verlaten.

Azure biedt mainframe-achtige functies en opslagcapaciteit die vergelijkbaar is met IBM z14-gebaseerde systemen (het meest recente model vanaf dit schrijven). In dit artikel vindt u hoe u vergelijkbare resultaten op Azure krijgen.

## <a name="mainframe-storage-at-a-glance"></a>Mainframe-opslag in één oogopslag

Het IBM mainframe kenmerkt opslag op twee manieren. De eerste is een direct access storage device (DASD). De tweede is sequentiële opslag. Voor het beheer van opslag biedt het mainframe het Subsysteem Gegevensfaciliteit opslagbeheer (DFSMS). Het beheert de toegang tot gegevens tot de verschillende opslagapparaten.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) verwijst naar een apart apparaat voor secundaire (niet in-memory) opslag waarmee een uniek adres kan worden gebruikt voor directe toegang tot gegevens. Oorspronkelijk was de term DASD van toepassing op draaiende schijven, magnetische drums of gegevenscellen. Nu kan de term echter ook van toepassing zijn op solid-state opslagapparaten (SSD's), storage area networks (SAN's), network attached storage (NAS) en optische drives. Voor de toepassing van dit document verwijst DASD naar draaiende schijven, SAN's en SSD's.

In tegenstelling tot DASD-opslag verwijst sequentiële opslag op een mainframe naar apparaten zoals tapedrives waar gegevens vanaf een beginpunt worden geopend en vervolgens in een regel worden gelezen of geschreven.

Opslagapparaten zijn meestal verbonden met behulp van een glasvezelverbinding (FICON) of zijn rechtstreeks toegankelijk op de IO-bus van het mainframe met behulp van [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), een IBM-technologie voor snelle communicatie tussen partities op een server met een hypervisor.

De meeste mainframesystemen scheiden opslag in twee typen:

- *Online opslag* (ook bekend als hot storage) is nodig voor dagelijkse activiteiten. DASD-opslag wordt meestal gebruikt voor dit doel. Sequentiële opslag, zoals dagelijkse tapeback-ups (logisch of fysiek), kan echter ook voor dit doel worden gebruikt.

- *Archiefopslag* (ook wel koude opslag genoemd) is niet gegarandeerd om op een bepaald moment te worden gemonteerd. In plaats daarvan wordt het gemonteerd en toegankelijk als dat nodig is. Archiefopslag wordt vaak geïmplementeerd met behulp van sequentiële tapeback-ups (logisch of fysiek) voor opslag.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe versus IO-latentie en IOPS

Mainframes worden vaak gebruikt voor toepassingen die hoge prestaties IO en lage IO latency vereisen. Ze kunnen dit doen met behulp van de FICON-verbindingen met IO-apparaten en HiperSockets. Wanneer HiperSockets worden gebruikt om toepassingen en apparaten rechtstreeks aan te sluiten op het IO-kanaal van een mainframe, kan latentie in de microseconden worden bereikt.

## <a name="azure-storage-at-a-glance"></a>Azure-opslag in één oogopslag

Azure infrastructure-as-a-service[(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)opties voor opslag bieden vergelijkbare mainframecapaciteit.

Microsoft biedt petabytes aan opslagruimte voor toepassingen die in Azure worden gehost en u hebt verschillende opslagopties. Deze variëren van SSD-opslag voor hoge prestaties tot goedkope blobopslag voor massaopslag en archieven. Daarnaast biedt Azure een optie voor redundantie voor gegevens voor opslag, iets dat meer moeite kost om in een mainframeomgeving in te stellen.

Azure-opslag is beschikbaar als [Azure Disks,](/azure/virtual-machines/windows/managed-disks-overview) [Azure Files](/azure/storage/files/storage-files-introduction)en [Azure Blobs,](/azure/storage/blobs/storage-blobs-overview) zoals de volgende tabel samenvat. Meer informatie over [wanneer u elk .](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Beschrijving</th><th>Gebruik wanneer u dat wilt:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Biedt een SMB-interface, clientbibliotheken en een <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST-interface</a> waarmee toegang vanaf elke locatie tot opgeslagen bestanden mogelijk is.
</td>
<td><ul>
<li>Een toepassing naar de cloud opheffen en verplaatsen wanneer de toepassing de API's voor native bestandssystemen gebruikt om gegevens te delen tussen de toepassing die in Azure wordt uitgevoerd.</li>
<li>Bewaar ontwikkel- en foutopsporingstools die vanuit veel VM's moeten worden geopend.</li>
</ul>
</td>
</tr>
<tr><td>Azure-blobs
</td>
<td>Biedt clientbibliotheken en een <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST-interface</a> waarmee ongestructureerde gegevens op grote schaal kunnen worden opgeslagen en geopend in blokblobs. Ondersteunt ook <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> voor oplossingen voor big data-analyse van bedrijven.
</td>
<td><ul>
<li>Ondersteuning voor streaming- en random-access scenario's in een toepassing.</li>
<li>Overal toegang tot toepassingsgegevens hebben.</li>
<li>Bouw een enterprise data lake op Azure en voer big data analytics uit.</li>
</ul></td>
</tr>
<tr><td>Azure-schijven
</td>
<td>Biedt clientbibliotheken en een <a href="https://docs.microsoft.com/rest/api/compute/disks">REST-interface</a> waarmee gegevens voortdurend kunnen worden opgeslagen en geopend vanaf een aangesloten virtuele harde schijf.
</td>
<td><ul>
<li>Schakel toepassingen op en verschuif die gebruikmaken van native bestandssysteem API's om gegevens te lezen en te schrijven naar hardnekkige schijven.</li>
<li>Sla gegevens op die niet van buiten de VM hoeven te worden geopend waaraan de schijf is gekoppeld.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure hot (online) en koude (archief)opslag

Het type opslag voor een bepaald systeem is afhankelijk van de vereisten van het systeem, inclusief opslaggrootte, doorvoer en IOPS. Voor DASD-opslag op een mainframe gebruiken toepassingen op Azure doorgaans Azure Disks-schijfopslag. Voor mainframearchiefopslag wordt blob-opslag gebruikt op Azure.

SSD's bieden de hoogste opslagprestaties op Azure. De volgende opties zijn beschikbaar (vanaf het schrijven van dit document):

| Type         | Grootte           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra - SSD    | 4 GB tot 64 TB  | 1.200 tot 160.000 IOPS |
| Premium - SSD  | 32 GB tot 32 TB | 12 tot 15.000 IOPS     |
| Standard - SSD | 32 GB tot 32 TB | 12 tot 2.000 IOPS      |

Blob-opslag biedt het grootste volume aan opslagruimte op Azure. Naast de opslaggrootte biedt Azure zowel beheerde als onbeheerde opslag. Met beheerde opslag zorgt Azure voor het beheer van de onderliggende opslagaccounts. Met onbeheerde opslag neemt de gebruiker de verantwoordelijkheid voor het instellen van Azure-opslagaccounts van de juiste grootte om aan de opslagvereisten te voldoen.

## <a name="next-steps"></a>Volgende stappen

- [Mainframemigratie](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting op Azure Virtual Machines](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mainframecompute verplaatsen naar Azure](mainframe-compute-Azure.md)
- [Beslissen wanneer Azure Blobs, Azure Files of Azure Disks moeten worden gebruikt](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standaard SSD-beheerde schijven voor Azure VM-workloads](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

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
