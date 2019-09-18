---
title: Een HPC-cache van Azure maken
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: c3d14eaefaa1f317cb061273866ffee83747f12b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036848"
---
# <a name="configure-aggregated-namespace"></a>Geaggregeerde naam ruimte configureren
<!-- change link in GUI -->

Met de HPC-cache van Azure kunnen clients toegang krijgen tot diverse opslag systemen via een virtuele naam ruimte die de details van het back-end-opslag systeem verbergt.

Wanneer u een opslag doel toevoegt, stelt u het client gerichte bestandspad in. Client computers koppelen dit bestandspad. U kunt het opslag doel dat is gekoppeld aan het pad, wijzigen. U kunt bijvoorbeeld een hardware-opslag systeem vervangen door de Cloud opslag zonder dat u client gerichte procedures moet herschrijven.

## <a name="aggregated-namespace-example"></a>Voor beeld van geaggregeerde naam ruimte

Plan uw geaggregeerde naam ruimte, zodat client computers de benodigde informatie kunnen bereiken en beheerders en werk stroom technici de paden eenvoudig kunnen onderscheiden.

Denk bijvoorbeeld aan een systeem waarbij een Azure HPC-cache-exemplaar wordt gebruikt voor het verwerken van gegevens die zijn opgeslagen in Azure Blob. De analyse vereist sjabloon bestanden die zijn opgeslagen in een on-premises Data Center.

De sjabloon gegevens worden opgeslagen in een Data Center en de informatie die nodig is voor deze taak wordt opgeslagen in de volgende submappen:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

In het Data Center-opslag systeem zijn de volgende export bewerkingen beschikbaar: 

    /
    /goldline
    /goldline/templates

De gegevens die moeten worden geanalyseerd, zijn gekopieerd naar een Azure Blob Storage-container met de naam ' sourcecollection ' met het [hulp programma CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Als u eenvoudige toegang via de cache wilt toestaan, kunt u overwegen om opslag doelen met deze virtuele naam ruimte paden te maken:

| Pad van de back-end-NFS of BLOB-container | Pad naar virtuele naam ruimte |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

Aangezien de NFS-bron paden submappen van dezelfde export zijn, moet u meerdere naam ruimte paden van hetzelfde opslag doel definiëren. 

| Hostnaam van opslag doel  | Pad naar NFS-export      | Pad naar submap | Pad naar naam ruimte    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP-adres of hostnaam* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP-adres of hostnaam* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Een client toepassing kan de cache koppelen en eenvoudig toegang krijgen tot de geaggregeerde naam ruimte bestandspad/source,/templates/sku798 en/templates/sku980.

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt besloten hoe u het virtuele bestands systeem instelt, [maakt u opslag doelen](hpc-cache-add-storage.md) om uw back-end-opslag toe te wijzen aan uw client gerichte virtuele filepath.
