---
title: 'Azure VPN-gateway: een gatewayverbinding verifiëren'
description: In dit artikel ziet u hoe u een VPN-gatewayverbinding voor virtuele netwerken verifiëren.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 40417b078577424b1ba13ce60f4f773a1c8fd4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780144"
---
# <a name="verify-a-vpn-gateway-connection"></a>Een VPN-gatewayverbinding verifiëren

In dit artikel ziet u hoe u een VPN-gatewayverbinding verifiëren voor zowel de klassieke als de implementatiemodellen van Resource Manager.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Als u een VPN-gatewayverbinding voor het implementatiemodel van Resource Manager wilt verifiëren met PowerShell, installeert u de nieuwste versie van de [PowerShell-cmdlets](/powershell/azure/overview)van Azure Resource Manager.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure-CLI

Als u een VPN-gatewayverbinding voor het implementatiemodel van Resource Manager wilt verifiëren met Azure CLI, installeert u de nieuwste versie van de [CLI-opdrachten](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 of hoger).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Azure Portal (klassiek)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klassiek)

Installeer de nieuwste versies van de Azure PowerShell-cmdlets om uw VPN-gatewayverbinding voor het klassieke implementatiemodel te verifiëren met PowerShell. Zorg ervoor dat u de [servicebeheermodule](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) downloadt en installeert. Gebruik 'Add-AzureAccount' om in te loggen op het klassieke implementatiemodel.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Volgende stappen

* U kunt virtuele machines toevoegen aan uw virtuele netwerken. Zie [Een virtuele machine maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de stappen.
