---
title: Problemen met Azure IoT Hub-fout 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Informatie over het oplossen van fout 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960839"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

In dit artikel worden de oorzaken en oplossingen beschreven voor fouten van **403006 DeviceMaximumActiveFileFileUploadLimitexceeded.**

## <a name="symptoms"></a>Symptomen

Uw aanvraag voor het uploaden van bestanden mislukt met de foutcode **403006** en een bericht "Aantal actieve aanvragen voor het uploaden van bestanden mag niet hoger zijn dan 10".

## <a name="cause"></a>Oorzaak

Elke apparaatclient is beperkt tot [10 gelijktijdige bestandsuploads.](./iot-hub-devguide-quotas-throttling.md#other-limits) 

U de limiet eenvoudig overschrijden als uw apparaat de IoT Hub niet op de hoogte stelt wanneer het uploaden van bestanden is voltooid. Dit probleem wordt vaak veroorzaakt door een onbetrouwbaar netwerk aan de apparaatzijde.

## <a name="solution"></a>Oplossing

Zorg ervoor dat het apparaat de voltooiing van [het uploaden van IoT Hub-bestanden](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)onmiddellijk op de hoogte kan stellen. Probeer vervolgens [de TTL van het SAS-token te verminderen voor bestandsuploadconfiguratie.](iot-hub-configure-file-upload.md)

## <a name="next-steps"></a>Volgende stappen

Zie [Bestanden uploaden met IoT Hub](./iot-hub-devguide-file-upload.md) en [IoT Hub-bestandsuploads configureren met de Azure-portal](./iot-hub-configure-file-upload.md)voor meer informatie over bestandsuploads.