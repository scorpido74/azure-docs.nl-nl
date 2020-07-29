---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be014a257a68a266a5946bf5822b094c5a968f6e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535525"
---
- Er kan slechts één virtueel netwerk aan een object voor schijftoegang worden gekoppeld.
- Het virtuele netwerk moet zich in hetzelfde abonnement als uw object voor schijftoegang bevinden om ze te koppelen.
- Er kunnen maximaal 10 schijven of momentopnamen worden geïmporteerd of geëxporteerd op hetzelfde moment met hetzelfde object voor schijftoegang.
- U kunt geen handmatige goedkeuring aanvragen om een virtueel netwerk te koppelen aan een object voor schijftoegang.
- De differentiële mogelijkheid wordt niet ondersteund voor incrementele momentopnamen die zijn gekoppeld aan een object voor schijftoegang.