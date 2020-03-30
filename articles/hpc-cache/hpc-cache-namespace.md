---
title: Een Azure HPC-cache maken
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582183"
---
# <a name="plan-the-aggregated-namespace"></a>De geaggregeerde naamruimte plannen

Azure HPC Cache biedt clients toegang tot verschillende opslagsystemen via een virtuele naamruimte die de details van het back-endopslagsysteem verbergt.

Wanneer u een opslagdoel toevoegt, stelt u het clientgerichte bestandspad in. Clientmachines monteren dit bestandspad en kunnen bestandsleesverzoeken indienen in de cache in plaats van het opslagsysteem rechtstreeks te monteren.

Omdat Azure HPC Cache dit virtuele bestandssysteem beheert, u het opslagdoel wijzigen zonder het pad dat naar de client gericht is te wijzigen. U bijvoorbeeld een hardwareopslagsysteem vervangen door cloudopslag zonder dat u clientgerichte procedures hoeft te herschrijven.

## <a name="aggregated-namespace-example"></a>Voorbeeld van geaggregeerde naamruimte

Plan uw geaggregeerde naamruimte zo dat clientmachines gemakkelijk de informatie kunnen bereiken die ze nodig hebben, zodat beheerders en workflowengineers de paden gemakkelijk kunnen onderscheiden.

Denk bijvoorbeeld aan een systeem waarbij een Azure HPC-cache-exemplaar wordt gebruikt om gegevens te verwerken die zijn opgeslagen in Azure Blob. De analyse vereist sjabloonbestanden die zijn opgeslagen in een on-premises datacenter.

De sjabloongegevens worden opgeslagen in een datacenter en de informatie die nodig is voor deze taak wordt opgeslagen in deze submappen:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Het datacenteropslagsysteem legt deze export bloot:

    /
    /goldline
    /goldline/templates

De te analyseren gegevens zijn gekopieerd naar een Azure Blob-opslagcontainer met de naam 'bronverzameling' met behulp van het [HULPPROGRAMMA CLFSLoad.](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)

Als u eenvoudig toegang wilt verlenen via de cache, u overwegen opslagdoelen te maken met deze virtuele naamruimtepaden:

| Back-end opslagsysteem <br/> (NFS-bestandspad of Blob-container) | Pad voor virtuele naamruimte |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /sjablonen/sku798      |
| /goldline/templates/acme2017/sku980     | /sjablonen/sku980      |
| bronverzameling                        | /bron/               |

Een NFS-opslagdoel kan meerdere virtuele naamruimtepaden hebben, zolang elk doel verwijst naar een uniek exportpad.

Omdat de NFS-bronpaden submappen van dezelfde export zijn, moet u meerdere naamruimtepaden definiëren vanuit hetzelfde opslagdoel.

| Hostname van opslagdoel  | NFS-exportpad      | Submappad | Naamruimtepad    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP-adres of hostnaam* | /goldline/sjablonen  | acme2017/sku798   | /sjablonen/sku798 |
| *IP-adres of hostnaam* | /goldline/sjablonen  | acme2017/sku980   | /sjablonen/sku980 |

Een clienttoepassing kan de cache monteren en eenvoudig ``/source``toegang ``/templates/sku798``krijgen ``/templates/sku980``tot de geaggregeerde bestandspaden voor naamruimte , en .

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt besloten hoe u uw virtuele bestandssysteem instelt, [maakt u opslagdoelen](hpc-cache-add-storage.md) om uw back-endopslag in kaart te brengen op uw clientgerichte virtuele bestandspaden.
