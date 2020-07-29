---
title: Problemen met Azure IoT Hub-fout 400027 ConnectionForcefullyClosedOnNewConnection
description: Meer informatie over het oplossen van fout 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960527"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

In dit artikel worden de oorzaken en oplossingen voor **400027 ConnectionForcefullyClosedOnNewConnection** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Uw apparaat-naar-Cloud-dubbele bewerking (zoals gerapporteerde eigenschappen voor lezen of patches) of directe methode aanroep mislukt met de fout code **400027**.

## <a name="cause"></a>Oorzaak

Een andere client heeft een nieuwe verbinding gemaakt met IoT Hub met dezelfde referenties, waardoor de vorige verbinding IoT Hub gesloten. IoT Hub mogen niet meer dan één client verbinding maken met behulp van dezelfde set referenties.

## <a name="solution"></a>Oplossing

Zorg ervoor dat elke client verbinding maakt met IoT Hub met behulp van een eigen identiteit.