---
title: FQDN maken voor een Windows-VM in de Azure Portal
description: Meer informatie over het maken van een FQDN-naam (Fully Qualified Domain Name) voor een virtuele machine op basis van een resource manager in de Azure Portal.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7708e2ad7e84c4449d425e8bd6eb7d4d1f6a27eb
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288243"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Een Fully Qualified Domain Name maken in de Azure Portal voor een Windows-VM

Wanneer u een virtuele machine (VM) maakt in de [Azure Portal](https://portal.azure.com), wordt er automatisch een open bare IP-resource voor de virtuele machine gemaakt. U gebruikt dit IP-adres om op afstand toegang te krijgen tot de virtuele machine. Hoewel de portal geen [Fully Qualified Domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)of FQDN maakt, kunt u één keer maken nadat de virtuele machine is gemaakt. In dit artikel worden de stappen beschreven voor het maken van een DNS-naam of FQDN.

## <a name="create-a-fqdn"></a>Een FQDN maken
In dit artikel wordt ervan uitgegaan dat u al een virtuele machine hebt gemaakt. Als dat nodig is, kunt u [een virtuele machine maken in de portal](quick-create-portal.md) of [met Azure PowerShell](quick-create-powershell.md). Volg deze stappen als uw virtuele machine actief is:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

U kunt nu extern verbinding maken met de virtuele machine met behulp van deze DNS-naam, zoals voor Remote Desktop Protocol (RDP).

## <a name="next-steps"></a>Volgende stappen
Nu uw VM een open bare IP-en DNS-naam heeft, kunt u algemene toepassings raamwerken of-services implementeren, zoals IIS, SQL of share point.

U kunt ook meer lezen over het [gebruik van Resource Manager](../../azure-resource-manager/management/overview.md) voor tips over het bouwen van uw Azure-implementaties.

