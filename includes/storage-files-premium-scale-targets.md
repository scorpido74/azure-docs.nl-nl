---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057618"
---
#### <a name="additional-premium-file-share-level-limits"></a>Extra limieten voor Premium file share-niveaus

|Gebied  |Doel  |
|---------|---------|
|Toename van minimale grootte/afname    |1 GiB      |
|IOPS basis lijn    |1 IOPS per GiB, Maxi maal 100.000|
|IOPS-bursting    |3x IOPS per GiB, Maxi maal 100.000|
|Uitgangs frequentie         |60-MiB/s + 0,06 * ingerichte GiB        |
|Ingangs frequentie| 40-MiB/s + 0,04 * ingerichte GiB |

#### <a name="file-level-limits"></a>Limieten voor bestands niveau

|Gebied  |Standaard bestand  |Premium-bestand  |
|---------|---------|---------|
|Grootte     |1 TiB         |4 TiB         |
|Maximum aantal IOPS per bestand      |1000         |5\.000         |
|Gelijktijdige ingangen     |2.000         |2.000         |
|Uitgaand verkeer     |Zie standaard waarden voor doorvoer bestanden         |300-MiB per seconde         |
|Inkomend verkeer     |Zie standaard waarden voor doorvoer bestanden         |200-MiB per seconde         |
|Doorvoer     |Maxi maal 60 MiB per seconde         |Zie Premium file ingress/uitgangs waarden         |