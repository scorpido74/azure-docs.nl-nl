---
title: Azure-setvan virtuele machineschaalsets co-lokaliseren
description: Meer informatie over hoe co-locating Azure virtual machine scale set resources de prestaties kunnen verbeteren.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 9d6e4e15c552cc4467dd17221a1ba901fd8e4f58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250827"
---
# <a name="co-location"></a>Co-locatie

Een van de grootste bijdragen aan latentie tussen VM's is gewoon afstand.

## <a name="preview-proximity-placement-groups"></a>Voorbeeld: plaatsingsgroepen voor nabijheid 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Volgende stappen

Maak een [plaatsingsgroep](proximity-placement-groups.md) voor nabijheid voor uw schaalset.

