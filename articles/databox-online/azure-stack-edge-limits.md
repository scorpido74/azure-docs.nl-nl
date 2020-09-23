---
title: Pro-limieten Azure Stack rand | Microsoft Docs
description: Meer informatie over limieten en aanbevolen grootten tijdens het implementeren en uitvoeren van Azure Stack Edge Pro, inclusief service limieten, limieten voor apparaten en opslag limieten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5f3c39ce7334145d3ffc1d54badb1f7b766da70f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904449"
---
# <a name="azure-stack-edge-pro-limits"></a>Limieten voor Azure Stack Edge Pro R

Houd rekening met deze beperkingen wanneer u uw Microsoft Azure Stack Edge Pro-oplossing implementeert en gebruikt. 

## <a name="azure-stack-edge-service-limits"></a>Service limieten voor Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Limieten voor Azure Stack edge-apparaten

In de volgende tabel worden de limieten voor het Azure Stack Edge Pro-apparaat beschreven. 

| Description | Waarde |
|---|---|
|Nee. bestanden per apparaat |100.000.000 |
|Nee. van shares per apparaat |24 |
|Nee. van shares per container |1 |
|De maximale bestands grootte die naar een share is geschreven| 5 TB |

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limieten voor Azure Storage-account en object grootte

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden om Azure Stack Edge Pro te implementeren](azure-stack-edge-deploy-prep.md)
