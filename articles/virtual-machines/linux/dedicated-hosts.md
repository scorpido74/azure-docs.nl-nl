---
title: Overzicht van voor Azure toegewezen hosts voor virtuele machines
description: Meer informatie over hoe met Azure toegewezen hosts kunnen worden gebruikt voor het implementeren van virtuele machines.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970734"
---
# <a name="azure-dedicated-hosts"></a>Met Azure toegewezen hosts

De toegewezen Azure-host is een service die fysieke servers biedt en een of meer virtuele machines kan hosten die zijn toegewezen aan één Azure-abonnement. Toegewezen hosts zijn dezelfde fysieke servers die worden gebruikt in onze data centers als een resource. U kunt toegewezen hosts inrichten binnen een regio, een beschikbaarheids zone en een fout domein. Vervolgens kunt u Vm's rechtstreeks op uw ingerichte hosts plaatsen, in welke configuratie het beste voldoet aan uw behoeften.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Volgende stappen

- U kunt een speciale host implementeren met behulp van [Azure cli](dedicated-hosts-cli.md), [Portal](dedicated-hosts-portal.md)en [Power shell](../windows/dedicated-hosts-powershell.md).

- Zie voor meer informatie het overzicht [gespecialiseerde hosts](dedicated-hosts.md) .

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.

- U kunt ook besparen op kosten met een [gereserveerd exemplaar van voor Azure toegewezen hosts](../prepay-dedicated-hosts-reserved-instances.md).
