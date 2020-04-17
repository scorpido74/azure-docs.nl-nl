---
title: FQDN maken voor een Windows-vm in de Azure-portal
description: Meer informatie over het maken van een volledig gekwalificeerde domeinnaam of FQDN voor een virtuele machine op basis van Resource Manager in de Azure-portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7bfa510068d71afb7701ab8964f06053d38ac70
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458893"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Een volledig gekwalificeerde domeinnaam maken in de Azure-portal voor een Windows-vm

Wanneer u een virtuele machine (VM) maakt in de [Azure-portal,](https://portal.azure.com)wordt automatisch een openbare IP-bron voor de virtuele machine gemaakt. U gebruikt dit IP-adres om op afstand toegang te krijgen tot de VM. Hoewel de portal geen [volledig gekwalificeerde domeinnaam](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)of FQDN maakt, u er een maken zodra de VM is gemaakt. In dit artikel worden de stappen getoond om een DNS-naam of FQDN te maken.

## <a name="create-a-fqdn"></a>Een FQDN maken
In dit artikel wordt ervan uitgegaan dat u al een vm hebt gemaakt. Indien nodig u [een VM maken in de portal](quick-create-portal.md) of met Azure [PowerShell.](quick-create-powershell.md) Volg deze stappen zodra uw vm actief is:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

U nu op afstand verbinding maken met de VM met deze DNS-naam, zoals voor Extern bureaublad-protocol (RDP).

## <a name="next-steps"></a>Volgende stappen
Nu uw VM een openbare IP- en DNS-naam heeft, u algemene toepassingsframeworks of -services implementeren, zoals IIS, SQL of SharePoint.

U ook meer lezen over [het gebruik van Resource Manager](../../azure-resource-manager/management/overview.md) voor tips over het bouwen van uw Azure-implementaties.

