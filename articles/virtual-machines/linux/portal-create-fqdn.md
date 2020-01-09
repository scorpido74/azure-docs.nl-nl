---
title: FQDN maken voor een virtuele Linux-machine in de Azure Portal
description: Meer informatie over het maken van een FQDN-naam (Fully Qualified Domain Name) voor een virtuele machine op basis van een resource manager in de Azure Portal.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 256e1e60ec8f50df2faea64f31e88d00370b33c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458718"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Een Fully Qualified Domain Name maken in de Azure Portal voor een Linux-VM

Wanneer u een virtuele machine (VM) maakt in de [Azure Portal](https://portal.azure.com), wordt er automatisch een open bare IP-resource voor de virtuele machine gemaakt. U gebruikt dit IP-adres om op afstand toegang te krijgen tot de virtuele machine. Hoewel de portal geen [Fully Qualified Domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)of FQDN maakt, kunt u één keer toevoegen wanneer de virtuele machine is gemaakt. In dit artikel worden de stappen beschreven voor het maken van een DNS-naam of FQDN.

## <a name="create-a-fqdn"></a>Een FQDN maken
In dit artikel wordt ervan uitgegaan dat u al een virtuele machine hebt gemaakt. Als dat nodig is, kunt u [een virtuele machine maken in de portal](quick-create-portal.md) of [met de Azure cli](quick-create-cli.md). Volg deze stappen als uw virtuele machine actief is:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

U kunt nu extern verbinding maken met de virtuele machine met behulp van deze DNS-naam, zoals bij `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Volgende stappen
Nu uw VM een open bare IP-en DNS-naam heeft, kunt u algemene toepassings raamwerken of-services implementeren, zoals nginx, MongoDB, docker, enzovoort.

U kunt ook meer lezen over het [gebruik van Resource Manager](../../azure-resource-manager/management/overview.md) voor tips over het bouwen van uw Azure-implementaties.

