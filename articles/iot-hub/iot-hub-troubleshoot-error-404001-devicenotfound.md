---
title: Problemen met Azure IoT Hub-fout 404001 DeviceNotFound
description: Meer informatie over het oplossen van fout 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76960826"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

In dit artikel worden de oorzaken en oplossingen voor **404001 DeviceNotFound** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Tijdens een Cloud-naar-apparaat-communicatie (C2D), zoals C2D-bericht, dubbele update of directe methode, mislukt de bewerking met fout **404001 DeviceNotFound**.

## <a name="cause"></a>Oorzaak

De bewerking is mislukt omdat het apparaat niet kan worden gevonden door IoT Hub. Het apparaat is niet geregistreerd of is uitgeschakeld.

## <a name="solution"></a>Oplossing

Registreer de apparaat-ID die u hebt gebruikt en probeer het opnieuw.