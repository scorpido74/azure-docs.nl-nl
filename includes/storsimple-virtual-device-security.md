---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176079"
---
<!--v-sharos 10/13/2105 virtual device security-->

Houd rekening met de volgende beveiligingsoverwegingen wanneer u het virtuele StorSimple-apparaat gebruikt:

* Het virtuele apparaat is beveiligd via uw Microsoft Azure-abonnement. Dit betekent dat als u het virtuele apparaat gebruikt en uw Azure-abonnement is gecompromitteerd, de gegevens die op uw virtuele apparaat zijn opgeslagen, ook gevoelig zijn.
* De openbare sleutel van het certificaat dat wordt gebruikt om gegevens te versleutelen die zijn opgeslagen in Azure StorSimple, wordt veilig beschikbaar gesteld aan de klassieke Azure-portal en de privésleutel wordt bewaard met het StorSimple-apparaat. Op het virtuele storSimple-apparaat worden zowel de openbare als de privésleutels opgeslagen in Azure.
* Het virtuele apparaat wordt gehost in het Microsoft Azure-datacenter.

