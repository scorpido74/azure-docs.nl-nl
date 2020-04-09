---
title: De RDP-gebruikersnaam en -wachtwoord bijwerken in PowerShell
description: Azure PowerShell-voorbeeldscript - RDP-gebruikersnaam en -wachtwoord bijwerken voor alle Service Fabric-clusterknooppunten van een bepaald knooppunttype.
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
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b7f229162ed745408121d898f5af516d22a0c039
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984926"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Gebruikersnaam en wachtwoord van de beheerder van de VM's in een cluster bijwerken

Elk [knooppunttype](../service-fabric-cluster-nodetypes.md) in een Service Fabric-cluster is een virtuele-machineschaalset. Met dit voorbeeldscript worden de gebruikersnaam en het wachtwoord van de beheerder voor de virtuele clustermachines in een specifiek knooppunttype bijgewerkt.  Voeg de extensie VMAccessAgent aan de schaalset toe, want het beheerderswachtwoord is geen bewerkbare schaalseteigenschap.  De wijziging van de gebruikersnaam en het wachtwoord is van toepassing op alle knooppunten in de schaalset. Pas de parameters zo nodig aan.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer zo nodig Azure PowerShell met behulp van de instructies in de [Azure PowerShell-handleiding](/powershell/azure/overview). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Hiermee worden de eigenschappen van een clusterknooppunttype (een virtuele-machineschaalset) opgehaald.   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Hiermee wordt een extensie toegevoegd aan de virtuele-machineschaalset.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Hiermee wordt de status van een virtuele-machineschaalset ingesteld op de status van een lokaal VMSS-object.|

## <a name="duration"></a>Duur

Een enkel knooppunttype met bijvoorbeeld vijf knooppunten heeft een duur van 45 tot 60 minuten om de gebruikersnaam of het wachtwoord te wijzigen. 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
