---
title: Uw account beheren
description: Meer informatie over een Azure Batch account
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83722963"
---
# <a name="manage-your-batch-account"></a>Uw batch-account beheren

Een Batch-account is een uniek geïdentificeerde entiteit in de Batch-service. Alle verwerkingen zijn gekoppeld aan een Batch-account.

U kunt een Azure Batch-account maken met de [Azure Portal](batch-account-create-portal.md) of via een programma, zoals met de [Batch Management .NET-bibliotheek](batch-management-dotnet.md). Wanneer u het account maakt, kunt u een gekoppeld Azure-opslagaccount maken waarin u taakgerelateerde invoer- en uitvoergegevens of toepassingen kunt opslaan.

U kunt in één Batch-account meerdere Batch-workloads uitvoeren of uw workloads verdelen over Batch-accounts in hetzelfde abonnement maar in verschillende Azure-regio's.

## <a name="components-of-the-batch-account"></a>Onderdelen van het batch-account

Met het batch-account kunt u op efficiënte wijze grootschalige parallelle en HPC-batch taken (High Performance Computing) uitvoeren in Azure. Binnen het account dat u beheert:

- De toepassingen die u uitvoert

- De toewijzing van Pools en knoop punten in Pools

- Het aantal en de typen taken 

- De invoer en uitvoer van gegevens. U hoeft geen extra software te installeren om taken te beheren.

- Wanneer u het batch-account maakt, wordt u gevraagd om een naam toe te wijzen. Deze naam is de ID en de toewijzing kan niet worden gewijzigd.

- Als u de naam van een account wilt wijzigen, moet u deze verwijderen en een nieuw batch-account maken.

- Het account wordt gemaakt binnen het abonnement dat u wilt gebruiken.

- Gebruik het account om primaire en secundaire account sleutels te identificeren en op te halen uit een batch-account in uw abonnement.

- Het account houdt informatie bij over pool toewijzing en kern quota.  

- Het account bevat locatie-informatie.

- Het account identificeert uw opslag account.

## <a name="next-steps"></a>Volgende stappen

- Maak een batch-account met behulp van de [Azure Portal](batch-account-create-portal.md).
- Maak via een programma een batch-account, zoals met de [Batch Management .net-bibliotheek](batch-management-dotnet.md).
- [Externe toegang tot reken knooppunten in een Azure batch groep configureren of uitschakelen](pool-endpoint-configuration.md).
- [Taak voorbereiding en taak release taken uitvoeren op batch Compute-knoop punten](batch-job-prep-release.md)

