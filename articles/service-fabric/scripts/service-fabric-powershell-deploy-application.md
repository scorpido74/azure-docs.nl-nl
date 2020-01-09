---
title: Toepassing implementeren in een cluster in Power shell
description: Azure PowerShell-voorbeeld script-een toepassing implementeren op een Service Fabric cluster.
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
ms.openlocfilehash: 207f2a4e8173aa1e5009435665532973045d9198
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610298"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Een toepassing implementeren naar een Service Fabric-cluster

Met dit voorbeeld script wordt een toepassings pakket gekopieerd naar een cluster installatie kopie archief, wordt het toepassings type geregistreerd in het cluster, wordt het overbodige toepassings pakket verwijderd en wordt er een toepassings exemplaar van het toepassings type gemaakt.  Als er standaard services zijn gedefinieerd in het toepassings manifest van het type doel toepassing, worden deze services op dit moment gemaakt. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeld script is uitgevoerd, kan het script voor het [verwijderen van een toepassing](service-fabric-powershell-remove-application.md) worden gebruikt om het toepassings exemplaar te verwijderen, de registratie van het toepassings type op te heffen en het toepassings pakket te verwijderen uit het archief met installatie kopieën.

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Hiermee maakt u een verbinding met een Service Fabric cluster. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopieert een toepassings pakket naar het cluster installatie kopie archief.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registreert een toepassings type en-versie op het cluster. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Hiermee maakt u een toepassing van een geregistreerd toepassings type. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee verwijdert u een Service Fabric toepassings pakket uit het archief met installatie kopieën.|

## <a name="next-steps"></a>Volgende stappen

Zie [Azure PowerShell-documentatie](/powershell/azure/service-fabric/?view=azureservicefabricps)voor meer informatie over de service Fabric Power shell-module.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
