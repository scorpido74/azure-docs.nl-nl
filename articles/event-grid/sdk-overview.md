---
title: Azure Event Grid Sdk's
description: Hierin worden de Sdk's voor Azure Event Grid beschreven. Deze Sdk's bieden beheer, publicatie en verbruik.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: 2b21d8bf2c393b096e9046765e6cfadc76baffbf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119103"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid Sdk's voor beheer en publicatie

Event Grid biedt Sdk's waarmee u uw resources programmatisch kunt beheren en gebeurtenissen kunt plaatsen.

## <a name="management-sdks"></a>Management-Sdk's

Met de Sdk's voor beheer kunt u Event grid-onderwerpen en-abonnementen maken, bijwerken en verwijderen. Momenteel zijn de volgende Sdk's beschikbaar:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Knooppunt](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Gegevens vlak Sdk's

Met de Sdk's voor gegevens vlakken kunt u gebeurtenissen naar onderwerpen posten door te verifiëren, de gebeurtenis te vormen en asynchroon te boeken naar het opgegeven eind punt. U kunt er ook voor zorgen dat u gebeurtenissen van de eerste partij gebruikt. Momenteel zijn de volgende Sdk's beschikbaar:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Knooppunt](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid code voorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid)voor voorbeeld toepassingen.
* Zie [Wat is Event grid?](overview.md) voor een inleiding tot Event grid.
* Zie [Azure cli](/cli/azure/eventgrid)voor Event grid-opdrachten in azure cli.
* Zie voor Event Grid-opdrachten in Power shell [Power shell](/powershell/module/az.eventgrid).
