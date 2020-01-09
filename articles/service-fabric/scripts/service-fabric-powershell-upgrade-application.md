---
title: Een Service Fabric-toepassing bijwerken in Power shell
description: Azure PowerShell script voorbeeld-een Azure Service Fabric-toepassing bijwerken en bewaken met behulp van Power shell.
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
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 3a4ef9fad8567eb145d51c6fef61773cc3a00b11
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614737"
---
# <a name="upgrade-a-service-fabric-application"></a>Een Service Fabric-toepassing bijwerken

Met dit voorbeeld script wordt een actief Service Fabric toepassings exemplaar bijgewerkt naar versie 1.3.0. Het script kopieert het nieuwe toepassings pakket naar het cluster installatie kopie archief, registreert het toepassings type en verwijdert het overbodige toepassings pakket.  Het script start een bewaakte upgrade en controleert doorlopend de status van de upgrade totdat de upgrade is voltooid of teruggedraaid. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Hiermee worden alle toepassingen in het Service Fabric cluster of een specifieke toepassing opgehaald.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Hiermee wordt de status van een Service Fabric toepassing bijgewerkt. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Hiermee haalt u de Service Fabric toepassings typen die zijn geregistreerd op het Service Fabric cluster. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Hiermee wordt de registratie van een Service Fabric toepassings type ongedaan gemaakt.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee wordt een Service Fabric toepassings pakket gekopieerd naar de archief met installatie kopieën.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Registreert een Service Fabric toepassings type. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Hiermee wordt een Service Fabric-toepassing bijgewerkt naar de opgegeven versie van het toepassings type. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee verwijdert u een Service Fabric toepassings pakket uit het archief met installatie kopieën.|


## <a name="next-steps"></a>Volgende stappen

Zie [Azure PowerShell-documentatie](/powershell/azure/service-fabric/?view=azureservicefabricps)voor meer informatie over de service Fabric Power shell-module.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
