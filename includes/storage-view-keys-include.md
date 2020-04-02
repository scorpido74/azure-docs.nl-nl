---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521288"
---
Ga als het gaat om het weergeven en kopiëren van uw toegangssleutels voor opslagaccount of verbindingstekenreeks vanuit de Azure-portal:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. Selecteer **Access-toetsen**onder **Instellingen**. De toegangssleutels van uw account worden weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Sleutel** onder **key1** en klik op de knop **Kopiëren** om de accountsleutel te kopiëren.
5. U afwisselend de volledige verbindingstekenreeks kopiëren. Zoek de waarde van de **Verbindingsreeks** onder **key1** en klik op de knop **Kopiëren** om de verbindingsreeks te kopiëren.

    ![Schermafbeelding van het weergeven van toegangssleutels in de Azure-portal](media/storage-view-keys-include/portal-connection-string.png)

U een van beide sleutels gebruiken om toegang te krijgen tot Azure Storage, maar over het algemeen is het een goede gewoonte om de eerste sleutel te gebruiken en het gebruik van de tweede sleutel te reserveren voor wanneer u sleutels draait.

Om de toegangssleutels van een account te bekijken of te lezen, moet de gebruiker een Serviceadministrator zijn of een RBAC-rol krijgen toegewezen die de **Microsoft.Storage/storageAccounts/listkeys/action**bevat. Sommige ingebouwde RBAC-rollen die deze actie bevatten, zijn de rollen van de functie **rolrollen Van**de eigenaar, **de inzender**en **de opslagaccountsleutel.** Zie Klassieke functies voor [abonnementsbeheerders, Azure RBAC-rollen en Azure AD-rollen](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie over de functie Servicebeheerder. Zie de sectie **Opslag** in [azure-ingebouwde rollen voor Azure RBAC voor](../articles/role-based-access-control/built-in-roles.md#storage)meer informatie over ingebouwde rollen voor Azure Storage.
