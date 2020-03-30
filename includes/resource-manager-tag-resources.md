---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132183"
---
Als een gebruiker niet over de vereiste toegang voor het toepassen van tags beschikt, u de rol **Tag Inzender** aan de gebruiker toewijzen. Zie [Zelfstudie: Een gebruiker toegang verlenen tot Azure-bronnen met RBAC en de Azure-portal](../articles/role-based-access-control/quickstart-assign-role-user-portal.md)voor meer informatie.

1. Als u de tags voor een resource of een resourcegroep wilt weergeven, zoekt u naar bestaande tags in het overzicht. Als u nog niet eerder tags hebt toegepast, is de lijst leeg.

   ![Tags weergeven voor resource of resourcegroep](./media/resource-manager-tag-resources/view-tags.png)

1. Als u een tag wilt toevoegen, selecteert **u Klik hier om tags toe te voegen**.

1. Geef een naam en waarde op.

   ![Tag toevoegen](./media/resource-manager-tag-resources/add-tag.png)

1. Ga door met het toevoegen van tags als dat nodig is. Selecteer **Opslaan** wanneer u klaar bent.

   ![Tags opslaan](./media/resource-manager-tag-resources/save-tags.png)

1. De tags worden nu weergegeven in het overzicht.

   ![Tags weergeven](./media/resource-manager-tag-resources/view-new-tags.png)

1. Als u een tag wilt toevoegen of verwijderen, selecteert u **Wijzigen**.

1. Als u een tag wilt verwijderen, selecteert u het prullenbakpictogram. Selecteer vervolgens **Opslaan**.

   ![Tag verwijderen](./media/resource-manager-tag-resources/delete-tag.png)

Als u in bulk tags aan meerdere bronnen wilt toewijzen, moet u tags toewijzen:

1. Schakel in een lijst met bronnen het selectievakje in voor de resources die u de tag wilt toewijzen. Selecteer vervolgens **Tags toewijzen**.

   ![Meerdere bronnen selecteren](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Namen en waarden toevoegen. Selecteer **Opslaan** wanneer u klaar bent.

   ![Toewijzen selecteren](./media/resource-manager-tag-resources/select-assign.png)

Ga als lid van het werk om alle bronnen met een tag weer te geven:

1. Zoek in het menu azure portal naar **tags**. Selecteer deze in de beschikbare opties.

   ![Zoeken op tag](./media/resource-manager-tag-resources/find-tags-general.png)

1. Selecteer de tag voor het weergeven van bronnen.

   ![Tag selecteren](./media/resource-manager-tag-resources/select-tag.png)

1. Alle bronnen met die tag worden weergegeven.

   ![Bronnen weergeven op tag](./media/resource-manager-tag-resources/view-resources-by-tag.png)
