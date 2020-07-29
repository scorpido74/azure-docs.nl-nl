---
title: Overzicht van voor Azure toegewezen hosts voor virtuele machines
description: Meer informatie over hoe met Azure toegewezen hosts kunnen worden gebruikt voor het implementeren van virtuele machines.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 327eebb4ea1804fc19c8f1b82a57dd0a9a994803
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284571"
---
# <a name="azure-dedicated-hosts"></a>Met Azure toegewezen hosts

De toegewezen Azure-host is een service die fysieke servers biedt en een of meer virtuele machines kan hosten die zijn toegewezen aan één Azure-abonnement. Toegewezen hosts zijn dezelfde fysieke servers die worden gebruikt in onze data centers als een resource. U kunt toegewezen hosts inrichten binnen een regio, een beschikbaarheids zone en een fout domein. Vervolgens kunt u Vm's rechtstreeks op uw ingerichte hosts plaatsen, in welke configuratie het beste voldoet aan uw behoeften.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Volgende stappen

- U kunt een toegewezen host implementeren met behulp van [Azure PowerShell](dedicated-hosts-powershell.md), de [Portal](dedicated-hosts-portal.md)en [Azure cli](../linux/dedicated-hosts-cli.md).

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.

- U kunt ook besparen op kosten met een [gereserveerd exemplaar van voor Azure toegewezen hosts](../prepay-dedicated-hosts-reserved-instances.md).
