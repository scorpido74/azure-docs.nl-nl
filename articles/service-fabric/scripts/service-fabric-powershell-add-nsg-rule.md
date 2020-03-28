---
title: Een regel voor netwerkbeveiligingsgroepen toevoegen in Powershell
description: Azure PowerShell Script-voorbeeld - Hiermee voegt u een netwerkbeveiligingsgroep toe om binnenkomend verkeer op een specifieke poort toe te staan.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 33ee8028031bf94d69d11b08c347f84db94494a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610417"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Een inkomende netwerkbeveiligingsgroepregel toevoegen

Met dit voorbeeldscript wordt een regel voor netwerkbeveiliginggemaakt om binnenkomend verkeer op poort 8081 toe te staan.  Het script krijgt de netwerkbeveiligingsgroep, maakt een nieuwe regel voor netwerkbeveiliging en werkt de netwerkbeveiligingsgroep bij. Pas de parameters zo nodig aan.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer zo nodig Azure PowerShell met behulp van de instructies in de [Azure PowerShell-handleiding](/powershell/azure/overview). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Haalt de `Microsoft.Network/networkSecurityGroups`-resource op. |
|[Get-azNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Krijgt de netwerkbeveiligingsgroep bij naam.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u een configuratie van de netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
|[Set-azNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Hiermee stelt u de doelstatus in voor een netwerkbeveiligingsgroep.|

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).
