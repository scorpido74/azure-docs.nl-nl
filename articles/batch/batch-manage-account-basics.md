---
title: 'Uw account beheren: Azure Batch | Microsoft Documenten'
description: Meer informatie over wat een Azure Batch-account omvat
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4df2ec0439e659fd8dc1448c6209c9718114791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479787"
---
# <a name="manage-your-batch-account"></a>Uw Batch-account beheren

Een Batch-account is een uniek geïdentificeerde entiteit in de Batch-service. Alle verwerkingen zijn gekoppeld aan een Batch-account.

U kunt een Azure Batch-account maken met de [Azure Portal](batch-account-create-portal.md) of via een programma, zoals met de [Batch Management .NET-bibliotheek](batch-management-dotnet.md). Wanneer u het account maakt, kunt u een gekoppeld Azure-opslagaccount maken waarin u taakgerelateerde invoer- en uitvoergegevens of toepassingen kunt opslaan.

U kunt in één Batch-account meerdere Batch-workloads uitvoeren of uw workloads verdelen over Batch-accounts in hetzelfde abonnement maar in verschillende Azure-regio's.

## <a name="components-of-the-batch-account"></a>Onderdelen van de Batch-account

Met het Batch-account u hpc-batchtaken (large-scale parallel en high-performance computing) efficiënt uitvoeren in Azure. Binnen het account dat u beheert:

- de toepassingen die u uitvoert

- de toewijzing van pools en knooppunten binnen pools

- het aantal en de soorten taken 

- de invoer en output van gegevens. U hoeft geen extra software te installeren om taken te beheren.

- Wanneer u het Batch-account maakt, wordt u gevraagd er een naam aan toe te wijzen. Deze naam is de id en eenmaal toegewezen kan niet worden gewijzigd.

- Als u de naam van een account wilt wijzigen, moet u deze verwijderen en een nieuw Batch-account maken.

- Het account wordt gemaakt binnen het abonnement dat u wilt gebruiken.

- Gebruik het account om primaire en secundaire accountsleutels te identificeren en op te halen van een Batch-account binnen uw abonnement.

- Het account houdt informatie bij over de toewijzing van de groep en de kernquota.  

- Het account bevat locatiegegevens.

- Het account identificeert uw opslagaccount.

## <a name="next-steps"></a>Volgende stappen

- Maak een Batch-account met de [Azure-portal](batch-account-create-portal.md).
- Maak programmatisch een Batch-account aan, bijvoorbeeld met de [Batchmanagement .NET-bibliotheek](batch-management-dotnet.md).
- [Externe toegang tot rekenknooppunten configureren of uitschakelen in een Azure Batch-groep.](pool-endpoint-configuration.md)
- [Taken voor taakvoorbereiding en taakrelease uitvoeren op batchcomputeknooppunten](batch-job-prep-release.md)

