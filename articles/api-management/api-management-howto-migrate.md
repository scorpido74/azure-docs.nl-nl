---
title: Azure API-beheer migreren tussen regio's
description: Meer informatie over het migreren van een API-beheerexemplaar van de ene regio naar de andere.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073466"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Azure API-beheer migreren tussen regio's
Als u API Management-exemplaren wilt migreren van de ene Azure-regio naar de andere, u de [back-up- en herstelfunctie](api-management-howto-disaster-recovery-backup-restore.md) gebruiken. U moet dezelfde prijscategorie API-beheer kiezen in de bron- en doelregio's. 

> [!NOTE]
> Back-upmaken en herstellen werkt niet tijdens het migreren tussen verschillende cloudtypen. Daarvoor moet u de resource [exporteren als sjabloon.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) Pas vervolgens de geëxporteerde sjabloon aan voor het doelazure-gebied en maak de bron opnieuw. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Optie 1: Een andere instantienaam API-beheer gebruiken

1. Maak in het doelgebied een nieuwe API-beheerinstantie met dezelfde prijscategorie als de instantie Bron API-beheer. Het nieuwe exemplaar moet een andere naam hebben. 
1. Een back-up maken van bestaande API-beheerinstantie naar een opslagaccount.
1. De back-up die in stap 2 is gemaakt, herstellen naar de nieuwe instantie API-beheer die in de nieuwe regio in stap 1 is gemaakt.
1. Als u een aangepast domein hebt dat naar de instantie API-beheer van het brongebied wijst, werkt u het aangepaste domein CNAME bij om naar de nieuwe instantie API-beheer te verwijzen. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Optie 2: Dezelfde instantienaam API-beheer gebruiken

> [!NOTE]
> Deze optie zal resulteren in downtime tijdens de migratie.

1. Back-ups maken van de instantie API-beheer in het brongebied naar een opslagaccount.
1. Verwijder het API-beheer in het brongebied. 
1. Maak een nieuwe api-beheerinstantie in het doelgebied met dezelfde naam als die in het brongebied.
1. De back-up die in stap 1 is gemaakt, herstellen naar de nieuwe instantie API-beheer in het doelgebied.  


## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen
* Zie voor meer informatie over de back-up- en herstelfunctie hoe u [disaster recovery implementeert.](api-management-howto-disaster-recovery-backup-restore.md)
* Zie [Migratierichtlijnen voor azure-migratie](https://github.com/Azure/Azure-Migration-Guidance)voor informatie over migratie azure-bronnen voor verschillende regio's .