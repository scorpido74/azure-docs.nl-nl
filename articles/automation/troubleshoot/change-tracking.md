---
title: Problemen met Azure Wijzigingen bijhouden oplossen
description: Meer informatie over het oplossen van problemen met de functie Wijzigingen bijhouden en de inventaris van Azure Automation.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 51a9dbf8be6538534c05a4b8b6fcd913ef8c6ae3
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769928"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Problemen met Wijzigingen bijhouden en Inventaris oplossen

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenario: Wijzigingen bijhouden records niet worden weer gegeven voor Windows-computers

#### <a name="issue"></a>Probleem

U ziet geen inventaris-of Wijzigingen bijhouden resultaten voor Windows-computers die zijn voor bereid op Wijzigingen bijhouden.

#### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

1. **Micro soft Monitoring Agent** wordt niet uitgevoerd
2. De communicatie met het Automation-account wordt geblokkeerd.
3. De Management Packs voor Wijzigingen bijhouden zijn niet gedownload.
4. De virtuele machine die wordt uitgevoerd, kan afkomstig zijn van een gekloonde computer die niet is Sysprep met micro soft monitoring agent geïnstalleerd.

#### <a name="resolution"></a>Resolutie

1. Controleer of de **micro soft Monitoring Agent** (HealthService. exe) wordt uitgevoerd op de computer.
1. Controleer **Logboeken** op de computer en zoek naar gebeurtenissen die het woord `changetracking` bevatten.
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
> Fout tracering is standaard ingeschakeld, als u uitgebreide fout berichten wilt inschakelen, zoals in het voor gaande voor beeld, gebruikt u `VER`-para meter. Gebruik `INF` bij het aanroepen van `StartTracing.cmd`voor informatie tracering.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
