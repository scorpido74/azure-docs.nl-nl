---
title: Azure IoT Hub-fout oplossen 400027 ConnectionForcefullyClosedOnNewConnection
description: Begrijpen hoe u fout 400027 ConnectionForcefullyClosedOnNewConnection oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960527"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

In dit artikel worden de oorzaken en oplossingen beschreven voor fouten die zijn gesloten met **400027 ConnectionForcefullyClosedOnNewConnection.**

## <a name="symptoms"></a>Symptomen

Uw apparaat-naar-cloud-dubbele bewerking (zoals lees- of patchgerapporteerde eigenschappen) of directe methodeaanroep mislukt met de foutcode **400027**.

## <a name="cause"></a>Oorzaak

Een andere client heeft een nieuwe verbinding met IoT Hub gemaakt met dezelfde referenties, zodat IoT Hub de vorige verbinding heeft gesloten. IoT Hub staat niet toe dat meer dan één client verbinding maakt met dezelfde set referenties.

## <a name="solution"></a>Oplossing

Zorg ervoor dat elke client verbinding maakt met IoT Hub met zijn eigen identiteit.