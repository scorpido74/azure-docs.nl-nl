---
title: Overzicht van Azure Dedicated Hosts voor virtuele machines
description: Meer informatie over hoe Azure Dedicated Hosts kunnen worden gebruikt voor het implementeren van virtuele machines.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251557"
---
# <a name="azure-dedicated-hosts"></a>Azure Dedicated Hosts

Azure Dedicated Host is een service die fysieke servers biedt - die een of meer virtuele machines kunnen hosten - die zijn toegewezen aan één Azure-abonnement. Dedicated hosts zijn dezelfde fysieke servers die worden gebruikt in onze datacenters, die als een bron worden geleverd. U speciale hosts inrichten binnen een regio, beschikbaarheidszone en foutdomein. Vervolgens u VM's rechtstreeks in uw ingerichte hosts plaatsen, in elke configuratie die het beste aan uw behoeften voldoet.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Volgende stappen

- U een dedicated host implementeren met [Azure PowerShell,](dedicated-hosts-powershell.md)de [portal](dedicated-hosts-portal.md)en [Azure CLI.](../linux/dedicated-hosts-cli.md)

- Er is voorbeeld sjabloon, [hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)gevonden, dat zowel zones en fout domeinen gebruikt voor maximale tolerantie in een regio.

- U ook kosten besparen met een [gereserveerdexemplaar van Azure Dedicated Hosts.](../prepay-dedicated-hosts-reserved-instances.md)
