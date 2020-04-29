---
title: Azure PowerShell-voorbeeldscript - Het RDP-poortbereik wijzigen | Microsoft Docs
description: Azure PowerShell-voorbeeldscript - Wijzigt het RDP-poortbereik van een geïmplementeerd cluster.
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.custom: sfrev
ms.openlocfilehash: b3d922cb5a7cdf34e63731ab32f21c94d7b6bca0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76025020"
---
# <a name="update-the-rdp-port-range-values"></a>De waarden voor het RDP-poortbereik bijwerken

Met dit voorbeeldscript worden de waarden voor het RDP-poortbereik op de clusterknooppunt-VM's gewijzigd nadat het cluster is geïmplementeerd.  Azure PowerShell wordt gebruikt, zodat de onderliggende VM's niet uit en aan hoeven te worden gezet.  Met het script wordt `Microsoft.Network/loadBalancers` de resource in de resource groep van het cluster opgehaald `inboundNatPools.frontendPortRangeStart` en `inboundNatPools.frontendPortRangeEnd` worden de waarden en bijgewerkt. Pas de parameters zo nodig aan.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer zo nodig Azure PowerShell met behulp van de instructies in de [Azure PowerShell-handleiding](/powershell/azure/overview).

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Haalt de `Microsoft.Network/loadBalancers`-resource op. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Werkt de `Microsoft.Network/loadBalancers`-resource bij.|

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
