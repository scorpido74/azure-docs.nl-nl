---
title: Azure Data Box Gateway-limieten | Microsoft Documenten
description: Beschrijft systeemlimieten en aanbevolen formaten voor de Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: 641d7410e414be2adae2a83840c90a680aedd2fc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683357"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway-limieten

Houd rekening met deze limieten wanneer u uw Microsoft Azure Data Box Gateway-oplossing implementeert en exploiteert. 

## <a name="data-box-gateway-service-limits"></a>Servicelimieten voor Data Box Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Beperkingen van databoxgateway-apparaten

In de volgende tabel worden de limieten voor het gegevensboxgatewayapparaat beschreven.

| Beschrijving | Waarde |
|---|---|
|Nee. bestanden per apparaat |100 miljoen <br> Voor elke 25 miljoen bestanden die worden toegevoegd (met maximale limiet van 100 miljoen), moet u 2 TB schijfruimte, 8 GB RAM en 4 cpu-cores toevoegen. |
|Nee. aandelen per apparaat |24 |
|Nee. aandelen per Azure-opslagcontainer |1 |
|Maximale bestandsgrootte geschreven naar een aandeel|Voor een virtueel apparaat van 2 TB is de maximale bestandsgrootte 500 GB. <br> De maximale bestandsgrootte neemt toe met de grootte van de gegevensschijf in de voorgaande verhouding tot maximaal 5 TB. |

## <a name="azure-storage-limits"></a>Azure-opslaglimieten

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Grootte van Azure-opslagaccount en objectgroottelimieten

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limieten voor azure-objectgrootte

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Volgende stappen

- [Voorbereidingen voor de implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
