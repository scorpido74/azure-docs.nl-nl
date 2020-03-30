---
title: Azure Event Grid SDKs
description: Beschrijft de SDK's voor Azure Event Grid. Deze SDK's bieden beheer, publicatie en verbruik.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60822846"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid SDKs voor beheer en publicatie

Event Grid biedt SDK's waarmee u uw resources programmatisch beheren en gebeurtenissen plaatsen.

## <a name="management-sdks"></a>Beheer SDKs

Met de beheer-SDK's u onderwerpen en abonnementen voor gebeurtenisrasters maken, bijwerken en verwijderen. Momenteel zijn de volgende SDK's beschikbaar:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [OK](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Datavlak SDKs

Met de SDK's met gegevensvlak u gebeurtenissen naar onderwerpen plaatsen door te zorgen voor het verifiëren, de gebeurtenis te vormen en asynchroon te posten op het opgegeven eindpunt. Ze stellen u ook in staat om first party-evenementen te consumeren. Momenteel zijn de volgende SDK's beschikbaar:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [OK](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Volgende stappen

* Zie bijvoorbeeld toepassingen, zie [Voorbeelden van gebeurtenisrastercodes](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Zie Wat is Event Grid voor een inleiding tot Event [Grid?](overview.md)
* Zie [Azure CLI](/cli/azure/eventgrid)voor opdrachten voor gebeurtenisrasters in Azure CLI .
* Zie [PowerShell](/powershell/module/az.eventgrid)voor opdrachten voor gebeurtenisrasters in PowerShell.
