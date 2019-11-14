---
title: FQDN maken voor een Windows-VM in de Azure Portal
description: Meer informatie over het maken van een FQDN-naam (Fully Qualified Domain Name) voor een virtuele machine op basis van een resource manager in de Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 727636b4b59bf4b31fd7592b1a0f2d52d5dbbfd3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032990"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Een Fully Qualified Domain Name maken in de Azure Portal voor een Windows-VM

Wanneer u een virtuele machine (VM) maakt in de [Azure Portal](https://portal.azure.com), wordt er automatisch een open bare IP-resource voor de virtuele machine gemaakt. U gebruikt dit IP-adres om op afstand toegang te krijgen tot de virtuele machine. Hoewel de portal geen [Fully Qualified Domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)of FQDN maakt, kunt u één keer maken nadat de virtuele machine is gemaakt. In dit artikel worden de stappen beschreven voor het maken van een DNS-naam of FQDN.

## <a name="create-a-fqdn"></a>Een FQDN maken
In dit artikel wordt ervan uitgegaan dat u al een virtuele machine hebt gemaakt. Als dat nodig is, kunt u [een virtuele machine maken in de portal](quick-create-portal.md) of [met Azure PowerShell](quick-create-powershell.md). Volg deze stappen als uw virtuele machine actief is:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

U kunt nu extern verbinding maken met de virtuele machine met behulp van deze DNS-naam, zoals voor Remote Desktop Protocol (RDP).

## <a name="next-steps"></a>Volgende stappen
Nu uw VM een open bare IP-en DNS-naam heeft, kunt u algemene toepassings raamwerken of-services implementeren, zoals IIS, SQL of share point.

U kunt ook meer lezen over het [gebruik van Resource Manager](../../azure-resource-manager/resource-group-overview.md) voor tips over het bouwen van uw Azure-implementaties.

