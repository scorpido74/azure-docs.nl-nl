---
title: Azure Data Box Gateway limieten | Microsoft Docs
description: Hierin worden systeem limieten en aanbevolen grootten voor de Microsoft Azure Data Box Gateway beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d160b0ead1ceb8c41bede20cb389a360d24258f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82561386"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway limieten

Houd rekening met deze beperkingen wanneer u uw Microsoft Azure Data Box Gateway oplossing implementeert en gebruikt.

## <a name="data-box-gateway-service-limits"></a>Data Box Gateway-Service limieten

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limieten voor Data Box Gateway apparaten

In de volgende tabel worden de limieten voor het Data Box Gateway apparaat beschreven.

| Beschrijving | Waarde |
|---|---|
|Nee. bestanden per apparaat |100.000.000 <br> Voor elke 25.000.000 bestanden die worden toegevoegd (met een maximum limiet van 100.000.000), moet u 2 TB aan schijf ruimte, 8 GB aan RAM-geheugen en 4 kernen CPU toevoegen. |
|Nee. van shares per apparaat |24 |
|Nee. van shares per Azure-opslag container |1 |
|De maximale bestands grootte die naar een share is geschreven|Voor een virtueel apparaat van 2 TB is de maximale bestands grootte 500 GB. <br> De maximale bestands grootte neemt toe met de grootte van de gegevens schijf in de vorige verhouding tot deze Maxi maal 5 TB bereikt. |

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limieten voor Azure Storage-account en object grootte

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Volgende stappen

- [Voorbereidingen voor de implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
