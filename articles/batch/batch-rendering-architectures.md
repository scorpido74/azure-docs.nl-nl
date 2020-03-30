---
title: Azure-weergavereferentiearchitecturen - Azure Batch
description: Architecturen voor het gebruik van Azure Batch en andere Azure-services om een on-premises renderfarm uit te breiden door te barsten naar de cloud
services: batch
ms.service: batch
author: davefellows
manager: evansma
ms.author: labrenne
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: 20442a6618ca9357bb3be95879b68bffca45a40d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022950"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referentiearchitecturen voor Azure-rendering

In dit artikel worden architectuurdiagrammen op hoog niveau weergegeven voor scenario's die een on-premises renderfarm uit te breiden of te 'barsten' uit te breiden naar Azure. In de voorbeelden worden verschillende opties weergegeven voor azure compute-, netwerk- en opslagservices.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybride met NFS of CVS

In het volgende diagram wordt een hybride scenario weergegeven dat de volgende Azure-services bevat:

* **Compute** - Azure Batch pool of Virtual Machine Scale Set.

* **Netwerk** - On-premises: Azure ExpressRoute of VPN. Azure: Azure VNet.

* **Opslag** - Invoer- en uitvoerbestanden: NFS of CFS met Azure VM's, gesynchroniseerd met on-premises opslag via Azure File Sync of RSync. Alternatief: Avere vFXT voor het invoeren of uitvoer van bestanden van on-premises NAS-apparaten met NFS.

  ![Wolkbreuk - Hybride met NFS of CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybride met Blobfuse

In het volgende diagram wordt een hybride scenario weergegeven dat de volgende Azure-services bevat:

* **Compute** - Azure Batch pool of Virtual Machine Scale Set.

* **Netwerk** - On-premises: Azure ExpressRoute of VPN. Azure: Azure VNet.

* **Opslag** - Invoer- en uitvoerbestanden: Blob-opslag, die is gemonteerd om resources te berekenen via Azure Blobfuse.

  ![Wolk barsten - Hybride met Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybride rekenkracht en opslag

In het volgende diagram wordt een volledig verbonden hybride scenario weergegeven voor zowel rekenkracht als opslag en bevat het de volgende Azure-services:

* **Compute** - Azure Batch pool of Virtual Machine Scale Set.

* **Netwerk** - On-premises: Azure ExpressRoute of VPN. Azure: Azure VNet.

* **Opslag** - Cross-premises: Avere vFXT. Optionele archivering van on-premises bestanden via Azure Data Box naar Blob-opslag of on-premises Avere FXT voor NAS-versnelling.

  ![Cloud bursting - Hybride rekenkracht en opslag](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van [Render-managers](batch-rendering-render-managers.md) met Azure Batch.

* Meer informatie over opties voor [Rendering in Azure](batch-rendering-service.md).