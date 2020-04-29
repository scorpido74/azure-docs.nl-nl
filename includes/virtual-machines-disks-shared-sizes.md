---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008327"
---
Momenteel kunnen alleen Ultra disks en Premium Ssd's gedeelde schijven inschakelen. Verschillende schijf grootten kunnen een andere `maxShares` limiet hebben, die u niet overschrijdt bij `maxShares` het instellen van de waarde. Voor Premium Ssd's zijn de schijf grootten die ondersteuning bieden voor het delen van hun schijven P15 en hoger.

Voor elke schijf kunt u een `maxShares` waarde definiëren die het maximum aantal knoop punten vertegenwoordigt die de schijf gelijktijdig kunnen delen. Als u bijvoorbeeld van plan bent om een failover-cluster met twee knoop punten in te stellen, `maxShares=2`stelt u in. De maximum waarde is een bovengrens. Knoop punten kunnen worden toegevoegd aan of het cluster verlaten (koppelen of ontkoppelen van de schijf) zolang het aantal knoop punten lager is dan `maxShares` de opgegeven waarde.

> [!NOTE]
> De `maxShares` waarde kan alleen worden ingesteld of bewerkt wanneer de schijf wordt losgekoppeld van alle knoop punten.

### <a name="premium-ssd-ranges"></a>Premium-SSD bereiken

In de volgende tabel ziet u de toegestane maximum waarden `maxShares` voor Premium-schijf grootten:

|Schijf grootten  |limiet voor maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

De limiet voor IOPS en band breedte voor een schijf wordt niet beïnvloed `maxShares` door de waarde. De maximale IOPS van een P15-schijf is bijvoorbeeld 1100 of maxShares = 1 of maxShares > 1.

### <a name="ultra-disk-ranges"></a>Ultra schijf bereiken

De minimum `maxShares` waarde is 1 en de maximum `maxShares` waarde is 5. Er zijn geen beperkingen voor de grootte van ultra schijven, ongeacht de grootte van een ultra schijf `maxShares`kan elke waarde voor bevatten, tot en met de maximum waarde.