---
title: Rollen en machtigingen voor Azure Data Factory
description: Beschrijft de rollen en machtigingen die nodig zijn om gegevensfabrieken te maken en om te werken met onderliggende bronnen.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 8b7791c5c04e986b30959d2fcae17142fdd8b7ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418317"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Rollen en machtigingen voor Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


In dit artikel worden de rollen beschreven die nodig zijn voor het maken en beheren van Azure Data Factory-resources en de machtigingen die door die rollen worden verleend.

## <a name="roles-and-requirements"></a>Functies en vereisten

Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rol *Inzender* of *Eigenaar*, of moet dit een *beheerder* van het Azure-abonnement zijn. Als u de machtigingen die u in het abonnement hebt, wilt bekijken, gaat u naar Azure Portal, selecteert u rechtsboven uw gebruikersnaam en selecteert u vervolgens **Machtigingen**. Als u toegang tot meerdere abonnementen hebt, moet u het juiste abonnement selecteren. 

Als u onderliggende resources wilt maken en beheren voor Data Factory, waaronder gegevenssets, gekoppelde services, pijplijnen, triggers en integratieruntimes, zijn de volgende vereisten van toepassing:
- Als u onderliggende resources in Azure Portal wilt maken en beheren, moet u de rol **Data Factory-inzender** op minimaal het niveau van de resourcegroep hebben.
- Voor het maken en beheren van onderliggende resources met PowerShell of de SDK is de rol **Inzender** op minimaal het resourceniveau voldoende.

Zie het artikel [Rollen toevoegen](../cost-management-billing/manage/add-change-subscription-administrator.md) voor voorbeelden van instructies voor het toevoegen van een gebruiker aan een rol.

## <a name="set-up-permissions"></a>Machtigingen instellen

Nadat u een gegevensfabriek hebt gemaakt, u andere gebruikers laten werken met de gegevensfabriek. Om deze toegang te geven aan andere gebruikers, moet u deze toevoegen aan de ingebouwde rol **in datafabriekinzender** in de brongroep die de gegevensfabriek bevat.

### <a name="scope-of-the-data-factory-contributor-role"></a>Bereik van de rol datafabriekbijdrager

Met het lidmaatschap van de rol **Data Factory-bijdrager** kunnen gebruikers de volgende dingen doen:
- Maak, bewerk en verwijder gegevensfabrieken en onderliggende bronnen, waaronder gegevenssets, gekoppelde services, pijplijnen, triggers en gebruikstijden voor integratie.
- Resourcebeheersjablonen implementeren. Resource manager-implementatie is de implementatiemethode die wordt gebruikt door Gegevensfabriek in de Azure-portal.
- App Insights-waarschuwingen beheren voor een gegevensfabriek.
- Maak ondersteuningstickets.

Zie De rol Van [datafabriekbijdrager](../role-based-access-control/built-in-roles.md#data-factory-contributor)voor meer informatie over deze rol .

### <a name="resource-manager-template-deployment"></a>Implementatie van resourcebeheer-sjabloon

Met de rol **Data Factory Inzender,** op het niveau van de resourcegroep of hoger, kunnen gebruikers Resource Manager-sjablonen implementeren. Als gevolg hiervan kunnen leden van de rol Resource Manager-sjablonen gebruiken om zowel gegevensfabrieken als hun onderliggende resources te implementeren, waaronder gegevenssets, gekoppelde services, pijplijnen, triggers en implementatieruntimes. Lidmaatschap in deze rol laat de gebruiker echter geen andere bronnen maken.

Machtigingen voor Azure Repos en GitHub zijn onafhankelijk van machtigingen voor Gegevensfabriek. Als gevolg hiervan kan een gebruiker met repo-machtigingen die alleen lid is van de reader-rol onderliggende bronnen van Data Factory bewerken en wijzigingen in de repo vastleggen, maar deze wijzigingen niet publiceren.

> [!IMPORTANT]
> De implementatie van resourcebeheersjablonen met de rol **Data Factory-inzender** verhoogt uw machtigingen niet. Als u bijvoorbeeld een sjabloon implementeert waarmee een virtuele Azure-machine wordt gemaakt en u geen toestemming hebt om virtuele machines te maken, mislukt de implementatie met een autorisatiefout.

### <a name="custom-scenarios-and-custom-roles"></a>Aangepaste scenario's en aangepaste rollen

Soms moet u verschillende toegangsniveaus toekennen aan verschillende gebruikers in de gegevensfabriek. Bijvoorbeeld:
- Mogelijk hebt u een groep nodig waar gebruikers alleen machtigingen hebben voor een specifieke gegevensfabriek.
- Of u hebt een groep nodig waar gebruikers alleen een gegevensfabriek (of fabrieken) kunnen controleren, maar deze niet kunnen wijzigen.

U deze aangepaste scenario's bereiken door aangepaste rollen te maken en gebruikers aan die rollen toe te wijzen. Zie Aangepaste rollen in [Azure voor](..//role-based-access-control/custom-roles.md)meer informatie over aangepaste rollen.

Hier volgen een paar voorbeelden die aantonen wat u bereiken met aangepaste rollen:

- Laat een gebruiker een gegevensfabriek in een resourcegroep maken, bewerken of verwijderen vanuit de Azure-portal.

  Wijs de ingebouwde **bijdragerin datafabriek toe** op het niveau van de resourcegroep voor de gebruiker. Als u toegang wilt verlenen tot een gegevensfabriek in een abonnement, wijst u de rol toe op abonnementsniveau.

- Laat een gebruiker een gegevensfabriek bekijken (lezen) en controleren, maar deze niet bewerken of wijzigen.

  Wijs de ingebouwde **leesrol** toe aan de bron van de gegevensfabriek voor de gebruiker.

- Laat een gebruiker één gegevensfabriek bewerken in de Azure-portal.

  Dit scenario vereist twee roltoewijzingen.

  1. Wijs de ingebouwde **bijdragerfunctie** toe op het niveau van de gegevensfabriek.
  2. Maak een aangepaste rol met de machtiging **Microsoft.Resources/deployments/**. Wijs deze aangepaste rol toe aan de gebruiker op resourcegroepniveau.

- Laat een gebruiker alleen verbinding in een gekoppelde service testen

    Maak een aangepaste rolrol met machtigingen voor de volgende acties: **Microsoft.DataFactory/factories/getFeatureValue/read** en **Microsoft.DataFactory/factories/getDataPlaneAccess/read**. Wijs deze aangepaste rol toe aan de bron van de gegevensfabriek voor de gebruiker.

- Laat een gebruiker een gegevensfabriek bijwerken vanuit PowerShell of de SDK, maar niet in de Azure-portal.

  Wijs de ingebouwde **bijdragerrol** toe aan de bron van de gegevensfabriek voor de gebruiker. Met deze rol kan de gebruiker de bronnen in de Azure-portal zien, maar heeft de gebruiker geen toegang tot de knoppen **Alle publiceren** en **publiceren.**

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over rollen in Azure - [Functiedefinities begrijpen](../role-based-access-control/role-definitions.md)

- Meer informatie over de rol **van datafactory-bijdrager** - [rol van datafabriekbijdrager](../role-based-access-control/built-in-roles.md#data-factory-contributor).
