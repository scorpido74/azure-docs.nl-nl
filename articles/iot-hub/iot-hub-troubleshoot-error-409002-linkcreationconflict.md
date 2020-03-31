---
title: Azure IoT Hub-fout oplossen 409002-koppelingsconflict
description: Begrijpen hoe u fout 409002 LinkCreationConflict oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3c7515be573a0b74a39a77a91fbc554862c7f7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960774"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

In dit artikel worden de oorzaken en oplossingen voor **409002 LinkCreationConflict-fouten** beschreven.

## <a name="symptoms"></a>Symptomen

U ziet de fout **409002 LinkCreationConflict** die is aangemeld bij diagnostische logboeken, samen met de verbinding van het apparaat of een storing in het bericht van cloud naar apparaat. 

<!-- When using AMQP? -->

## <a name="cause"></a>Oorzaak

Over het algemeen treedt deze fout op wanneer IoT Hub detecteert dat een client meer dan één verbinding heeft. Wanneer er een nieuwe verbindingsaanvraag binnenkomt voor een apparaat met een bestaande verbinding, wordt de bestaande verbinding met deze fout verbroken.

### <a name="cause-1"></a>Oorzaak 1

In het meest voorkomende geval zorgt een afzonderlijk probleem (zoals [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) ervoor dat het apparaat de verbinding verbreekt. Het apparaat probeert de verbinding onmiddellijk te herstellen, maar IoT Hub beschouwt het apparaat nog steeds als verbonden. IoT Hub sluit de vorige verbinding en registreert deze fout.

### <a name="cause-2"></a>Oorzaak 2

Foutieve logica aan de apparaatzijde zorgt ervoor dat het apparaat de verbinding tot stand brengt wanneer er al een is geopend.

## <a name="solution"></a>Oplossing

Deze fout wordt meestal weergegeven als een bijwerking van een ander, tijdelijk probleem, dus zoek naar andere fouten in de logboeken om verder op te lossen. Zorg er anders voor dat u alleen een nieuwe verbindingsaanvraag uitgeeft als de verbinding wordt verbroken.
