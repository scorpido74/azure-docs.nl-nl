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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008327"
---
Voorlopig kunnen alleen ultraschijven en premium SSD's gedeelde schijven inschakelen. Verschillende schijfformaten kunnen `maxShares` een andere limiet hebben, `maxShares` die u niet overschrijden bij het instellen van de waarde. Voor premium SSD's zijn de schijfformaten die het delen van hun schijven ondersteunen P15 en groter.

Voor elke schijf `maxShares` u een waarde definiëren die het maximum aantal knooppunten vertegenwoordigt dat tegelijkertijd de schijf kan delen. Als u bijvoorbeeld van plan bent een failovercluster met twee `maxShares=2`nodes in te stellen, stelt u . De maximale waarde is een bovengrens. Knooppunten kunnen het cluster aansluiten of verlaten (de schijf monteren of ontkoppelen) `maxShares` zolang het aantal knooppunten lager is dan de opgegeven waarde.

> [!NOTE]
> De `maxShares` waarde kan alleen worden ingesteld of bewerkt wanneer de schijf is losgekoppeld van alle knooppunten.

### <a name="premium-ssd-ranges"></a>Premium SSD-bereiken

In de volgende tabel worden de `maxShares` toegestane maximumwaarden per premium schijfgrootte geïllustreerd:

|Schijfformaten  |maxShares-limiet  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

De IOPS- en bandbreedtelimieten voor een `maxShares` schijf worden niet beïnvloed door de waarde. De maximale IOPS van een P15-schijf is bijvoorbeeld 1100, of het nu gaat om maxShares = 1 of maxShares > 1.

### <a name="ultra-disk-ranges"></a>Ultra-schijfbereiken

De `maxShares` minimumwaarde is 1, `maxShares` terwijl de maximale waarde 5 is. Er zijn geen grootte beperkingen op ultra schijven, elke `maxShares`grootte ultra schijf kan elke waarde gebruiken voor, tot en met de maximale waarde.