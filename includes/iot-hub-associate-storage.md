---
title: bestand opnemen
description: bestand opnemen
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76020927"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Een Azure Storage-account koppelen aan IoT Hub

Omdat de gesimuleerde apparaat-app een bestand uploadt naar een blob, moet u een [Azure Storage-account](../articles/storage/common/storage-account-create.md) hebben dat is gekoppeld aan uw IoT-hub. Wanneer u een Azure Storage-account koppelt aan een IoT-hub, genereert de IoT-hub een SAS URI. Een apparaat kan deze SAS URI gebruiken om een bestand veilig te uploaden naar een blobcontainer. De IoT Hub-service en de sdk's van het apparaat coördineren het proces dat de SAS URI genereert en maakt het beschikbaar voor een apparaat dat kan worden gebruikt om een bestand te uploaden.

Volg de instructies in [Bestandsuploads configureren met behulp van de Azure-portal.](../articles/iot-hub/iot-hub-configure-file-upload.md) Controleer of een blobcontainer is gekoppeld aan uw IoT-hub en dat bestandsmeldingen zijn ingeschakeld.

![Bestandsmeldingen inschakelen in portal](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
