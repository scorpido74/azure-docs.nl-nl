---
title: Problemen met Azure Wijzigingen bijhouden oplossen
description: In dit artikel vindt u informatie over het oplossen van problemen Wijzigingen bijhouden
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: beb0b89bdbf143c89a83c0813313a8bbda7235d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564856"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Problemen met Wijzigingen bijhouden en Inventaris oplossen

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Omstandigheden Wijzigingen bijhouden records niet worden weer gegeven voor Windows-computers

#### <a name="issue"></a>Probleem

U ziet geen inventaris-of Wijzigingen bijhouden resultaten voor Windows-computers die zijn voor bereid op Wijzigingen bijhouden.

#### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

1. **Micro soft Monitoring Agent** wordt niet uitgevoerd
2. De communicatie met het Automation-account wordt geblokkeerd.
3. De Management Packs voor Wijzigingen bijhouden zijn niet gedownload.
4. De virtuele machine die wordt uitgevoerd, kan afkomstig zijn van een gekloonde computer die niet is Sysprep met micro soft monitoring agent geïnstalleerd.

#### <a name="resolution"></a>Oplossing

1. Controleer of de **micro soft Monitoring Agent** (HealthService. exe) wordt uitgevoerd op de computer.
1. Controleer **Logboeken** op de computer en zoek naar gebeurtenissen die het woord `changetracking` erin bevatten.
1. Bezoek, [netwerk planning](../automation-hybrid-runbook-worker.md#network-planning) voor meer informatie over welke adressen en poorten moeten worden toegestaan om wijzigingen bijhouden te kunnen werken.
1. Controleer of de volgende Wijzigingen bijhouden-en Inventory Management Packs lokaal bestaan:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Micro soft. intelligence packs. InventoryChangeTracking. *
    * Micro soft. intelligence packs. SingletonInventoryCollection. *
1. Als u een gekloonde installatie kopie gebruikt, sysprepert u eerst de installatie kopie en installeert u de agent van micro soft monitoring agent na het feit.

Als deze oplossingen uw probleem niet oplossen en u contact opneemt met de ondersteuning, kunt u de volgende opdrachten uitvoeren om de diagnose op de agent te verzamelen

Ga op de agent computer naar `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` en voer de volgende opdrachten uit:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Standaard is tracering van fouten ingeschakeld, als u uitgebreide fout berichten zoals het vorige voor beeld wilt inschakelen, gebruikt `VER` u de para meter. Gebruik `INF` bij het `StartTracing.cmd`aanroepen van informatie traceringen.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
