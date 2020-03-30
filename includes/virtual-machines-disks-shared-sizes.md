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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471681"
---
Voorlopig kunnen alleen premium SSD's gedeelde schijven inschakelen. De schijfformaten die deze functie ondersteunen zijn P15 en groter. Verschillende schijfformaten kunnen `maxShares` een andere limiet hebben, `maxShares` die u niet overschrijden bij het instellen van de waarde.

Voor elke schijf `maxShares` u een waarde definiëren die het maximum aantal knooppunten vertegenwoordigt dat tegelijkertijd de schijf kan delen. Als u bijvoorbeeld van plan bent een failovercluster met twee `maxShares=2`nodes in te stellen, stelt u . De maximale waarde is een bovengrens. Knooppunten kunnen het cluster aansluiten of verlaten (de schijf monteren of ontkoppelen) `maxShares` zolang het aantal knooppunten lager is dan de opgegeven waarde.

> [!NOTE]
> De `maxShares` waarde kan alleen worden ingesteld of bewerkt wanneer de schijf is losgekoppeld van alle knooppunten.

In de volgende tabel worden de `maxShares` toegestane maximumwaarden per schijfgrootte geïllustreerd:

|Schijfformaten  |maxShares-limiet  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

De IOPS- en bandbreedtelimieten voor een `maxShares` schijf worden niet beïnvloed door de waarde. De maximale IOPS van een P15-schijf zijn bijvoorbeeld 1100, of het nu gaat om maxShares = 1 of maxShares > 1.