---
title: Een HPC-cache van Azure maken
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73582183"
---
# <a name="plan-the-aggregated-namespace"></a>De geaggregeerde naamruimte plannen

Met de HPC-cache van Azure kunnen clients toegang krijgen tot diverse opslag systemen via een virtuele naam ruimte die de details van het back-end-opslag systeem verbergt.

Wanneer u een opslag doel toevoegt, stelt u het op de client gerichte bestandspad in. Client computers koppelen dit bestandspad en kunnen aanvragen voor het lezen van bestanden naar de cache maken in plaats van het opslag systeem rechtstreeks te koppelen.

Omdat Azure HPC cache dit virtuele bestands systeem beheert, kunt u het opslag doel wijzigen zonder het client gerichte pad te wijzigen. U kunt bijvoorbeeld een hardware-opslag systeem vervangen door de Cloud opslag zonder dat u client gerichte procedures moet herschrijven.

## <a name="aggregated-namespace-example"></a>Voor beeld van geaggregeerde naam ruimte

Plan uw geaggregeerde naam ruimte, zodat de client computers de benodigde informatie kunnen bereiken, zodat beheerders en werk stroom engineers de paden eenvoudig kunnen onderscheiden.

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

| Back-end-opslag systeem <br/> (NFS-bestandspad of BLOB-container) | Pad naar virtuele naam ruimte |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

Een NFS-opslag doel kan meerdere paden voor virtuele naam ruimten hebben, zolang elk een pad naar een uniek exportpad verwijst.

Omdat de NFS-bron paden submappen van dezelfde export zijn, moet u meerdere naam ruimte paden van hetzelfde opslag doel definiëren.

| Hostnaam van opslag doel  | Pad naar NFS-export      | Pad naar submap | Pad naar naam ruimte    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP-adres of hostnaam* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP-adres of hostnaam* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Een client toepassing kan de cache koppelen en eenvoudig toegang krijgen tot de geaggregeerde bestands ``/source``paden ``/templates/sku798``van de ``/templates/sku980``naam ruimte, en.

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt bepaald hoe uw virtuele bestands systeem moet worden ingesteld, [maakt u opslag doelen](hpc-cache-add-storage.md) om uw back-end-opslag toe te wijzen aan de client gerichte virtuele bestands paden.
