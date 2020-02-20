---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471681"
---
Voorlopig kunnen alleen Premium-Ssd's gedeelde schijven inschakelen. De schijf grootten die deze functie ondersteunen, zijn P15 en meer. Verschillende schijf grootten kunnen een andere `maxShares` limiet hebben, die u niet overschrijdt wanneer u de `maxShares` waarde instelt.

Voor elke schijf kunt u een `maxShares` waarde definiëren die het maximum aantal knoop punten vertegenwoordigt die de schijf gelijktijdig kunnen delen. Als u bijvoorbeeld van plan bent om een failover-cluster met twee knoop punten in te stellen, stelt u `maxShares=2`in. De maximum waarde is een bovengrens. Knoop punten kunnen worden toegevoegd aan of het cluster verlaten (koppelen of ontkoppelen van de schijf) zolang het aantal knoop punten lager is dan de opgegeven `maxShares` waarde.

> [!NOTE]
> De `maxShares` waarde kan alleen worden ingesteld of bewerkt wanneer de schijf wordt losgekoppeld van alle knoop punten.

In de volgende tabel ziet u de toegestane maximum waarden voor `maxShares` per schijf grootte:

|Schijf grootten  |limiet voor maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

De limieten voor IOPS en band breedte voor een schijf worden niet beïnvloed door de `maxShares` waarde. De maximale IOPS van een P15-schijf is bijvoorbeeld 1100, ongeacht of maxShares = 1 of maxShares > 1.