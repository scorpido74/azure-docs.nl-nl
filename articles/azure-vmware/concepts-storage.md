---
title: Concepten-opslag
description: Meer informatie over de mogelijkheden voor de belangrijkste opslag in de Azure VMware-oplossing (AVS) preview van persoonlijke Clouds.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7e58a829a40d590b7936a58ccdc866211a4f5cb4
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740354"
---
# <a name="azure-vmware-solution-avs-preview-storage-concepts"></a>Azure VMware-oplossing (AVS) voor beeld van opslag concepten

Persoonlijke Clouds van AVS bieden systeem eigen opslag voor het hele cluster met VMware vSAN. Alle lokale opslag van elke host in een cluster wordt gebruikt in een vSAN-Data Store, en versleuteling van gegevens op rest is standaard beschikbaar en is ingeschakeld. U kunt Azure Storage resources gebruiken om de opslag mogelijkheden van uw persoonlijke Clouds uit te breiden.

## <a name="vsan-clusters"></a>vSAN-clusters

Lokale opslag in elke clusterhost wordt gebruikt als onderdeel van een vSAN-gegevens opslag. Alle diskgroups gebruiken een NVMe-cache-laag van 1,6 TB met de onbewerkte, per host op SSD gebaseerde capaciteit van 15,4 TB. De grootte van de laag voor onbewerkte capaciteit van een cluster is de capaciteit per host van het aantal hosts. Zo biedt een cluster met vier hosts een onbewerkte capaciteit van 61,6 TB in de laag vSAN-capaciteit.

Lokale opslag in clusterhosts wordt gebruikt in vSAN gegevens opslag voor het hele cluster. Alle gegevens opslag worden gemaakt als onderdeel van een privécloud-implementatie en zijn direct beschikbaar voor gebruik. De cloudadmin-gebruiker en alle gebruikers in de groep CloudAdmin kunnen gegevens opslag beheren met deze vSAN bevoegdheden:
- Data Store. AllocateSpace
- Datastore.Browse
- Data Store. config
- Data Store. Delete File
- Data Store. File Management
- Data Store. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Versleuteling van data-at-rest

vSAN data stores gebruiken standaard versleuteling voor Data-at-rest. De versleutelings oplossing is op KMS gebaseerd en ondersteunt vCenter-bewerkingen voor sleutel beheer. Sleutels worden opgeslagen versleuteld, verpakt met een Azure Key Vault hoofd sleutel op basis van HSM. Wanneer een host om de een of andere reden uit een cluster wordt verwijderd, worden de gegevens op Ssd's onmiddellijk ongeldig gemaakt.

## <a name="scaling"></a>Schalen

De capaciteit van de systeem eigen cluster opslag wordt geschaald door hosts toe te voegen aan een cluster. Voor clusters die fungeren als host, wordt de onbewerkte capaciteit van het cluster uitgebreid met 15,4 TB per extra host. Clusters die zijn gebouwd met GP-hosts hebben hun onbewerkte capaciteit met 7,7 TB gestegen met elke extra host. In beide typen clusters nemen hosts ongeveer 10 minuten toe om aan een cluster te worden toegevoegd. Zie de [zelf studie zelf een persoonlijke Cloud schalen] [zelf studie-schalen-Private-Cloud] voor instructies voor het schalen van clusters.

## <a name="azure-storage-integration"></a>Integratie van Azure Storage

U kunt Azure Storage-services gebruiken voor workloads die worden uitgevoerd in uw privécloud. De Azure Storage-services omvatten opslag accounts, Table Storage en Blob Storage. Met de verbinding van werk belastingen voor Azure Storage-services wordt het Internet niet gepasseren. Deze connectiviteit biedt extra beveiliging en maakt het u mogelijk om op SLA gebaseerde Azure Storage-services te gebruiken in uw werk belastingen in uw privécloud.

## <a name="next-steps"></a>Volgende stappen

De volgende stap is om meer te weten te komen over [persoonlijke Cloud identiteits concepten][concepts-identity].

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorials-scale-private-cloud]: ./tutorials-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md