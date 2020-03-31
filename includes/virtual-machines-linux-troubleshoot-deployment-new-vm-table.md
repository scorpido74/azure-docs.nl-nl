---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d93de4ed758afb5e951bb5e19f4f7adb290e461c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176038"
---
De volgende tabel bevat de mogelijke upload- en opnamecombinaties van Linux-gegeneraliseerde en gespecialiseerde OS-afbeeldingen. De combinaties die zonder fouten worden verwerkt, worden aangegeven door een Y en de combinaties die fouten zullen gooien, worden aangegeven door een N. De oorzaken en oplossingen voor de verschillende fouten die u tegenkomt worden weergegeven onder de tabel.

| OS | Upload spec. | Upload gen. | Leg spec vast. | Vang gen. |
| --- | --- | --- | --- | --- |
| Linux gen. |N<sup>1</sup> |J |N<sup>3</sup> |J |
| Linux spec. |J |N<sup>2</sup> |J |N<sup>4</sup> |

