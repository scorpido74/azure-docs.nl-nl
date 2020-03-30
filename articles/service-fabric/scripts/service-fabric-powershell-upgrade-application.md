---
title: Een Service Fabric-toepassing upgraden in Powershell
description: Azure PowerShell Script-voorbeeld - Upgrade en monitor een Azure Service Fabric-toepassing met Powershell.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614737"
---
# <a name="upgrade-a-service-fabric-application"></a>Een Service Fabric-toepassing upgraden

Met dit voorbeeldscript wordt een uitvoerende servicefabric-toepassingsinstantie ge-op-versie 1.3.0 uitgevoerd. Het script kopieert het nieuwe toepassingspakket naar het clusterimagearchief, registreert het toepassingstype en verwijdert het onnodige toepassingspakket.  Het script start een bewaakte upgrade en controleert continu de upgradestatus totdat de upgrade is voltooid of teruggedraaid. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-ServiceFabric-toepassing](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Haalt alle toepassingen in het cluster Service Fabric of een specifieke toepassing.  |
| [Get-serviceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Krijgt de status van een servicefabric-toepassingsupgrade. |
| [Get-serviceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Als u de toepassingstypen Service Fabric laat registreren in het cluster Service Fabric. |
| [Register-ServiceFabricApplicationType uitschrijven](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | De registratie van een servicefabric-toepassingstype wordt uitgedaan.  |
| [Copy-serviceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee kopieert u een servicefabric-toepassingspakket naar de afbeeldingswinkel.  |
| [Register-serviceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Registreert een servicefabric-toepassingstype. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Hiermee wordt een Service Fabric-toepassing bijgewerkt naar de opgegeven versie van het toepassingstype. |
| [Remove-serviceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee verwijdert u een servicefabric-toepassingspakket uit het afbeeldingsarchief.|


## <a name="next-steps"></a>Volgende stappen

Zie [Azure PowerShell-documentatie](/powershell/azure/service-fabric/?view=azureservicefabricps)voor meer informatie over de PowerShell-module servicefabric.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
