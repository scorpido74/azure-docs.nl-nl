---
title: Problemen met Azure IoT Hub-fout 409002 LinkCreationConflict
description: Meer informatie over het oplossen van fout 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81758739"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

In dit artikel worden de oorzaken en oplossingen voor **409002 LinkCreationConflict** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

U ziet de fout **409002 LinkCreationConflict** vastgelegd in Diagnostische logboeken Samen met het verbreken van het apparaat of het mislukken van Cloud-naar-apparaat-berichten. 

<!-- When using AMQP? -->

## <a name="cause"></a>Oorzaak

Deze fout treedt doorgaans op wanneer IoT Hub detecteert dat een client meer dan één verbinding heeft. Als er een nieuwe verbindings aanvraag wordt ontvangen voor een apparaat met een bestaande verbinding, IoT Hub sluit de bestaande verbinding met deze fout.

### <a name="cause-1"></a>Oorzaak 1

In het meest voorkomende geval zorgt een afzonderlijk probleem (zoals [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) ervoor dat het apparaat wordt losgekoppeld. Het apparaat probeert de verbinding onmiddellijk opnieuw tot stand te brengen, maar IoT Hub houdt toch rekening met het apparaat. IoT Hub sluit de vorige verbinding en wordt deze fout vastgelegd.

### <a name="cause-2"></a>Oorzaak 2

Beschadigde logica van het apparaat zorgt ervoor dat het apparaat de verbinding tot stand brengt wanneer er al een is geopend.

## <a name="solution"></a>Oplossing

Deze fout wordt doorgaans weer gegeven als een neven effect van een ander, tijdelijk probleem, dus zoek naar andere fouten in de logboeken om de problemen verder op te lossen. Anders moet u ervoor zorgen dat u alleen een nieuwe verbindings aanvraag uitgeeft als de verbinding wordt verbroken.
