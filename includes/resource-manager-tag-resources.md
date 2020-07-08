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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80132183"
---
Als een gebruiker niet beschikt over de vereiste toegang voor het Toep assen van labels, kunt u de rol van de **Label bijdrage** toewijzen aan de gebruiker. Zie [zelf studie: een gebruiker toegang verlenen tot Azure-resources met RBAC en de Azure Portal](../articles/role-based-access-control/quickstart-assign-role-user-portal.md)voor meer informatie.

1. Als u de tags voor een resource of resource groep wilt weer geven, zoekt u naar bestaande tags in het overzicht. Als u nog niet eerder tags hebt toegepast, is de lijst leeg.

   ![Tags voor de resource of resource groep weer geven](./media/resource-manager-tag-resources/view-tags.png)

1. Als u een tag wilt toevoegen, selecteert u **hier klikken om tags toe te voegen**.

1. Geef een naam en waarde op.

   ![Tag toevoegen](./media/resource-manager-tag-resources/add-tag.png)

1. Ga verder met het toevoegen van tags als dat nodig is. Selecteer **Opslaan** wanneer u klaar bent.

   ![Tags opslaan](./media/resource-manager-tag-resources/save-tags.png)

1. De tags worden nu weer gegeven in het overzicht.

   ![Labels weer geven](./media/resource-manager-tag-resources/view-new-tags.png)

1. Selecteer **wijzigen**om een tag toe te voegen of te verwijderen.

1. Als u een tag wilt verwijderen, selecteert u het prullenbak pictogram. Selecteer vervolgens **Opslaan**.

   ![Tag verwijderen](./media/resource-manager-tag-resources/delete-tag.png)

Voor het bulksgewijs toewijzen van labels aan meerdere resources:

1. Schakel in een lijst met resources het selectie vakje in voor de resources die u wilt toewijzen aan de tag. Selecteer vervolgens **labels toewijzen**.

   ![Meerdere resources selecteren](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Voeg namen en waarden toe. Selecteer **Opslaan** wanneer u klaar bent.

   ![Toewijzing selecteren](./media/resource-manager-tag-resources/select-assign.png)

Om alle resources met een tag weer te geven:

1. Zoek in het menu Azure Portal naar **labels**. Selecteer de optie in de beschik bare opties.

   ![Zoeken op label](./media/resource-manager-tag-resources/find-tags-general.png)

1. Selecteer de tag voor het weer geven van resources.

   ![Tag selecteren](./media/resource-manager-tag-resources/select-tag.png)

1. Alle resources met die tag worden weer gegeven.

   ![Resources weer geven op label](./media/resource-manager-tag-resources/view-resources-by-tag.png)
