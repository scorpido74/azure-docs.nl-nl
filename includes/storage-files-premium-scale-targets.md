---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180043"
---
#### <a name="additional-premium-file-share-level-limits"></a>Aanvullende limieten voor het niveau van het aandeel van premiumbestanden

|Onderwerp  |Doel  |
|---------|---------|
|Minimale grootte verhoging/afname    |1 GiB      |
|IOPS basislijn    |1 IOPS per GiB, tot 100.000|
|IOPS barst    |3x IOPS per GiB, tot 100.000|
|Uitgangssnelheid         |60 MiB/s + 0,06 * ingerichte GiB        |
|Invallend tarief| 40 MiB/s + 0,04 * ingerichte GiB |

#### <a name="file-level-limits"></a>Limieten voor bestandsniveau

|Onderwerp  |Premium-bestand  |Standaardbestand |
|---------|---------|---------|
|Grootte                  |1 TiB         |1 TiB|
|Max IOPS per bestand     |5.000         |1000|
|Gelijktijdige handgrepen    |2.000         |2.000|
|Uitgaand verkeer  |300 MiB/sec|      Zie standaardwaarden voor bestandsdoorvoer|
|Inkomend verkeer  |200 MiB/sec| Zie standaardwaarden voor bestandsdoorvoer|
|Doorvoer| Zie waardevan het binnendringen/ingaan van het bestand| Tot 60 MiB/sec|
